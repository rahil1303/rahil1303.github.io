---
layout: page
title: "Energy–Performance Trade-offs of Java Garbage Collection Strategies"
description: Quantitative empirical study comparing GC strategies under controlled workloads using statistical analysis
importance: 2
category: research
---

**VU Amsterdam, 2025**

## Overview

A quantitative empirical study comparing the **Serial, Parallel, and G1 garbage collectors** under controlled Java workloads, investigating the trade-offs between energy consumption, throughput, and latency. Measurements were taken using **EnergiBridge and Intel RAPL** for hardware-level energy profiling.

## Research Question

How do Java garbage collection strategies differ in energy efficiency and runtime performance, and what system-level trade-offs do these differences imply for sustainable software engineering?

## Methodology

- Designed a **Randomized Complete Block Design (RCBD)** experimental setup to control for confounding variables
- Built automated execution and data collection pipelines in Python
- Applied **ANOVA and Tukey HSD** statistical methods to evaluate significance of differences across configurations
- Compared **OpenJDK and Oracle JDK** implementations under varying workload conditions

## Key Findings

Identified measurable crossover points between GC strategies depending on workload characteristics, with quantitative analysis establishing trade-offs between throughput, latency, and energy consumption relevant to sustainable systems design.

## Links

- **GitHub**: [Energy-Efficiency-of-Java-Garbage-Collection-Strategies](https://github.com/rahil1303/Energy-Efficiency-of-Java-Garbage-Collection-Strategies)
- **arXiv**: upcoming
