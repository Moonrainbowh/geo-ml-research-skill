# Rebuttal 工作流

Pressure-test vocabulary note: project-specific terms are examples, not defaults; map them to the new project's terminology before use.

用于处理审稿意见、major revision、rebuttal letter、逐点回复和修改计划。

## 总流程

审稿意见回复不是只写 response letter。默认流程是：

1. 读取 editor/reviewer comments，拆成逐条 comment matrix。
2. 判断每条意见类型、优先级和证据需求。
3. 先修稿，尤其是 LaTeX 全文修改；不能只在回复信里解释。
4. 给出修改位置和修复意见，记录每条 comment 对应的 manuscript action。
5. 对比修改前后，确认 claim、图表、引用、编号、交叉引用和 LaTeX 编译没有被破坏。
6. 最后撰写 response letter。

如果用户提供的是 LaTeX 项目，优先修改 `.tex` 源文件，不改生成的 PDF。修改前确认主文件、章节文件、bib 文件、图表路径和编译命令。

## 输入整理

先提取：

```text
Venue:
Decision:
Scores:
Reviewer comments:
Editor comments:
Available new evidence:
Feasible new experiments:
Deadline:
Manuscript source: .tex/.docx/pdf/other
Original version:
Revision version:
```

不要在没有证据时承诺新结果。

## LaTeX 全文修改流程

处理 LaTeX 稿件时按以下顺序执行：

1. 建立 comment matrix：

```text
comment_id | reviewer | issue | type | priority | manuscript target | evidence needed | planned action
```

2. 定位正文：

- 找相关 section、figure/table、equation、citation、appendix/supplement。
- 如果意见涉及方法或实验有效性，先回读 experiment/review，而不是直接润色。
- 如果意见涉及引用，先回读 citation。
- 如果要修改 Abstract、Results、Discussion、Conclusion 或任何 claim 句，先回读 writing，用 claim-evidence 表约束改写。
- 如果意见指向 Figure 1/2、主图、图注、Results 表达或工程语义不清，先回读 domain-style，再决定正文和图注修改。

3. 修改 `.tex`：

- 保留 LaTeX 结构、label、ref、cite、bib key 和 figure/table 环境。
- 不凭空新增结果、图、引用或实验。
- 对每条关键修改保留 revision log。
- 可用短注释标记复杂改动，例如 `% R1-C2 revision`，最终提交前按期刊要求决定是否移除。

4. 编译/静态检查：

- 能编译则编译，检查 unresolved references/citations。
- 不能编译则至少检查修改片段、括号、环境闭合、图表/公式引用。

5. 前后对比：

- 优先使用 `latexdiff` 或 git diff 生成 old/new 对比。
- 输出每条 comment 的 before/after 摘要，说明改了哪里、为什么改、是否影响 claim。

6. 回复信：

- 只有在 manuscript action 明确后再写 response。
- 回复中不要说“we have revised”而不给位置。
- response letter 中引用的 revised text 必须与 `.tex` 实际修改片段一致；编译或静态检查后再更新 page/line。

## Revision log

修稿后输出：

```text
| comment_id | file | section | old text/issue | revised text/action | reason | status |
```

`status` 使用：

- `done`：已修改并有位置。
- `clarified`：正文已澄清，未新增实验。
- `claim-shrunk`：收缩 claim 或补 limitation。
- `planned`：用户确认后才执行的新实验/分析。
- `not feasible`：说明原因并给替代处理。

## 评论分类

| 类型 | 判断 | 默认策略 |
|---|---|---|
| decision blocker | 影响接收的核心问题，例如 novelty、baseline、主 claim、实验无效 | 优先处理，补证据或收缩 claim |
| misunderstanding | 审稿人误读已有内容 | 礼貌澄清，并修改正文让误读不再发生 |
| fixable clarity | 表达、符号、图注、方法细节不清 | 给出具体修改位置 |
| scope mismatch | 审稿人要求超出本文范围但问题合理 | 说明边界，补限制或未来工作 |
| low-value | 模糊、格式、低影响建议 | 简短回应，不浪费篇幅 |

## 优先级

1. 多个 reviewer 共同提出的问题。
2. 最低分 reviewer 的主要 blocker。
3. 支持性 reviewer 可能撤回支持的问题。
4. Area chair/editor 可能关注的问题。
5. 小的清晰度和格式问题。

## 回复块结构

```text
Concern:
Acknowledge:
Evidence or clarification:
Action in manuscript:
Impact:
```

每个回复块必须包含正文位置：

```text
Location:
- Section/Figure/Table:
- File/line if LaTeX source is available:
- Before/after summary:
```

注意：

