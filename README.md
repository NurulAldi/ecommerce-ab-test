# A/B Testing E-Commerce: New Page vs Old Page

## Project Overview

This project analyzes an A/B test conducted on an e-commerce website to determine whether a newly designed page (`new_page`) produces a higher conversion rate compared to the existing page (`old_page`). The goal is to make a data-driven decision: **adopt the new page, keep the old page, or extend the experiment**.

---

## Dataset

| File | Description |
|---|---|
| `dataset/raw/ab_test.csv` | Raw A/B test data |
| `dataset/raw/countries_ab.csv` | Country data per user |
| `dataset/cleaned/ab_test_clean.csv` | Cleaned & merged dataset |

### Schema (Cleaned Dataset)

| Column | Type | Description |
|---|---|---|
| `id` | int | Unique user identifier |
| `time` | chr | Time spent on page (MM:SS format) |
| `con_treat` | chr | Group assignment: `control` or `treatment` |
| `page` | chr | Page shown: `old_page` or `new_page` |
| `converted` | int | Whether user converted: `1` = Yes, `0` = No |
| `country` | chr | User's country: `US`, `UK`, `CA` |

- **Rows:** 290,584
- **No missing values** after cleaning & join

---

## Analysis

### 1. Exploratory Data Analysis (EDA)

#### Univariate Analysis
| Variable | Finding |
|---|---|
| `page` | Balanced split — ~50% new_page, ~50% old_page |
| `con_treat` | Balanced split — ~50% control, ~50% treatment |
| `country` | US dominates (~75%), followed by UK and CA |
| `converted` | ~12% overall conversion rate |
| `time_num_min` | Visiting time uniformly distributed across 0–60 minutes |

#### Conversion Rate per Variable
| Variable | Conversion Rate |
|---|---|
| **new_page** | 11.88% |
| **old_page** | 12.04% |
| **US** | 11.9% |
| **UK** | 12.0% |
| **CA** | 11.5% |
| **0–10 min** | 12.0% |
| **10–20 min** | 11.8% |
| **20–30 min** | 12.0% |
| **30–40 min** | 12.1% |
| **40–50 min** | 12.0% |
| **50–60 min** | 11.9% |

> No clear pattern found — conversion rates are nearly identical across all variables.

---

### 2. Multivariate Analysis

Combinations analyzed:
- Conversion Rate by **Country × Page Type**
- Conversion Rate by **Time Spent × Page Type**
- Conversion Rate by **Time Spent × Country**

> **Finding:** Still no meaningful pattern. In many combinations, `old_page` slightly outperforms `new_page`, but differences are within 0.x% range.

---

### 3. Hypothesis Testing — Z-Test for Proportions

#### Hypotheses
$$H_0: \text{conv\_rate}(\text{new\_page}) = \text{conv\_rate}(\text{old\_page})$$
$$H_1: \text{conv\_rate}(\text{new\_page}) \neq \text{conv\_rate}(\text{old\_page})$$

- **Significance level (α):** 0.05
- **Test:** 2-sample Z-test for equality of proportions (`prop.test`, no continuity correction)

#### Results

| Metric | Value |
|---|---|
| Conversion Rate — New Page | **11.88%** |
| Conversion Rate — Old Page | **12.04%** |
| Difference | **-0.16%** |
| X-squared | 1.7185 |
| Degrees of Freedom | 1 |
| **p-value** | **0.1899** |
| 95% Confidence Interval | (-0.0039, 0.0008) |

#### Interpretation

- **p-value = 0.1899 > 0.05** → **Fail to Reject H₀**
- **95% CI contains 0** → No statistically significant difference between the two pages
- The observed 0.16% difference is attributable to **random chance**, not a true effect of the new page design

---

## Conclusion & Recommendation

> **The new page does NOT produce a significantly higher conversion rate than the old page.**

| Option | Decision |
|---|---|
| ✅ **Keep old_page** | Recommended — slightly higher conversion rate (12.04%) and no evidence that new_page is better |
| ❌ **Roll out new_page** | Not recommended — no statistically significant improvement |
| ❌ **Extend the experiment** | Not recommended — with 290,584 users (very large sample), any real effect would have been detected by now |

---

## Project Structure

```
ab_testing_ecommerce/
├── dataset/
│   ├── raw/
│   │   ├── ab_test.csv
│   │   └── countries_ab.csv
│   └── cleaned/
│       └── ab_test_clean.csv
├── scripts/
│   ├── cleaning.ipynb      # Data cleaning & preprocessing
│   └── eda.ipynb           # EDA, visualization & hypothesis testing
├── renv/                   # R environment & package management
└── README.md
```

---

## Tools & Libraries

| Tool | Purpose |
|---|---|
| **R** | Primary analysis language |
| **dplyr** | Data manipulation |
| **ggplot2** | Data visualization |
| **scales** | Percentage formatting on plots |
| **renv** | R environment reproducibility |
