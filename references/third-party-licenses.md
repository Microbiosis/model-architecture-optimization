# 第三方项目与许可证说明

## 本技能的许可与引用边界

本技能原创内容采用 [Apache License 2.0](../LICENSE)，版权署名为 `Copyright 2026 Microbiosis`。该许可不改变第三方项目的版权或许可证，也不授予超出上游条款的代码、模型、数据、商标或专利权利。

当前技能源码及其 `.skill` 安装包、源码归档不随包分发下列项目的第三方源码文件、模型权重或数据集。引用形式主要是中文工程摘要、来源链接、短 API/命令示例及明确标为示意的模板；不是将上游项目整体纳入本技能，也不是运行时依赖声明。日后若复制、翻译、改编或分发上游受版权保护的内容，必须按对应文件、版本及资产的实际许可证履行义务，不能仅凭本技能的 Apache-2.0 声明处理。

## 正式参考来源：11 项

[相关项目实践参考](related-projects.md)的“项目实例与适用边界”表正式列出 **11 项来源：10 个 GitHub 项目仓库、1 个 Hugging Face 模型仓库**。按下列固定提交的根许可证或默认许可统计为 **6 项 Apache-2.0、2 项 BSD-3-Clause、3 项 MIT**。Megatron-LM 是含文件级例外的混合许可仓库，不能将此统计理解为其全部文件只有一种许可。

以下许可证均于 2026-10-18 直接读取指定提交的 `LICENSE` 原文核验。已有固定提交的案例沿用原提交；其余条目记录本次许可核验快照。许可快照不表示已把原文中所有滚动文档链接、实现行为或性能主张固定到同一提交。

