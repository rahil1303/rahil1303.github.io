---
layout: page
title: "Auditable Financial Fraud Detection: Graph Features & Agentic Investigation"
description: End-to-end fraud detection pipeline with graph-based features, AI-assisted investigation, and human-in-the-loop governance
importance: 5
category: research
---

**UvA Amsterdam, 2026**

## Overview

An end-to-end **fraud detection pipeline** built on 6.3M+ financial transactions, designed around two principles: detections must be explainable, and the system must know when to defer to human judgement. The project combines graph-based feature engineering, agentic investigation, and a governance framework that routes decisions based on model confidence.

## Research Questions

- Can graph-based features — derived from the relational structure of transaction networks — improve detection on difficult, low-confidence cases where tabular models struggle?
- How should a fraud detection system handle uncertainty: when should it resolve a case automatically, and when should it escalate?

## System Design

**Detection Layer**
- Engineered graph-based and anomaly-detection features from transaction network structure
- Validated the graph-based approach through a controlled **fraud-ring experiment** isolating its marginal contribution
- Calibrated thresholds to balance detection rate against manual review volume

**Agentic Investigation Layer**
- Built an AI-assisted investigation workflow combining model explanations, transaction context, and similar historical cases into structured case rationales
- Designed to reduce analyst effort on clear cases while surfacing the right evidence for ambiguous ones

**Governance Framework**
- Confidence-based escalation policy: clear cases resolved automatically, conflicting decisions routed to human review
- Full audit trail maintained across detection, investigation, and resolution stages

## Links

- **GitHub**: [auditable-fraud-investigation](https://github.com/rahil1303/auditable-fraud-investigation)
- **arXiv**: [2607.19266](https://arxiv.org/abs/2607.19266)
