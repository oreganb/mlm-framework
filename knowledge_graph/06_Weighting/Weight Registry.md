---
type: weight_registry
ontology: MLM
status: active
tags: [mlm, weights, coefficients, calibration]
aliases: [Weight Registry, MLM Coefficient Table]
---

# MLM Weight Registry

## Purpose
Single source of truth for all formula coefficients, weights, and calibration parameters across the MLM. Every coefficient in every model formula links here.

**Rule:** If a value appears in a formula, it is registered here. No magic numbers in code.

---

## W-Series Weights

| Weight ID | Model | Parameter | Symbol | Value | Unit | Justification | Calibration Source |
|---|---|---|---|---|---|---|---|
| W-W01-KCLOUD | W01 | Cloud attenuation | k_cloud | 0.11 | — | Perez model, maritime climate | Perez et al. (1990); Met Éireann |
| W-W01-DNI | W01 | DNI coefficient | w_DNI | 1.0 | — | Physically exact, no attenuation | Conservation of energy |
| W-W01-DHI | W01 | DHI coefficient | w_DHI | 1.0 | — | Isotropic sky assumption | Hay-Davies model |
| W-W01-NENS | W01 | Ensemble size | N_ens | 50 | — | Computational budget | FLEXUS benchmarking |
| W-W05-OKTAS | W05 | OKTAS normalisation | 1/8 | 0.125 | — | Scale 0–8 to 0–1 | Meteorological standard |

---

## T-Series Weights

| Weight ID | Model | Parameter | Symbol | Value | Unit | Justification | Calibration Source |
|---|---|---|---|---|---|---|---|
| W-T01-RENV | T01 | Thermal resistance (uninsulated) | R_env | 0.15 | K/W | Pre-1980 Irish masonry | SEAI building stock 2023 |
| W-T01-RENV-INS | T01 | Thermal resistance (insulated) | R_env | 0.80 | K/W | Post-2010 Irish A-rated | SEAI 2023 |
| W-T01-CZONE | T01 | Thermal capacitance (mid) | C_zone | 3.5×10⁶ | J/K | Typical semi-detached | EnergyPlus calibration |
| W-T01-TMIN | T01 | Comfort lower bound | T_min | 18.0 | °C | CIBSE Guide A | CIBSE 2021 |
| W-T01-TMAX | T01 | Comfort upper bound | T_max | 24.0 | °C | CIBSE Guide A | CIBSE 2021 |
| W-T01-DT | T01 | Timestep | Δt | 900 | s | Smart meter resolution | FLEXUS platform spec |
| W-T02-COP-BASE | T02 | Base COP (ASHP at 7°C outdoor) | COP_base | 3.2 | — | Typical Irish ASHP | SEAI heat pump field trials 2022 |
| W-T02-COP-BETA | T02 | COP temperature sensitivity | β_COP | -0.08 | /°C | Linear fit to lab data | SEAI 2022 |
| W-T13-COP-HTHP | T13 | HTHP base COP | COP_HTHP | 2.8 | — | High-temp process (>80°C) | INNOV8HEAT field data |

---

## R-Series Weights

| Weight ID | Model | Parameter | Symbol | Value | Unit | Justification | Calibration Source |
|---|---|---|---|---|---|---|---|
| W-R01-ETA | R01 | Panel STC efficiency | η_PV | 0.20 | fraction | Modern monocrystalline | IEA PVPS 2023 |
| W-R01-BETA | R01 | Temp power coefficient | β | -0.0035 | /°C | Standard monocrystalline Si | IEC 61215 |
| W-R01-TNOCT | R01 | Nominal operating cell temp | T_NOCT | 45 | °C | Standard condition | IEC 61215 |
| W-R01-GNOCT | R01 | NOCT irradiance | G_NOCT | 800 | W/m² | Standard NOCT | IEC 61215 |
| W-R01-ETAINV | R01 | Inverter efficiency | η_inv | 0.96 | fraction | Modern string inverter | DS-INVERTER default |
| W-R01-PCLAMP | R01 | Min power clamp | P_min | 0.0 | kW | Physical — no negative generation | Physics |

---

## B-Series Weights

| Weight ID | Model | Parameter | Symbol | Value | Unit | Justification | Calibration Source |
|---|---|---|---|---|---|---|---|
| W-B01-ETCH | B01 | Charge efficiency | η_ch | 0.95 | fraction | Li-ion, charge side | IEC 62619 |
| W-B01-ETDIS | B01 | Discharge efficiency | η_dis | 0.95 | fraction | Li-ion, discharge side | IEC 62619 |
| W-B01-SOCMIN | B01 | Minimum SoC | SoC_min | 0.10 | fraction | Deep discharge protection | Battery manufacturer + B02 |
| W-B01-SOCMAX | B01 | Maximum SoC | SoC_max | 0.90 | fraction | Calendar aging reduction | IEC 62619 + B02 |
| W-B01-LSTMWIN | B01 | LSTM history window | N_hist | 24 | steps | Daily cycle capture (6h@15min) | ARC Tool ML calibration |
| W-B02-CYCLE | B02 | Degradation per cycle | δ_cycle | 0.0001 | /cycle | 10,000 cycle life typical | Battery manufacturer |
| W-B02-ARRH | B02 | Arrhenius thermal factor | E_a/R | 6000 | K | Li-ion Arrhenius fit | Bernardi et al. 2002 |

