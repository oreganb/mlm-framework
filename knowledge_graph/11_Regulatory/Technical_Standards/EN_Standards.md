---
type: regulatory_node
category: Technical_Standards
id: EN_Standards
full_title: European Norm (EN) Standards Referenced in MLM
citation: CENELEC / CEN
status: active
tags: [mlm, regulatory, technical-standard, en, voltage-quality, power-quality, grid]
aliases: [EN Standards, EN 50160, EN 50549, EN 62053]
---

# EN Standards Referenced in MLM

## EN 50160 — Voltage Characteristics at the Point of Connection

**Full title:** Voltage characteristics of electricity supplied by public electricity networks
**Relevance:** Defines the voltage quality limits (Vmin/Vmax) that G01/G04 enforce, and that C01 must not violate through DER dispatch.

| Parameter | EN 50160 Limit | MLM Weight ID | Model |
|---|---|---|---|
| Voltage (steady state) | ±10% of nominal (94–106% pu) | W-G01-VMIN = 0.94, W-G01-VMAX = 1.06 | [[02_Models/G_Models/G04]] |
| Frequency (continental) | 49.5–50.5 Hz (99.5% of time) | W-G07-FDEV = 0.5 Hz | [[02_Models/G_Models/G07]] |
| Voltage unbalance | ≤2% negative sequence | Checked in G04 | [[02_Models/G_Models/G04]] |
| Harmonics (THD) | ≤8% (LV) | Not modelled in current MLM | Future G-Series extension |

**MLM Models:**
- [[02_Models/G_Models/G04]] — Voltage Stability: enforces EN 50160 Vpu ∈ [0.94, 1.06]
- [[02_Models/G_Models/G07]] — Islanding Detection: 50 Hz ± 0.5 Hz (EN 50160 frequency limits)
- [[02_Models/C_Models/C01]] — MILP: G04 PQ_feasible flag gates C01 dispatch (EN 50160 compliance gate)

## EN 50549-1/2 — Requirements for Generating Plants

**Full title:** Requirements for generating plants to be connected in parallel with distribution networks
**Relevance:** Governs PV inverter grid connection requirements. R01/R05 inverter model operates within EN 50549 limits.

| Parameter | EN 50549 Requirement | MLM Model |
|---|---|---|
| Frequency operating range | 47.5–51.5 Hz | [[02_Models/G_Models/G07]] |
| Voltage operating range | 0.85–1.10 pu | [[02_Models/G_Models/G04]] |
| LVRT (Low Voltage Ride Through) | Remain connected during voltage dips | [[02_Models/G_Models/G09]] protection |
| Reactive power capability | Q(U) characteristic required | [[02_Models/G_Models/G05]] power factor |

## EN 62053-21 — Electricity Metering

**Full title:** Electricity metering equipment — Particular requirements for static meters for AC active energy (classes 0.5, 1 and 2)
**Relevance:** HotDrop sub-meters in ODEON are Class 2 (±2% accuracy). D05 QA tolerance calibrated to EN 62053.

**MLM Model:** [[02_Models/D_Models/D05]] — Smart Meter QA: ±2% tolerance from EN 62053 Class B

## EN ISO 13790 / 52016-1 — Building Energy Calculation

**Full title:** Energy performance of buildings — Calculation of energy use for space heating and cooling
**Relevance:** RC thermal model in T01 follows the monthly/hourly calculation approach of EN ISO 52016-1 (successor to EN 13790).

**MLM Models:**
- [[02_Models/T_Models/T01]] — Building Thermal RC: method consistent with EN ISO 52016-1
- [[02_Models/T_Models/T11]] — Archetype parameters calibrated to EN ISO 13790 thermal classes
