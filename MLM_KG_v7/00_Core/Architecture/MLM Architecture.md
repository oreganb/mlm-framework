---
type: kg_architecture
ontology: MLM
status: active
tags: [mlm, architecture, layers, cognitive-stack]
aliases: [MLM Architecture, Four-Layer Stack, Cognitive Stack]
---

# MLM Architecture

## Overview

The MLM architecture is a **four-layer cognitive stack** where each layer has a distinct epistemic role. Layers are not interchangeable — they process fundamentally different knowledge types and pass structured outputs upward.

```
┌─────────────────────────────────────────────────────┐
│  RL / Q-Learning Layer   │  C, P, K series          │
│  Agency & Adaptation     │  What to DO next          │
├─────────────────────────────────────────────────────┤
│  AI / ML Layer           │  D, F, B series           │
│  Inference & Patterns    │  What to LEARN            │
├─────────────────────────────────────────────────────┤
│  Stochastic Layer        │  W, O, M series           │
│  Likelihood & Risk       │  What is LIKELY           │
├─────────────────────────────────────────────────────┤
│  Deterministic Layer     │  T, G, R, L series        │
│  Certainty & Feasibility │  What MUST be true        │
└─────────────────────────────────────────────────────┘
```

---

## Layer Definitions

### Deterministic Layer
- **Epistemic Role:** Certainty / Feasibility
- **What it knows:** Hard physical limits, conservation laws, equipment ratings, grid codes
- **Key method types:** RC thermal networks, Kirchhoff's laws, COP equations, NTU-ε heat exchangers, LP/MILP constraints
- **Series:** [[T-Series]], [[G-Series]], [[R-Series]], [[L-Series]]
- **Outputs:** Feasibility bounds, baseline physics states, hard constraint boundaries
- **Cannot:** Compute or optimise Response Derivative; handle uncertainty

### Stochastic Layer
- **Epistemic Role:** Likelihood & Risk
- **What it knows:** Probability distributions of uncertain quantities (weather, occupancy, market prices)
- **Key method types:** Monte Carlo, Markov chains, ARIMA, ensemble forecasting, Hidden Markov Models
- **Series:** [[W-Series]], [[O-Series]], [[M-Series]]
- **Outputs:** Predictive distributions, quantiles (p10/p50/p90), uncertainty bands, covariance matrices
- **Key bridge:** Determinization into Deterministic Layer via quantile, ensemble, or chance-constraint methods

### AI / ML Layer
- **Epistemic Role:** Latent structure discovery
- **What it knows:** Patterns, non-linear relationships, latent states not explicitly modellable
- **Key method types:** Supervised ML (GBM, RF, SVM), Deep Learning (LSTM, CNN), federated averaging, surrogate models
- **Series:** [[D-Series]], [[F-Series]], [[B-Series]]
- **Outputs:** Learned model weights, imputed data, transfer-adapted parameters, SoC/SoH predictions
- **Cannot:** Override stochastic estimates; violate physical bounds

### RL / Q-Learning Layer
- **Epistemic Role:** Decision & Learning
- **What it knows:** What action to take next, given all lower-layer knowledge
- **Key method types:** Q-Tables, Deep Q-Networks (DQN), Actor-Critic, Multi-Agent RL (MARL)
- **Series:** [[C-Series]], [[P-Series]], [[K-Series]]
- **Inputs:** Response Derivative from stochastic+ML layers; feasibility bounds from deterministic layer
- **Outputs:** Dispatch decisions, trading bids, carbon compliance actions, governance signals

---

## Data Flow Architecture

```
[W,O,M Stochastic]──→ [Predictive Distributions]
        │                        │
        ↓                        ↓
[Determinization Bridge] ←── [D,F,B AI/ML Refinement]
        │
        ↓
[T,G,R,L Deterministic Engine (LP/MILP)]
        │
        ↓
[Flex↑, Flex↓ per timestep k]
        │
        ↓
[C,P,K RL Decision Layer] ←── [Response Derivative]
        │
        ↓
[Dispatch / Trade / Carbon Action]
```

---

## Determinization Bridge

The bridge converts stochastic outputs into deterministic inputs for the optimiser. Three methods:

| Method | Speed | Use Case | Risk Profile |
|---|---|---|---|
| **Quantile** | Fast (single solve) | Real-time ≤60 min | Conservative |
| **Ensemble** | Medium (S≈50 parallel) | Near-term 3 h | Robust |
| **Chance-Constraint** | Single solve, risk-aware | Day-ahead 24 h | 90% reliability |

---

## Response Derivative (RD) in Architecture

RD is the cross-cutting sensitivity metric. It is:
- **Estimated** in Stochastic + AI layers (not computed)
- **Bounded** by Deterministic layer constraints
- **Consumed** by RL layer as primary decision primitive

See [[07_Relationships/Response Derivative Map]] for full propagation graph.

---

## Related Nodes
- [[MLM Knowledge Graph]] — Root
- [[Semantic Relationships]] — Linkage types
- [[Naming Conventions]] — ID and field conventions
- [[00_Core/Layers/Deterministic Layer]]
- [[00_Core/Layers/Stochastic Layer]]
- [[00_Core/Layers/AI ML Layer]]
- [[00_Core/Layers/RL Layer]]
