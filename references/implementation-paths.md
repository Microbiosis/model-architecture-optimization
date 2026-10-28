# 框架与特殊执行路径检查指引

本文件是仓库审查导航，不代表项目必然使用这些框架。先按主技能的证据层级追踪真实入口，再选匹配分支；记录框架/运行时版本、有效配置、调用位置和本次运行覆盖。仅依赖声明或静态可达路径不能表述为实际运行。表中命令是最小验证片段，假设模型已通过项目真实入口构造；按实际变量、输入签名、版本和权限调整，片段通过也只证明其覆盖范围。

## 通用追踪顺序

1. 从应用入口找到模型构造/加载、配置解析、权重装载、实际 `forward`/`apply` 或生成调用；记录文件、符号、触发条件和动态路由。
2. 追踪 eager、trace/script、graph capture、compile/export、服务端引擎等实际运行边界。确认被调用的是修改后的模块/图，不是旁路实现、旧导出物或远端服务。
3. 检查张量契约：输入/输出树结构、shape、轴语义、dtype/device、mask、动态维度、状态/RNG、参数注册和序列化。
4. 对实际路径运行最小固定夹具；如存在多执行模式，分别比较参考路径和目标路径，并标注未覆盖分支。编译/捕获初始化与稳态性能计时分开。

## 框架路径速查

| 路径 | 优先追踪与核验 | 常见误判/补充验证 | 最小验证片段（按项目变量替换） |
| --- | --- | --- | --- |
| PyTorch eager | 从 factory/config 到 `nn.Module`、`forward` 和调用方；检查子模块注册、`named_parameters`、`state_dict`、设备迁移及保存/加载。 | 普通 Python list/dict 中的层可能未注册；只定义模块不表示接入前向。训练任务才检查梯度和 optimizer param groups；推理专用/冻结参数不要求进 optimizer。 | `print(model); print([(n, tuple(p.shape)) for n,p in model.named_parameters()]); model.eval(); model(x)` |
| PyTorch FX / `torch.compile` | 找出实际 trace/compile 调用、backend、dynamic-shape 选项和编译后的调用点；检查图中目标运算、graph break、重编译和输入签名。 | FX symbolic trace 对数据依赖控制流有限；`torch.compile` 可 graph-break 或回退。先对照 eager，再测目标 compiled path；编译可能耗时/耗资源，先核预算。 | `gm = torch.fx.symbolic_trace(model); print(gm.graph)`（仅用于 trace 兼容模型；对 compile 还要调用项目实际 compiled 对象和目标输入。） |
| TorchScript / `torch.jit.trace` / `script` | 区分 `trace` 与 `script`、追踪样本/shape、保存并重载 artifact，以及部署端实际加载路径。 | `trace` 只记录样例走过的分支；`script` 有不同类型/控制流约束。检查代表性分支、输出结构和序列化往返。 | `traced = torch.jit.trace(model, x); print(traced(x)); traced.save("/tmp/model.pt")`（临时路径；按目标路径另行 reload 并测试。） |
| Hugging Face Transformers | 找到 config 解析、实际模型类/Auto 映射、`from_pretrained` 或自定义加载、权重转换及调用方；追踪 `forward`、`ModelOutput`、generation/cache、tokenizer/词表。 | 默认 `trust_remote_code=False`；需开启时先审查并固定可信 revision。核对 loading info，不忽略不兼容警告；检查 `save_pretrained`/重载和 token ID 语义。 | `out = model(**batch); print(out.logits.shape)`（`batch` 必须是固定、脱敏且与模型输入签名匹配的编码输入。） |
| JAX / Flax | 从 `init`/参数树到 `apply`、训练/推理 step、`jit`/`pmap`/`pjit`；检查 pytree、显式 RNG、状态集合、optimizer state 和 sharding/mesh。 | shape 相同的 pytree leaf 不证明语义对应；RNG/state 是路径输入。分别检查 eager 与 jit/并行路径、静态参数、分片和 checkpoint 往返。 | `out = model.apply(variables, x); print(jax.tree_util.tree_map(lambda z: (z.shape, z.dtype), out))`（按模型的实际 RNG/state 参数补齐。） |
| TensorFlow / Keras | 追踪模型/Layer 构造、`call`、Functional graph、`tf.function`/XLA、训练或服务入口；检查变量、签名和 SavedModel/Keras 保存重载。 | 图函数可能 retrace，Python 分支未必是图内动态分支。核验图/eager 数值、变量注册和序列化后行为。 | `y = model(x, training=False); print([(v.name, tuple(v.shape)) for v in model.variables])` |

若目标框架或工具链未列出，沿用通用顺序并查当前仓库版本的官方文档/测试；不得仅根据相似 API 推断语义。

## 分布式模型与 checkpoint

适用于数据、张量、流水线、专家、上下文/序列并行及分片优化器状态。

