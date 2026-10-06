---
layout: post
title: Choosing a Statistical Test in Practice - Worked Examples
date: 2026-10-06
description: A companion to the decision tree post. Seven concrete situations, each walked through the three steps and paired with the Python call that runs the test.
tags: statistics hypothesis-testing python
categories: research-notes
---

This is a companion to my [decision tree for choosing a statistical test](/blog/2026/statistical-test-decision-tree/). That post is the lookup table. This one applies it to concrete situations, each with the reasoning (outcome type, number of groups, independent or paired, normality) and the Python call that runs the test.

All snippets use `scipy.stats`, plus `statsmodels` where scipy has no equivalent. The variable names are placeholders for your own arrays, so no results are shown.

```python
import numpy as np
from scipy import stats
```

### A habit before every test: check normality

For any numeric comparison, run a normality check first, and look at a histogram or Q-Q plot alongside it, since the formal tests depend heavily on sample size. For a paired design, check the differences, not the two raw columns.

```python
stat, p = stats.shapiro(x)          # H0: x is normally distributed
# p < 0.05  -> normality not assumed, prefer the non-parametric route
# p >= 0.05 -> normality assumed
```

### 1. One sample against a known value

**Situation:** the mean response time of a service should be 200 ms. Is it?
**Reasoning:** numeric outcome, one group against a known value, so a one-sample t-test, provided the data are roughly normal or the sample is large.

```python
stats.ttest_1samp(latency_ms, popmean=200)
```

### 2. Two independent groups

**Situation:** the runtime of the same job under configuration A and configuration B, on different runs.
**Reasoning:** numeric outcome, two groups, independent. If both groups look normal, use the independent t-test. If not, use Mann-Whitney U.

```python
stats.ttest_ind(runtime_a, runtime_b, equal_var=False)   # Welch version, safe default
stats.mannwhitneyu(runtime_a, runtime_b, alternative="two-sided")
```

Setting `equal_var=False` gives Welch's t-test, which does not require the two variances to be equal. It is a sensible default when you are not sure.

### 3. Two paired samples

**Situation:** the same 20 tasks, timed before and after an optimisation.
**Reasoning:** numeric outcome, two groups, but paired because each task appears in both. Check that the differences are normal, then use the paired t-test, or Wilcoxon signed-rank if they are not.

```python
diff = before - after
stats.shapiro(diff)
stats.ttest_rel(before, after)
stats.wilcoxon(before, after)
```

### 4. Three or more independent groups

**Situation:** three algorithms, each run on separate inputs.
**Reasoning:** numeric outcome, three groups, independent. One-way ANOVA if the assumptions hold, Kruskal-Wallis if not.

```python
stats.f_oneway(alg_a, alg_b, alg_c)
stats.kruskal(alg_a, alg_b, alg_c)
```

Both of these only tell you that at least one group differs. If the result is significant, follow up with a post-hoc test (for example Tukey's HSD after ANOVA, or Dunn's test after Kruskal-Wallis) to find out which groups differ.

### 5. Three or more paired groups (repeated measures)

**Situation:** the same 15 subjects measured under three conditions.
**Reasoning:** numeric outcome, three groups, paired. Repeated-measures ANOVA (which assumes sphericity), or the Friedman test when normality fails.

```python
stats.friedmanchisquare(cond_1, cond_2, cond_3)   # each array: same subjects, same order

from statsmodels.stats.anova import AnovaRM
AnovaRM(df, depvar="score", subject="subject", within=["condition"]).fit()
```

`AnovaRM` expects a long-format data frame with one row per subject per condition.

### 6. Categorical outcomes: independent groups

**Situation:** does a pass/fail rate differ between two groups?
**Reasoning:** categorical outcome, two independent groups, so a chi-square test of independence (or a two-proportion z-test). If any expected cell count is below 5, use Fisher's exact test instead.

```python
table = [[pass_a, fail_a],
         [pass_b, fail_b]]

chi2, p, dof, expected = stats.chi2_contingency(table)
if expected.min() < 5:
    odds_ratio, p = stats.fisher_exact(table)
```

For a single proportion against a known value, use a one-sample z-test:

```python
from statsmodels.stats.proportion import proportions_ztest
proportions_ztest(count=successes, nobs=n, value=0.5)
```

### 7. Categorical outcomes: paired, and goodness of fit

**Paired categorical (before and after on the same subjects):** McNemar's test, which only uses the subjects whose answer changed.

```python
from statsmodels.stats.contingency_tables import mcnemar
table = [[both_yes, yes_then_no],
         [no_then_yes, both_no]]
mcnemar(table, exact=True)
```

**Goodness of fit to a distribution:** does the observed distribution of counts match what you expected?

```python
stats.chisquare(f_obs=observed_counts, f_exp=expected_counts)
```

### One more thing to report

A p-value says whether an effect is detectable, not how large it is. Alongside any of these tests, report an effect size and a confidence interval, such as the difference in means, so that a tiny effect in a large sample is not mistaken for an important one.
