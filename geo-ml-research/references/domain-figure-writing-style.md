# 领域图文风格

Pressure-test vocabulary note: project-specific terms are examples, not defaults; map them to the new project's terminology before use.

用于把盐穴储能、岩土工程、机器学习/深度学习论文的图文风格转成可执行写作和绘图规则。只借鉴组织逻辑，不复制论文原图、图注、配色、照片或长文本。

## 术语映射优先

本文件中的盐穴、造腔、六阶段、selected RMSE、radius/effective-radius 等词是当前压测项目示例。新项目必须先映射为：

```text
engineering_object:
process_or_stage:
task_unit:
primary_metric:
derived_engineering_metric:
boundary_or_failure_mode:
```

除非用户材料明确包含这些词，不要把示例项目词汇带入新图序或图注。

## 适用场景

当用户要求以下任务时读取本文件：

- 学习或统一领域论文图文风格。
- 设计盐穴/岩土 ML 论文的 Figure 1、Figure 2 或整套图序。
- 把实验图从“只有 RMSE/R2”改成有工程语义的论文图。
- 写或润色 Results、figure caption、graphical workflow、方法流程图说明。
- 精读论文后提取可迁移的图表逻辑和图文表达方式。

## 核心叙事层

领域论文应同时交代四层，不要只写模型指标：

1. 工程对象：盐穴、岩体、造腔阶段、地层、工艺参数、稳定性指标。
2. 数据来源：仿真、现场、实验、参数抽样、阶段样本、输入输出标签。
3. 方法流程：BP/CNN/GRU、Stacking、HPO、多任务模型、物理/机理约束或对照模型。
4. 工程结果：几何形态、容量、半径、体积、稳定性、误差分布、适用边界。

若某一层缺失，在输出中标注缺失层，不要用“模型先进”替代工程解释。

## 多 PDF 风格学习工作流

当用户提供 3 篇以上 PDF 或要求“学习这些文献的图文风格”时，不要让单个上下文硬吞全部内容。优先拆成只读分片，再汇总：

| 分片 | 关注问题 | 输出 |
|---|---|---|
| 工程对象/Figure 1 | 工程过程、变量、阶段、预测目标 | 工程对象契约和不可缺变量 |
| 数据任务/Figure 2 | 数据来源、输入输出、样本、split、标签 | 数据任务契约和边界 |
| 方法流程 | baseline、HPO、模型流、评价指标 | 方法图应表达的数据流/训练流 |
| Results/Caption | 结果段落、图注、工程意义、限制 | 可迁移句法结构和风险词 |
| QA | 路由、版权、过度声称、重复/缺口 | 最小修改建议 |

子任务只读分析，不编辑 skill；最终由主流程去重、抽象、写回。禁止把任一 PDF 的图、图注、长句或视觉布局作为模板原样迁移。

## 图序角色 Schema

默认按以下角色组织岩土/工程 ML 论文图表；盐穴内容只是示例，不是必须图序：

| 图位 | 角色 | 推荐内容 |
|---|---|---|
| Figure 1 | 工程对象和变量定义 | 工程对象、过程/阶段、关键参数、预测目标；例如盐穴或岩体对象、造腔/检测流程 |
| Figure 2 | 数据和任务定义 | 输入特征、标签、任务上下文、样本来源、划分边界、单位 |
| Figure 3 | 方法或实验流程 | 模型族、HPO/selection、baseline/tuned/selected、评价指标、复现实验路径 |
| Figure 4 | 主结果 | 主指标矩阵、预测-实测、任务级误差、形态或状态预测对比 |
| Figure 5 | 公平性或机制解释 | baseline/tuned/selected、消融、特征重要性、误差来源 |
| Figure 6 | 工程边界或失败模式 | 残差随阶段/几何量/工况变化，失败案例或外推风险 |

如果图数有限，优先保留 `工程问题 -> 数据任务 -> 主结果 -> 工程误差` 四类图。

## 数据任务契约

每个 geo-ML 实验先建立任务卡，避免把工程序列问题写成普通表格预测：