- 记录并固定 world size、rank 到设备映射、mesh/并行维、TP/PP/EP/CP 等策略、分片轴/范围、通信组和训练/推理配置。明确 checkpoint 是全局张量、rank-local shard 还是可重分片格式。
- 对每个参数/状态核对 global/local shape、轴顺序、分片范围、复制/聚合规则、padding、专家编号和 tied/shared 权重；结构匹配但分片元数据不匹配仍属不兼容。
- 若改 optimizer state、并行度或参数身份/形状，按语义决定重建还是转换；不要盲目复用旧 moment、step、RNG 或 scheduler 状态。
- 最小验证检查目标布局可构造、关键 collective shape 一致、forward/必要时 backward 不死锁、各 rank 无缺失/重复权重；资源允许时做保存—恢复和目标 world size 的 reshard。单进程测试只证明单进程路径。
- 多 rank 用相同输入和预设容差对照参考/单 rank 行为；记录非确定通信及浮点归约条件。按 rank 脱敏日志，避免汇总掩盖局部错误。

## 量化权重与低精度执行

- 记录量化算法/权重格式、group size、量化轴、scale/zero-point 类型与形状、packing/layout、舍入/截断、校准数据版本和 activation/accumulator dtype；确认元数据随 checkpoint 保存并被目标 kernel 读取。
- 验证量化/反量化的轴、group 边界、尾部 padding、scale 广播和打包顺序；检查非有限值、饱和/裁剪及输入 shape 边界，不可只看 key/shape。
- 保持行为时，用固定探针对照 reference 与量化路径，预先选定适合 dtype 的指标/容差；精度损失按任务门槛验收。报告校准/测试数据隔离。
- 在目标硬件/kernel 上分开报告质量、延迟、吞吐和内存；浮点模拟、反量化检查或 CPU smoke test 不能证明目标量化 kernel 性能。

## KV cache 与增量解码

- 追踪 cache API/状态、K/V shape/轴、层/头索引、写入位置、position ID、RoPE/相对位置约定、mask、dtype/device 和 cache 更新/重排代码。
- 用固定 token ID 对照无 cache 全序列计算与 prefill + 逐步 decode；比较 next-token logits/输出（预设浮点容差），逐步验证位置索引和 cache 长度。
- 覆盖适用的 padding/变长批次、batch/beam reorder、滑窗/淘汰、paged/block table、batch 扩缩、序列边界和 reset；不支持情形明确标为未覆盖。
- 分开测 prefill/decode 延迟、吞吐、峰值显存和 cache 增长；固定 batch、上下文/生成长度、dtype、并发和硬件。检查 cache 不会跨请求泄漏状态。

## 模型导出与部署

导出成功或格式检查通过，不代表导出图与原模型数值等价，也不代表目标设备性能达标。以同一固定输入比较框架参考路径与目标 artifact；覆盖动态 shape、数据类型、mask/state、数值容差、算子替换/融合、量化校准及序列化往返。先查工具版本和 `--help`，控制构建/编译成本，使用临时输出路径；没有目标硬件时明确标记未验证。

| 目标 | 可执行的最小检查 | 关键边界 |
| --- | --- | --- |
| ONNX / ONNX Runtime | `onnx.checker.check_model("model.onnx"); sess = ort.InferenceSession("model.onnx"); outputs = sess.run(None, feeds)` | `feeds` 的名字、dtype、shape 要匹配模型输入。checker 只查图结构；还需用目标 Execution Provider 与框架参考输出做数值对照，覆盖动态轴、opset 和支持算子。 |
| TensorRT | `trtexec --loadEngine=/tmp/model.engine --dumpLayerInfo --profilingVerbosity=detailed` | 示例路径须替换为已有 engine 路径；先用当前 SDK 的 `trtexec --help` 核对选项。构建时显式固定 profile、精度、workspace/内存与校准设置；记录构建时间和目标 GPU，不能用 parse/build 成功代替 engine 推理验证。 |
| OpenVINO | `core = ov.Core(); compiled = core.compile_model(core.read_model("model.xml"), "CPU"); result = compiled(inputs)` | `inputs` 按真实端口/shape 提供；另测所需设备插件（CPU/GPU/NPU）、压缩/量化与动态维度。CPU 结果不证明其他插件行为或性能。 |
| TFLite | `it = tf.lite.Interpreter(model_path="model.tflite"); it.allocate_tensors(); detail = it.get_input_details()[0]; it.set_tensor(detail["index"], input_array); it.invoke()` | 输入需按实际 tensor dtype/shape 设置；检查 quantization parameters、动态 resize、delegate/算子支持和目标端输出。解释器 CPU 测试不代表移动端 delegate 性能。 |
| Core ML | `ml = coremltools.models.MLModel("model.mlpackage"); result = ml.predict(input_dict)` | 需匹配模型 feature 名称/类型；预测通常要求 macOS Core ML runtime。Linux 上只做格式/静态检查时应标记目标运行未执行，另覆盖设备、compute units 与量化路径。 |
| Qualcomm QNN / Hexagon | `qnn-net-run --model ./model.serialized --input_list ./input_list.txt --backend ./libQnnHtp.so --output_dir /tmp/qnn-out` | 示例文件名须替换为当前 SDK 生成的 context、输入清单和 backend；工具/参数随版本和设备变化，先核对 SDK 帮助及设备权限。记录 converter/quantizer、校准数据与设备版本；仿真或生成 context 不等于 Hexagon 实机验证。 |

这些片段仅是核验起点，不要求为检查而安装新 SDK。涉及导出或部署的架构修改时，另用[权重语义兼容性标准](checkpoint-compatibility.md)检查映射，并按项目接口用最小固定夹具比较各路径输出；未测的设备、delegate、执行提供程序和 shape 明确列为未覆盖。