- 可以认同“需要更清楚/更公平/更多证据”，不要虚假承认不存在的技术错误。
- 不写 “the reviewer is wrong”。
- 不写 “as clearly stated”。
- 不承诺无法完成的实验。
- 新实验必须给具体数字，或者明确标为 planned/placeholder。

## 常见问题处理

### 缺少 baseline

- 能补：说明新增 baseline、同样数据划分、同样指标、结果位置。
- 不能补：解释该 baseline 测试的是不同问题或不适用，同时提供替代证据。
- 不能只说“future work”。

### Novelty 被质疑

从以下维度重建区别：

- 工程问题。
- 数据/工况。
- 方法机制。
- 评价设置。
- 工程边界。
- 证据链。

### 实验公平性

必须说明：

- 相同数据划分。
- 相同特征。
- 相同或合理的训练预算。
- HPO 策略。
- 指标和统计。
- `n_trials`、`trial_epochs`、`confirm_epochs`、搜索空间、随机种子或重复次数。

### HPO 没有整体提升

如果 reviewer 指出 local-search、stage2 HPO 或 tuned 结果没有提升：

- 先承认原稿如果表述成整体提升是不准确的。
- 说明 selected 规则：`selected = tuned` only when `keep_tuned=True`; otherwise baseline is retained.
- 把结论收缩为“未带来一致整体改善”“作为负结果/稳健性分析/搜索边界展示”。
- 对单任务或其他指标的例外单独说明，不把例外扩展为整体 claim。
- 给出正文动作：改 Results、改图注、补 baseline/tuned/selected 表，或移到 Supplement。

### 单数据集或泛化不足

没有外部数据、重复实验或独立工程验证时，默认动作是收缩 claim，不承诺新数据实验：

- 将 claim 限定为当前数据集、当前任务定义和当前划分。
- 在 Limitations/Future work 写 external validation。
- 若能补现有数据内的分析，可补分组误差、阶段误差或残差分析。
- 不写 “we will add a new dataset” 除非用户确认有可用数据和时间。

### 审稿人误解

先把正文写得更清楚，再回复：

```text
We agree that the current presentation can make this point easier to miss. We will revise ...
```

不要把问题推给审稿人。

## 输出模板

按用户材料选择输出层级：

- `仅回复草稿`：只有 comments 或用户只要策略时，输出分类、总策略和逐条回复草稿。
- `LaTeX 修改 + revision log`：用户提供 `.tex` 项目时，先改正文并输出 revision log，再写回复。
- `完整 major revision 包`：同时包含 comment matrix、正文修改摘要、before/after、response letter 和剩余风险。

```text
审稿意见分类：
| reviewer | comment | type | priority | action |

总策略：

AC/editor-facing summary:

逐条回复：
R1-C1:
Concern:
Response:
Manuscript change:

需要补的实验/分析：

风险：
```

## 三色回复格式

参考常见审稿回复 PDF，使用三色提高审稿人阅读效率。必须在回复信开头声明颜色含义，并全篇保持一致。

默认三色方案：

| 内容 | 颜色 | 用途 |
|---|---|---|
| Reviewer/editor comments | black | 原始审稿意见，不改写原意 |
| Author response and explanation | green | 作者回复、解释、承认问题、总结动作 |
| Revised manuscript text | blue | 稿件中新增或修改后的正文片段 |

如果期刊或用户更偏好中文样例，也可使用：

| 内容 | 颜色 |
|---|---|
| 审稿意见 | black |
| 作者回复和解释 | blue |
| 稿件中修改或新增内容 | green |

无论采用哪一套，开头都写明：

```text
Note: Review comments are shown in black; author responses are shown in [color]; revised manuscript text is shown in [color].
```

推荐结构：

```text
Response to Review Comments and Explanation of Revisions

Manuscript:
Manuscript Number:

Note: ...

Summary of major revisions:
1. ...
2. ...

Reviewer 1
Comment 1: [black]
Response 1: [green/blue]
Location: Section ..., Page ..., Lines ...
Revised text: [blue/green]
```

Comment 块可使用浅灰底或左侧强调线，但不要让颜色影响可读性；最终 PDF 必须检查颜色、分页、图表和长段落是否清晰。

## 最终检查

- 每个高优先级意见都有回复。
- 每个回复都映射到 reviewer comment。
- 每个承诺都有具体修改位置或证据。
- 语气礼貌、坚定、基于证据。
- 没有虚假认错或攻击审稿人。
- 没有把“收缩 claim”伪装成“已补充证明”。
- 回复信中的三色说明、颜色使用和正文摘录一致。
- 回复信摘录的 revised text 与 `.tex` 实际修改一致，页码/行号已按最新编译或静态检查结果更新。
- 修改前后对比已经完成，且没有未解释的关键差异。
