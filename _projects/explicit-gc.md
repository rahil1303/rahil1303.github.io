---
layout: page
title: "The Marginal Value of Explicit Garbage Collection: Cadence, Reclamation, and Cost Across Java Workloads"
description: Empirical study of when System.gc() requests pay off in reclamation, runtime, and energy across Java collectors and workloads
importance: 2.5
category: research
---

**VU Amsterdam, 2026** · Under review
Supervised by Dr. I. Malavolta (VU Amsterdam)

## Overview

Java performance guidance generally discourages calling `System.gc()`, since an unnecessary collection can add substantial overhead. Yet explicit collection is still used in application code, JDK components, benchmarking, and operational tooling, and its actual effects on modern collectors remain poorly understood. This project treats explicit-GC requests as an experimental treatment and separates four things that are usually conflated: **requests issued, collection realised, additional memory reclaimed, and runtime and energy cost** relative to natural GC.

## Research Questions

- Does a higher cadence of explicit GC requests translate into additional realised collection activity?
- When it does, do runtime and energy improve, or do requests merely add collection overhead?

## Methodology

- Studied **12 Java workloads** (DaCapo Chopin benchmarks, SableCC, Spring PetClinic REST, and a Train Ticket microservice) across **5 OpenJDK 21 collectors**: Serial, Parallel, G1, Shenandoah, and ZGC
- Injected requests at four cadences (none, low, medium, high) through DaCapo's callback API, always layered on natural GC and with a fixed amount of useful work per run
- Collected **2,382 valid runs** on a dedicated Green Lab server, measuring processor-package energy with **EnergiBridge** and attributing GC events as natural or explicit from JVM logs and **JFR** recordings
- Analysed each workload separately using blocked models with **Holm-corrected** comparisons across cadence levels

## Key Findings

The standard advice to avoid `System.gc()` holds for some collectors and fails for others. Raising the request cadence made G1 and Shenandoah steadily slower and more energy-hungry, yet under ZGC the share of runs where both runtime and energy improved climbed from 24% at low cadence to 59% at high cadence. Since ZGC and Shenandoah are both concurrent collectors, this split cannot be explained by the usual concurrent versus stop-the-world distinction: what matters is how each collector fits an explicit request into its own scheduling. A second lesson runs underneath: more reclaimed memory did not mean better execution, because explicit collection often just did work that natural GC would have done anyway.

## Links

- **GitHub**: [gc-explicit-cadence-replication](https://github.com/rahil1303/gc-explicit-cadence-replication)
- **arXiv**: upcoming
