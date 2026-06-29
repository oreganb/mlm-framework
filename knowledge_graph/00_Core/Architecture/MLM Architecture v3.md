---
type: architecture
version: 3.0
---

# MLM Architecture v3

The MLM architecture is organised as a connected cognitive stack: deterministic models define feasibility, stochastic models estimate uncertainty and probability, AI/ML models infer latent patterns and repair data, and RL/Q-learning models make safe, governed actions. Response Derivative (RD) is the connective concept that describes how much useful response is expected from a stimulus, under constraints and uncertainty.

| Layer | Role | Series | RD responsibility |
|---|---|---|---|
| Deterministic | Thermal Envelope & HVAC/Plant; Grid, Microgrid & Protection; Renewables & DER; Load & End-Use Decomposition | T-Series, G-Series, R-Series, L-Series | Bounds feasibility |
| Stochastic | Weather & Exogenous; Occupancy, Behaviour & DHW; Market, Tariff & Carbon Pricing | W-Series, O-Series, M-Series | Estimates probabilistic sensitivity |
| AI/ML | Data Quality & Imputation; Meta-Learning, Transfer & Privacy; Battery, EV & Storage | D-Series, F-Series, B-Series | Refines latent/nonlinear sensitivity |
| RL/Q-Learning | Optimisation & Control; Trading, Revenue & KPIs; Carbon, Compliance & Policy | C-Series, P-Series, K-Series | Consumes RD for safe action |
