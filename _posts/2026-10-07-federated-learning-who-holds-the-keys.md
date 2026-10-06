---
layout: post
title: Federated Learning and the Question of Who Holds the Keys
date: 2026-10-07
description: A summary of my reading on federated learning, cryptographic protections, algorithmic bias and governance, framed by Rogaway's argument that cryptographic work is never ethically neutral.
tags: security machine-learning federated-learning ethics
categories: research-notes
---

For an MSc course on security and machine learning, my topic was ethics and morality in security and AI. I chose federated learning, a technique usually sold as privacy-preserving, and asked a blunt question: who actually holds the keys? This post summarises what I read and what I took from it.

### The basic idea

In centralised learning, everyone's data is gathered on one server. In federated learning (FL), clients keep their data and send model updates instead. A server combines them, most commonly with federated averaging (FedAvg, from McMahan et al. 2017), which weights each client's update by how much data it has. The promise is less data exposure and a better fit with privacy rules like the GDPR.

### Problems that remain

Moving the data does not remove the risks. Updates can leak information (membership inference, model inversion). Malicious clients can poison the model or plant backdoors. And FL brings its own problems: client data is non-IID (not identically distributed), which makes training harder and fairness worse, and the whole system needs someone to run the aggregation.

### Cryptography as a layer, not a solution

I read about the standard tools: secure aggregation (Bonawitz et al., 2017), where the server only sees the sum of updates; homomorphic encryption; differential privacy, which adds calibrated noise; and secure multi-party computation. Each helps and each has a price in computation, communication, or accuracy. I also read about PolyFLAM and PolyFLAP (Moshawrab et al., 2024), which use polymorphic encryption and trade communication against computation: one sends whole models, the other only parameters.

The most useful reading was Rogaway's *The Moral Character of Cryptographic Work*. His argument is that cryptography is not ethically neutral: the tools that protect people can also entrench power if the people who control the keys, the protocols or the infrastructure are not accountable. Applied to FL, strong encryption can make an unfair or biased model harder to audit, so privacy and accountability can pull in opposite directions.

### Bias, in a technical sense

FL does not remove bias, and it can worsen it. Because FedAvg weights clients by data size, data-rich clients dominate. If a group is underrepresented in the participating clients, the model serves it worse, and differential privacy noise can hit small datasets harder. In a healthcare example, hospitals with fewer patients or less diverse data would get less accurate models.

I used two real cases to show how unnoticed bias causes harm: Amazon's recruiting tool (development started around 2014, reported and scrapped by 2018), and the SafeRent tenant screening case, which ended in a settlement after the scoring system was found to disadvantage voucher holders. Both are centralised systems, but they show what is at stake when a model cannot be inspected.

### Governance: who actually controls FL?

FL is decentralised in where the data sits, not in who runs it. A handful of large companies and cloud providers build the frameworks, run the aggregation and set the encryption standards. I read The Atlantic's *Europe's Elon Musk Problem* as a way of asking whether European regulation can hold when the infrastructure is controlled elsewhere. I also looked at the GDPR and the EU AI Act, and at a tension I found interesting: the GDPR pushes for explainability and audit, while cryptographic protections deliberately limit what an auditor can see.

My report also drew on public-opinion surveys from Cisco, Pew and Deloitte, which pointed in the same direction: public trust in how companies handle AI and data is low. I would re-check the exact figures against the sources before quoting them.

### What I took away

1. FL shifts risk and power instead of removing them. The honest question is who controls aggregation, keys and auditing.
2. Privacy and accountability are not the same goal. Techniques that improve one can weaken the other, and that trade-off should be explicit.
3. The proposals I ended up with were governance ones: independent audits, transparency reports, explainability standards, and open-source FL frameworks so that no single company is the only gatekeeper.

### What I would question now

This was a position paper, and it took a clear stance. A stronger version would test it, for example by measuring how much FedAvg weighting changes per-group accuracy on a controlled non-IID split. I also leaned on a couple of secondary sources (a video lecture and news articles) for points that deserve primary references.

### References

- McMahan, Moore, Ramage, Hampson, Aguera y Arcas. Communication-Efficient Learning of Deep Networks from Decentralized Data. AISTATS, 2017.
- Bonawitz et al. Practical Secure Aggregation for Privacy-Preserving Machine Learning. CCS, 2017.
- Kairouz et al. Advances and Open Problems in Federated Learning. Foundations and Trends in Machine Learning, 2021.
- Rogaway. The Moral Character of Cryptographic Work. 2015.
- Dwork, McSherry, Nissim, Smith. Calibrating Noise to Sensitivity in Private Data Analysis. TCC, 2006.
- Moshawrab, Adda, Bouzouane, Ibrahim, Raad. A Maneuver in the Trade-off Space of Federated Learning Aggregation Frameworks Secured with Polymorphic Encryption: PolyFLAM and PolyFLAP Frameworks. Electronics, 2024.
- Wachter, Mittelstadt, Floridi. Why a Right to Explanation of Automated Decision-Making Does Not Exist in the General Data Protection Regulation. International Data Privacy Law, 2017.
- The Atlantic. Europe's Elon Musk Problem. 2025.
