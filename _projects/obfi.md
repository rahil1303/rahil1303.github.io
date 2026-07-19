---
layout: page
title: "OBFI: Implementation, Empirical Analysis, and Crossover Characterization of Competing Protocols"
description: Distributed implementation and empirical comparison of two cryptographic protocols for privacy-preserving computation
img: assets/img/projects/obfi.png
importance: 1
category: research
---

**MSc Thesis — CWI Amsterdam, 2026**
Supervised by Dr. M. van Dijk (CWI Amsterdam / VU Amsterdam)

## Overview

This project introduces and implements **Oblivious Bloom Filter Insertion (OBFI)**, a cryptographic protocol for constructing encrypted Bloom filters while concealing insertion access patterns from a semi-honest server. The thesis provides the first empirical implementation and systematic comparison of OBFI against an alternative privacy-preserving protocol (WS12) that solves the same problem.

## Research Questions

- How do OBFI and WS12 compare in computational and communication cost across varying batch sizes and filter capacities?
- At what operating parameters does one protocol become preferable to the other?
- What implementation decisions arise when translating formal cryptographic specifications into executable distributed systems?

## System Design

Built a distributed **client-server system in Python** using gRPC for communication, with encrypted channels, persistent storage, and configurable execution workflows. The system translates formal theoretical protocol specifications into executable code, resolving ambiguities between prior research and practical implementation constraints.

## Experimental Evaluation

Designed and ran a systematic sensitivity study across varying batch sizes and Bloom filter capacities. Developed a decision framework for protocol selection based on observed crossover points and deployment parameters. Results establish empirical bounds on where each protocol is preferable.

## Links

- **GitHub**: [obfi-implementation-and-empirical-analysis](https://github.com/rahil1303/obfi-implementation-and-empirical-analysis)
- **Manuscript**: upcoming
- **arXiv**: upcoming
