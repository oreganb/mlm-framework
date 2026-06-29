---
type: governance_node
ontology: MLM
status: active
tags: [mlm, governance, weighting, calibration]
aliases: [Weighting Protocol, MLM Weight Governance]
---

# Weighting Protocol

## Principles
1. **Every coefficient is registered** — no magic numbers in code. All weights in [[06_Weighting/Weight Registry]].
2. **Weights have justification** — calibration source documented (SEAI, IEC, literature, empirical).
3. **Weights have uncertainty** — where applicable, confidence intervals are noted.
4. **Weights are updateable** — follow the update protocol below.

## Weight Types

| Type | Meaning | Example |
|---|---|---|
| `multiplicative` | Applied as multiplier to variable | η_PV × G_POA |
| `additive` | Added to sum | Q_solar + Q_int + Q_HVAC |
| `exponent` | Variable raised to this power | v³ in wind power |
| `constraint` | Hard limit (not multiplier) | SoC_max = 0.90 |
| `parameter` | Physical/empirical constant | R_env, C_zone |
| `objective` | Objective function weight | α, β, γ in C01 |

## Update Protocol

| Trigger | Action | Authority |
|---|---|---|
| New seasonal data | Recalibrate AR/MA in M01 | Automated (monthly) |
| Pilot data cycle complete | Update R, C per archetype in T11 | EIG team (quarterly) |
| Federated learning round | Update LSTM weights via F01 | Automated |
| Policy change | Update EF, γ, tariff rates | Manual (BOR/EIG) |
| Model drift detected | Trigger F01 retraining | D08 automated flag |
| Academic literature update | Review and update physical params | Annual review (BOR) |

## Synthetic Dataset Weights
Population distribution weights (for synthetic data generation) are documented in [[06_Weighting/Weight Registry]] under "Synthetic Dataset Weighting". These reflect Irish housing stock distributions from SEAI 2023.

## Navigation
- [[06_Weighting/Weight Registry]] — All coefficients
- [[00_Core/Governance/Naming Conventions]] — Weight ID scheme
