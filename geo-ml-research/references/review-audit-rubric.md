# 投稿前审稿式体检

Pressure-test vocabulary note: project-specific terms are examples, not defaults; map them to the new project's terminology before use.

用于从审稿人角度检查岩土工程 + ML/DL 论文、初稿、图表、实验结果或论文 idea。

如果体检对象涉及 Figure 1/2、主图图序、图注、Results 图文叙事或工程对象是否讲清楚，同时读取 `domain-figure-writing-style.md`。

## 先建立 Paper Map

```text
Main claim:
Contribution type:
Engineering problem:
Core insight:
Method:
Data:
Evidence chain:
Boundary:
Closest prior work:
Target venue:
```

如果 `Main claim` 不能写成一句清楚的话，把它列为首要问题。

## 审查顺序

1. 问题是否真实：工程痛点是否具体，还是泛泛说“岩土复杂”。
2. Insight 是否成立：是否发现了明确失败机制、变量或工程规律。
3. Novelty 是否站得住：去掉模型名后贡献是否还存在。
4. 方法是否服务 claim：每个模块是否对应问题、假设或失败模式。
5. 实验是否支撑 claim：baseline、消融、误差分析、边界是否足够。
6. 统计与复现：随机种子、方差、显著性、数据划分、代码环境。
7. 图表是否诚实：主图是否直观证明问题和结果。
8. 写作是否引导审稿人：title、abstract、intro、method、results 是否一致。
9. 合规与开放：数据可用性、代码、伦理、匿名、引用。

## 多审稿人面板

当用户要求投稿前体检、重大修改、submission readiness 或“像审稿人一样看”时，优先把单一审查拆成多视角面板。每个视角都必须给出 decision impact，而不是泛泛建议。

推荐面板：

| reviewer | 关注点 | 典型 critical issue |
|---|---|---|
| EIC / handling editor | novelty、scope、story、目标期刊匹配、拒稿风险 | 贡献不清、scope 不匹配、claim 超证据 |
| geotechnical reviewer | 工程对象、工况、变量、边界、工程意义 | 工程问题不具体、设计可接受性无阈值 |
| ML methods reviewer | split、baseline、HPO、公平性、复现、统计 | row-level split 误写为独立泛化、test selection |
| figures/data reviewer | 图表诚实性、source-data、caption、单位、编号 | 图无法证明 claim、source-data/图注链断裂 |
| citation reviewer | 引用支撑等级、metadata、no-go claim、重复引用 | metadata-only 被当最终支撑、综述支撑性能 claim |

输出格式：

```text
Reviewer panel:
| reviewer | verdict | critical issues | major issues | decision impact | required action |

Conflict map:
| issue | reviewers | agreement | conflict | resolution |
```

如果不同 reviewer 对同一问题判断不同，优先采用更保守的 evidence boundary；若需要作者/用户决策，放入 confirmation gate。

## 岩土/ML 专项风险

- 数据泄漏：同一工程或相邻阶段样本跨训练和测试。
- split 层级误写：随机行级 split 被写成工程、case、run 或仿真组级独立验证。
- 重复样本泄漏：全行重复或关键工况重复跨 train/test，但正文仍声称独立测试。
- 工况外推：测试集与真实应用边界不同。
- 指标单一：只给 RMSE，不给残差或工程约束。
- baseline 不公平：不同模型训练预算或调参强度不同。
- HPO 污染：根据测试集反复调整模型。
- 物理不合理：预测值违反非负、几何或阶段约束。
- 工程意义弱：性能提升没有转化为工程解释或应用价值。
- 图文叙事弱：Figure 1/2 没有建立工程对象和数据任务契约，主图只展示模型指标。
- 过度声称：单一数据集写成普适规律。
- HPO 展示误导：只展示 tuned，不说明 `selected = keep_tuned ? tuned : baseline`。
- 指标冲突误写：主指标改善但辅助指标变差，却写成整体提升。
- stage2/local-search 误写：selected RMSE 未整体提升却写成“进一步提升”。
- 产物混淆：把 GPU 速度/可运行性测试包当论文主结果包。

## HPO 审查

HPO 相关论文必须分三层审查：

