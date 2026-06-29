---
type: compliance_matrix
ontology: MLM
status: active
tags: [mlm, regulatory, compliance-matrix, evidence, audit-trail]
aliases: [Compliance Matrix, MLM Compliance Evidence, Regulatory Compliance Map]
---

# MLM Compliance Matrix

The compliance matrix maps each regulatory requirement to the specific MLM model nodes that constitute evidence of compliance. Designed for EU project deliverables, peer review responses, and audit documentation.

## How to Read This Matrix

- **Requirement** — specific article or clause from the instrument
- **Compliance Mechanism** — how MLM addresses it
- **Evidence Nodes** — specific KG nodes that can be cited
- **Evidence Type** — Model (computational proof), Dataset (data pipeline), Weight (calibrated parameter), Export (machine-readable artefact)

---

## EPBD Compliance Evidence

| Art. | Requirement | Compliance Mechanism | Evidence Nodes | Type |
|---|---|---|---|---|
| Art. 3 | Renovate worst 16% by 2030 | Building archetype identifies EPC D-G buildings for SEAI alert | [[02_Models/T_Models/T11]], [[02_Models/K_Models/K02]], [[02_Models/K_Models/K05]] | Model |
| Art. 7 | New buildings = ZEB from 2030 | EPC classification model validates ZEB status | [[02_Models/T_Models/T19]], W-K02-THRESH2030 | Model, Weight |
| Art. 8 | HVAC efficiency documentation | COP model and heat pump model document HVAC performance | [[02_Models/T_Models/T02]], [[02_Models/T_Models/T03]] | Model |
| Art. 10 | Smart Readiness Indicator | MILP + MPC dispatch capabilities = SRI Levels 3-4 | [[02_Models/C_Models/C01]], [[02_Models/C_Models/C02]] | Model |
| Art. 22 | EPC includes renewable share | Self-consumption ratio included in EPC context | [[02_Models/P_Models/P04]], [[02_Models/K_Models/K01]] | Model |
| Art. 26 | Smart EV charging | Smart charging model optimises EV charging schedule | [[02_Models/B_Models/B04]], [[02_Models/B_Models/B05]] | Model |

## EED Compliance Evidence

| Art. | Requirement | Compliance Mechanism | Evidence Nodes | Type |
|---|---|---|---|---|
| Art. 11 | 1.5%/yr energy savings | Avoided carbon model quantifies savings | [[02_Models/K_Models/K01]], [[02_Models/P_Models/P05]] | Model |
| Art. 18 | Demand response access | MILP produces bankable Flex↑/↓ signals | [[02_Models/C_Models/C01]], [[02_Models/P_Models/P03]] | Model |
| Art. 18 | Small consumer aggregation | Portfolio aggregation enables collective DR | [[02_Models/P_Models/P08]] | Model |
| Art. 19 | Smart meter data access | 15-min data pipeline | [[03_Datasets/DS-SMART-METER]] | Dataset |
| Art. 22 | Energy audits | Load decomposition provides audit baseline | [[02_Models/L_Models/L01]], [[02_Models/L_Models/L02]] | Model |

## RED III Compliance Evidence

| Art. | Requirement | Compliance Mechanism | Evidence Nodes | Type |
|---|---|---|---|---|
| Art. 22 | Renewable Energy Community rights | Community energy balance model quantifies REC performance | [[02_Models/G_Models/G12]], [[02_Models/M_Models/M07]] | Model |
| Art. 15a | Prosumer self-consumption rights | Self-consumption KPI and FIT revenue model | [[02_Models/P_Models/P04]], [[02_Models/M_Models/M11]] | Model |
| Art. 20a | Grid flexibility for storage | V2G and battery dispatch models | [[02_Models/B_Models/B05]], [[02_Models/C_Models/C01]] | Model |
| Art. 24 | Renewable heating & cooling | Heat pump COP and thermal models | [[02_Models/T_Models/T02]], [[02_Models/T_Models/T03]] | Model |
| Art. 31 | Guarantees of Origin | Renewable generation tracking | [[02_Models/R_Models/R01]], [[02_Models/R_Models/R02]], [[02_Models/K_Models/K01]] | Model |

## GDPR Compliance Evidence