| 项目与上游 | 固定版本 / commit | 根许可证 / 默认许可与原文 |
| --- | --- | --- |
| [Hugging Face Transformers](https://github.com/huggingface/transformers) | `d4804f9d76b8c071b993d813e9e87067b780cbee`（许可核验快照） | [Apache-2.0 — LICENSE](https://github.com/huggingface/transformers/blob/d4804f9d76b8c071b993d813e9e87067b780cbee/LICENSE) |
| [timm / PyTorch Image Models](https://github.com/huggingface/pytorch-image-models) | `0f6e2a48a811bfff95a4e8f050405ddae3e19906`（许可核验快照） | [Apache-2.0 — LICENSE](https://github.com/huggingface/pytorch-image-models/blob/0f6e2a48a811bfff95a4e8f050405ddae3e19906/LICENSE) |
| [LitGPT](https://github.com/Lightning-AI/litgpt) | `d28a00d8a0014d2c0a9038fcd6bcfdd30fad4368`（原案例固定提交） | [Apache-2.0 — LICENSE](https://github.com/Lightning-AI/litgpt/blob/d28a00d8a0014d2c0a9038fcd6bcfdd30fad4368/LICENSE) |
| [torchtune](https://github.com/meta-pytorch/torchtune) | `v0.6.0` → `235c0fe238eda6627bdec8628c862d249c8f61e4`（对应所引 0.6 文档系列） | [BSD-3-Clause — LICENSE](https://github.com/meta-pytorch/torchtune/blob/235c0fe238eda6627bdec8628c862d249c8f61e4/LICENSE) |
| [nanoGPT](https://github.com/karpathy/nanoGPT) | `3adf61e154c3fe3fca428ad6bc3818b27a3b8291`（原案例固定提交） | [MIT — LICENSE](https://github.com/karpathy/nanoGPT/blob/3adf61e154c3fe3fca428ad6bc3818b27a3b8291/LICENSE) |
| [Megatron-LM](https://github.com/NVIDIA/Megatron-LM) | `62c063f2b731b57b1c2031b2b4f4c6a769271ccb`（许可核验快照） | [BSD-3-Clause 默认；另有 Apache-2.0、MIT 等文件级条款 — LICENSE](https://github.com/NVIDIA/Megatron-LM/blob/62c063f2b731b57b1c2031b2b4f4c6a769271ccb/LICENSE) |
| [TransformerLens](https://github.com/TransformerLensOrg/TransformerLens) | `bd55eab6737584685b921fdfcd15f191579e64b1`（许可核验快照） | [MIT — LICENSE](https://github.com/TransformerLensOrg/TransformerLens/blob/bd55eab6737584685b921fdfcd15f191579e64b1/LICENSE) |
| [MiniMind LLM](https://github.com/jingyaogong/minimind) | `f659b55761b754d306bd140573493a6543cafd7f`（原案例固定提交） | [Apache-2.0 — LICENSE](https://github.com/jingyaogong/minimind/blob/f659b55761b754d306bd140573493a6543cafd7f/LICENSE) |
| [MiniMind-V](https://github.com/jingyaogong/minimind-v) | `1862b633fc082a723e78dbead9777545f09d960c`（原案例固定提交） | [Apache-2.0 — LICENSE](https://github.com/jingyaogong/minimind-v/blob/1862b633fc082a723e78dbead9777545f09d960c/LICENSE) |
| [MiniMind-O](https://github.com/jingyaogong/minimind-o) | `f900448c608318c53314ebf8a947ab05cd8c038e`（原案例固定提交） | [Apache-2.0 — LICENSE](https://github.com/jingyaogong/minimind-o/blob/f900448c608318c53314ebf8a947ab05cd8c038e/LICENSE) |
| [DeepSeek-V4.1-Flash](https://huggingface.co/deepseek-ai/DeepSeek-V4.1-Flash)（模型卡案例） | `314113f8a7dadb388215396ba5fbee2c610a04d6`（原案例固定 revision） | [MIT — LICENSE](https://huggingface.co/deepseek-ai/DeepSeek-V4.1-Flash/blob/314113f8a7dadb388215396ba5fbee2c610a04d6/LICENSE) |

这里的“11 项”是正式项目案例清单，不是文中所有框架、产品名称的总数，也不是它们的传递依赖清单。技能还提到 PyTorch、TensorFlow/Keras、JAX/Flax、MLX、LangChain/LangGraph、AutoGen、CrewAI、ONNX/ONNX Runtime、TensorRT、OpenVINO、TFLite、Core ML、Qualcomm QNN/Hexagon 等作为检查路径或工具示例；相关文档与论文作为背景来源，不因被提及就构成本技能随包分发的软件依赖。若实际采用其中任何实现、SDK 或资产，仍须单独核验其确切版本、许可证及分发条款。

## 使用第三方内容时必须遵循的规则

1. **先核对来源和具体文件。** 固定版本或完整 commit，读取该文件的版权头、根许可证、子目录许可证、适用的 `NOTICE` 及第三方声明。根许可证不必然覆盖整个仓库；Megatron-LM 的根 `LICENSE` 明确采用“除另有说明外”的默认规则，并附部分外来代码的其他许可证，应按实际文件判断，不可把所附 Apache 文本误当成全仓默认许可。
2. **Apache-2.0 内容。** 分发时提供适用许可证副本；保留相关版权、专利、商标和署名声明；在修改过的文件中醒目标注修改。若上游分发中有适用 `NOTICE`，按第 4(d) 条保留可读的署名通知；不得用本项目作者署名替换上游权利人。兼顾专利条款、免责声明和商标限制。
3. **BSD-3-Clause 内容。** 源码分发保留版权声明、条件清单与免责声明；二进制分发在文档或其他随附材料中重现这些内容。不得未经许可使用版权人或贡献者名称为衍生产品背书或推广。
4. **MIT 内容。** 在软件的所有副本或实质性部分中保留原版权与许可声明及其免责声明，不因置于 Apache-2.0 项目中而删除或改写原许可。
5. **记录复制与修改。** 若未来引入上游内容，逐项记录原项目、原文件、commit、适用许可、保留的版权/NOTICE 和修改情况，并在源码、安装包和源码归档中一并携带依法应保留的文本。对翻译、摘要中实际包含的受保护表达也作同样判断，不把“仅供参考”当作许可豁免。条款不清、来源不明或不兼容时，停止引入，先澄清权利与兼容性。
6. **模型、数据和 SDK 单独核验。** 代码仓库的许可证不能自动授权外部预训练权重、tokenizer、视觉/音频编码器、codec、数据集、服务 API 或厂商 SDK。MiniMind 系列复用组件及 DeepSeek 模型资产须按实际下载文件、发布版本、模型卡与数据条款另核，不能将本表的根许可证结论泛化为所有资产、下游用途或第三方组件均已获授权。

本文件是来源与合规操作说明，不新增第三方授权，不替代各上游完整许可证；目前没有因这 11 项参考而随包复制它们的全部许可证或 NOTICE。未来分发实际第三方内容时，应依据该内容的具体条款补齐所需文件与声明，而非只保留本表链接。

## 审计与预登记文件的来源

[让声明站得住](../references/claims.md) 与 [预登记模板](../templates/audit-preregistration.md) 的案例数字与工程教训来自作者自有项目（`Microbiosis/hetero-moe`）的实测记录，不含第三方源码、权重或数据，也不改变本文件的第三方许可结论。
