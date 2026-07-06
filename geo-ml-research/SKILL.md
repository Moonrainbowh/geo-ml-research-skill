---
name: geo-ml-research
description: Use when organizing, auditing, writing, reviewing, citing, visualizing, or planning Chinese-first geotechnical machine-learning research, including literature/PDF reading, domain figure style learning, model/HPO comparison, claim-evidence mapping, manuscript sections, publication figures, citations, reviewer critique, rebuttal, or paper-ready research notes.
---
# Geo ML Research

用这个 skill 把岩土工程 + 机器学习科研任务分流到合适流程，并产出可继续加工的科研工件。它不是一个大而全的自动论文机器，而是一个个人化路由器：先判断任务属于哪一类，再读取对应 reference，最后输出下一步最小可执行产物。

## 核心原则

- 证据先于文字。没有实验、图表、文献或代码结果支撑，不写强 claim。
- 工程边界必须显式。土层、工况、阶段、几何参数、单位、数据来源、外推范围都要写清楚。
- ML 结果必须可审计。区分 baseline、tuned、selected、keep_tuned、随机种子、数据划分、HPO 设置和测试集使用。
- 图表服务 claim。每张图先说明要证明什么，再决定图型。
- 引用支撑具体句子。分段、检索、定级，不用标题相关性冒充证据。
- 中文输入友好。允许中文实验笔记，但英文论文输出必须重构逻辑，不逐句硬翻译。
- 路由轻，模块窄。总 skill 只做分流、约束、状态组织；细节从 `references/` 按需读取。

## 项目词汇卫生

本 skill 中出现的盐穴、GRU/BP/CNN、六阶段、stage2、radius/effective-radius、simulator name、`example_training_data.csv`、`keep_tuned` 等词，除非用户材料明确包含，否则只视为压测示例，不是默认模板。

处理新项目时先建立 terminology mapping：

```text
engineering_object:
task_unit:
data_file:
model_family:
selection_rule:
derived_engineering_metric:
validation_scope:
```

再套用 split、provenance、HPO、claim-evidence、figure、citation 和 review 规则。不能把示例项目的模型名、任务数、图序、指标或数据文件名继承到新项目。

## 快速路由

先判断用户的主要意图，再打开相应 reference：

| 用户意图                                                  | 读取                                     |
| --------------------------------------------------------- | ---------------------------------------- |
| 不确定该从哪一步开始、想整理一个科研任务                  | `references/module-routing.md`         |
| 找文献、做综述前期、建立研究背景                          | `references/literature-workflow.md`    |
| 精读论文、做中英文对照、提取图表和方法                    | `references/paper-reading-card.md`     |
| 记录训练/HPO/消融实验、比较结果                           | `references/experiment-ledger.md`      |
| 写摘要、引言、方法、实验、结果或把中文 notes 写成英文论文 | `references/writing-claim-evidence.md` |
| 画论文图、训练曲线、预测-实测、残差、HPO/消融图           | `references/figure-decision-tree.md`   |
| 学习或统一盐穴/岩土 ML 论文图文风格、设计 Figure 1/2 或图注 | `references/domain-figure-writing-style.md` |
| 补引用、分段引用、判断文献支撑等级或整理 reference metadata lock | `references/citation-support-scale.md` |
| 投稿前检查、submission readiness / preflight、像审稿人一样诊断论文 | `references/review-audit-rubric.md`    |
| 回复审稿意见、rebuttal、major revision                    | `references/rebuttal-playbook.md`      |

如果任务跨多个模块，默认顺序是：

`literature -> reader -> domain-style -> experiment -> figure -> writing -> citation -> review -> rebuttal`

其中 `domain-style` 是可选插入点：当用户提供多篇领域论文、要求学习图文风格、设计 Figure 1/2、图注或 Results 叙事时启用。

但如果用户已经给出明确产物，例如“帮我写 Results”，直接进入写作模块，并在输出中列出缺失证据。

涉及盐穴/岩土 ML 论文的图序、Figure 1/2、图注或 Results 图文风格时，在主模块之外同时读取 `references/domain-figure-writing-style.md`，先保证工程对象和数据任务讲清楚，再处理模型指标。

## 默认输入检查

开始工作前，尽量从用户提供的材料或当前目录中确认：

