---
type: relationship_graph
ontology: MLM
status: active
tags: [mlm, relationships, dependencies, data-flow, graph]
aliases: [Cross-Series Dependencies, MLM Dependency Graph, Data Flow Graph]
---

# Cross-Series Dependencies

## Overview
This node defines all inter-model and inter-series data flows. Every `feeds_into` relationship is listed here with the exact variable passed, enabling code-level traceability.

---

## Execution Order

Models must be executed in this sequence within each timestep:

```
Step 1:  D-Series  (data quality gate — clean all inputs first)
Step 2:  W-Series  (weather context — independent, external)
Step 3:  O-Series  (occupancy — depends on W for temperature context)
Step 4:  M-Series  (market — depends on W for weather-price correlation)
Step 5:  R-Series  (renewables — depends on W01 for irradiance)
Step 6:  T-Series  (thermal — depends on W02, O, R)
Step 7:  B-Series  (battery — depends on R, C from prev step)
Step 8:  G-Series  (grid — depends on R, T, B net loads)
Step 9:  C-Series  (optimisation — depends on all above)
Step 10: P-Series  (trading — depends on C outputs)
Step 11: K-Series  (carbon — depends on C, P outputs)
Step 12: F-Series  (federated update — end of cycle, asynchronous)
```

---

## Full Dependency Matrix

| Producer | Output Variable | Symbol | Consumer | Consumer Input |
|---|---|---|---|---|
| **W01** | Solar irradiance quantiles | G_POA.{p10,p50,p90} | **R01** | W01 → R01-I01 |
| **W01** | Solar irradiance quantiles | G_POA.p50 | **T04** | Solar heat gain calc |
| **W02** | Outdoor temperature | T_out | **T01** | T01-I02 |
| **W02** | Outdoor temperature | T_out | **R01** | T_cell calculation |
| **W02** | Outdoor temperature | T_out | **B08** | Battery thermal limit |
| **W05** | Cloud cover | OKTAS | **W01** | Cloud attenuation |
| **W05** | Cloud cover | OKTAS | **W20** | PV availability |
| **W08** | Solar geometry | θ_z | **W01** | cos(θ_z) factor |
| **W14** | Weather ensemble | Ω_w | **C03** | Stochastic input to bridge |
| **W_SCORE** | Composite score | W_SCORE | **C01** | Environmental objective term |
| **O01** | Occupancy probability | P_occ(t) | **T01** | Q_int (heat gain) |
| **O01** | Occupancy state | occ_state | **C01** | C01-I07, comfort gate |
| **O01** | Occupancy probability | P_occ(t) | **L01** | Appliance use scaling |
| **O03** | Comfort feedback | override_flag | **C10** | Governance override |
| **M01** | Day-ahead price | price_DA[k] | **C01** | C01-I05, energy cost |
| **M01** | Day-ahead price | price_DA[k] | **P01** | Revenue calculation |
| **M08** | Carbon emission factor | EF[k] | **C01** | C01-I06, carbon cost |
| **M08** | Carbon emission factor | EF[k] | **K01** | Avoided carbon calc |
| **M08** | Intraday price | price_ID[k] | **P02** | Intraday trading |
| **R01** | PV generation quantiles | P_PV.{p10,p50,p90} | **C01** | Net PCC power |
| **R01** | PV generation | P_PV.p50 | **L01** | Net load |
| **R01** | PV surplus | P_PV - P_base | **B01** | Charging opportunity |
| **R01** | PV generation | P_PV.p50 | **K01** | Avoided CO₂ |
| **T01** | Indoor temperature | T_in(t) | **C01** | Thermal constraint |
| **T01** | Indoor temperature | T_in(t) | **T10** | PMV comfort index |
| **T01** | Indoor temperature | T_in(t) | **O03** | Occupant feedback |
| **T02** | COP value | COP(T_in,T_out) | **C01** | HVAC efficiency in objective |
| **T02** | COP value | COP | **L03** | HVAC energy decomposition |
| **T11** | Archetype parameters | R_env, C_zone | **T01** | RC model params |
| **T11** | EPC label | EPC | **K02** | Carbon compliance check |
| **B01** | Battery SoC | SoC[t] | **C01** | SoC constraint |
| **B01** | Stored energy | E_stored | **C01** | Flexibility headroom |
| **B01** | Battery SoC | SoC[t] | **B05** | V2G availability gate |
| **B01** | Battery SoC | SoC[t] | **P01** | Arbitrage opportunity |
| **B05** | V2G flag | V2G_avail | **C01** | V2G dispatch gate |
| **G01** | Grid import limit | P_grid_max | **C01** | Hard import constraint |
| **G01** | Bus voltage | V_bus | **G06** | Voltage stability |
| **G09** | Island detection flag | island_flag | **C09** | Islanding controller |
| **C01** | HP dispatch | P_HP[k] | **T01** | Q_HVAC input |
| **C01** | Battery dispatch | P_bat_chg/dis[k] | **B01** | Charging/discharging |
| **C01** | Flexibility signals | Flex↑/↓[k] | **P01** | Market bid |
| **C01** | Net grid power | P_grid[k] | **K01** | Carbon accounting |
| **C01** | Shadow prices | ∂obj/∂x | **C08** | Response Derivative inputs |
| **C08** | Response Derivative | RD(t) | **C05** | DQN decision input |
| **C08** | Response Derivative | RD(t) | **C09** | Islanding viability |
| **C10** | Governance signals | allow/derate/block | **C01** | Gate on all actions |
| **P01** | Revenue | R_total | **P-Series KPIs** | Platform KPIs |
| **K01** | Avoided CO₂ | CO₂_avoided | **K-Series** | Compliance reporting |
| **D01** | Cleaned inputs | x_clean | All series | Validated data stream |
| **F01** | Global model weights | θ_global | **B01** | LSTM weights update |
| **F01** | Global model weights | θ_global | **O01** | HMM weights update |

