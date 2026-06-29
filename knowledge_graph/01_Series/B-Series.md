---
type: series_index
ontology: MLM
series_id: B
series_title: Battery, EV & Storage
layer: ai-ml
status: active
tags: [mlm, series:B, layer:ai-ml, battery, ev, storage]
aliases: [B-Series, Battery Series, Storage Series]
sub_model_count: 10
id_range: B01–B10
---

# B-Series — Battery, EV & Storage

## Series Overview
Models energy storage behaviour across stationary batteries and EVs, including State of Charge (SoC), State of Health (SoH), and Vehicle-to-Grid (V2G) interactions.

## Master Formula
$$E_{t+1} = E_t + \eta_{ch} P_{ch} \Delta t - \frac{P_{dis}}{\eta_{dis}} \Delta t$$

Where efficiency parameters `η_ch`, `η_dis` are bounded by T-Series thermal limits.

## Sub-Model Registry

| ID | Title | Method | Key Output |
|---|---|---|---|
| [[B01]] | Battery SoC Prediction Model | Coulomb counting + LSTM | SoC(t), E_stored |
| [[B02]] | Battery SoH Degradation Model | Cycle counting + Arrhenius | SoH(t), capacity_fade |
| [[B03]] | EV Arrival/Departure Model | Markov chain | arrival_time, departure_time |
| [[B04]] | EV Charging Demand Model | LSTM on trip patterns | P_EV_demand(t) |
| [[B05]] | V2G Availability Model | Bernoulli + SoC constraint | V2G_avail_flag |
| [[B06]] | Battery Lifetime MC Model | Monte Carlo degradation | E[lifetime], P(failure) |
| [[B07]] | Multi-Battery Coordination Model | MILP | P_bat_split between units |
| [[B08]] | Thermal Limit Battery Model | T-Series boundary | P_max(T_bat) |
| [[B09]] | Federated Battery Model | Federated averaging | θ_global_battery |
| [[B10]] | Battery Revenue Optimisation | LP + market price | E_arbitrage, R_bat |

## Dataset Dependencies
- **[[DS-BATTERY]]** — Battery specs: capacity, chemistry, C-rate, η_ch, η_dis
- **[[DS-EV]]** — EV trip logs, arrival/departure, SoC on return
- **[[DS-SMART-METER]]** — Net import/export validation

## Series Outputs → Consumed By
- SoC(t) → [[C01]] (SoC constraint), [[P01]] (arbitrage revenue)
- E_stored → [[C01]] flexibility headroom calculation
- V2G_avail_flag → [[C01]] (V2G dispatch decision)
- SoH → [[K-Series]] (carbon lifetime accounting)
