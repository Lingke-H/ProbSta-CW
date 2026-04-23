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
> 是否需要调换6和7的顺序使逻辑更通顺？

## Section 2: Investigating the relationship between Attendance and other variables

1. **散点图矩阵 (pairs)** — Attendance + 自选 4 个变量的 pairwise scatterplot matrix
2. **Pearson 相关系数矩阵** — 5 个变量之间的 pairwise correlation（5×5 表格）
3. **选出最强相关变量** — 取与 Attendance 绝对相关最高的变量作为 `data2`（即 `Income_rate`）
4. **线性回归模型** — 建立 $\text{Attendance} = \beta_0 + \beta_1 \times \text{Income\_rate} + \varepsilon$
5. **回归散点图** — 画散点图 + 最佳拟合线 (line of best fit)
6. **残差图** — 画残差 vs 拟合值，评估线性回归假设

## Section 3: A Further Investigation — "Glasgow 的低出勤是因为贫困，还是另有原因？"

**逻辑线**：S1 发现入学率 Glasgow < Edinburgh；S2 发现 `Income_rate` 是最强预测因子。Section 3 追问：Glasgow 的低出勤是否完全由收入贫困解释，还是有其他额外的负面因素在影响？

### 分析步骤

> **核心问题**：Glasgow 的低出勤是否完全由收入贫困解释？如果不是，还有什么原因？

#### 一、确立 Glasgow 的独特性（验证铺垫）

1. **三区域 boxplot + F-test + t-test** — 快速建立事实（Ch.12–15）
   - 并排 boxplot 直观展示 Glasgow / Edinburgh / Rest of Scotland 的 Attendance 差异
   - Pairwise F-test 确认方差差异 → 选择正确的 t-test 类型
   - Pairwise Welch's t-test 正式验证均值排序：Edinburgh > Rest > Glasgow（均显著）
   - → **结论：Glasgow 的低出勤不仅相对 Edinburgh 差，相对全国也差——这是 Glasgow 特有的问题**

#### 二、证明 Income 不能完全解释 Glasgow（核心论证）

2. **分区域回归 + prediction interval** — 分别拟合 Attendance ~ `Income_rate`，比较三条回归线（Ch.17–18）
   - 如果 Glasgow 的低出勤完全由贫困解释，三个区域的回归斜率和 $R^2$ 应接近
   - 实际结果：Glasgow 的 $R^2$ 更低 / 斜率更平 → Income_rate 对 Glasgow 的解释力不足
   - 在同一个 Income_rate 水平下，用三个模型分别做 prediction interval：Glasgow 的预测出勤率更低、区间更宽
   - → **结论：存在某种"Glasgow 效应"——除贫困外，还有其他因素在额外拉低 Glasgow 的出勤率**

#### 三、提取并量化"Glasgow 效应"（从定性到定量）

3. **残差提取 + 单样本 t-test** — 用非 Glasgow 数据建立基准模型，提取 Glasgow 残差（Ch.14, Ch.18）
   - 用 Edinburgh + Rest of Scotland（300 个数据区域）拟合基准回归：$\text{Attendance} = \beta_0 + \beta_1 \times \text{Income\_rate} + \varepsilon$
   - 用该模型预测 Glasgow 的 100 个数据区域的"预期出勤率"（即：如果 Glasgow 和全国一样，在其贫困水平下出勤率应该是多少）
   - 计算残差：$e_i = \text{Actual}_i - \text{Predicted}_i$（每个 Glasgow 数据区域"比预期差了多少"）
   - 对 Glasgow 残差做单样本 t-test：$H_0: \mu_e = 0$（即 Glasgow 没有额外偏差）
   - 若拒绝 $H_0$ 且 $\bar{e} < 0$：**Glasgow 在剥离收入影响后，出勤率平均额外下降了 $|\bar{e}|$** → 这就是"Glasgow 效应"的定量大小
   - → **结论：Glasgow 效应 = X%（具体数字），这不是随机波动，而是统计上显著的系统性偏差**

#### 四、追踪根源——是什么在制造"Glasgow 效应"？