| Art. | Requirement | Compliance Mechanism | Evidence Nodes | Type |
|---|---|---|---|---|
| Art. 5(1)(c) | Data minimisation | QA gates discard unnecessary readings | [[02_Models/D_Models/D01]]–[[02_Models/D_Models/D05]] | Model |
| Art. 25 | Privacy by design | Data residency controller — raw data stays on-island | [[02_Models/F_Models/F06]] | Model |
| Art. 32 | Security of processing | Differential privacy on model weights | [[02_Models/F_Models/F03]] | Model |
| Art. 44 | Transfer restrictions | Only anonymised weight deltas leave island | [[02_Models/F_Models/F01]], [[02_Models/F_Models/F06]] | Model |

## IEC 61215 (PV) Compliance Evidence

| Clause | Requirement | Compliance Mechanism | Evidence Nodes | Weight ID |
|---|---|---|---|---|
| STC conditions | Reference conditions | R01 parameterised at STC | [[02_Models/R_Models/R01]] | W-R01-ETA |
| NOCT test | Normal Operating Cell Temp | T_NOCT = 45°C, G_NOCT = 800 W/m² | [[02_Models/R_Models/R01]] | W-R01-TNOCT, W-R01-GNOCT |
| Temp coefficient | Power-temperature relationship | β = -0.0035/°C | [[02_Models/R_Models/R01]] | W-R01-BETA |

## IEC 62619 (Battery) Compliance Evidence

| Clause | Requirement | Compliance Mechanism | Evidence Nodes | Weight ID |
|---|---|---|---|---|
| SoC limits | Safe operating range | SoC ∈ [0.10, 0.90] hard clamp | [[02_Models/B_Models/B01]] | W-B01-SOCMIN, W-B01-SOCMAX |
| Efficiency | Round-trip efficiency | η_ch = η_dis = 0.95 | [[02_Models/B_Models/B01]] | W-B01-ETCH, W-B01-ETDIS |
| Thermal limits | Temperature derating | B08 derates above 40°C / below 5°C | [[02_Models/B_Models/B08]] | W-B08-HOT, W-B08-COLD |

## ISO 7730 (Thermal Comfort) Compliance Evidence

| Category | PMV Range | Compliance Mechanism | Evidence Nodes |
|---|---|---|---|
| Category A | \|PMV\| ≤ 0.5 | C01 minimises comfort penalty (β=0.30) | [[02_Models/C_Models/C01]], [[02_Models/T_Models/T10]] |
| Override trigger | \|PMV\| > 1.0 | O03 triggers comfort override above Cat C | [[02_Models/O_Models/O03]] |
| Temperature bounds | T ∈ [18,24]°C | Hard constraints in T01 and C01 | W-T01-TMIN, W-T01-TMAX |

## EN 50160 (Voltage Quality) Compliance Evidence

| Parameter | EN 50160 Limit | Compliance Mechanism | Evidence Nodes | Weight ID |
|---|---|---|---|---|
| Voltage | ±10% (0.94–1.06 pu) | G04 enforces Vpu limits; C01 blocked if violated | [[02_Models/G_Models/G04]], [[02_Models/C_Models/C01]] | W-G04-VMIN, W-G04-VMAX |
| Frequency | ±0.5 Hz from 50 Hz | G07 islanding detection uses ±0.5 Hz threshold | [[02_Models/G_Models/G07]] | W-G07-FDEV |
| ROCOF | 1.0 Hz/s (Irish grid code) | G07 ROCOF check before C09 islanding action | [[02_Models/G_Models/G07]] | W-G07-ROCOF |

## Climate Action Plan 2024 Compliance Evidence

| Target | Goal | Compliance Mechanism | Evidence Nodes |
|---|---|---|---|
| 80% RES-E by 2030 | Increase renewable share | PV+wind generation models; PED KPI | [[02_Models/R_Models/R01]], [[02_Models/R_Models/R02]], [[02_Models/P_Models/P06]] |
| 680k building retrofits | BER B2 or better | EPC compliance model flags retrofit candidates | [[02_Models/K_Models/K02]], [[02_Models/T_Models/T11]] |
| 400k heat pumps | Replace fossil heating | Heat pump COP and thermal models | [[02_Models/T_Models/T02]], [[02_Models/T_Models/T03]] |
| Carbon budget | Sectoral emissions ceiling | Carbon budget tracker vs annual target | [[02_Models/K_Models/K04]] |
