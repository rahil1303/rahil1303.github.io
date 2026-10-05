---
layout: post
title: Choosing the Right Statistical Test - A Core Decision Tree
date: 2026-10-05
description: A working decision tree for picking a hypothesis test, from the type of outcome variable to normality checks, p-values, and a lookup table of tests for numeric and categorical data.
tags: statistics hypothesis-testing methods
categories: research-notes
---

These are my handwritten notes on how to pick a statistical test, cleaned up into one place. The idea is to answer three questions in order, check the assumptions the test needs, and only then look up the test itself.

### Step 1: What is your outcome variable?

- **Continuous (numeric):** parametric or non-parametric tests on means or medians.
- **Categorical or binary:** tests on proportions or counts.
- **Time to event:** survival analysis.

### Step 2: How many groups are you comparing?

- One group against a known value
- Two groups
- Three or more groups

This is also what decides whether parametric or non-parametric tests are appropriate, which depends on the normality check below.

### Step 3: Are your groups independent or paired?

Groups are either independent (different subjects in each) or paired/related (the same subjects measured twice, or matched subjects). A single sample compared against a known mean uses a one-sample t-test, provided the data are roughly normal or the sample is large.

### Checking normality

A normally distributed data set follows a symmetrical, bell-shaped curve, so its mean, median and mode are the same number. Three common formal tests check this, and all three share the same null hypothesis:

- Kolmogorov-Smirnov test
- Shapiro-Wilk test
- Anderson-Darling test

**H0:** the data are normally distributed.

What matters is whether the resulting p-value is smaller or larger than 0.05:

- **p < 0.05:** reject H0, so normality is not assumed.
- **p ≥ 0.05:** normality is assumed.

One caution: the result depends heavily on how much data you have. More data can push the p-value down, while less data tends to give a higher p-value, so a normality test on a tiny sample says little. This is why the graphical checks matter too: a histogram with a normal curve overlaid, and a quantile-quantile (Q-Q) plot.

### What is a p-value?

The p-value is the probability of observing the result you got, or an even more extreme one, if the null hypothesis is true. If that probability is very small, it is reasonable to ask whether the assumption about the population holds at all.

For example, if p = 0.03, then assuming there is no true difference, a difference of the size you observed (say, 300 euros) or larger would show up in about 3% of random samples.

### Significance level

The significance level, written α, is fixed before running the test. If the calculated p-value falls below α, the null hypothesis is rejected, and otherwise it is not. As a rule, α = 5% is used. A common way to read the result:

- p ≤ 0.01: highly significant
- p ≤ 0.05: significant
- p > 0.05: not significant

### Numeric outcomes: which test?

| Scenario | Test | Assumptions |
| --- | --- | --- |
| 2 independent groups | Independent (unpaired) t-test | Normal, equal variance |
| 2 paired/related samples | Paired t-test | Differences normally distributed |
| 3+ independent groups | One-way ANOVA | Normality, homogeneity of variance |
| 3+ groups, repeated measures | Repeated-measures ANOVA | Sphericity |
| 2+ factors affecting the outcome | Two-way (factorial) ANOVA | Normality, homogeneity of variance |
| Non-normal, 2 independent groups | Mann-Whitney U | No assumption on distribution shape |
| Non-normal, 2 paired groups | Wilcoxon signed-rank test | None on distribution shape |
| Non-normal, 3+ independent groups | Kruskal-Wallis | None on distribution shape |
| Non-normal, 3+ paired groups | Friedman test | None on distribution shape |

### Categorical outcomes: which test?

| Scenario | Test |
| --- | --- |
| Compare a proportion to a known value | One-sample z-test for a proportion |
| Compare 2 independent proportions | Two-proportion z-test, or chi-square test of independence |
| Association between 2 categorical variables | Chi-square test of independence |
| Small sample or sparse cells (expected count < 5) | Fisher's exact test |
| Paired categorical data (before/after, same subjects) | McNemar's test |
| 3+ categorical outcomes across groups | Chi-square test |
| Goodness of fit to a distribution | Chi-square goodness of fit |
