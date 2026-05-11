# MATH1108 Group 39: An Analysis of School Attendance in Scotland

> [中文版见下方 / Chinese version below](#中文版)

## Overview

This repository contains the coursework for **MATH1108 Probability & Statistics**, completed by **Group 39**. The report investigates the distribution of school attendance rates across 400 Scottish data zones and their relationship with socio-economic factors, using data from the 2020 Scottish Index of Multiple Deprivation (SIMD).

## Authors

Yuchen Xu, Yu Li, Lingke Huang, Sirui Lu

## Report Structure

The report (`Group-39.Rmd` → `Group-39.pdf`) consists of three main analytical sections:

### Section 1: Glasgow vs Edinburgh Comparison

- Side-by-side boxplots and histograms for exploratory visualisation
- Sample means, variances, and 95% confidence intervals
- **Welch's t-test** for difference in means → Glasgow significantly lower (*p* = 0.013)
- **F-test** for equality of variances → no significant difference (*p* = 0.133)

### Section 2: Attendance vs Socio-economic Variables

- Pairwise scatterplot matrix and Pearson correlation analysis across four candidate variables (Attainment, Crime_rate, CIF, Income_rate)
- **Income_rate** identified as the strongest predictor (*r* = −0.756)
- Simple linear regression: *R*² = 0.58, slope = −0.635
- Residual diagnostics to verify model assumptions

### Section 3: The "Glasgow Effect"

- Three-region comparison (Glasgow / Edinburgh / Elsewhere) with pairwise F-tests and t-tests
- Region-specific regressions reveal Glasgow has a flatter slope and lower *R*²
- Baseline model trained on non-Glasgow data → Glasgow residuals significantly ≠ 0
- Residual correlation scan identifies **Employment_rate** as the leading candidate variable
- Cross-validated with a χ² test of independence

## Statistical Methods

| Method | Chapter | Sections |
|--------|---------|----------|
| Confidence intervals (t, χ²) | Ch.12 | S1, S3 |
| Hypothesis testing framework | Ch.13 | S1, S3 |
| Comparing means (t-test) | Ch.14 | S1, S3 |
| Comparing variances (F-test) | Ch.15 | S1, S3 |
| Pearson correlation | Ch.17 | S2, S3 |
| Simple linear regression & residual analysis | Ch.18 | S2, S3 |
| Chi-squared test of independence | Ch.19 | S3 |

## Repository Structure

```
.
├── Group-39.Rmd                    # Main report (R Markdown source)
├── Group-39.pdf                    # Compiled report
├── MATH1108_Statistical_Report.pdf # Course guidelines
├── Report 思路整理.md               # Analysis planning notes (Chinese)
├── scottishData.csv                # Dataset (400 data zones, 34 variables)
├── section_1/                      # Section 1 draft & output
├── section_2/                      # Section 2 draft & output
├── section_3/                      # Section 3 draft & output
├── workshops/                      # Workshop exercise files
└── lectures/                       # Lecture materials
```

## How to Reproduce

1. Ensure **R** and the following packages are installed:
   - `ggplot2`, `dplyr`, `broom`, `knitr`, `bookdown`
2. Open `Group-39.Rmd` in RStudio.
3. Click **Knit** (or run `rmarkdown::render("Group-39.Rmd")`) to generate the PDF report.

## Key Findings

1. **Glasgow's mean attendance is significantly lower** than Edinburgh's, but variance does not differ significantly.
2. **Income_rate is the strongest single predictor** of attendance (*R*² = 0.58), but ~42% of variation remains unexplained.
3. **The "Glasgow effect" is real but nuanced** — given its extreme income deprivation, Glasgow's attendance is actually *higher* than the non-Glasgow baseline predicts, reflecting a structurally different income–attendance relationship.
4. **Employment_rate** is most strongly associated with the Glasgow effect, suggesting interventions beyond poverty reduction are needed.

## License

This project is for academic purposes as part of the MATH1108 module at the University of Glasgow.

---

<a id="中文版"></a>

# 中文版：MATH1108 第39组 — 苏格兰学校出勤率分析

## 概述

本仓库为 **MATH1108 概率与统计** 课程的小组作业（第 39 组）。报告基于 2020 年苏格兰多重贫困指数（SIMD）数据，分析了苏格兰 400 个数据区域的学校出勤率分布及其与社会经济因素之间的关系。

## 作者

Yuchen Xu, Yu Li, Lingke Huang, Sirui Lu

## 报告结构

报告（`Group-39.Rmd` → `Group-39.pdf`）包含三个主要分析部分：

### 第一部分：Glasgow 与 Edinburgh 的出勤率比较

- 使用并排箱线图和直方图进行探索性可视化
- 计算样本均值、方差及 95% 置信区间
- **Welch's t 检验**：Glasgow 的平均出勤率显著低于 Edinburgh（*p* = 0.013）
- **F 检验**：两市方差无显著差异（*p* = 0.133）

### 第二部分：出勤率与社会经济变量的关系

- 对四个候选变量（Attainment、Crime_rate、CIF、Income_rate）进行散点图矩阵和 Pearson 相关分析
- **Income_rate**（收入贫困率）被确定为最强线性预测因子（*r* = −0.756）
- 简单线性回归模型：*R*² = 0.58，斜率 = −0.635
- 残差诊断验证模型假设

### 第三部分："Glasgow 效应"探究

- 三区域比较（Glasgow / Edinburgh / 其他地区），配合成对 F 检验和 t 检验
- 分区域回归显示 Glasgow 斜率更平、*R*² 更低
- 基于非 Glasgow 数据训练基准模型 → Glasgow 残差显著 ≠ 0
- 残差相关性扫描确定 **Employment_rate**（就业贫困率）为首要候选解释变量
- 使用 χ² 独立性检验进行交叉验证

## 统计方法

| 方法 | 对应章节 | 使用部分 |
|------|---------|---------|
| 置信区间（t 分布、χ² 分布） | Ch.12 | S1, S3 |
| 假设检验框架 | Ch.13 | S1, S3 |
| 均值比较（t 检验） | Ch.14 | S1, S3 |
| 方差比较（F 检验） | Ch.15 | S1, S3 |
| Pearson 相关系数 | Ch.17 | S2, S3 |
| 简单线性回归与残差分析 | Ch.18 | S2, S3 |
| 卡方独立性检验 | Ch.19 | S3 |

## 仓库结构

```
.
├── Group-39.Rmd                    # 主报告（R Markdown 源文件）
├── Group-39.pdf                    # 编译后的报告
├── MATH1108_Statistical_Report.pdf # 课程指南
├── Report 思路整理.md               # 分析思路笔记
├── scottishData.csv                # 数据集（400 个数据区域，34 个变量）
├── section_1/                      # 第一部分草稿与输出
├── section_2/                      # 第二部分草稿与输出
├── section_3/                      # 第三部分草稿与输出
├── workshops/                      # 工作坊练习文件
└── lectures/                       # 课程讲义
```

## 如何复现

1. 确保已安装 **R** 及以下 R 包：
   - `ggplot2`、`dplyr`、`broom`、`knitr`、`bookdown`
2. 在 RStudio 中打开 `Group-39.Rmd`。
3. 点击 **Knit**（或运行 `rmarkdown::render("Group-39.Rmd")`）即可生成 PDF 报告。

## 主要发现

1. **Glasgow 的平均出勤率显著低于 Edinburgh**，但方差无显著差异。
2. **Income_rate 是最强的单变量预测因子**（*R*² = 0.58），但仍有约 42% 的变异未被解释。
3. **"Glasgow 效应"真实存在但含义微妙** —— 考虑到其极端的收入贫困水平，Glasgow 的出勤率实际上高于非 Glasgow 基准模型的预测，反映了一种结构性不同的收入-出勤关系。
4. **Employment_rate** 与 Glasgow 效应关联最强，表明解决 Glasgow 的出勤差距需要超越单纯减贫的干预措施。

## 许可

本项目仅用于 Glasgow 大学 MATH1108 课程的学术用途。
