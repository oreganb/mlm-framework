---
type: kg_root
ontology: MLM
version: 1.0
project: O-CEI / FLEXUS / ODEON
status: active
tags:
  - mlm
  - knowledge-graph
  - flexgpt
  - o-cei
  - energy-informatics
aliases:
  - MLM KG
  - Multi-Layered Modelling Knowledge Graph
---

# MLM Knowledge Graph

## Purpose

The **Multi-Layered Modelling Knowledge Graph (MLM KG)** is the canonical semantic reference for the FLEXGPT modelling architecture developed by the Energy Informatics Group (EIG), Tyndall National Institute / IERC, University College Cork.

It defines — with full resolution — every **model**, **formula**, **dataset**, **input variable**, **output variable**, **weight**, and **inter-model dependency** across all 13 series and 139 sub-models of the MLM framework.

This KG is the single source of truth for:
- Application development (Cursor AI code alignment)
- Dataset wiring (what data, from which dataset, into which model parameter)
- Formula weighting (coefficient values and their justification)
- Cross-series dependency (which models depend on other models' outputs)
- Response Derivative computation (how sensitivity propagates across layers)

---

## Architecture Summary

The MLM is structured as four cognitive layers, each served by specific series:

| Layer | Epistemic Role | Series |
|---|---|---|
| **Deterministic** | Physical feasibility, hard constraints | T, G, R, L |
| **Stochastic** | Uncertainty, variability, probability | W, O, M |
| **AI / ML** | Pattern inference, learning, imputation | D, F, B |
| **RL / Q-Learning** | Agency, adaptation, decision-making | C, P, K |

---

## Series Registry (13 Series, 139 Sub-Models)

| ID | Series | Sub-Models | Layer |
|---|---|---|---|
| [[W-Series]] | Weather & Exogenous | W01–W20 | Stochastic |
| [[O-Series]] | Occupancy, Behaviour & DHW | O01–O15 | Stochastic |
| [[M-Series]] | Market, Tariff & Carbon Pricing | M01–M12 | Stochastic |
| [[L-Series]] | Load & End-Use Decomposition | L01–L13 | Deterministic |
| [[R-Series]] | Renewables & DER | R01–R10 | Deterministic |
| [[T-Series]] | Thermal Envelope & HVAC/Plant | T01–T20 | Deterministic |
| [[B-Series]] | Battery, EV & Storage | B01–B10 | AI/ML |
| [[G-Series]] | Grid, Microgrid & Protection | G01–G12 | Deterministic |
| [[C-Series]] | Optimisation & Control | C01–C10 | RL/Q-Learning |
| [[D-Series]] | Data Quality & Imputation | D01–D08 | AI/ML |
| [[P-Series]] | Trading, Revenue & KPIs | P01–P08 | RL/Q-Learning |
| [[K-Series]] | Carbon, Compliance & Policy | K01–K05 | RL/Q-Learning |
| [[F-Series]] | Meta-Learning, Transfer & Privacy | F01–F06 | AI/ML |

---

## Knowledge Graph Navigation

- **[[00_Core/Architecture/MLM Architecture]]** — Four-layer cognitive stack, epistemic roles
- **[[00_Core/Architecture/Semantic Relationships]]** — Inter-series and inter-model linkage types
- **[[00_Core/Governance/Naming Conventions]]** — ID schemes, field types, weight notation
- **[[00_Core/Governance/Weighting Protocol]]** — How weights are assigned and updated
- **[[00_Core/Layers/Deterministic Layer]]** — Physics layer definition
- **[[00_Core/Layers/Stochastic Layer]]** — Uncertainty layer definition
- **[[00_Core/Layers/AI ML Layer]]** — Inference layer definition
- **[[00_Core/Layers/RL Layer]]** — Agency layer definition
- **[[05_Formulas/Formula Registry]]** — All formulae with LaTeX and weight annotations
- **[[06_Weighting/Weight Registry]]** — Coefficients, defaults, calibration sources
- **[[07_Relationships/Cross-Series Dependencies]]** — Inter-model data flow graph
- **[[07_Relationships/Response Derivative Map]]** — RD propagation across layers

---

## Dataset Families

| Family | Generation Order | Purpose |
|---|---|---|
| [[DS-F Foundational]] | 1st | Weather, building, socioeconomic baselines |
| [[DS-E Energy]] | 2nd | Usage, HVAC, generation, storage patterns |
| [[DS-O Occupancy]] | 3rd | Behavioural, DHW, occupancy time series |
| [[DS-A Advanced]] | 4th | Control, anomaly, DER system data |
| [[DS-P Predictive]] | 5th | Forecasting targets, analytics, anomaly labels |

---

## Applications

- **[[ODEON]]** — Aran Islands O-CEI pilot, 70+ buildings, far-edge deployment
- **[[FLEXUS]]** — Platform execution layer, cloud+edge orchestration
- **[[ARC Tool]]** — Automatic target variable selection, ML pipeline alignment

---

## AI Context (Cursor AI Alignment Note)

When developing code against this KG:
1. Every function parameter must map to a named input variable in a model node
2. Every formula coefficient must match the weight registered in [[06_Weighting/Weight Registry]]
3. Dataset field names must match the schema defined in the relevant [[03_Datasets]] node
4. Cross-series calls must follow the dependency graph in [[07_Relationships/Cross-Series Dependencies]]
5. Model outputs become inputs to downstream models — trace through the graph before implementing