```text
data_source:
sample_unit:
group_id:
inputs:
outputs:
stage_context:
label_timing:
split_method:
leakage_risk:
external_validation:
```

规则：

- `sample_unit` 必须说明样本是盐穴、仿真 run、设计方案、阶段序列、单阶段记录还是实验 specimen。
- `group_id` 优先对应盐穴、设计方案、仿真 run、工程项目或实验批次；同一父对象不要跨 train/test。
- 多阶段或序列工程任务必须区分本阶段参数、前序累计状态、当前输出，禁止使用未来阶段信息。盐穴六阶段造腔只是该规则的一个例子。
- split 先于 normalization、特征选择、增强、HPO 和 selected 决策；test 只用于最终评估。
- 仿真、实验、现场数据要分清训练来源和验证来源；跨来源验证单独标为 external/transfer/generalization check。

## Figure 1 / Figure 2 规则

Figure 1 不要直接画神经网络结构。先让读者看懂工程对象：

- 标出盐穴、注水/排卤/油垫或相关工程过程。
- 标出阶段、设计参数、几何参数和预测目标。
- 使用简单、可审稿的工程示意，不做装饰性拼贴。
- 每个箭头都表示物理过程、数据流或决策流。
- 它是工程对象契约，必须回答：研究对象是什么、过程如何发生、哪些变量可控、预测什么、工程风险在哪里。
- 盐穴造腔类优先采用“剖面结构 + 阶段推进 + 关键参数 + 几何目标”的组合。

Figure 2 用于把工程对象转成 ML 任务：

- 明确输入特征、输出标签、单位和阶段上下文。
- 若是六阶段任务，说明哪些阶段已知、哪些阶段预测。
- 展示数据来源和划分边界；跨目录比较前确认数据哈希、样本量、任务配置和 split。
- 若有仿真/现场/实验混合数据，分开标记来源，避免暗示同质数据。
- 它是数据任务契约，必须回答：工程对象如何变成样本、输入是什么、标签是什么、样本从哪里来、哪些边界不能外推。
- 可采用“参数组合/现场或仿真来源 -> 数据清洗/约束 -> 输入输出矩阵 -> 训练/测试边界”的流程。
- 对六阶段任务，图中显式标出 Stage k 可用的本阶段工艺参数、前序阶段状态/累计量和当前预测目标。

若使用形态样本阵列，只表达输出多样性或数据覆盖范围，不暗示未经验证的现场真实性。过多变量优先放入相邻表格，图中只保留读者理解任务所需的最小变量集。

输出模板：

```text
Figure 1 design brief:
- Claim:
- Panels:
- Engineering variables:
- Arrows/process:
- Caption first sentence:
- Risks:

Figure 2 design brief:
- Data sources:
- Inputs/labels:
- Split/boundary:
- Workflow panels:
- Caption first sentence:
- Risks:
```

## 结果图规则

结果图必须服务一个 claim：

- 主结果图默认展示 `selected`，不只展示 tuned 最优值。
- HPO 图必须同时展示 baseline、tuned、selected，并解释 `keep_tuned`。
- HPO 图和补充表必须区分 searched candidate、tuned candidate 和 final selected config；selected 回退 baseline 时，不把 best trial 参数画成最终模型参数。
- 多任务图优先保留任务级信息，不只给平均值。
- 工程变量图要把残差或误差映射到阶段、半径、有效半径、体积、深度或工况。
- 如果没有重复种子、统计检验或外部验证，不用视觉设计暗示显著性或泛化。

与 `figure-decision-tree.md` 一起使用：先用本文件决定图序和工程语义，再用图表决策树选择具体图型、字号、颜色和 QA。

方法流程图建议采用：

```text
data source -> preprocessing -> grouped split -> baseline -> HPO/tuned -> selected -> evaluation -> engineering interpretation
```

其中 baseline、tuned/HPO、selected 必须分层表达；selected 必须说明保留 tuned 或回退 baseline 的判据。

若图表或 caption 展示模型结构/超参数：

