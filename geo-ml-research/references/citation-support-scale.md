# 引用支撑等级

Pressure-test vocabulary note: project-specific terms are examples, not defaults; map them to the new project's terminology before use.

用于给论文段落补引用、做分段引用、判断文献是否真正支撑 claim，并准备 BibTeX/RIS/EndNote 导出。

## 术语映射优先

本文件中的 salt-cavern、SSCLS、GRU、BP/CNN、static baseline、radius 等词是当前压测项目示例。新项目先映射：

```text
engineering_domain:
data_source_claim:
method_claim:
performance_comparator:
derived_metric:
validation_risk:
```

再判断引用支撑等级。不得把示例领域文献或示例模型性能 claim 迁移到新项目；外部文献只能支撑它实际证明的句子。

## 工作流

1. 把输入段落拆成可引用 claim。
2. 每个 claim 改写成英文检索句。
3. 检索候选文献。
4. 检查摘要、方法、结果或全文证据。
5. 给出支撑等级和插入位置。
6. 输出引用表和风险。

补引用任务如果已有 reading cards 或 evidence anchors，必须优先使用本地锚点；外部检索只用于补缺口或核验元数据。不得绕过已有 `cannot_support`。

## Claim 类型

| 类型 | 说明 | 优先证据 |
|---|---|---|
| background | 背景事实或领域趋势 | 综述、高被引论文 |
| method | 方法或模型能力 | 方法论文、benchmark |
| engineering | 工程场景或工况 | 岩土工程实证论文 |
| performance | 性能提升 | 同任务实验论文 |
| mechanism | 机制解释 | 机理研究或可解释分析 |
| limitation | 限制或风险 | 失败案例、审查论文、讨论段 |

## 支撑等级

- `strong support`：同一问题、同一关系、同一或高度相近数据/工况，直接支持。
- `partial support`：支持部分对象、部分工况、部分方法或相近任务。
- `background support`：支持背景，不足以支撑具体性能或机制。
- `contradictory/limiting`：与 claim 冲突或限制 claim。
- `metadata-only`：只看标题/摘要元数据，不能作为最终支撑。
- `preflight-only`：API、MCP、publisher 页面、下载尝试或自动抽取显示可能可用，但尚未核验正文身份或证据位置。
- `file-verified`：已打开本地或开放全文并核对题名、年份，尽量核对作者或 DOI。
- `source-map-ready`：已建立 page/section/figure/table/evidence anchor，可直接进入 claim-evidence map。
- `unverified/no-source-provided`：没有用户提供文献、实验表或联网检索结果；只能列证据需求，不能给真实引用。
- `not recommended`：不建议引用为该句支撑。

## 插入位置

输出时说明：

- 插在整句后。
- 插在某个子句后。
- 需要拆句后分别引用。
- 该句不应补引用，而应改写或删除。

## 多索引元数据核验

正式 citation lock 前，文献身份优先通过多个索引交叉核验：

| 优先级 | 来源 | 用途 |
|---|---|---|
| 1 | DOI/Crossref、出版社页面、期刊官方 BibTeX | 题名、作者、年份、卷期、article number、DOI |
| 2 | PubMed、arXiv、Semantic Scholar、OpenAlex | 补充 ID、摘要、引用关系、preprint/正式版关系 |
| 3 | 本地 PDF 身份核验 | 题名、年份、作者或 DOI 的人工确认 |
| 4 | Google Scholar 或搜索结果 | 发现入口，不作为最终元数据唯一来源 |

去重规则：

- DOI、arXiv ID、Semantic Scholar ID、OpenAlex ID 相同则合并。
- 标题规范化 + 年份 + 第一作者高度一致时，作为 duplicate candidate，人工确认后合并。
- preprint 与正式出版版合并时，最终引用优先正式出版版；若引用 preprint 的特殊内容，必须说明原因。

核验输出建议：

