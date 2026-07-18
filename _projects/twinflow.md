---
layout: page
title: TwinFlow
description: Digital twin framework for supply chain optimisation with agentic decision-making
img: assets/img/projects/twinflow.png
importance: 2
category: research
related_publications: false
---

**TwinFlow** is a digital twin framework for supply chain systems that combines
simulation-driven state modelling with an agentic decision-making layer capable of
autonomous planning and intervention under uncertainty.

## Research Question

Can a digital twin — a continuously updated simulation of a physical supply chain —
serve as the environment for an agentic system that learns to make optimisation
decisions, and how does simulation fidelity affect downstream decision quality?

## Framework Design

**Digital Twin Layer**
The supply chain is modelled as a dynamic graph: facilities, inventory locations,
transport legs, and suppliers are nodes; material and information flows are edges.
The twin is updated continuously from simulated (or real) event streams, maintaining
a live state representation.

**Simulation Engine**
A discrete-event simulation (DES) engine drives the twin, enabling counterfactual
reasoning: *"What happens to lead times if this supplier fails?"* The simulation
supports stochastic disruption modelling (demand shocks, transport delays, stockouts).

**Agentic Decision-Making**
An LLM-backed agent with access to the twin's state and the simulation engine can:
- Query current inventory and capacity constraints
- Run forward simulations of candidate interventions
- Recommend or execute reordering, rerouting, or capacity reallocation decisions
- Explain decisions by citing the simulation evidence that motivated them

**Optimisation**
Baseline optimisation uses mixed-integer programming (MIP) for static scenarios.
The agentic layer extends this to dynamic, multi-step planning where the environment
evolves between decisions.

## Positioning

TwinFlow targets the intersection of **digital twin research**, **supply chain
optimisation**, and **agentic AI** — an application domain where simulation fidelity,
decision transparency, and real-time adaptation all matter simultaneously.

## Status

In development &mdash; [GitHub →](https://github.com/rahil1303)