1. 绝对性能：selected 指标在多任务/多工况中是否更好。
2. HPO 是否改善：baseline、tuned、selected 和 `keep_tuned` 是否一致支撑。
3. 搜索成本是否合理：`n_trials`、`trial_epochs`、`confirm_epochs`、搜索空间和总耗时是否公平。

如果 stage2/local-search 的 selected RMSE 未整体提升，只能写“局部搜索未带来一致改善/作为负结果或稳健性分析”，不能写“进一步提升”。

若 HPO 的 `keep_tuned` 由 RMSE 决定，审查时必须区分主指标和辅助指标。当 RMSE 改善但 R2、MAE 或工程约束指标变差时，只能写成 mixed evidence，不能写整体性能提升。

必须确认 selected 决策指标：

- `keep_tuned = delta_val_* < 0`：可作为 HPO 选择规则，但仍需说明 test 只用于最终报告。
- `keep_tuned = delta_test_* < 0`：属于 test-selection 风险，主文应排除或标为 exploratory。
- 无法确认：按高风险处理，不允许写 `HPO improved generalization`。

反向核查法：找一行 `delta_test_*` 改善但 `delta_val_*` 变差的任务。如果 `keep_tuned=False`，说明该 summary 更可能按 validation 决策；如果为 True，要追查是否仍按 test 决策。

## Split 审查

检查 Methods/Experiments 是否明确：

- split unit 是 row、case、run、project、scenario 还是 external dataset。
- 是否存在显式 group id；若没有，是否避免写 group-level generalization。
- train/val/test 索引是否互斥。
- 是否检查了全行重复或关键工程变量重复跨 split。
- scaler/imputer/feature selection 是否只在 train 上 fit。

若只有随机行级 split，审稿意见应直接指出：当前证据只能支持 within-dataset row-level holdout performance，不能支持 independent project/case/simulation-run/scenario-level 或 external engineering generalization。

## 图文叙事审查

检查 Figure 1/2、主结果图和图注是否完成三件事：

- Figure 1 说明工程对象、阶段/工况、变量和预测目标，而不是一开始只放神经网络结构。
- Figure 2 说明数据来源、样本单位、输入输出、split 边界和外推限制。
- 主结果图和 Results 段落把 RMSE/R2 等指标连接回阶段、几何参数、稳定性或工程决策。

## 投稿前 preflight

当用户要求 `submission readiness`、`preflight`、投稿前检查、整稿体检或最终 submission package 时，不要只做普通 reviewer critique。必须额外检查四条链：

1. **claim chain**：Abstract、Results、Discussion、Conclusion 是否都使用同一验证层级和主 claim；强词是否只出现在否定、边界或 no-go 语境。
2. **artifact chain**：主图、supplement、source-data、result/package lock、citation map 是否互相引用一致；编号如 Table S1/S2、Note S1/S2/S3 不能冲突。
3. **release chain**：source-data manifest 只是本地锁还是公开投稿包；若公开共享权限、license、路径清理、最终图格式未确认，必须列为 confirmation gate。
4. **declaration chain**：target journal、data/code availability、AI disclosure、funding、acknowledgements、COI、author contributions、ethics/permissions 是否仍有 `[TO CONFIRM]`。

preflight 必须把以下通用确认门写清楚：

- `target venue`：目标期刊/模板、reference style、figure limit、abstract limit 和 AI disclosure 规则。
- `validation route`：新训练、resplit、group split、external validation、repeated seeds、HPO 或指标重算。
- `data/code availability`：数据、source data、raw predictions、checkpoints、代码、repository、license 和共享级别。
- `author declarations`：作者、单位、funding、acknowledgements、COI、CRediT、ethics、AI/tool disclosure。
- `engineering tolerance`：项目阈值、设计标准、稳定性/可接受性结论或 no-acceptability-claim。
- `reviewer/editor comments`：只有真实审稿意见/编辑意见到来后才进入 rebuttal，不预写假 comment matrix。
- `global skill installation`：正式安装或迁移 skill 前必须确认。

如果体检对象是 clean author-review manuscript draft，还要检查正文是否引用 Figure/Table 但图注只存在于 figure plan、source manifest 或内部 map 文件中；若是，输出或要求一个独立 figure-caption package，避免作者审阅稿缺少图文对应关系。

preflight 输出必须至少包含：