```text
citation_key, source_id, doi_status, title_match, author_match, year_match, official_url, alternate_ids, duplicate_group, verification_status
```

## 输出模板

```text
分段 claim：
S001: ...

检索查询：
- ...

引用候选：
| segment | paper | support | why | insertion |

风险：
- ...

导出建议：
- BibTeX / RIS / ENW
```

## Reference metadata lock

当任务是整理候选文献、准备 Related Work、投稿前补引用或把 reading cards 转成可写作的 citation map 时，先做 reference metadata lock。必须同时读取：

- 候选表：`literature-candidates*.csv` 或等价检索表。
- 已有 reading cards。
- citation plan / related-work draft。
- 本文件和 `literature-workflow.md`。
- 已有 reference metadata lock，如果目录中已经存在。

锁定表至少包含三类字段：

| 类别 | 必要字段 |
|---|---|
| 文献身份 | `ID`, `priority`, `title`, `year`, `authors`, `venue`, `doi`, `url`, `source`, `query_id`, `theme`, `cited_by_count` |
| 证据状态 | `reading_card`, `local_pdf_candidate`, `pdf_match_status`, `evidence_level`, `support_level`, `verification_status`, `source_map_status`, `anchor_ids`, `next_action` |
| claim 边界 | `claim_type`, `manuscript_role`, `can_support`, `cannot_support`, `engineering object`, `prediction target`, `data source`, `model`, `baseline`, `metrics`, `split strategy`, `leakage/generalization risk` |

硬规则：

- `metadata-only` 只能用于筛选，不得进入最终 manuscript citation。
- `preflight-only` 不能进入最终 manuscript citation；必须升级到 `file-verified` 或 `source-map-ready`。
- 只有 reading card 中列入 `can_support` 的内容可以支撑 manuscript claim。
- `cannot_support` 是写作禁区；句子落入禁区时必须改写、删除或补新的证据。
- `local_pdf_candidate` 只是本地文件候选。必须打开文件并核对题名和年份，尽量核对作者或 DOI；未核验前不得升级为全文证据。
- `anchor_ids` 是 claim 到来源位置的最小证据桥。没有锚点时，可以写背景级引用，但不能支撑精确的图表、split、baseline 或性能 claim。
- 每篇 ML/工程类文献都要保留 split、baseline、metrics、HPO/model selection 和 leakage/generalization risk 字段；缺失时写 `not reported in metadata` 或 `needs full-text check`。
- 方法论文只能支撑风险、边界或 protocol rationale，不能替代本项目的 split audit、data provenance audit 或性能证据。

## Citation conversion lock

当 reading cards 和 reference metadata lock 已经完成，而任务进入投稿前引用整理、BibTeX 导出、或把 `[Lxxx]` 这类本地标记转为正式引用时，必须单独做 citation conversion lock。

最少输出两个文件或等价内容：

- `references-final-v*.bib`：只包含已核验身份的文献。
- `citation-conversion-map-v*.md`：把本地 marker、BibTeX key、manuscript role、可支撑 claim 和 no-go claim 锁在一起。

硬规则：

- 本地 marker 不是最终编号。正式稿前必须有 marker -> BibTeX key 的映射。
- 转换完成前必须扫描目标稿件中的组合本地 marker，例如 `rg -n "\[L[0-9]{3}[^\]]*\]" manuscript*.md`；只扫 `\[L[0-9]{3}\]` 会漏掉 `[L003, L014]` 这类组合标记。
- 书目信息优先用 DOI/Crossref、出版社页、期刊官方 BibTeX、PubMed、arXiv 或已核验本地 PDF 身份；不要从记忆补作者、卷期、页码或 DOI。
- 如果官方记录没有 DOI，例如部分 JMLR 论文，保留官方 URL，并标记 `no DOI found in official record`。
- 发现 reading card 或 metadata lock 中题名、作者顺序、卷期、article number 与 DOI 元数据不一致时，先修正引用锁，再写稿。
- `metadata-only` 候选不得进入 `references-final-v*.bib`。
- conversion map 仍要保留 `can_support/cannot_support`，防止正式引用后被误用成性能或泛化证据。

