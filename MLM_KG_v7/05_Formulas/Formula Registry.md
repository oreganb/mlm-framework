---
type: formula_registry
ontology: MLM
status: active
tags: [mlm, formulas, equations, mathematics]
aliases: [Formula Registry, MLM Equations]
---

# MLM Formula Registry

## Overview
All master-level and key sub-model formulas. Each formula entry includes: LaTeX, Python pseudocode reference, weight annotations, and links to the model node where the full implementation lives.

---

## F-T01 — Building Thermal RC (Deterministic)

**Model:** [[T01]]  
**Layer:** Deterministic

$$C \frac{dT_{in}}{dt} = \frac{T_{out} - T_{in}}{R_{env}} + Q_{solar} + Q_{int} + Q_{HVAC}$$

**Discrete (Euler):**
$$T_{in}[k+1] = T_{in}[k] + \frac{\Delta t}{C} \left( \frac{T_{out}[k] - T_{in}[k]}{R_{env}} + Q_{solar}[k] + Q_{int}[k] + Q_{HVAC}[k] \right)$$

| Term | Weight | Source |
|---|---|---|
| (T_out - T_in)/R_env | 1/R_env (conductance) | DS-BUILDING.thermal_resistance |
| Q_solar | 1.0 | [[T04]] ← [[W01]] |
| Q_int | 1.0 | [[O08]] |
| Q_HVAC | 1.0 (signed) | [[C01]] dispatch |
| Δt/C | 1/(C×Δt⁻¹) | DS-BUILDING.thermal_capacitance |

---

## F-R01 — PV Generation (Deterministic)

**Model:** [[R01]]  
**Layer:** Deterministic (physics bridge from stochastic G_POA)

$$P_{PV} = G_{POA} \cdot A \cdot \eta_{PV} \cdot [1 - \beta(T_{cell} - 25)] \cdot \eta_{inv}$$

$$T_{cell} = T_{out} + \frac{G_{POA}}{G_{NOCT}} \cdot (T_{NOCT} - T_{out,NOCT})$$

| Parameter | Value | Source |
|---|---|---|
| A (area) | from DS-PV | multiplicative |
| η_PV | 0.20 | W-R01-ETA |
| β | -0.0035/°C | W-R01-BETA |
| T_NOCT | 45°C | W-R01-TNOCT |
| G_NOCT | 800 W/m² | W-R01-GNOCT |
| η_inv | 0.96 | W-R01-ETAINV |

---

## F-B01 — Battery SoC (AI/ML hybrid)

**Model:** [[B01]]  
**Layer:** AI/ML

$$E_{t+1} = E_t + \eta_{ch} \cdot P_{ch}[t] \cdot \Delta t - \frac{P_{dis}[t]}{\eta_{dis}} \cdot \Delta t$$

$$SoC_{t+1} = \frac{E_{t+1}}{E_{nom}} + f_{LSTM}(\mathbf{x}_{hist})$$

| Parameter | Value | Source |
|---|---|---|
| η_ch | 0.95 | W-B01-ETCH |
| η_dis | 0.95 | W-B01-ETDIS |
| E_nom | from DS-BATTERY | normaliser |
| SoC_min | 0.10 (0.15 ODEON) | W-B01-SOCMIN |
| SoC_max | 0.90 | W-B01-SOCMAX |

---

## F-C01 — MILP Optimisation Objective (RL)

**Model:** [[C01]]  
**Layer:** RL/Q-Learning

$$\min_{\mathbf{x}_t} \sum_{k \in H} \left[ \alpha \cdot C_{energy}(k) + \beta \cdot C_{comfort}(k) + \gamma \cdot C_{carbon}(k) \right]$$

$$P^{grid}_k = P^{base}_k - P^{PV}_k - P^{bat,dis}_k + P^{bat,chg}_k - P^{shed}_k + P^{HP}_k + P^{EV}_k$$

| Weight | Value | Constraint |
|---|---|---|
| α (energy) | 0.50 | W-C01-ALPHA |
| β (comfort) | 0.30 | W-C01-BETA |
| γ (carbon) | 0.20 | W-C01-GAMMA |
| α+β+γ | = 1.00 | Must be normalised |

