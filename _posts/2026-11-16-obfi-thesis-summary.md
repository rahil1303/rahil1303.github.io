---
layout: post
title: MSc Thesis Summary - Oblivious Bloom Filter Insertion
date: 2026-11-16
description: A summary of my MSc thesis on implementing and empirically comparing OBFI and WS12, two protocols for inserting into an encrypted Bloom filter without leaking access patterns.
tags: cryptography oblivious-protocols privacy distributed-systems
categories: research-notes
---

**Oblivious Bloom Filter Insertion: Implementation, Empirical Analysis, and Crossover Characterisation of Competing Protocols**

Rahil S | MSc Computer Science, Vrije Universiteit Amsterdam & University of Amsterdam
CWI Amsterdam | Supervisor: Marten van Dijk | 2026
[doi.org/10.5281/zenodo.22019435](https://doi.org/10.5281/zenodo.22019435) | [Summary (PDF)](/assets/pdf/OBFI_Thesis_Summary.pdf)

### The Research Question

The central question of this thesis was whether the theoretical efficiency of oblivious Bloom filter insertion protocols could be realised in practice, and under what conditions one construction would outperform another. More specifically, could restricting oblivious computation to the elements being inserted, rather than a structure proportional to the entire filter capacity, preserve the same security requirements while substantially reducing computational cost? Answering this required moving beyond asymptotic complexity analysis to understand how these cryptographic constructions behave in actual distributed systems. The study therefore brought together theoretical computer science, applied cryptography, distributed systems engineering, and empirical performance evaluation.

### Implementation and Research Artifact

This research focused on identifying computational bottlenecks in privacy-preserving client-server systems, using a mathematical occupancy bound (Lemma 1) and a novel primitive, Oblivious Bucket Distribution (OBD). As neither OBFI nor WS12 had previously been implemented in a documented working system, both protocols had to be built from scratch, translating their theoretical specifications into executable code through first-principles reasoning. We developed a shared distributed framework in Python using gRPC, encrypted storage, and oblivious sorting to evaluate both protocols. Data Analysis of recorded data was done using R! The implementation was also released as a Python library, *pyobfi*, to support reproducibility and further research.

### The Idea Behind the Research

Over the past two decades, the growing adoption of cloud computing and outsourced storage has changed how organisations manage and process information. While these systems offer scalability and flexibility, they also introduce an important security question: how can sensitive information remain private when it is stored and processed on infrastructure that its owner does not fully control?

Encryption offers one layer of protection by concealing the contents of the data, but it does not necessarily conceal how that data is accessed or modified. Consider, for example, a hospital storing encrypted patient records on an external server. An observer might not be able to read a single medical record, but by examining which records are accessed, how often, and the patterns between successive queries, they could potentially infer sensitive information about groups of patients, such as the prevalence of a particular condition within a community. Such information could then be exploited for profiling, discrimination, or targeted attacks. This is the problem of access-pattern leakage, where the behaviour of a system reveals information that encryption was intended to protect.

This problem is particularly interesting in the context of Bloom filters, which are space-efficient, probabilistic data structures used to test whether an element belongs to a set. When a Bloom filter is stored on an untrusted server, even encrypting its contents may not be enough to preserve privacy. Bloom filters provided a useful starting point for this research because of their compact representation and efficient membership queries, allowing us to investigate the practical costs of protecting access patterns in a well-defined setting. While this thesis focuses specifically on Bloom filter insertion, the broader questions it raises about privacy, correctness, and performance could also be explored in other data structures, such as arrays and linked lists.

### Challenges and Approach

This project was governed by two important conditions. First, Obliviousness: throughout the insertion process, the server must not learn which elements are being inserted or which positions are accessed or modified. It may only learn the size of each insertion batch. Second, bounded client storage: the client has very limited local memory, while the server provides the storage needed to maintain the encrypted data structure. This reflects the practical reality of outsourcing data to external infrastructure. The challenge was therefore to preserve access-pattern privacy without requiring the client to store or process the entire data structure locally.

This problem was previously addressed by Williams and Sion in 2012 through a protocol referred to as WS12, which uses oblivious sorting to conceal the positions being updated. However, an important limitation of this approach is that its sorting operation scales with the capacity of the entire Bloom filter, rather than just the elements being inserted. For example, consider a filter designed to accommodate one million elements, but receiving only a thousand new elements in a particular batch. WS12 must still sort a structure containing records representing segments of the much larger filter, alongside the incoming elements. This becomes particularly problematic in practical systems, where data structures are often provisioned to accommodate large datasets and future growth, while individual updates involve only a small fraction of that capacity. As the filter capacity increases, so does the computational overhead, regardless of how small the insertion batch may be. This raises an interesting question: could we preserve the same privacy guarantees while restricting the expensive oblivious computation to the incoming batch instead?

This question led to the introduction of Oblivious Bloom Filter Insertion (OBFI), a newer protocol built around two novel contributions. The first is a mathematical property that allows us to determine, with high probability, an upper bound on how many elements can occupy a given region of an array. The second is a novel subprotocol called Oblivious Bucket Distribution (OBD), which uses this bound to distribute incoming elements into fixed-size buckets, padding any unused spaces with dummy elements so that every bucket appears identical to the server. Together, these two contributions allow OBFI to organise and obliviously sort only the incoming batch, rather than a structure proportional to the entire filter capacity, while preserving access-pattern privacy and bounded client storage.

### Implementation and Results

The central question of my MSc thesis was whether the architectural difference between WS12's capacity-wide sorting and OBFI's batch-local sorting would translate into measurable performance improvements, at what point one protocol would outperform the other, and under what conditions. To investigate this, I implemented both protocols in Python within a shared client-server framework, using gRPC for communication and cryptographic primitives for encrypted storage and oblivious operations. Translating the mathematical specifications into working systems required developing clear logical execution flows, mapping pseudocode to executable components, and resolving several implementation details that were not fully specified in the original protocols. By standardising the underlying oblivious sorting algorithm and evaluating both approaches under identical infrastructure, I established a controlled experimental environment in which the impact of their architectural differences could be measured independently of implementation choices.

The experiments confirmed that WS12's runtime increased with filter capacity, while OBFI's remained largely independent of it. The crossover occurred when the insertion batch represented approximately 5–25% of the filter's element capacity, with OBFI performing better for smaller batches and WS12 becoming competitive for larger ones. At the widest tested separation, OBFI was **16.1× faster** than WS12. Correctness tests also showed zero false negatives and a false-positive rate below 1%.

### Conclusions and Reflections

Using measurements collected across different protocol phases, we developed an empirical cost model to explain performance differences and estimate when one protocol would outperform the other. More importantly, the research demonstrated that stronger privacy guarantees need not come at the expense of practical performance, provided we reconsider how computation is organised. Together, the experimental data and cost model provide a foundation for evaluating future designs and exploring similar approaches in other privacy-preserving data structures.

### Links

- **Full thesis**: [Zenodo](https://doi.org/10.5281/zenodo.22019435)
- **Summary**: [PDF](/assets/pdf/OBFI_Thesis_Summary.pdf)
- **Code**: [obfi-implementation-and-empirical-analysis](https://github.com/rahil1303/obfi-implementation-and-empirical-analysis)
- **Library**: [pyobfi on PyPI](https://pypi.org/project/pyobfi/)
- **Project page**: [OBFI](/projects/obfi/)
