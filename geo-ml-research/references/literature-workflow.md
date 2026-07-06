# 文献检索工作流

Pressure-test vocabulary note: project-specific terms are examples, not defaults; map them to the new project's terminology before use.

用于岩土工程 + 机器学习/深度学习方向的文献发现、综述前期和研究背景搭建。

## 检索档位

| 档位 | 场景 | 目标 |
|---|---|---|
| Quick | 用户只要几篇高价值文献 | 找 5-10 篇种子文献，区分经典和近期 |
| Standard | 用户要了解一个方向 | 找 30-80 篇候选，形成方法谱系 |
| Deep | 用户准备写综述或论文 Related Work | 多轮扩展，形成主题矩阵和研究空白 |
| Audit | 系统综述预备 | 需要明确纳入排除标准、PRISMA-S 日志，执行前先确认 |

默认使用 Standard。Audit 需要明确征得用户确认。

## 岩土 + ML 关键词扩展

从中文问题生成英文检索式时，至少覆盖：

- 工程对象：geotechnical engineering, soil, tunnel, foundation, slope, settlement, excavation, grouting, underground engineering.
- 任务类型：prediction, forecasting, stage forecasting, deformation, displacement, radius, volume, risk, stability.
- 模型类型：machine learning, deep learning, neural network, BP, CNN, RNN, GRU, LSTM, Transformer, XGBoost, random forest, surrogate model.
- 评价：RMSE, MAE, R2, generalization, ablation, baseline, hyperparameter optimization.
- 约束：physical constraint, mechanics-informed, data leakage, extrapolation, uncertainty.

盐穴/溶腔方向必须额外覆盖：

- 工程对象：salt cavern, solution-mined cavern, solution mining, brine mining, cavern leaching, underground gas storage, salt rock.
- 形态目标：cavern geometry, cavern shape, cavity shape, radius profile, volume evolution, roof/floor shape, sonar survey.
- 阶段/时间：staged construction, leaching stage, sequential prediction, time-series forecasting, stage forecasting.
- 对比对象：static baseline, sequence model, recurrent neural network, GRU, LSTM, CNN, BP neural network, multi-task learning.

盐穴/溶腔检索式至少同时包含“工程对象 + 形态/阶段预测目标 + 模型/对照 + 评价或泛化风险”四类词，避免只检索泛泛的 geotechnical machine learning。

## 检索策略

1. 写出 2-4 个核心英文 query。
2. 分出“问题背景”“传统方法”“机器学习”“深度学习”“工程应用”“评价指标”六类。
3. 优先找综述和高被引种子，再找近 3-5 年方法论文。
4. 对 ML 论文额外提取数据来源、划分方式、baseline、指标和是否开源。
5. 若涉及临床、生物或安全等高风险领域，必须用当前权威源重新核验；岩土/ML 一般以论文和官方数据/代码为准。

## 多源检索账本

Standard、Deep 和 Audit 档不得只给最终候选表。必须保留一份可复跑的检索账本，用来说明候选从哪里来、为什么保留或排除、哪些来源失败。账本可以是 Markdown、CSV 或 JSONL，但字段必须稳定。

最小 query ledger：

```text
query_id, database, query, date, filters, hit_count, retrieved_count, deduped_count, notes
```

最小 candidate ledger：

```text
source_id, query_id, title, year, authors, venue, doi, url, source_database, cited_by_count, abstract_available, oa_status, pdf_status, screening_status, include_reason, exclude_reason, next_action
```

推荐数据库分层：

- 通用索引：Semantic Scholar、OpenAlex、Crossref、Google Scholar、arXiv。
- 出版社或学会：ScienceDirect、SpringerLink、Wiley、ASCE、OnePetro、MDPI、Nature/Springer Nature。
- 领域检索：GeoRef、Engineering Village、Web of Science、Scopus（若用户有权限）。
- 代码与数据：GitHub、Zenodo、Figshare、OSF、institutional repository。

来源优先级不是“越多越好”，而是可核验性优先。DOI、arXiv ID、Semantic Scholar paper ID、OpenAlex ID 和出版社 URL 应尽量同时保留，用于去重和后续 citation lock。

## 获取与尝试日志

借鉴可复跑文献获取工具的做法，批量检索或全文获取时必须区分：

- `metadata-only`：只有标题、摘要、DOI 或索引页。
- `oa-fulltext`：开放 PDF/HTML 已找到。
- `local-pdf-candidate`：本地可能匹配的 PDF，但未打开核验身份。
- `file-verified`：已打开文件并核对题名、年份，尽量核对作者或 DOI。
- `source-map-ready`：已经提取页码、section、figure/table 或证据锚点。
- `needs-user-access`：需要用户机构登录、订阅、验证码、SSO 或手动下载。
- `failed`：尝试失败，保留原因和下一步。

批量任务建议写 `attempts.jsonl`，每行一个 source 或 DOI：

