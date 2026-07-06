# 论文精读卡

用于把单篇论文、PDF、DOI、arXiv、网页或粘贴文本转成对个人项目有用的精读结果。

## 先判断阅读目标

- 快速判断是否值得读。
- 提取数据、模型、baseline、指标。
- 做中英文对照。
- 找可借鉴写法、图表或实验设计。
- 为自己的岩土/ML论文补引用。

如果阅读目标包含“学习图文风格”“借鉴 Figure 1/2”“改图注/Results 风格”，同时读取 `domain-figure-writing-style.md`，把图表逻辑、caption 信息顺序和可借鉴边界单独记录。不得复制原图注句子，只抽象图注结构。

如果用户只提供摘要、标题或片段，输出标题必须标为“摘要级精读卡”或“片段级精读卡”。不得填写 DOI、图表编号、source location、具体页码、全文方法细节或可引用原句；未知项统一写“未提供/需全文确认”。

如果来源是 DOI/出版页/HTML/PDF 混合访问，必须先标注证据等级：

- `全文级`：可访问并检查完整正文或开放 HTML。
- `开放论文级，待页码级 source map`：可访问完整 PDF/HTML，但本轮未做页码块级提取。
- `摘要/出版页级`：只看到摘要、publisher snippets、section snippets 或元数据。
- `片段级`：只看到用户粘贴片段或搜索结果片段。
- `本地文件候选，身份未核验`：只根据文件名、路径或历史记录推测匹配。

证据等级决定可写内容。摘要/出版页级卡不得填写未确认的 split 比例、完整 baseline 公平性、随机种子、页码、完整图表编号或强支撑 claim。可写为“需全文确认”。

如果后续用本地 PDF 或开放全文升级已有摘要/出版页级 reading card，必须先核对文献身份，包括题名、年份，并尽量核对作者或 DOI。升级后同步更新：

- reading card 的 `证据等级`、`source map`、`可引用 claim` 和 `不能引用的 claim`。
- citation/reference metadata lock 中的 `verification_status`、`source_map_status`、`pdf_match_status` 和 `can_support/cannot_support`。
- 旧卡片中所有“需全文确认”的占位；已经核验的改成具体结论，仍未核验的保留为缺口。

本地 PDF 文件名不能单独作为全文依据；只有打开并核对身份后，才能把 `local_pdf_candidate` 升级为可用全文入口。

## 证据锚点

全文级精读卡必须尽量建立 evidence anchors。锚点不是长引用，而是后续写作、citation lock 和 review 可追踪的定位信息。

最小锚点字段：

```text
anchor_id, claim, evidence_note, source_location, figure_table, page_or_section, support_level, can_support, cannot_support
```

`source_location` 可以是页码、section 标题、figure/table 编号、PDF 块编号、HTML heading 或用户提供片段编号。无法定位时写 `needs source map`，不得补造页码。

锚点编号建议：

```text
L003-A01 method-data
L003-A02 split-baseline
L003-A03 main-result
L003-A04 limitation
```

证据锚点只记录必要事实，不复制大段原文。需要英文论文写作时，先把锚点转成 claim-evidence map，再写段落。

## 多 agent 精读分派

多篇论文或长 PDF 精读时，优先按问题分派只读子任务，而不是让一个 agent 通读所有内容。推荐分工：

| agent | 关注点 | 必交字段 |
|---|---|---|
| identity-agent | 题名、作者、年份、DOI、出版信息 | identity_status, mismatch |
| method-agent | 数据、模型、输入输出、实验设置 | method anchors |
| validation-agent | split、baseline、公平性、HPO、随机种子 | ML credibility anchors |
| figure-agent | 图表结构、caption 信息顺序、可迁移图型 | figure anchors |
| limitation-agent | 外推边界、作者 limitation、失败模式 | limitation anchors |

合并规则：

- 只合并结构化锚点、表格和抽象规则，不复制原文长段。
- 同一事实冲突时，标为 `conflict` 并列出冲突来源；不得选择更符合预期的一方。
- 摘要级 agent 输出不能覆盖全文级核验结果。
- 图文风格借鉴只吸收组织逻辑，不吸收图像、图注原句或未授权数据。

## 精读顺序

1. 读标题、摘要、结论，识别核心 claim。
2. 读 Introduction，找工程问题、现有方法缺口、论文贡献。
3. 读 Method，提取模型结构、输入输出、关键假设和实现细节。
4. 读 Experiments/Results，提取数据、划分、baseline、指标、主表和主图。
5. 读 Discussion/Limitations，记录边界和不能外推的内容。
6. 反向映射到你的项目：能借鉴什么，不能借鉴什么。

## 输出模板

```text
论文信息：
- 标题：
- 年份/期刊：
- DOI/arXiv：
- 证据等级：
- 身份核验：

一句话结论：

研究问题：

数据与工况：
| 数据 | 样本/工况 | 输入 | 输出 | 划分 |

方法：
- 模型：
- 关键模块：
- 物理/工程约束：

实验：
| claim | evidence | figure/table | support |

baseline 与指标：
| baseline | 指标 | 结果 | same split? | same features? | same tuning budget? | 是否公平 |

ML 可信度：
- split unit：
- leakage risk：
- baseline fairness：
- HPO budget：
- random seeds/repeats：
- external validation：

图表价值：
- 可借鉴图：
- 可借鉴图注结构/信息顺序：

对我的项目的启发：
- 可直接借鉴：
- 需要谨慎：
- 不适用：

可引用句/claim：
| claim | source location | 用途 |

证据锚点：
| anchor_id | claim | evidence_note | source_location | support_level | can_support | cannot_support |

缺口：
- 复现信息：
- 数据边界：
- 可能风险：
```

## 中英文对照要求

如果用户要求中英文对照：

- 保留段落顺序。
- 每段给英文原意、中文解释、图表位置或来源锚点。
- 图表必须就近解释，不能把图表丢到最后。
- 不得无来源地解释图中不存在的信息。

## 阅读质量底线

- 不把精读降级成摘要，除非用户只要摘要。
- 不忽略数据划分和 baseline，因为这是 ML 论文可信度核心。
- 不把作者 claim 原样当事实，必须区分“作者声称”和“实验支持”。
- 摘要级输入不能推断全文图表、完整实验设置、DOI 或可引用句。
- 没有 source map 时，不得声称“第几页/图几证明”；写 `needs source map`。
- 证据锚点必须能回到原文、PDF、HTML 或用户材料；不能从模型记忆创建。
- 对外部 ML 论文中的 `superior/generalization/robust/adaptability/significant` 等强词，必须降级检查：它是否只是在该论文自己的 split、任务和模型预算下成立。
- hybrid model vs plain model 比较要额外检查 same features、same preprocessing、same tuning budget。若 hybrid 使用额外 decomposition、optimizer、工程变量或搜索预算，只能作为 analog，不能当公平同预算 baseline 证据。
- field validation、external validation、GNSS/InSAR cross-check、test set 是不同证据类型，不能混用。现场工程验证可支撑该论文的工程可信度，但不自动支撑我们的数据泛化。
