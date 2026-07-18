---
layout: page
title: FraudGraph
description: Knowledge-graph-native fraud detection with agentic investigation and graph-aware SHAP explainability
img: assets/img/projects/fraudgraph.png
importance: 1
category: research
related_publications: sharma2024pacshap
---

**FraudGraph** is a fraud detection system built around a transaction knowledge graph,
combining graph neural network detection with an agentic investigation layer that
autonomously traces suspicious patterns across entity relationships.

## Research Question

Can a knowledge-graph-native architecture — where accounts, transactions, merchants,
and devices are first-class graph citizens — improve both detection accuracy *and*
explanation quality over flat, tabular-feature-based fraud models?

## System Architecture

**Data Engineering Pipeline**
Raw transaction streams are ingested and normalised into a property graph (Neo4j).
Entities (accounts, merchants, devices, IPs) become nodes; transaction events become
typed edges with temporal and monetary attributes.

**Detection Layer**
- Graph neural network (GraphSAGE) trained on k-hop subgraph features centred on each transaction node
- Ensemble with gradient-boosted model (XGBoost) on hand-engineered node-level features
- Threshold calibration via precision-recall curves to control false positive rate

**Explainability (PACSHAP)**
Graph-aware SHAP attribution, extending ideas from the PACSHAP framework: SHAP values
are propagated across edges and weighted by relationship type, so explanations surface
which *entity relationships* contributed to a fraud flag — not just which raw features.

**Agentic Investigation Layer**
A tool-equipped LLM agent receives a flagged transaction and autonomously:
1. Queries the knowledge graph for related entities and historical patterns
2. Retrieves similar past fraud cases (vector-based information retrieval)
3. Synthesises a structured investigation report with an evidence chain

## Key Design Choices

- Graph-first schema: all fraud signals are relational by design
- Separation of detection (fast, batch) from investigation (agentic, on-demand)
- Explanation at the relationship level, not the feature level

## Status

Active development &mdash; [GitHub →](https://github.com/rahil1303)
