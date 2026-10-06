---
layout: post
title: Thinking About Security for Brain-Computer Interfaces - A Threat Model and a Game
date: 2026-10-07
description: A summary of my paper design exercise on securing brain-computer interfaces connected to large AI systems, with a layered defence and a Stackelberg game model, and an honest note on what it leaves unproven.
tags: security machine-learning privacy game-theory
categories: research-notes
---

For an MSc course on security and machine learning, I was asked to think about security for large-scale AI infrastructure, and I picked a deliberately forward-looking case: a brain-computer interface (BCI) connected to a powerful AI system. This was a design exercise, not an experiment. This post summarises the threat model, the layered defence I proposed, and the game-theoretic model, and says plainly what is speculative.

### Why this case

Neural signals are about as personal as data gets, and a BCI closes the loop: signals go to a model, and the model's output acts on the world or on the person. That makes both privacy and integrity matter. I used "AGI" loosely in the report. Nothing in the design depends on it, and the same reasoning applies to any large model sitting behind a neural interface.

### Threats I collected

- **Privacy:** model inversion and membership inference (reconstructing or detecting training data from a model), eavesdropping on wireless neural signals, and poisoned data in federated training.
- **Integrity:** backdoors planted in the model, adversarial inputs crafted to flip an output, and a small group of malicious clients skewing a federated model.
- **Trust:** models that cannot be explained in real time, and biases inherited from training data.
- **Deployment:** the latency of heavy cryptography, unclear regulation about who owns neural data, and the larger attack surface of many devices and clouds.

### The layered defence

I proposed a pipeline where each stage addresses a different class of threat:

1. **Identity check** with a zero-knowledge proof, so the system can verify the user without learning secrets.
2. **Protect the data** with homomorphic encryption or differential privacy before it reaches the model.
3. **Train and process with federated learning** and adversarial defences.
4. **Detect poisoning or backdoors** and roll back to a safe model state if something looks wrong.
5. **Log verified model updates** on a tamper-evident ledger so they can be audited.

### The game-theoretic model

I modelled the defender and attacker as a Stackelberg game: the defender commits to a security strategy S first, and the attacker observes it and chooses the attack that is best for them. With a cost function for the defender and a payoff for the attacker, the defender wants a strategy S* that minimises their own cost given the attacker's best response a*(S):

S* is in the argmin over S of C_D(S, a*(S)), where a*(S) is in the argmax over attacks a of U_A(a, S).

Since the defender does not know which type of attacker they face (a data poisoner, a backdoor planter, a model inverter), I extended it to a Bayesian game where costs are averaged over a probability distribution of attacker types, which the defender can update as evidence arrives. (In my original report the equilibrium expression was written loosely and did not define the attacker's best response. The version above is what it should have said.)

### What I understood

- Defences do not stack for free. Each layer adds latency, cost and new things to trust.
- Game theory is useful as a discipline for stating assumptions: who moves first, who knows what, what each side can afford. It is far less useful for producing numbers unless the payoffs are estimated from data, and I had none.
- A "blockchain for audit" is easy to draw and hard to justify without saying what exactly is logged, who can write to it, and how it stays private.

### What the design does not show

This was never tested. Fully homomorphic encryption and zero-knowledge proofs are, today, too slow for real-time neural control, as I noted in the report itself. The framework is a way to organise the problem, not evidence that it works, and the next step would be a prototype on a simulated neural signal pipeline with measured latency for each layer.

### References

- Kairouz et al. Advances and Open Problems in Federated Learning. Foundations and Trends in Machine Learning, 2021.
- Fredrikson, Jha, Ristenpart. Model Inversion Attacks that Exploit Confidence Information and Basic Countermeasures. CCS, 2015.
- Madry, Makelov, Schmidt, Tsipras, Vladu. Towards Deep Learning Models Resistant to Adversarial Attacks. ICLR, 2018.
- Dwork, McSherry, Nissim, Smith. Calibrating Noise to Sensitivity in Private Data Analysis. TCC, 2006.
- Doshi-Velez and Kim. Towards a Rigorous Science of Interpretable Machine Learning. arXiv:1702.08608, 2017.
