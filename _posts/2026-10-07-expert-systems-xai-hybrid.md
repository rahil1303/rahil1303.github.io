---
layout: post
title: Expert Systems Meet Explainable AI - A Hybrid Detector and a Rough Energy Experiment
date: 2026-10-07
description: A summary of my reading on expert systems and explainable AI, the hybrid intrusion detection design I proposed, a small energy comparison, and what I would tighten.
tags: security machine-learning explainability energy
categories: research-notes
---

For an MSc course on security and machine learning, I wrote about expert systems, an old and somewhat unfashionable corner of AI, and asked whether they still have a role next to modern deep learning in security. This post summarises what I read, the hybrid design I proposed, and a small experiment on energy use, along with what the experiment can and cannot say.

### Why look at expert systems at all

A classic expert system has a knowledge base of rules written by domain experts, an inference engine that applies them, and an interface for users. Its strength is that every decision can be traced to a rule. Its weakness is that someone has to write and maintain the rules, so it copes badly with anything new. Deep learning is the reverse: it adapts, but it is hard to explain. In security, where an analyst has to justify why an alert fired, that trade-off matters.

### What I read

- **Lucas and van der Gaag, *Principles of Expert Systems*,** for production rules, semantic nets and frames, forward versus backward inference, and how uncertainty is handled with certainty factors and Bayesian updating.
- **Been Kim's work on interpretability,** especially TCAV (Testing with Concept Activation Vectors). The idea is to explain a model in terms of human concepts instead of pixels or features. You collect examples of a concept, train a linear classifier in the model's internal activation space to find the direction that represents it (the concept activation vector), and then measure how often moving along that direction increases the prediction for a class.
- **The Amazon recruiting tool case.** Development started around 2014, and it was reported in 2018 that the tool had learned to penalise resumes associated with women, after which it was scrapped. It is a clean example of a bias that stayed hidden because nobody could see inside the model.

### The hybrid design

The idea was a cascade for something like network intrusion detection:

1. Traffic first meets an expert-system layer of explicit rules for known threats. A rule hit triggers an immediate, explainable response.
2. If the rules cannot decide, a classifier routes the case to a machine learning anomaly detector, ideally one with explanations attached.
3. A human analyst reviews flagged cases, and their feedback is used to refine the rules.
4. A final layer combines everything into a response.

I also wrote the decision as a weighted sum, D(x) = α ES(x) + β ML(x), and added a Bayesian posterior over threats and certainty factors for rule confidence. Looking back, the weighted sum does not match the cascade. A weighted sum needs both scores for every input, so it would run the expensive model every time, which defeats the energy argument. The saving only exists if rules can short-circuit the model. The cascade is the real design, and the sum is better described as a way to combine scores for the cases that do reach the model.

### The experiment

I compared a deep learning detector with the hybrid on an intrusion detection task. The energy numbers were estimated in abstract CPU units, not measured in joules.

- Deep learning only: about 493 units, accuracy about 0.95.
- Hybrid: about 225 units, accuracy about one point lower.

That is roughly a 54% reduction in estimated energy for about a one-point drop in accuracy. I also defined a power efficiency ratio, accuracy divided by energy, which came out around 0.0019 for the deep model and 0.0042 for the hybrid.

### What the experiment does and does not show

- **The units are estimates.** For a real claim about energy, I would use measured power on real hardware.
- **The saving depends on rule coverage.** If rules resolve most traffic, you save a lot. If attackers deliberately avoid known patterns, you save much less. The result says nothing about that.
- **My report quoted different savings in different places.** Elsewhere it says 35% and 32%, which do not match the plotted figures. The figure-based number (about 54%) is the one I trust.
- **I also claimed a 12% reduction in false positives.** No plot in the report backs that up, so I would leave it out.
- **Rules tuned on the same data flatter the hybrid.** A fair comparison needs a held-out test set that the rules never saw.

### What I understood

The most useful idea for me was not the energy result but the framing. Explainability and efficiency are often treated as costs. A cascade turns the explainable layer into the cheap first line of defence, and the opaque model is kept for the hard cases. Whether it works depends on how well the rules cover real traffic, which is an empirical question my small experiment only touches.

### References

- Lucas and van der Gaag. Principles of Expert Systems. Addison-Wesley, 1991.
- Kim, Wattenberg, Gilmer, Cai, Wexler, Viegas, Sayres. Interpretability Beyond Feature Attribution: Quantitative Testing with Concept Activation Vectors (TCAV). ICML, 2018.
- Dastin. Amazon scraps secret AI recruiting tool that showed bias against women. Reuters, 2018.
