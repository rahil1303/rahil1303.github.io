---
layout: post
title: Traffic Analysis Attacks on Tor - What I Read and What I Took From It
date: 2026-10-07
description: A summary of my reading on website fingerprinting, flow correlation and video fingerprinting, how raw traffic becomes model input, and why the base rate fallacy matters more than the architecture.
tags: security machine-learning tor privacy
categories: research-notes
---

For an MSc course on security and machine learning, I spent a few days reading about traffic analysis attacks on anonymity networks and wrote a report on them. This post is the short version: the papers I read, how the pieces fit together, and what I would question about my own report now.

### The problem in one paragraph

Encryption hides what you send, not how you send it. Packet sizes, directions and timing stay visible, and they leak a surprising amount. Tor sends traffic through an entry guard, a middle relay and an exit relay, so an attacker can sit in two places. At the client side of the entry guard, they can try to work out which website you are visiting (website fingerprinting). At both ends of the circuit, they can try to link the traffic going in to the traffic coming out (end-to-end flow correlation).

### Papers I read

- **Murdoch and Danezis (2005), Low-Cost Traffic Analysis of Tor.** An early attack that used timing statistics and hand-chosen features. It showed that Tor's design did not hide timing patterns.
- **Rimmer et al. (2018), Automated Website Fingerprinting through Deep Learning.** Replaced hand-crafted features with learned ones. They compared deep models (a stacked denoising autoencoder, a CNN and an LSTM) against classical baselines like CUMUL, k-NN and k-FP. In my notes, CNN and autoencoder models did best in the closed world, while the LSTM held up better over time as traffic drifted.
- **Nasr et al. (2018), DeepCorr.** A deep network that decides whether two flows, one at each end, belong together. I also read a follow-up study from KU Leuven and Radboud that rebuilt DeepCorr's evaluation with a more realistic data collection setup (many proxies instead of one), a weighted loss for the heavy class imbalance, and average precision as a metric.
- **Rimmer et al. (2022), Trace Oddity.** A methodology paper on how data-driven traffic analysis on Tor should be collected and evaluated.
- **Hasselquist et al. (2024), Raising the Bar.** Video fingerprinting: identify which video is being streamed from encrypted traffic. It adapts website fingerprinting models (Deep Fingerprinting, Tik-Tok), introduces a large dataset, and proposes a defence called Scrambler that tries to hide the periodic shape of video traffic without hurting quality of experience.

### From raw traffic to model input

A traffic trace is just a sequence of packets, each with a size, a direction and a time:

T = ((s1, d1, t1), (s2, d2, t2), ..., (sn, dn, tn))

Models usually use direction as +1 for outgoing and -1 for incoming, pad or cut the sequence to a fixed length, and feed it to a network. A CNN slides filters over the sequence and picks up local patterns such as bursts, which are characteristic of how a page loads. An LSTM carries a hidden state along the sequence and can pick up longer-range timing structure. The point that stuck with me is that the model is not doing anything exotic. It is learning which burst patterns identify a site, and that replaces the hand-picked statistics of the early papers.

### The base rate fallacy, worked properly

This was the part I found most useful. In flow correlation, you compare many flows against many flows. Suppose 25,000 flows enter and 25,000 leave. Only 25,000 pairs are true matches, but there are 624,975,000 wrong pairs.

- With a true positive rate of 80%, you find 20,000 true matches.
- At a false positive rate of 0.1%, you also raise about 624,975 false alarms, so only about 3% of your alerts are right.
- At a false positive rate of 0.001%, you raise about 6,250 false alarms, and about 76% of your alerts are right.

(In my original report I attached the 624,975 figure to the 0.001% rate, which was wrong. It corresponds to 0.1%.) The lesson is that a rate that sounds tiny can still swamp you when the negatives vastly outnumber the positives. This is why precision-recall metrics such as average precision are a more honest way to report these attacks than a headline true positive rate.

### What I took away

1. **Evaluation matters more than architecture.** Closed-world numbers are flattering. Concept drift, open-world settings, base rates and realistic data collection change the picture a lot.
2. **Defences live in trade-offs.** Scrambler is interesting because it treats quality of experience as a constraint, not an afterthought.
3. **The same tools cut both ways.** Traffic analysis helps with anomaly detection and network diagnostics, and it also powers censorship and surveillance.

### What I would question now

My report compared the three attack families on a radar chart (realism, scalability and so on). Those scores were my own judgement, not measurements, and I should have said so. A few of my real-world examples also came from blog posts instead of primary sources. If I rewrote it, I would replace those with papers or drop them.

### References

- Murdoch and Danezis. Low-Cost Traffic Analysis of Tor. IEEE S&P, 2005.
- Rimmer, Preuveneers, Juarez, Van Goethem, Joosen. Automated Website Fingerprinting through Deep Learning. NDSS, 2018.
- Nasr, Bahramali, Houmansadr. DeepCorr: Strong Flow Correlation Attacks on Tor Using Deep Learning. CCS, 2018.
- Rimmer, Schnitzler, Van Goethem, Romero, Joosen, Preuveneers. Trace Oddity: Methodologies for Data-Driven Traffic Analysis on Tor. PoPETs, 2022.
- Hasselquist et al. Raising the Bar: Improved Fingerprinting Attacks and Defenses for Video Streaming Traffic. PoPETs, 2024.