- 先确认字段来自 selected config、模型源码、模型注册表或配置类。
- 训练脚本共用的字段名不得直接解释成架构名；例如通用 `units` / `hidden` / `gru_units` key 可能只是 override 容器。
- 若图中展示字段复用的架构参数，例如 `kernel_size` 借由另一个配置字段传入，caption 或补充表必须标注 source-code/config mapping。
- 出现在 trial 表或 best candidate 中、但未进入 selected override 的字段，只能标为 searched candidate parameter。
- 缺失字段用 `not recorded` / `not available in inspected artifacts`，不要用默认值、图形符号或图注暗示已经确认。

## 图注模式

图注用短而自洽的结构：

```text
Figure X. [一句话说明整张图展示什么。]
(a) ...; (b) ...; (c) ...
Notes: define units, task split, selected/keep_tuned rule, data source, or boundary.
```

写图注时检查：

- 第一短句不写空泛价值判断。
- panel 说明和图内 panel 一一对应。
- 指标、单位、数据来源和筛选规则明确。
- HPO 或跨目录图说明 selected 回退规则和可比性边界。
- 数据图说明数据版本、样本单位、split 方法和外部验证来源。
- 不把正文 Results 整段塞进图注。

## Results 段落模式

每个 Results 段落按以下顺序写：

1. 定位图表：说明图/表展示的任务、模型、指标或工况。
2. 观察事实：给数值、方向和对照来源。
3. 指标证据：说明指标含义、评价数据和比较基准。
4. 工程意义：解释对盐穴几何、容量、半径、阶段预测、稳定性或设计筛选的意义。
5. 边界：说明数据集、划分、HPO 回退、缺少重复种子或外部验证。

每个 Results 小节必须回答：任务是什么、数据来自哪里、预测对象是什么、和谁比较、对工程有什么用、限制在哪里。

模板：

```text
Figure/Table X compares [models/tasks] using [metric] under [data/task setting].
[Model/result] achieved [number/trend] relative to [baseline/comparator].
This pattern suggests [engineering interpretation tied to geometry/stage/stability].
The conclusion is limited to [dataset/split/task]; [missing evidence] should be addressed before claiming broader generalization.
```

与 `writing-claim-evidence.md` 一起使用：先建立 claim-evidence 表，再按本文件把 claim 连接到工程对象和图表叙事。

工程 claim 用词分级：

| 用词 | 何时使用 |
|---|---|
| `demonstrates` | 有直接实验、同一数据/任务、明确对照支撑 |
| `suggests` / `indicates` | 只有趋势、局部任务或间接证据 |
| `supports preliminary design` | 可辅助筛选或初步设计，但未替代现场/仿真验证 |
| `optimizes` | 只在给定目标、约束和搜索空间内使用；否则写 candidate design |

不要把 SHAP/相关性写成物理因果；不要暗示 ML 可替代现场 sonar、实验或数值模拟验证。

单数据集随机测试只能支持当前数据分布内结论，不能声称跨盐穴、跨矿区或现场泛化。

## 精读论文时提取风格

读领域论文时，不要只摘结论。记录：

```text
Paper:
Engineering object:
First major figure:
Data/task figure:
Method workflow:
Main result figure:
Caption pattern:
What to borrow:
What to avoid:
Boundary for our paper:
```

与 `paper-reading-card.md` 一起使用：该文件负责论文内容精读，本文件负责抽取可迁移的图文组织方式。

## 不要照搬

- 不复制原图、照片、图标、长图注、图中文字或排版。
- 不复刻重红框、过度拼贴、装饰性流程图等视觉习惯。
- 不把概念图画成没有变量、单位和数据流的宣传图。
- 不用仿真/机理论文的严密性包装当前 ML 结果。
- 不把单一数据集高指标写成跨工程泛化。
- 不让图注承担正文论证，不用图注隐藏重要边界。

## 输出检查

```text
Engineering object visible: yes/no
Data/task definition clear: yes/no
Model workflow connected to task: yes/no
Main claim supported by figure: yes/no
Metric and units present: yes/no
Stage/geometry/stability meaning stated: yes/no
Boundary stated: yes/no
No copied source figure/text: yes/no
```
