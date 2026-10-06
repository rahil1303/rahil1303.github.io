---
layout: post
title: Undetectable Backdoors in ML - The Theory, My Small Experiment, and What It Did Not Show
date: 2026-10-07
description: A summary of the undetectable backdoors paper, a CIFAR-10 experiment I ran on backdoor persistence, and an honest look at what that experiment can and cannot support.
tags: security machine-learning backdoors cryptography
categories: research-notes
---

When you outsource model training, how do you know the model you get back is the model you asked for? For an MSc course on security and machine learning, I read a paper that argues you may not be able to tell, even in principle, and then ran a small experiment of my own. This post covers both, including where my experiment falls short.

### The idea in the paper

The paper is Goldwasser, Kim, Vaikuntanathan and Zamir, *Planting Undetectable Backdoors in Machine Learning Models*. The setting is a client who hires a provider to train a classifier. A backdoor is a pair of procedures: one trains a model that behaves like the honest one almost everywhere, and a second one takes any input and nudges it slightly so the model's answer flips. The backdoored model is called undetectable if no efficient algorithm can distinguish it from an honestly trained model.

The black-box intuition is the part that made it click for me. Wrap the honest classifier in a check. If the input carries a valid digital signature hidden in a few low-order bits, output the opposite label. Otherwise, behave normally. The model contains only the public verification key, and the attacker keeps the secret signing key. Without the signing key, nobody can forge a triggered input, so the backdoor is hard to find and impossible for anyone else to use (the paper calls this non-replicability). The paper also gives a white-box version for specific model families, where even looking at all the weights does not reveal the backdoor, based on hardness assumptions like learning with errors.

The takeaway is that undetectability here is a statement about a particular kind of adversary under cryptographic assumptions, not a claim that all backdoors are invisible. That distinction is easy to lose.

### Other reading

- Gu, Dolan-Gavitt and Garg, *BadNets* (2017), the classic data poisoning backdoor.
- Kalavasis et al., *Injecting Undetectable Backdoors in Obfuscated Neural Networks and Language Models* (arXiv 2406.05660), a follow-up extending the idea.
- Liu, Reiter and Gong, *Mudjacking* (USENIX Security 2024), on patching backdoors in foundation models.

### What I did

I trained a small CNN on CIFAR-10 and injected two kinds of backdoor. The first was a 3x3 white square in the corner of 10% of the training images, relabelled to a target class. The second was a perturbation in the high-frequency (Fourier) components of the images, meant to be less visible. I then tried to detect and remove the backdoors with SHAP, Integrated Gradients, fine-tuning on clean data, and pruning up to 30% of the weights.

As I recorded them, clean accuracy was 70.51% before and 69.37% after backdoor insertion, and the backdoor success rate (BSR) was about 95% for both triggers. SHAP and Integrated Gradients showed no obvious difference between clean and triggered inputs.

### What the experiment does not show

Here is where I have to be honest about my own work. After fine-tuning, I recorded a BSR of about 8.6%, and after 30% pruning about 8.3%. I read that as "the backdoor weakened but persisted". On CIFAR-10 there are ten classes, so a triggered input lands in a given target class about 10% of the time by chance. A BSR of 8 to 9% is at or below chance. That means both defences most likely removed these backdoors, which is the opposite of my conclusion.

There are two further problems. One plot in my report showed a BSR of about 9.5% before any defence, which I cannot reconcile with the 95% from the injection stage, and clean accuracy differs between the text (about 70%) and one of the plots (about 84%). And a white square or a frequency-domain pattern with relabelled poisoned data is an ordinary poisoning backdoor. It is not the signature-based construction from the paper, so it cannot test the paper's undetectability or persistence claims.

### What a proper version would need

1. A clean baseline model, to report the false trigger rate on a model with no backdoor.
2. Several random seeds, with confidence intervals, not one run per setting.
3. Fine-tuning at more than one learning rate and length, since the learning rate largely decides whether a poisoning backdoor survives.
4. Classical detection baselines, such as activation clustering or spectral signatures, alongside the explainability tools.
5. Consistent numbers across text and figures, from one script.

### What I understood

- The gap between theory and practice is large. Provable undetectability depends on cryptographic assumptions that simple poisoning backdoors do not satisfy.
- If inspecting the model cannot be trusted, the defence has to shift to the process: verifiable training, interactive or zero-knowledge proofs of training, and reproducible pipelines.
- The paper also discusses what a defender can do at evaluation time. I have not re-read that part carefully enough to summarise it here, and I would check it before saying more.

### References

- Goldwasser, Kim, Vaikuntanathan, Zamir. Planting Undetectable Backdoors in Machine Learning Models. FOCS 2022. arXiv:2204.06974.
- Gu, Dolan-Gavitt, Garg. BadNets: Identifying Vulnerabilities in the Machine Learning Model Supply Chain. arXiv:1708.06733, 2017.
- Kalavasis et al. Injecting Undetectable Backdoors in Obfuscated Neural Networks and Language Models. arXiv:2406.05660.
- Liu, Reiter, Gong. Mudjacking: Patching Backdoor Vulnerabilities in Foundation Models. USENIX Security, 2024.
