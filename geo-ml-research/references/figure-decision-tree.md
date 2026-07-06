# 图表决策树

Pressure-test vocabulary note: project-specific terms are examples, not defaults; map them to the new project's terminology before use.

用于岩土工程 + ML/DL 论文图表设计。原则：先理解数据和论证目标，再选图，再出图，再自检。

如果任务涉及盐穴/岩土 ML 论文的整体图序、Figure 1/2、领域论文图文风格或图注写法，同时读取 `domain-figure-writing-style.md`。先用它确定工程对象、数据任务和图文叙事，再用本文选择具体图型和 QA。

## 图表前检查

```text
Figure claim:
Data source:
Variables:
Grouping:
Sample size:
Metric:
Primary metric:
Secondary metrics:
Target venue:
Language:
Output format:
```

如果用户只说“帮我画图”，先问或推断这张图要证明什么。

## 常见任务到图型

| 任务 | 推荐图 | 不推荐 |
|---|---|---|
| 预测值 vs 真实值 | 散点 + y=x + RMSE/R2/MAE | 只放表格 |
| 残差分析 | 残差直方/KDE + 残差 vs 真实值/阶段/深度 | 只报平均误差 |
| 训练过程 | train/val loss 曲线 + early stopping 标记 | 只报最终 epoch |
| HPO 对比 | baseline/tuned/selected 分组条形或点图 | 只展示 tuned 最优 |
| 多模型多任务 | heatmap 或 grouped dot plot | 过宽大表格 |
| 消融实验 | 模块/特征移除对指标影响的点图 | 无 claim 的性能堆叠 |
| 阶段/工况变化 | 分面图或共享 x 的上下子图 | 双 Y 轴 |
| 小样本组间比较 | stripplot / box + points | 均值柱状图 |
| 特征重要性 | 排序条形 + 稳定性说明 | 3D 图 |
| 不确定性 | 误差带、置信区间、预测区间 | 单条曲线伪确定 |

## 从实验结果到作图数据

用 `summary.csv` 或 HPO 结果作图前，先生成一张长表：

```text
source, model, task, stage_count, metric, value, selection, keep_tuned, artifact
```

规则：

- HPO 图必须同时保留 `baseline`、`tuned`、`selected`，其中 `selected` 按 `keep_tuned` 决定。
- 多模型主图默认画 `selected`，并在图注说明回退规则。
- 跨目录作图前确认数据哈希、任务配置和划分策略；未确认时在图注标注边界。
- 完整 6 任务和单任务结果分开画，不把单任务 LSTM/MLP 塞进完整 6 任务平均图。
- 速度测试包只能作为速度/可运行性参考；除非数据、split、训练配置完全可比，否则不进入论文主性能排名。
- 若 `keep_tuned` 由 RMSE 决定，图注必须说明主指标；当 RMSE 改善但 R2、MAE 或工程约束指标变差时，标为 mixed evidence，不能画成整体提升。

推荐输出：

- `figure_data.csv`：图表使用的干净长表。
- `figure_notes.md`：数据来源、筛选规则、不能声称的内容。
- `*.svg` 或 `*.pdf`：论文优先矢量图。
- `*.png`：便于快速预览。

## 工程误差图

做 Figure 6、残差分析、预测-实测图或工程误差解释时，不能只用 `summary.csv` 硬凑。必须优先读取 inverse-transformed prediction artifact，例如：

```text
predictions/test_inputs_true_pred_baseline.csv
predictions/test_inputs_true_pred_tuned.csv
```

选择预测文件时遵守：

- HPO 结果按 `keep_tuned` 选择：`True -> tuned`，`False -> baseline`。
- 如果只画 selected 模型，脚本和 notes 必须记录每个 task 实际用了 tuned 还是 baseline。
- 如果画残差、预测-实测或工程阈值，必须使用原始单位列，不能用 normalized prediction。
- 若输出是多头/多阶段标签，先根据模型头定义拆分变量组；例如 radius、sediment、effective_volume 要分开解释。
- 有有效半径、工程半径或体积等派生指标时，优先使用已有 metrics 文件或明确复现公式，不要把全量 RMSE 当工程误差。

