# Report 思路整理

## Instructions
- 注意所有图表的统一大小：`{r, fig.height=4, fig.width=6}`
- 确保每个section开始于新的一页（`\newpage`）
- 主体代码用 `echo=FALSE`

## Your Task
scottishData.csv 包含*400个数据区域*的统计数据，其中包含*34个变量*，变量说名在 Table 1 附件中有详细呈现。在 Section 1-2 中，本组应重点分析的对象是 **Attendance（入学率）**；在 Section 3 中，本组应**自行选择**一个变量进行分析。

报告需要包含以下部分：**Summary, Introduction, Methods, Results, Conclusions**，更加详细的报告呈现指导细节请参考 “Writing a statistical report“ 附件和 “Marking Scheme” 部分。

其中，对于 **Results**，需要下面三个板块中的所有小点（绝不能遗漏，每个小点1-3分），并加上适当的连接语句使得推倒逻辑连贯，段落之间过渡自然：
- Section 1: Summary Statistics and Hypothesis test （比较 Glasgow 和 Edinburgh 的入学率，方法对应课程 Chapter 12-16 的内容）
- Section 2: Investigating the relationship between Attendance and other variables（选择`Income_rate`作为解释变量，方法对应课程 Chapter 17-19 的内容）
- Section 3: A brief investigation of the student's choosing

## Section 1: Summary Statistics and Hypothesis test — Glasgow 和 Edinburgh 的出勤率有何差异？

**逻辑线**：先用图形建立直觉，再用数值量化差异，最后用推断统计下结论。

### 分析步骤

> **核心问题**：Glasgow 和 Edinburgh 两城的 Attendance 在均值和方差上是否存在显著差异？

#### 一、图形探索（建立直觉）

1. **并排 Boxplot**（Ch.12 探索性分析）
   - 直观比较两城 Attendance 的中位数、四分位距和离群值
   - → **初步印象：Edinburgh 中位数更高，Glasgow 有低端离群值**

2. **分城 Histogram**（Ch.12 探索性分析）
   - 观察各自分布形态和偏态
   - → **进一步印象：Edinburgh 紧密集中在高值，Glasgow 更宽且明显左偏——某些 Glasgow 数据区域出勤率极低**

#### 二、描述性统计（量化差异）

3. **样本均值与方差**（Ch.12 描述性统计）
   - 计算 $\bar{x}_G$, $\bar{x}_E$, $s^2_G$, $s^2_E$，将视觉印象转化为具体数字
   - 实际结果：均值差 ≈ 3.2 个百分点；Glasgow 的 $s^2$ 反而略小于 Edinburgh（尽管直方图显示 Glasgow 下尾更重）
   - → **量化确认：Glasgow 均值明显更低；方差差异方向并不如箱线图暗示的那样简单**

4. **均值的 95% CI**（Ch.12 置信区间，$t$ 分布，$\sigma$ 未知）
   - 构造 $\bar{x} \pm t_{\alpha/2, n-1} \cdot s/\sqrt{n}$
   - 实际结果：Glasgow $(0.745, 0.778)$，Edinburgh $(0.774, 0.813)$，**两区间不重叠**
   - → **强初步证据：总体均值确实不同**

5. **方差的 95% CI**（Ch.12 置信区间，$\chi^2$ 分布）
   - 构造 $\left(\frac{(n-1)s^2}{\chi^2_{1-\alpha/2}},\ \frac{(n-1)s^2}{\chi^2_{\alpha/2}}\right)$
   - 实际结果：Glasgow $(0.00550, 0.00964)$，Edinburgh $(0.00746, 0.01305)$，**两区间重叠**
   - → **初步暗示：方差差异可能不显著——需要正式检验确认**

#### 三、假设检验（正式下结论）

6. **方差差异检验 — F-test**（Ch.15）
   - 先检验方差是否相等，以此决定后续 t-test 的类型
   - $H_0: \sigma^2_G / \sigma^2_E = 1$ vs $H_1: \sigma^2_G / \sigma^2_E \neq 1$（假设正态性）
   - 实际结果：$F = 0.738$, $df_1 = df_2 = 99$, $p = 0.133 > 0.05$ → **无法拒绝 $H_0$**
   - → **结论：没有充分证据表明两城出勤率的方差存在显著差异，与方差 CI 重叠的结果一致**