4. **残差相关性扫描** — Glasgow 残差 vs 所有其他连续变量的 Pearson 相关系数（Ch.17）
   - 残差 = "剥离收入影响后的纯净出勤表现"。与残差相关最强的变量，就是"Glasgow 效应"的最可能解释
   - 批量计算 Glasgow 残差与 crime_rate, health, overcrowded, Attainment, CIF 等变量的 $r$，按 $|r|$ 从大到小排序
   - 让数据自己说话，客观定位核心变量（假设扫描结果指向 `Variable_X`）
   - → **结论：`Variable_X` 与 Glasgow 效应的关联最强——它是收入之外拉低 Glasgow 出勤的首要嫌疑因素**

5. **核心因素深潜** — 残差 vs `Variable_X` 散点图 + 回归线（Ch.17–18）
   - 绘制 Glasgow 残差 vs `Variable_X` 的散点图 + 拟合线，直观展示："在同等收入下，`Variable_X` 越差，出勤率被额外拉低越多"
   - 报告该回归的 $r$, $R^2$, 斜率 CI → 量化 `Variable_X` 对 Glasgow 效应的解释力
   - **（可选 Bonus）卡方独立性检验**（Ch.19）：将残差三等分为 Negative / Neutral / Positive，`Variable_X` 也三等分，构造 3×3 列联表，$\chi^2$ 检验两者是否独立 → 用不同的统计框架交叉验证
   - → **结论：解决 Glasgow 的教育公平问题不能仅靠减贫，还需要针对 `Variable_X`（如社区安全、公共健康等）进行干预**

### 故事线总结

```
S1 发现 → S2 发现 → S3 追问
Glasgow更差  Income_rate最强  贫困能完全解释Glasgow的差吗？

Step 1: 确立事实 — Glasgow 比全国都差
    ↓
Step 2: 证伪假设 — Income 解释不了 Glasgow（R²低、同贫困下预测更差）
    ↓
Step 3: 量化效应 — 建基准模型 → 提取残差 → "Glasgow 效应 = -X%"
    ↓
Step 4-5: 追踪根源 — 残差相关扫描 → 锁定 Variable_X → 散点图验证
    ↓
→ 最终结论：Glasgow 的低出勤 = 贫困 + "Glasgow 效应"
  Glasgow 效应 ≈ Variable_X（如 crime/health）→ 政策建议：减贫 + 改善 X
```

### 方法覆盖

| Chapter | 方法 | S1 | S2 | S3 |
|---------|------|:--:|:--:|:--:|
| Ch.12 | CI for mean | ✅ | | ✅ |
| Ch.13 | Hypothesis testing (HT) framework | ✅ | | ✅ |
| Ch.14 | Comparing means using HT | ✅ | | ✅ (单样本 t-test on 残差) |
| Ch.15 | Comparing variances using HT | ✅ | | ✅ |
| Ch.17 | Pearson correlation | | ✅ | ✅ (残差相关扫描) |
| Ch.18 | Linear regression + residual + prediction | | ✅ | ✅ (基准模型 + 残差提取) |
| Ch.19 | Chi-squared tests | | | ⭐ (可选 bonus) |


### 注意事项
- 题目提示方向：在section 1的基础上增加考虑除了 Glasgow 和 Edinburgh 之外的城市；在section 2的基础上，讨论如果将除了 Glasgow 和 Edinburgh 之外的数据区域考虑在内的话结论是否成立；探索除了section 1和2提到的变量之外的其他变量。
- 有些在数值上有较为明显的相关性的变量可能背后的因果关系不值得研究，需要仔细斟酌。

## Hints & Tips
包含一些用 R 画图时可以巧用的语法和代码简化提示，写代码时可以参考。

## Marking Scheme
写 report 文段部分的时候需要重点对照里面对每个部分的要求，确保无遗漏。（注：似乎对 R 代码部分只要求“Effective use of R for plotting and demonstrating understanding of commands”，未对作图方法有限制。
> 是否以图片效果为评判 effectiveness of R usage的标准？