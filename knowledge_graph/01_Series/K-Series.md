---
type: series_index
ontology: MLM
series_id: K
series_title: Carbon, Compliance & Policy
layer: rl
status: active
tags: [mlm, series:K, layer:rl, carbon, compliance, policy]
aliases: [K-Series, Carbon Series]
sub_model_count: 5
id_range: K01–K05
---

# K-Series — Carbon, Compliance & Policy

## Master Formula
$$CO_{2,avoided} = (E_{ref} - E_{use}) \times EF_{marginal}$$
Avoided CO₂ = difference between reference and actual energy × marginal emission factor (0.295 kgCO₂/kWh, Ireland 2024).

## Sub-Model Registry

| ID | Title | Key Output |
|---|---|---|
| [[K01]] | Avoided Carbon Model | CO₂_avoided (kgCO₂) |
| [[K02]] | EPC Compliance Model | EPC_compliance_flag |
| [[K03]] | EU ETS Reporting Model | ETS_liability (€) |
| [[K04]] | Carbon Budget Tracker | carbon_budget_remaining |
| [[K05]] | Policy Alignment Model | policy_flags, incentive_eligibility |

## Weights
- EF_marginal = 0.295 kgCO₂/kWh (Ireland, SEAI 2024)
- EF_average = 0.218 kgCO₂/kWh (Ireland, SEAI 2024)