推荐 Figure 6 最小结构：

| panel | 内容 | 数据 |
|---|---|---|
| A | prediction vs reference | selected prediction CSV 的原始单位真值/预测值 |
| B | residual distribution by task/stage | `pred - true`，保留单位 |
| C | engineering metric comparison | effective-radius RMSE、阈值内比例或任务级工程指标 |

图注必须说明：

- selected 预测文件如何选择。
- 使用的是 radius、effective radius、volume 还是其他工程变量。
- 是否只是 row-level holdout。
- 是否存在 primary metric 和 engineering metric 不一致；不一致时写 mixed evidence。

## 主动拦截

发现以下情况时先提醒，再给替代方案：

- n 很小却画均值柱。
- 双 Y 轴暗示虚假相关。
- 分类变量被折线连接。
- Y 轴截断导致差异夸大。
- 使用 rainbow/jet 色图。
- 一张图塞多个 claim。
- 图例、单位、坐标轴缺失。
- 用测试集调参后的结果直接画成最终泛化结论。
- 只画 tuned HPO 最优值，却不展示 selected 或 `keep_tuned`。
- 用平均值柱状图掩盖任务级异常。
- 没有读取 prediction artifact，却画预测-实测或残差。
- primary RMSE 未提升，却用 effective-radius 小幅改善暗示整体提升。

## 出图规范

- 每张图只服务一个主要 claim。
- Figure 1/2/3 这类 schematic/workflow 图优先保证标签可读：如果四栏并排导致文字拥挤，改成 2x2 或主图+子图布局，不要硬塞。
- 不在图内放很大的总标题；Figure 编号和长标题交给 caption，图内只保留 panel label 和短 panel title。
- 窄色块、短箭头和小流程节点不要硬塞长标签；用 legend、caption 或相邻 note 承接完整解释。
- 坐标轴必须有单位。
- 同一变量在多 panel 中保持同一颜色。
- 多模型对比保持排序和颜色一致。
- 优先矢量输出：PDF/SVG。位图使用 300 dpi 以上。
- 中文图检查 CJK 字体，英文论文图优先英文标签。
- 图注要解释：数据、模型、指标、统计含义、样本或工况。
- 对 HPO 图，图注必须解释 baseline/tuned/selected 和 `keep_tuned` 的含义。
- 长模型名或长任务名优先分行显示，并为 footnote、x 轴标签和图例预留边距；最终必须打开图像检查是否重叠。
- 导出后逐项检查：tick label overlap、legend overlap、footnote overlap/clipping、numeric annotation overlap。
- 标签的数据数字不能被曲线所遮挡。
- 对 schematic 图也必须打开 PNG/SVG 预览检查：panel label 是否压住标题、流程框文字是否越界、footnote 是否被裁切、箭头是否穿过文字。

## 论文排版字号

按最终排版尺寸检查英文图中文字。没有期刊特殊要求时，默认使用：

| 元素 | 常用范围 | 默认 |
|---|---:|---:|
| 坐标轴标题 | 8-10 pt | 9 pt |
| 刻度数字 | 7-8 pt | 8 pt |
| 图例文字 | 7-8 pt | 8 pt |
| 子图编号 A/B/C | 10-12 pt | 11 pt bold |
| 图中文字注释 | 7-9 pt | 8 pt |

字体优先使用 Arial 或 Helvetica。导出前按论文最终列宽或双栏宽度预览，不按放大的 notebook 视图判断字号。

## 图表 QA 清单

```text
Claim visible: yes/no
Axes labeled: yes/no
Units present: yes/no
Legend clear: yes/no
Text overlap: yes/no
Tick label overlap: yes/no
Legend overlap: yes/no
Footnote overlap/clipping: yes/no
Numeric annotation overlap: yes/no
Colorblind safe: yes/no
Grayscale readable: yes/no
Panel labels aligned: yes/no
Data transformation explained: yes/no
Caption self-contained: yes/no
```

## 输出模板

```text
图表目标：

推荐图型：

为什么：

数据需求：

绘图方案：
- x:
- y:
- group:
- panel:

图注草稿：

QA 风险：

下一步：
```