---

## C-Series Weights

| Weight ID | Model | Parameter | Symbol | Value | Unit | Justification | Calibration Source |
|---|---|---|---|---|---|---|---|
| W-C01-ALPHA | C01 | Energy cost weight | α | 0.50 | — | Primary driver, Irish market | EIG policy calibration |
| W-C01-BETA | C01 | Comfort weight | β | 0.30 | — | Regulatory floor | CIBSE + SEAI |
| W-C01-GAMMA | C01 | Carbon weight | γ | 0.20 | — | EU ETS / SEAI policy | Carbon policy 2024 |
| W-C01-HORIZON-RT | C01 | Real-time horizon | H_rt | 4 steps | — | 60 min, quantile determinization | FLEXUS platform |
| W-C01-HORIZON-NT | C01 | Near-term horizon | H_nt | 12 steps | — | 3h, ensemble determinization | FLEXUS platform |
| W-C01-HORIZON-DA | C01 | Day-ahead horizon | H_da | 96 steps | — | 24h, chance-constraint | FLEXUS platform |
| W-C03-EPSILON | C03 | Chance-constraint reliability | ε | 0.10 | — | 90% reliability | FT stochastic bridge spec |
| W-C03-NSCEN | C03 | Ensemble scenario count | S | 50 | — | Robust coverage, feasible compute | FT notes 02-03-2026 |

---

## O-Series Weights

| Weight ID | Model | Parameter | Symbol | Value | Unit | Justification | Calibration Source |
|---|---|---|---|---|---|---|---|
| W-O01-QOCC | O01 | Heat gain per occupant | q_occ | 80 | W/person | Sedentary activity, CIBSE | CIBSE Guide A |
| W-O01-TRANS | O01 | Markov transition smoothing | — | 0.05 | — | HMM calibration | O-CEI pilot data |

---

## M-Series Weights

| Weight ID | Model | Parameter | Symbol | Value | Unit | Justification | Calibration Source |
|---|---|---|---|---|---|---|---|
| W-M01-AR | M01 | ARIMA AR coefficient | α_AR | 0.72 | — | Irish day-ahead price, fitted | SEMO price data 2023 |
| W-M01-MA | M01 | ARIMA MA coefficient | θ_MA | 0.31 | — | Irish day-ahead price, fitted | SEMO price data 2023 |
| W-M08-EF | M08 | Marginal emission factor | EF | 0.295 | kgCO₂/kWh | Ireland 2024 marginal | SEAI Energy Statistics 2024 |

---

## K-Series Weights

| Weight ID | Model | Parameter | Symbol | Value | Unit | Justification | Calibration Source |
|---|---|---|---|---|---|---|---|
| W-K01-EFMARG | K01 | Marginal emission factor | EF_marginal | 0.295 | kgCO₂/kWh | Ireland grid 2024 | SEAI 2024 |
| W-K01-EFAVG | K01 | Average emission factor | EF_avg | 0.218 | kgCO₂/kWh | Ireland grid 2024 average | SEAI 2024 |

---

## Synthetic Dataset Weighting (Population Distributions)

These weights apply to synthetic dataset generation for training/testing, not to model formulas.

| ID | Category | Attribute | Value | Weight |
|---|---|---|---|---|
| SD-BLD-01 | Building Type | Detached | Irish housing stock | 40% |
| SD-BLD-02 | Building Type | Semi-Detached | Irish housing stock | 30% |
| SD-BLD-03 | Building Type | Terraced | Irish housing stock | 20% |
| SD-BLD-04 | Building Type | Apartment | Irish housing stock | 10% |
| SD-EPC-01 | EPC Rating | A1-A3 | SEAI 2023 | 15% |
| SD-EPC-02 | EPC Rating | B1-B3 | SEAI 2023 | 35% |
| SD-EPC-03 | EPC Rating | C1-C3 | SEAI 2023 | 30% |
| SD-EPC-04 | EPC Rating | D or below | SEAI 2023 | 20% |
| SD-HTG-01 | Heating | Heat Pump | O-CEI pilot target | 30% |
| SD-HTG-02 | Heating | Gas Boiler | Existing stock | 40% |
| SD-HTG-03 | Heating | Oil Boiler | Rural Ireland | 20% |
| SD-HTG-04 | Heating | Electric | Storage heaters | 10% |
| SD-REN-01 | Renewables | Has system | Irish adoption | 35% |
| SD-INS-01 | Insulation | Insulated | SEAI retrofit stats | 70% |

---

## Weight Update Protocol

| Trigger | Action | Responsible Model |
|---|---|---|
| New seasonal data available | Recalibrate W-M01-AR, W-M01-MA | [[M01]] |
| Pilot measurement cycle complete | Update W-T01-RENV per archetype | [[T11]], [[D-Series]] |
| Federated learning cycle | Update W-B01-LSTM weights | [[F01]] |
| Policy change | Update W-K01-EFMARG, W-C01-GAMMA | Manual via [[K-Series]] |
| Detected model drift | Trigger [[D08]] retraining flag | [[D-Series]] |
