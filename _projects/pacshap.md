---
layout: page
title: "Explainable AI Framework for Privacy-Aware Feature Attribution"
description: Privacy-aware feature selection combining SHAP values and knowledge graphs, aligned with GDPR data minimization
importance: 3
category: research
---

**UvA Amsterdam, 2024**

## Overview

An automated framework for **privacy-aware feature selection** aligned with GDPR data minimization principles. The framework combines **SHAP values and knowledge graphs** to provide interpretable, context-aware feature attribution — moving beyond standard feature importance rankings to explanations grounded in semantic relationships between features.

## Research Question

Can combining SHAP-based attribution with knowledge graph structure produce feature selection decisions that are simultaneously more interpretable and more privacy-compliant than standard approaches?

## System Design

- Combined SHAP values with knowledge graph APIs to produce context-aware attribution
- Implemented **PAC-privacy and autoencoder-based encoding** mechanisms for secure data handling
- Built an automated end-to-end pipeline supporting heterogeneous datasets (numerical and categorical features)
- Applied transformer-based NLP techniques for feature importance evaluation and entity linking

## Key Contributions

The framework produces feature selections that are auditable at the relationship level — explaining not just which features matter, but why, in terms of their semantic connections. This supports responsible AI practices where feature selection decisions need to be justified to non-technical stakeholders.

## Links

- **GitHub**: [PACSHAP-FeatureGraph](https://github.com/rahil1303/PACSHAP-FeatureGraph)
- **arXiv**: upcoming