- 研究对象：岩土问题、工程场景、阶段或工况。
- 数据：来源、来源证据、样本量、特征、标签、单位、划分方式。
- 数据来源证据：生成脚本、原始导出说明、历史路径是否仍可访问、是否有 `case_id/run_id/scenario_id`；只有 CSV/列名时不得命名 simulator。
- 跨目录对比：先确认数据哈希、样本量、任务配置和 train/val/test 划分策略是否一致。
- 模型：BP、CNN、GRU、LSTM、Transformer、XGBoost、物理约束模型或其他方法。
- 对照：baseline、传统方法、消融、HPO、不同模型或不同工况。
- 指标：RMSE、MAE、R2、MAPE、误差分布、工程约束指标。
- 证据：表格、图、日志、summary.csv、result.json、run_config.json、论文段落、审稿意见。
- 目标：检索、读文献、写作、绘图、投稿前审查、回复审稿人。

如果缺少关键输入，不要编造。可以先向用户确认，后续可以继续产出带占位符的 scaffold，但必须标注“缺失信息”和“不能声称的内容”。

## 通用确认门

任何项目都要先区分 `safe work` 和 `confirmation gate`。缺少确认时，只能整理、审计、起草占位 scaffold，不能执行会改变证据、承诺政策或安装全局工具的动作。

默认需要确认的 gate：

- `target venue`：目标期刊、会议、学位模板或机构模板未定时，不做最终格式、reference style、abstract limit、figure limit 或 AI disclosure 定稿。
- `validation route`：去重重训、重新划分、group split、external validation、repeated seeds、HPO 或任何新评价都要先确认方案，再确认执行。
- `data/code availability`：数据、source data、raw predictions、checkpoints、代码、repository、license、public/on-request/restricted/unavailable 政策未定时，不创建 release 包或可共享目录。
- `author declarations`：author list、affiliation、funding、acknowledgements、COI、CRediT、ethics、AI/tool disclosure 只能来自作者、机构、项目负责人或目标期刊，不能推断。
- `engineering tolerance`：没有项目阈值、设计标准或明确 no-acceptability-claim 决定时，只能写工程解释，不能写 design/stability acceptance。
- `reviewer/editor comments`：只有收到真实审稿意见或编辑意见后才进入 rebuttal；不能编造 reviewer comments、comment matrix 或 response letter。
- `global skill installation`：正式安装、迁移或覆盖全局 skill 目录前必须得到用户确认。

## 轻量研究账本

处理复杂任务时维护一个简短账本，可直接放在回答里：

```text
Research task:
Data/artifacts:
Current stage:
Main claim:
Evidence:
Boundary:
Risks:
Next action:
```

对于实验和论文写作任务，必须至少维护：

- `Main claim`
- `Evidence`
- `Boundary`
- `Risks`

## 输出格式

默认输出中文，必要论文文本可给英文草稿。根据任务类型输出：

- `路由判断`：当前任务应该走哪个模块。
- `产物`：检索表、精读卡、实验账本、段落草稿、图表方案、引用映射、审稿体检或 rebuttal 草稿。
- `证据和边界`：哪些结论有证据，哪些只能弱化表达。
- `缺失信息`：继续推进前最需要补的材料。
- `下一步`：一个明确、可执行的动作。

## 质量底线

- 不复制外部仓库长文本、脚本或模板；只使用个人化重写后的流程。
- 不发明实验结果、引用、DOI、审稿人意见或论文发现。
- 不把测试集用于调参后还声称泛化可靠。
- 不把工程数据局部规律写成普适结论。
- 不复制用户提供论文的图、图注、照片、配色或长文本；只抽象图文组织逻辑。
- 不把 GPU 速度/可运行性测试包当作论文主结果包；同名模型也必须看配置、任务和产物用途。
- 不把 review 输出写成安慰性总结；必须指出真实风险。
- 不把投稿前 preflight 写成普通润色；必须同时检查 claim 边界、supplement/figure/source-data 链路、引用格式和作者确认门。
- 不在用户未要求时创建正式 skill 目录、安装外部依赖或启动长时间检索。

## 真实任务压测样例

这个 skill 初版应能处理以下任务：

- “对比 GRU/BP/CNN/HPO 结果，看看哪个适合写进论文。”
- “精读这篇岩土机器学习论文，提取数据、模型、baseline、图表和可借鉴点。”
- “把这段中文 Results notes 改成英文论文段落。”
- “根据 summary.csv 画预测-实测图、残差图或 HPO 对比图。”
- “这是 reviewer comments，帮我分类并写 rebuttal 草稿。”
