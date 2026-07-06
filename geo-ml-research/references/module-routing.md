# 模块路由

用本文件判断用户任务应该进入哪个模块。总原则：先识别用户要的产物，再选择最小可用流程。

## 路由顺序

1. 明确产物：用户想要表格、草稿、图、引用、审稿意见、回复信，还是下一步计划。
2. 判断当前阶段：文献、阅读、实验、绘图、写作、引用、审稿、rebuttal。
3. 检查证据：是否有数据、结果、图表、论文、审稿意见或代码路径。
4. 选择一个主模块，必要时声明后续模块。
5. 输出下一步最小可执行动作。

## 通用 confirmation gate

路由时先判断请求是否触发 confirmation gate。若触发，输出需要确认的决策和可安全推进的 scaffold，不直接执行 gated action。

必须显式确认的 gate：

- `target venue`：目标期刊、会议、模板、reference style、figure limit 或 AI disclosure 定稿。
- `validation route`：新训练、去重重训、resplit、group split、external validation、repeated seeds、HPO 或指标重算。
- `data/code availability`：数据、source data、raw predictions、checkpoints、代码、repository、license 或共享级别。
- `author declarations`：作者、单位、funding、acknowledgements、COI、CRediT、ethics、AI/tool disclosure。
- `engineering tolerance`：设计阈值、工程标准、稳定性/可接受性结论或 no-acceptability-claim 决定。
- `reviewer/editor comments`：真实审稿意见、编辑决定、comment matrix 或 response letter。
- `global skill installation`：安装、迁移、覆盖或发布 skill。

## 决策表

| 信号 | 主模块 | 典型输出 |
|---|---|---|
| “找文献”“综述”“相关工作”“有哪些方法” | literature | 检索策略、候选文献表、方法谱系 |
| “精读”“这篇文章讲什么”“中英文对照” | reader | 论文精读卡、图表和方法拆解 |
| “训练结果”“HPO”“baseline”“summary.csv” | experiment | 实验账本、模型对比、claim-evidence map |
| “画图”“figure”“预测-实测”“残差”“消融图” | figure | 图型建议、绘图方案、QA 清单 |
| “学习图文风格”“Figure 1/2”“图注”“主图/图序”“graphical workflow”“方法流程图”“工程语义” | domain-style | 图序、图文叙事、caption 规则、可借鉴/不可照搬 |
| “写摘要/引言/方法/结果”“改成英文论文” | writing | 段落草稿、section outline、证据边界 |
| “补引用”“分段引用”“支撑文献” | citation | claim 分段、候选引用、支撑等级 |
| “投稿前检查”“submission readiness/preflight”“像审稿人一样看”“会不会被拒” | review | paper map、风险清单、修改优先级、确认门 |
| “继续完整流程”“后续目标是什么”“一直做下去”“完成度审计”“哪些能自动做、哪些要确认” | review | workflow-completion audit、safe work queue、confirmation gates、next local win |
| “审稿意见回复”“rebuttal”“major revision” | rebuttal | 评论分类、策略、逐条回复 |

## 多模块任务

如果用户说“从头帮我做一遍”，默认走：

`literature -> reader -> domain-style -> experiment -> figure -> writing -> citation -> review`

其中 `domain-style` 只在用户要求学习领域图文风格、设计 Figure 1/2、图注、主图/图序、方法流程图或 Results 叙事时插入。`rebuttal` 只在收到 editor/reviewer comments、revision decision 或用户明确要求回复审稿意见后进入。

如果用户说“我已经有结果了”，跳过 literature/reader，优先 `experiment -> figure -> writing`。

如果用户说“我收到审稿意见了”，直接 `rebuttal`。涉及 baseline、泛化、claim、实验公平性、数据划分或 HPO 时，必须回读 `review` 和 `experiment`。

## 多 agent 分派协议

当任务明显包含多个独立读文件、读论文、检索来源或审稿视角时，优先使用只读多 agent 分治；主 agent 不把所有原始材料塞进同一个上下文。分派前先判断是否互相独立，避免多个 agent 修改同一文件或重复做同一件事。

