# Report 思路整理

## Instructions
- 注意所有图表的统一大小：`{r, fig.height=4, fig.width=6}`
- 确保每个section开始于新的一页（`\newpage`）
- 主体代码用 `echo=FALSE`

## Your Task
“scottishData.csv”包含*400个数据区域*的统计数据，其中包含*34个变量*，变量说名在 Table 1 附件中有详细呈现。在 Section 1-2 中，本组应重点分析的对象是 **Attendance（入学率）**；在 Section 3 中，本组应**自行选择**一个变量进行分析。

报告需要包含以下部分：**Summary, Introduction, Methods, Results, Conclusions**，更加详细的报告呈现指导细节请参考 “Writing a statistical report“ 附件和 “Marking Scheme”部分。

其中，对于 **Results**，需要下面三个板块中的所有小点（绝不能遗漏，每个小点1-3分），并加上适当的连接语句使得推倒逻辑连贯，段落之间过渡自然：
- Section 1: Summary Statistics and Hypothesis test （比较 Glasgow 和 Edinburgh 的入学率，方法对应课程 Chapter 12-16 的内容）
- Section 2: Investigating the relationship between Attendance and other variables（选择`Income_rate`作为解释变量，方法对应课程 Chapter 17-19 的内容）
- Section 3: A brief investigation of the student's choosing

## Section 1: Summary Statistics and Hypothesis test

1. **Boxplot** — 并排箱线图，比较两城 Attendance 的大致分布
2. **Histogram** — 分别画直方图，观察各自的偏态和集中程度
3. **均值与方差** — 计算两城的样本平均值和方差，量化差异
4. **均值的 95% CI** — σ 未知，用 $t$ 分布构造各城均值的置信区间
5. **方差的 95% CI** — 用 $\chi^2$ 分布构造各城方差的置信区间
6. **均值差异检验 (t-test)** — Welch's t-test 检验 $H_0: \mu_G = \mu_E$，用 p-value 下结
论
7. **方差差异检验 (F-test)** — F-test 检验 $H_0: \sigma^2_G = \sigma^2_E$，用 p-value 下
结论

## Section 2: Investigating the relationship between Attendance and other variables

1. **散点图矩阵 (pairs)** — Attendance + 自选 4 个变量的 pairwise scatterplot matrix
2. **Pearson 相关系数矩阵** — 5 个变量之间的 pairwise correlation（5×5 表格）
3. **选出最强相关变量** — 取与 Attendance 绝对相关最高的变量作为 `data2`（即 `Income_rate`）
4. **线性回归模型** — 建立 $\text{Attendance} = \beta_0 + \beta_1 \times \text{Income\_rate} + \varepsilon$
5. **回归散点图** — 画散点图 + 最佳拟合线 (line of best fit)
6. **残差图** — 画残差 vs 拟合值，评估线性回归假设

## Section 3: A Further Investigation — "Glasgow 的低出勤是因为贫困，还是另有原因？"

**逻辑线**：S1 发现 Glasgow < Edinburgh；S2 发现 `Income_rate` 是最强预测因子。Section 3 追问：Glasgow 的低出勤是否完全由收入贫困解释，还是存在区域本身的 "Glasgow effect"？

### 分析步骤

1. **三区域 boxplot + 描述统计** — Glasgow / Edinburgh / Rest of Scotland 的 Attendance 并排比较（Ch.12–13 延伸）
2. **Pairwise t-tests** — Glasgow vs Rest、Edinburgh vs Rest 的 Welch's t-test（Ch.14）
3. **Pairwise F-tests** — 对应的方差比较（Ch.15）
4. **分区域回归** — 分别拟合 Attendance ~ Income_rate，比较三条回归线的斜率 CI 是否重叠（Ch.18：回归 + CI for $\beta_1$）
5. **Prediction interval** — 给定某个典型 Income_rate，用各区域模型分别预测 Attendance 的 95% 预测区间（Ch.18）
6. **比例检验** ⭐ — 定义"低出勤"（Attendance < 全样本中位数），用两样本 z-test 检验 Glasgow 的低出勤比例是否显著高于其他区域（**Ch.16，新方法**）
7. **卡方独立性检验** ⭐ — 构造列联表 Region (3) × Attendance 等级 (Low/Med/High)，$\chi^2$ 检验出勤等级与区域是否独立（**Ch.19，新方法**）

### 方法覆盖

| Chapter | 方法 | S1 | S2 | S3 |
|---------|------|:--:|:--:|:--:|
| Ch.12 | CI for mean | ✅ | | ✅ |
| Ch.13 | Hypothesis testing framework | ✅ | | ✅ |
| Ch.14 | Two-sample t-test | ✅ | | ✅ |
| Ch.15 | F-test for variances | ✅ | | ✅ |
| Ch.16 | Proportion z-test | | | ⭐ |
| Ch.17 | Pearson correlation | | ✅ | ✅ |
| Ch.18 | Regression + prediction interval | | ✅ | ✅ |
| Ch.19 | Chi-squared independence | | | ⭐ |


### 注意事项
- 题目提示方向：在section 1的基础上增加考虑除了 Glasgow 和 Edinburgh 之外的城市；在section 2的基础上，讨论如果将除了 Glasgow 和 Edinburgh 之外的数据区域考虑在内的话结论是否成立；探索除了section 1和2提到的变量之外的其他变量。
- 有些在数值上有较为明显的相关性的变量可能背后的因果关系不值得研究，需要仔细斟酌。