7. **均值差异检验 — Welch's t-test**（Ch.13–14）
   - 已知方差无显著差异，现在正式检验均值
   - $H_0: \mu_G = \mu_E$ vs $H_1: \mu_G \neq \mu_E$（Welch's t-test 无论方差是否相等均有效）
   - 实际结果：$t = -2.505$, $df = 193.61$, $p = 0.013 < 0.05$ → **拒绝 $H_0$**
   - → **结论：Glasgow 的均值出勤率显著低于 Edinburgh，与均值 CI 不重叠的结果一致**

### Section 1 小结

> Glasgow 的**均值出勤率显著低于** Edinburgh，但**方差没有显著差异**。
> 这说明 Glasgow 的低出勤是一个系统性的水平偏移，而非简单的离散程度不同。
> 自然追问：**是什么因素导致了 Glasgow 的系统性低出勤？** → 引出 Section 2

### S1 → S2 过渡逻辑

> S1 确立了事实：Glasgow 的平均出勤率显著低于 Edinburgh。但 S1 只做了两城比较，没有探究原因。下一步需要将视角扩展到全部 400 个数据区域，考察 Attendance 与哪些社会经济变量最密切相关——这就是 S2 的任务。

---

## Section 2: Investigating the relationship between Attendance and other variables — 什么因素与出勤率关系最密切？

**逻辑线**：先看全部 4 个候选变量，再精确建模最强预测因子，最后检查模型假设是否成立。

### 分析步骤

> **核心问题**：在 Attainment、Crime_rate、CIF、Income_rate 四个候选变量中，哪个与 Attendance 的线性关系最强？能否用该变量建立一个合理的预测模型？

#### 一、多变量探索（初筛候选）

1. **Pairwise 散点图矩阵**（Ch.17 探索性分析）
   - 5 个变量（Attendance + 4 个候选）的 $5 \times 5$ 散点图矩阵
   - → **视觉初筛：Attendance 与 Income_rate 的负线性趋势最清晰；CIF 与 Income_rate 高度共线**

2. **Pearson 相关系数矩阵**（Ch.17 相关分析）
   - 计算 5 个变量之间的 pairwise $r$（$5 \times 5$ 表格）
   - 实际结果：Income_rate 与 Attendance 的 $|r| = 0.756$ 最大，CIF 次之（$|r| = 0.735$）；CIF 与 Income_rate 相关高达 $r = 0.953$
   - → **量化确认：Income_rate 是最强单变量预测因子；CIF 与 Income_rate 高度重叠，不提供独立信息**

3. **选定 Income_rate 作为回归预测变量**
   - 依据：与 Attendance 绝对相关最高
   - → **过渡：从相关分析进入回归建模**

#### 二、线性回归建模（量化关系）

4. **简单线性回归模型**（Ch.18 线性回归）
   - $\text{Attendance} = \beta_0 + \beta_1 \times \text{Income\_rate} + \varepsilon$
   - 实际结果：截距 $\hat{\beta}_0 = 0.889$，斜率 $\hat{\beta}_1 = -0.635$，两者均显著（$p < 0.001$）
   - 斜率 95% CI 完全为负 → 负关系方向确认
   - $R^2 = 0.5796$：Income_rate 解释了约 $58\%$ 的 Attendance 变异
   - → **结论：Income_rate 确实是最强线性预测因子，但 ~42% 的变异仍未解释**

5. **回归散点图 + 拟合线**（Ch.18 可视化）
   - 散点图叠加回归线 + 95% 置信带
   - → **视觉验证：拟合线捕捉了总体负趋势，但高 Income_rate 区域的散点明显更分散——暗示模型在高贫困地区表现较差**

#### 三、残差诊断（检验假设）