适合分派：

- 20 篇以上候选文献筛选、多个数据库检索或 citation chasing。
- 多 PDF 精读、图文风格学习、source map 建立。
- 投稿前多视角审查：岩土工程、ML 方法、引用、图表、EIC 风险。
- 大量 artifact 的一致性扫描，例如 citation map、figure captions、source-data manifest。

不适合分派：

- 用户只问一个事实或一个小文件。
- 下一个步骤依赖前一个结果，不能并行。
- 任务会修改同一文件或同一结果表，容易冲突。

子 agent 输出必须结构化，推荐字段：

```text
agent_role:
scope:
inputs:
findings:
evidence:
uncertainties:
conflicts:
recommended_action:
files_or_sources_checked:
```

主 agent 合并规则：

- 只合并抽象规则、表格字段和证据锚点，不复制长段原文。
- 发现冲突时标为 `conflict/needs manual check`，不静默仲裁。
- 子 agent 的 metadata-only 发现不能覆盖主流程已核验的 file/source-map 证据。
- 输出最终结论时必须说明哪些是已核验证据，哪些只是候选、preflight 或待确认。

## 多信号优先级

一个请求同时命中多个模块时，按用户要的最终产物选主模块；数据、图、文献和日志只决定前置审计或后续模块。

| 复杂请求 | 主模块 | 前置/后续 |
|---|---|---|
| 写 Results/Discussion/摘要/论文段落 + summary.csv/图/日志 | writing | 先用 experiment/figure 审计证据；涉及 Results 图文叙事、图注、Figure 1/2 或领域风格时同时读 domain-style；后用 citation 补引用 |
| 补引用 + 写作 | writing | 先 claim 分段，再用 citation 支撑具体句子 |
| 投稿前 readiness/preflight + 完整主稿/补充材料/声明 | review | 同时回读 writing、citation、figure/source-data，输出风险预检和用户确认门 |
| 长期论文工作流继续推进 + 多个已生成 artifact | review | 先输出 completion audit，按模块列 done / safe next / confirmation gate，再只推进不越界的小产物 |
| 收到审稿意见/帮我回复 + baseline/泛化/消融/数据划分质疑 | rebuttal | 必须回读 review 和 experiment，先判断评论是否成立 |
| 审稿意见 + LaTeX 全文修改 + 修改前后对比 + 回复信 | rebuttal | 先修 `.tex` 正文并记录 revision log，再比较前后，最后写三色 response letter |
| 不知道从哪开始/想把某方向做成论文/选题 | module-routing | 先输出研究账本、核心问题、最小证据路径，再进 literature |
| 多个 PDF/参考论文 + 学习图文风格/图序/图注 | reader | 同时读取 domain-figure-writing-style；输出风格提取卡、可借鉴结构和不可照搬清单 |
| 精读论文 + 图能否借鉴到我的结果 | reader | 同时读取 domain-figure-writing-style；后续 figure 提取可迁移图型，experiment 检查用户结果是否具备相同变量、指标和对比结构 |
| 领域论文风格 + 写 Results/图注/Figure 1/2 | domain-style | 后续 writing/figure 落成段落或图表方案 |

涉及真实补引用、未公开审稿材料、未提供论文全文/结果文件，或继续会导致编造实验与引用时，先问用户或输出带明确占位符的 scaffold。

借鉴论文图时，只借鉴图表逻辑、变量编码、对比结构和图注组织；不得复制原图、视觉资产或未授权数据。需要在写作中引用原论文的图表设计或实验设置时，转入 citation。

## 路由输出模板

```text
路由判断：
- 主模块：
- 需要读取：
- 当前阶段：
- 是否需要多 agent：

我需要的材料：
- ...

先做的产物：
- ...

下一步：
- ...
```

## 什么时候问用户

只在以下情况问：

- 用户要执行长时间联网检索或下载。
- 用户要用未公开论文、敏感数据或审稿材料。
- 多个目标冲突，例如“快速写完”和“投稿级严谨”。
- 缺少核心材料，且继续会导致编造。

能从文件、目录、结果表中确认的事实，先查再问。