---

## F-W01 — Weather Score (Stochastic)

**Model:** [[W-Series]]  
**Layer:** Stochastic

$$W_{SCORE} = \sum_{i=1}^{n} w_i W_i$$

Weights `w_i` updated via Q-learning on prediction error.

---

## F-M01 — Day-Ahead Price ARIMA (Stochastic)

**Model:** [[M01]]  
**Layer:** Stochastic

$$P^{DA}_{t+1} = \alpha P^{DA}_t + \beta P^{ID}_t + \gamma X_t + \epsilon_t$$

| Parameter | Value | Calibration |
|---|---|---|
| α (AR term) | 0.72 | SEMO 2023 |
| β (MA term) | 0.31 | SEMO 2023 |
| γ (exogenous) | fitted | Carbon price, weather |

---

## F-O01 — Occupancy HMM (Stochastic)

**Model:** [[O01]]  
**Layer:** Stochastic

$$P_{occ}(t) = \sum_j \pi_j S_j(t)$$

States: Unoccupied=0, Partially Occupied=1, Full Occupancy=2  
Transition probabilities `π_j` learned from PIR/BLE data.

---

## F-K01 — Avoided Carbon (RL)

**Model:** [[K01]]  
**Layer:** RL/Q-Learning

$$CO_{2,avoided} = (E_{ref} - E_{use}) \times EF_{marginal}$$

| Parameter | Value | Source |
|---|---|---|
| EF_marginal | 0.295 kgCO₂/kWh | W-K01-EFMARG (SEAI 2024) |
| E_ref | baseline from L08 | Reference (pre-intervention) load |
| E_use | monitored from L01 | Actual load |

---

## F-F01 — Federated Averaging (AI/ML)

**Model:** [[F01]]  
**Layer:** AI/ML

$$\theta_{global} = \frac{1}{N} \sum_{i=1}^{N} \theta_i$$

N = number of edge nodes participating in federated round.  
For ODEON: N = active Jetson Nano nodes (up to 70+).

---

## F-G01 — Voltage Drop (Deterministic)

**Model:** [[G01]]  
**Layer:** Deterministic

$$V_{drop} = I \cdot Z_{line} = I(R_{line} + jX_{line})$$

Used for island detection threshold and feeder hosting capacity.

---

## F-P01 — Trading Revenue (RL)

**Model:** [[P01]]  
**Layer:** RL/Q-Learning

$$R_{total} = \sum_t \left[ E_{sell}(t) \cdot P_{sell}(t) - E_{buy}(t) \cdot P_{buy}(t) \right]$$

Prices from [[M-Series]]; volumes from [[C01]] dispatch.

---

## Determinization Bridge Formulas

### Quantile Method (C03 → C01, real-time)
Conservative upward flex: PV=p10, base_load=p90  
Conservative downward flex: PV=p90, base_load=p10

### Chance-Constraint (C03 → C01, day-ahead)
$$T_k \leq T_{max} \Rightarrow \mu_{T,k} + z_{1-\epsilon} \sigma_{T,k} \leq T_{max}$$

Where `ε=0.10` (90% reliability), `z_{0.9} = 1.28`.

### RC Coupling in C01 (from T01 params)
$$a = e^{-\Delta t / (R_{env} \cdot C_{zone})}$$
$$b_1 = R_{env}(1-a), \quad b_2 = (1-a), \quad b_3 = R_{env}(1-a)$$

---

## Response Derivative Definition

**Model:** [[C08]]

$$RD_i(t) = \frac{\partial \text{Flex}^{\uparrow}(t)}{\partial x_i(t)}$$

Shadow prices from C01 LP dual solution provide RD per input variable `x_i`.

| Asset | RD Interpretation |
|---|---|
| PV (x_i = G_POA) | ∂Flex↑/∂G_POA: how much flex changes per W/m² irradiance |
| Battery (x_i = SoC) | ∂Flex↑/∂SoC: how much flex changes per % SoC |
| Temperature (x_i = T_out) | ∂Flex↑/∂T_out: weather-conditioned responsiveness |
| Price (x_i = price_DA) | ∂Flex↑/∂price: market-conditioned responsiveness |