6. **残差 vs 拟合值图**（Ch.18 模型诊断）
   - 检验线性性和同方差性假设
   - 实际结果：残差大致围绕零分布，无明显曲线型，线性假设合理；但仍存在少量较大残差
   - → **结论：线性模型总体合理，但部分数据区域的出勤率无法被 Income_rate 单独解释——其他因素可能也在起作用**

### Section 2 小结

> Income_rate 是四个候选变量中与 Attendance 线性关联最强的变量（$r = -0.756$, $R^2 = 0.58$），但模型仍有 ~42% 的变异未解释。残差图显示高 Income_rate 区域（即最贫困区域）的残差偏大。
> 自然追问：**S1 已知 Glasgow 出勤率最低，S2 发现 Income_rate 是最强预测因子。Glasgow 恰好是低收入最严重的区域——那么 Glasgow 的低出勤完全是由低收入造成的吗？** → 引出 Section 3

### S2 → S3 过渡逻辑

> S2 用全部 400 个数据区域拟合了 Attendance ~ Income_rate，得到 $R^2 = 0.58$——Income_rate 是最强单变量预测因子，但仍有 ~42% 的变异未被解释。S2 结尾已指出"additional factors may also influence Attendance"，且残差图中高 Income_rate 区域的散点明显偏大。
>
> 同时 S1 已证明 Glasgow 的出勤率显著低于 Edinburgh。一个自然的追问是：**S2 的全国模型在 Glasgow 是否同样有效？** Glasgow 恰好是 Income_rate 最高的区域（均值 0.23 vs 全国 0.12），如果 Income_rate 完全解释了出勤差异，那么 Glasgow 的低出勤只是"穷"的结果；但如果 Glasgow 的 income–attendance 关系与全国不同，则说明有 Glasgow 特有的因素在起作用。
>
> 这就是 S3 的出发点：**把 S2 的全国视角拆分到区域层面，检验 Income_rate 对 Glasgow 的解释力是否足够。**

## Section 3: A Further Investigation — Glasgow 的低出勤是因为贫困，还是另有原因？

**逻辑线**：S1 发现入学率 Glasgow < Edinburgh；S2 发现 `Income_rate` 是本组所选择的5个变量中的最强预测因子，但 $R^2 = 0.58$ 说明仍有大量未解释变异。Section 3 追问：这个全国模型在 Glasgow 是否同样有效？Glasgow 的低出勤是否完全由收入贫困解释，还是 income–attendance 关系在 Glasgow 存在结构性差异？

### 分析步骤

> **核心问题**：Glasgow 的低出勤是否完全由收入贫困解释？如果不是，还有什么原因？

#### 一、确立 Glasgow 的独特性（验证铺垫）

1. **三区域 boxplot + F-test + t-test** — 快速建立事实（Ch.12–15）
   - 并排 boxplot 直观展示 Glasgow / Edinburgh / Rest of Scotland 的 Attendance 差异
   - Pairwise F-test 确认方差差异 → 选择正确的 t-test 类型
   - Pairwise Welch's t-test：Glasgow 显著低于 Edinburgh ($p = 0.013$) 和 Elsewhere ($p < 0.001$)；但 Edinburgh vs Elsewhere 不显著 ($p = 0.057$)
   - → **结论：Glasgow 是唯一的异常值——Edinburgh 和 Elsewhere 无显著差异，说明低出勤是 Glasgow 特有的问题，而非一般城市效应**

#### 二、证明 Income 不能完全解释 Glasgow（核心论证）

2. **分区域回归 + prediction interval** — 分别拟合 Attendance ~ `Income_rate`，比较三条回归线（Ch.17–18）
   - 如果 Glasgow 的低出勤完全由贫困解释，三个区域的回归斜率和 $R^2$ 应接近
   - 实际结果：Glasgow 的 $R^2$ 更低 / 斜率更平 → Income_rate 对 Glasgow 的解释力不足
   - 在同一个 Income_rate 水平下做 prediction interval：Glasgow 的区间明显更宽，反映更大的未解释变异；且由于 Glasgow 截距较低但斜率更平，各区域的预测值排序随贫困水平变化
   - → **结论：income–attendance 关系在 Glasgow 根本不同——收入对出勤的预测力在 Glasgow 大打折扣，暗示有其他因素在起作用**