```text
Executive verdict:
Critical/Major/Minor risks:
Claim-boundary audit:
Methods/data/split/HPO audit:
Figure/source-data/supplement audit:
Citation audit:
Declarations/availability audit:
Safe actions without confirmation:
Actions requiring user confirmation:
```

preflight verdict 不能复述输入稿里的风险措辞作为自己的结论。遇到 `useful gains`、`negligible influence/effect`、`further improves`、`establishes a benchmark`、`deployment-ready` 这类词，必须先判断证据等级，再在 verdict 和 claim audit 中改成有边界的事实句。只能在 “source wording to revise” 或 “risk phrase” 中原样引用这些词。

特别是 duplicate / leakage 相关内容：preflight 的 `safe claim`、`claim-boundary audit` 和 `Executive verdict` 中不要写 `negligible`。改写为：

```text
The metric-only exclusion changed [metric] from A to B and did not materially change the reported summary at the displayed precision. It does not prove leakage-free, deduplicated, group-level, or external validation.
```

常见投稿前硬门：

- 目标期刊未定时，不做最终 reference style、abstract length、figure limit 或 AI disclosure 定稿。
- 数据/代码共享未确认时，不创建公开仓库、不打包原始数据、预测文件、checkpoint 或源码。
- supplement 编号和正文 map 不一致时，先统一编号，再谈格式。
- source-data hash 只能证明本地 artifact 状态；不能替代共享许可或目标期刊 source-data 格式。
- 正文仍是本地 citation marker 时，标为 draft 状态；等目标期刊 style 决定后再转 numbered/author-year。

## Workflow Completion Audit

当用户要求“继续完整流程”“一直做下去”“后续目标是什么”“总目标如何实现”，且目录里已经存在多个 manuscript / figure / citation / preflight / supplement artifact 时，先做 completion audit，不要继续堆零散文档。

completion audit 必须回答：

```text
Completion definition:
Module completion map:
Safe work queue without confirmation:
Confirmation gates:
Current scientific boundary:
Next local win:
```

模块至少覆盖：

- route / literature / reader / experiment / figure / writing / citation / review / rebuttal；
- supplement / source-data / release；
- declarations / target-journal formatting；
- skill validation / installation gate。

判断原则：

- `done` 只用于已有文件和验证能直接支撑的事项；
- `safe next` 只能包含不改变数据、不训练、不发布、不填作者事实、不安装全局 skill 的动作；
- `confirmation gate` 必须把需要用户/作者确认的动作单独列出；
- `next local win` 只能是一个具体、可验证、不会越界的动作。

## Reviewer objection 模板

把风险写成具体审稿意见，而不是泛泛建议：

```text
Objection:
Why it matters:
Evidence in current draft:
Fix:
Priority: critical / major / minor
```

好的 objection：

- “论文声称跨工况泛化，但测试集没有包含独立工程或土层外推。”
- “论文使用随机行级 holdout，但把结果写成独立工程或仿真 case 泛化。”
- “数据中存在重复样本跨 train/test，或作者未说明是否排除了重复样本泄漏。”
- “HPO 结果只展示 tuned 指标，没有说明 selected 是否回退到 baseline。”
- “HPO 的 selected 决策若基于测试集改善，将污染最终测试性能。”
- “主图展示平均 RMSE，但没有残差随阶段变化，无法判断工程风险集中在哪里。”
- “论文把 GPU speed baseline 的普通 GRU 结果与论文主 HPO 结果混在一起，导致证据来源不清。”

弱 objection：

- “需要更多实验。”
- “创新性不足。”
- “语言需要加强。”

## 输出模板

```text
Paper map:

总体判断：

主要风险：
| priority | issue | why | fix |

Claim-evidence audit:
| claim | evidence | status | action |

Reviewer objections:
1. ...

修改计划：
1. ...

投稿前 checklist:
- ...
```

## 质量底线

- 不做安慰性审稿。
- 不只改语言，先查 claim、method、experiment。
- 每条建议要能落到具体段落、图、表或实验。
- 每条 reviewer objection 必须映射到 manuscript action：改哪一节、哪张表、哪幅图或哪句 claim。
- 如果审稿式体检发生在收到 reviewer comments 之后，把 objection 转成 rebuttal 的 comment matrix 行。