```json
{"source_id":"L001","target":"doi-or-url","attempted_at":"YYYY-MM-DD","method":"semantic-scholar|crossref|unpaywall|publisher|local-pdf|browser","status":"metadata-only|file-verified|failed|needs-user-access","evidence":"short note or path","failure_reason":"","next_action":"read full text|retry publisher|ask user|exclude"}
```

任务结束时输出 manifest，至少包含：

```text
source_id, title, status, evidence_level, reading_card, citation_ready, next_action
```

Markdown 报告、CSV 表和 JSON/JSONL manifest 的计数必须一致；不一致时先修账本，不继续写 Related Work。

## 去重与 snowballing

去重顺序：

1. DOI、arXiv ID、Semantic Scholar ID、OpenAlex ID 精确匹配。
2. 标题规范化匹配：小写、去标点、去多余空格，结合年份和第一作者。
3. 出版前版本与正式版本合并：保留正式 DOI，同时记录 preprint URL。

Deep 和 Audit 档应显式记录 citation chasing：

```text
seed_id, direction(backward|forward), source_database, added_count, reason
```

不要把追引得到的文献自动视为相关；仍需走同一筛选字段和 evidence level。

## 多 agent 检索分工

当任务包含 20 篇以上候选、多个数据库、多个主题或多个 PDF 时，优先采用只读并行分工。每个子 agent 只返回结构化表，不返回长篇摘抄。

推荐角色：

| agent | 输入 | 输出 |
|---|---|---|
| search-agent | 主题、query 组、数据库范围 | query ledger + candidate ledger |
| dedupe-agent | 候选表 | merge log + duplicate groups |
| fulltext-agent | DOI/URL/PDF 路径 | attempts.jsonl + evidence level |
| screening-agent | 候选表和纳入标准 | include/exclude 表 |
| synthesis-agent | 已核验 reading cards | method matrix + research gaps |

主 agent 负责合并和仲裁：同一文献状态冲突时，以更强证据等级为准；无法判断时标为 `conflict/needs manual check`，不得静默选择更有利结果。

## 输出格式

```text
检索目标：

英文检索式：
1. ...

候选文献分组：
| 组别 | 文献 | 作用 | 备注 |

方法谱系：
- 传统/机理：
- 机器学习：
- 深度学习：
- 物理约束/混合模型：

数据与评价表：
| 文献 | 数据/工况 | 模型 | baseline | 指标 | 可借鉴点 |

检索账本摘要：
| database | query_id | hit_count | retrieved_count | deduped_count | notes |

获取状态：
| status | count | next action |

研究空白：
- ...

下一步：
- 精读哪些论文
- 哪些 claim 需要引用支撑
```

## 离线检索模式

如果不能联网或用户只要检索设计，不得列真实文献、作者、DOI、年份排名或“最新主流”判断。输出：

- query 组。
- 数据库建议。
- 纳入/排除标准。
- 筛选表结构。
- 每类文献在 Related Work 中的角色。

盐穴/溶腔 + ML 筛选表至少包含：

```text
| ID | database | query_id | engineering object | salt cavern/cavity? | stage/time info | prediction target | data source | model | baseline | metrics | split strategy | leakage risk | Related Work role | evidence to inspect |
```

筛选 BP/CNN/GRU 对比文献时，不只按模型名筛选；必须记录输入是静态特征还是序列特征、是否同数据划分、是否同预测目标和指标。

## 质量检查

- 不只列论文标题，必须说明每篇文献在你的论文中可能承担什么角色。
- 不用单一搜索结果证明领域结论。
- 不把综述当成具体性能 claim 的唯一支撑。
- 对“最新”或“当前主流”这类说法，必须说明检索日期和来源范围。
- 不把搜索命中、MCP/API preflight、publisher snippet 或下载尝试当成全文证据；只有核验过正文或 PDF 身份后，才能升级 evidence level。
- 不把失败或无权限静默丢弃；必须在 attempts/manifest 中记录 `failed` 或 `needs-user-access`。
- 候选分类只能基于标题、摘要、期刊、作者关键词或全文元数据；不能把 query 本身的词当成文献内容证据，否则会把无关结果误判为相关。
- `leakage` 必须消歧：区分 data leakage / model-selection leakage / test-set reuse 与 gas leakage、water leakage、privacy leakage、security leakage。只有前者能支撑 ML 验证风险。
- Standard 档可以加入少量人工核验的经典方法种子，但必须在 `source` 中标为 curated/manual，并保留 URL/DOI；不得伪装成数据库自动检索结果。
- 如果某个元数据源限流或超时，保留日志并继续使用其他来源；报告中明确说明来源范围，不把 v0 候选表写成“全领域完整覆盖”。
- validation/HPO 风险文献用于支撑方法边界和 limitation，不能替代当前数据 split audit，也不能证明本研究没有泄漏。