#### 三、提取并量化"Glasgow 效应"（从定性到定量）

3. **残差提取 + 单样本 t-test** — 用非 Glasgow 数据建立基准模型，提取 Glasgow 残差（Ch.14, Ch.18）
   - 用 Edinburgh + Rest of Scotland（300 个数据区域）拟合基准回归：$\text{Attendance} = \beta_0 + \beta_1 \times \text{Income\_rate} + \varepsilon$
   - 用该模型预测 Glasgow 的 100 个数据区域的"预期出勤率"（即：如果 Glasgow 和全国一样，在其贫困水平下出勤率应该是多少）
   - 计算残差：$e_i = \text{Actual}_i - \text{Predicted}_i$（每个 Glasgow 数据区域"比预期差了多少"）
   - 对 Glasgow 残差做单样本 t-test：$H_0: \mu_e = 0$（即 Glasgow 没有额外偏差）
   - 若拒绝 $H_0$：Glasgow 在剥离收入影响后存在**系统性偏差**
     - $\bar{e} < 0$：Glasgow 的出勤率比基准预测更低 → 收入之外有因素额外拉低出勤
     - $\bar{e} > 0$：Glasgow 的出勤率比基准预测更高 → 考虑到 Glasgow 极高的贫困水平，national baseline（斜率更陡）预测的更低，Glasgow 实际并没那么差；但 Section 3.2 已证明收入对 Glasgow 的解释力远低于其他地区（$R^2$ 更低），说明 income–attendance 关系在 Glasgow 根本不同
     - **无论正负，偏差显著 ≠ 0 本身就证明了"Glasgow 效应"的存在**
   - → **结论：Glasgow 效应真实可量化，income–attendance 关系在 Glasgow 显著不同于全国基准**

#### 四、追踪根源——是什么在制造"Glasgow 效应"？

4. **残差相关性扫描** — Glasgow 残差 vs 所有其他连续变量的 Pearson 相关系数（Ch.17）
   - 残差 = "剥离收入影响后的纯净出勤表现"。与残差相关最强的变量，就是"Glasgow 效应"的最可能解释
   - 批量计算 Glasgow 残差与 crime_rate, health, overcrowded, Attainment, CIF 等变量的 $r$，按 $|r|$ 从大到小排序
   - 让数据自己说话，客观定位核心变量（假设扫描结果指向 `Variable_X`）
   - → **结论：`Variable_X` 与 Glasgow 效应的关联最强——它是收入之外影响 Glasgow 出勤的首要候选因素**

5. **核心因素深潜** — 残差 vs `Variable_X` 散点图 + 回归线（Ch.17–18）
   - 绘制 Glasgow 残差 vs `Variable_X` 的散点图 + 拟合线，直观展示残差与该变量的关系
   - 报告该回归的 $r$, $R^2$, 斜率 CI → 量化 `Variable_X` 对 Glasgow 效应的解释力
   - **（可选 Bonus）卡方独立性检验**（Ch.19）：将残差三等分为 Negative / Neutral / Positive，`Variable_X` 也三等分，构造 3×3 列联表，$\chi^2$ 检验两者是否独立 → 用不同的统计框架交叉验证
   - → **结论：解决 Glasgow 的教育公平问题不能仅靠减贫，还需要针对 `Variable_X`（如社区安全、公共健康等）进行干预**

### 故事线总结

```
S1 发现 → S2 发现 → S3 追问
Glasgow更差  Income_rate最强  贫困能完全解释Glasgow的差吗？

Step 1: 确立事实 — Glasgow 比全国都差
    ↓
Step 2: 证伪假设 — Income 解释不了 Glasgow（R²低、斜率平、PI更宽）
    ↓
Step 3: 量化效应 — 建基准模型 → 提取残差 → 检验系统性偏差（ē ≠ 0）
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