## 性能 claim 规则

看到以下词，默认按 performance claim 处理：

- improve
- outperform
- superior
- better than
- significantly
- more accurate
- robust

性能 claim 必须有同任务或高度相近任务的实验支撑，包括同一测试集或清楚的数据划分、同一指标、公平 baseline 和结果数字。通用 RNN/GRU 能力论文、盐穴背景论文、综述或题目相近论文只能提供背景支撑，不能支撑“优于 BP/CNN/static baseline”。

如果一个 claim 同时包含工程对象、方法和性能比较，先拆句：

```text
S001 background: solution-mined cavern shape forecasting is an engineering problem.
S002 method: recurrent models can model sequential dependencies.
S003 performance: GRU improves staged cavity-shape forecasting over static baselines.
```

S001/S002 可用背景或方法文献；S003 只能用同任务实验或本文结果。`static baseline` 不能自动等同于 BP/CNN，必须说明输入特征和时间建模方式。

## Validation/HPO 引用规则

数据划分、HPO、test-set reuse、model selection bias、data leakage 相关引用只支撑方法边界和风险说明：

- 可支撑：为什么 model selection 与最终 test reporting 需要隔离。
- 可支撑：为什么 row-level split 不能自动等同于 project/case/simulation-run/scenario-level 或 external engineering generalization。
- 可支撑：为什么 HPO selected 规则应基于 validation，而不是 test improvement。
- 不可支撑：当前数据没有泄漏。
- 不可支撑：当前模型具有独立工程泛化能力。
- 不可支撑：某个 tuned/HPO 结果必然优于 baseline。

检索 validation 文献时要消歧 `leakage`：

- 采用：data leakage、test-set leakage、model-selection leakage、information leakage only if the paper is about ML evaluation.
- 排除：gas leakage、water leakage、refrigerant leakage、privacy/security leakage、fraud leakage，除非论文明确讨论 ML 评估流程中的数据泄漏。

如果候选来自人工核验种子，source 必须标为 curated/manual，并保留 URL/DOI。人工种子仍需摘要或全文确认后才能从 `metadata-only` 升级为 `partial/strong support`。

## Data provenance 引用规则

外部文献只能支撑“领域背景、变量意义、传统模拟路线或相近任务”，不能证明当前本地 CSV 的来源。

| claim | 可用支撑 | 不可用支撑 |
|---|---|---|
| salt-cavern leaching simulation can model geometry evolution | leaching simulation paper | local `example_training_data.csv` columns |
| radius/sediment/volume are meaningful engineering variables | 工程或 simulator 文献 | 模型结果表本身 |
| our dataset was generated by a named simulator | 本项目生成脚本、导出说明、数据说明文件 | 相关 simulator 论文、相似题目论文、历史路径字符串 |
| our split is project/case/simulation-run/scenario independent | 显式 project/case/run/scenario ID + group split 代码/日志 + 无跨组泄漏证据 | leakage/model-selection 文献或 data provenance 证据 |

如果当前只有 CSV 和字段语义，data-source claim 标为 `unverified/no-source-provided`，并建议改写为 `current numerical cavern-geometry forecasting dataset`。

即使 data provenance 被确认，引用只能支撑“数据从哪里来”；不能自动支撑独立泛化、外部工程泛化或 field deployment。

## 质量底线

- 不虚构 DOI、页码、卷期或作者。
- 不把题目相关当作强支撑。
- 不用综述支撑具体实验性能，除非该句只是背景。
- 对最新文献、投稿关键 claim 或期刊范围要求，要重新联网核验。
- 如果没有合适文献，直接说没有，不用硬配。
- 没有来源时，输出 `unverified/no-source-provided`，不得给任何真实引用。