---

## Response Derivative Propagation

The Response Derivative (RD) is a cross-cutting sensitivity metric. Its propagation chain:

```
Stochastic Layer (W, O, M)
    │
    ├── Local sensitivity estimates per series
    │   W-series: ∂response/∂G_POA, ∂response/∂T_out
    │   O-series: ∂response/∂occ_state
    │   M-series: ∂response/∂price_DA
    │
    ▼
AI/ML Layer (D, F, B)
    │
    ├── Non-linear correction of sensitivity estimates
    │   B01: ∂SoC/∂P_ch (learned, temperature-adjusted)
    │   F01: Cross-building sensitivity patterns
    │
    ▼
C08 — Response Derivative Engine
    │
    ├── Synthesises RD from all contributions
    │   RD = Σ wᵢ × ∂Flex↑/∂xᵢ (shadow prices from C01 LP)
    │
    ▼
C01/C05/C09 — Consumed as decision primitive
    │
    ├── Action gating: block if RD indicates no response
    ├── Derating: scale action by RD confidence
    └── Islanding: validate autonomy viability using RD
```

---

## Dataset → Model Mappings

| Dataset | Models That Read It | Fields Read |
|---|---|---|
| [[DS-WEATHER]] | W01, W02, W03–W06 | temperature, irradiance, wind, humidity, pressure |
| [[DS-NWP]] | W01, W14, W16 | forecast irradiance, temperature ensemble |
| [[DS-BUILDING]] | T01, T11, T19 | R_env, C_zone, EPC, construction_year, type |
| [[DS-PV]] | R01, R03 | area, efficiency, beta, NOCT, tilt, azimuth |
| [[DS-BATTERY]] | B01, B02, B06 | capacity, eta, SoC limits, chemistry |
| [[DS-EV]] | B03, B04, B05 | trip logs, arrival/departure, SoC on return |
| [[DS-OCCUPANCY]] | O01, O02 | occupancy time series, activity level |
| [[DS-SENSOR]] | T01, B01, D01–D04 | indoor temp, battery temp, voltage, current |
| [[DS-SMART-METER]] | L01, G01, D05 | net import/export, sub-metered loads |
| [[DS-MARKET]] | M01, M02, M08 | day-ahead prices, intraday, carbon signal |
| [[DS-HVAC]] | T02, T03, T14 | rated COP, flow rates, capacity |
| [[DS-INVERTER]] | R01, R05 | efficiency curve, rated capacity |

---

## Circular Dependency Resolution

T01 and C01 have apparent circular dependency (T01 needs Q_HVAC from C01; C01 needs T_in from T01). Resolved by:

1. **Initialisation:** T_in[0] from DS-SENSOR measurement
2. **Rolling horizon:** C01 uses T_in[k] to compute dispatch for [k+1]; T01 updates T_in[k+1]
3. **No true circularity** — always lagged by one timestep

Similarly B01 ↔ C01: C01 dispatches P_bat[k]; B01 computes SoC[k+1].
