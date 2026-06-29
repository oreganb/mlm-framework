---
type: series_index
ontology: MLM
series_id: R
series_title: Renewables & DER
layer: deterministic
status: active
tags: [mlm, series:R, layer:deterministic, renewables, pv, der]
aliases: [R-Series, Renewables Series]
sub_model_count: 10
id_range: R01–R10
---

# R-Series — Renewables & DER

## Series Overview
Models renewable energy generation from PV, wind, and hybrid sources. Outputs feed directly into the net power balance at the point of common coupling (PCC).

## Master Formula (PV)
$$P_{PV} = G_{POA} \cdot A \cdot \eta_{PV} \cdot [1 - \beta(T_{cell} - 25)]$$

## Sub-Model Registry

| ID | Title | Method | Key Output |
|---|---|---|---|
| [[R01]] | PV Generation Model | Irradiance model + temp coefficient | P_PV(t) (kW) |
| [[R02]] | Wind Generation Model | Power curve + Weibull distribution | P_wind(t) (kW) |
| [[R03]] | PV Degradation Model | Linear degradation + soiling factor | η_effective over time |
| [[R04]] | MPPT Model | P&O algorithm surrogate | P_MPPT, V_mpp |
| [[R05]] | Inverter Efficiency Model | Efficiency curve | P_AC from P_DC |
| [[R06]] | PV Curtailment Model | Grid limit rules | P_curtailed |
| [[R07]] | Micro-Wind Turbine Model | Site-specific power curve | P_micro_wind |
| [[R08]] | Hybrid PV+Battery Model | Combined R01 + B01 | P_net_der |
| [[R09]] | DER Availability Model | Stochastic up-time factors | avail_flag, Bernoulli |
| [[R10]] | Self-Consumption Optimisation | LP dispatch | SC_ratio, E_self |

## Dataset Dependencies
- **[[DS-PV]]** — Panel specs: area, rated efficiency, temperature coefficient, azimuth, tilt
- **[[DS-WEATHER]]** — G_POA, T_out, wind speed
- **[[DS-INVERTER]]** — Inverter efficiency curves, rated capacity

## Series Outputs → Consumed By
- P_PV(t) → [[L-Series]] (net load), [[B-Series]] (charging decision), [[C-Series]] (net PCC power)
- P_wind(t) → [[L-Series]], [[C-Series]]
- avail_flag → [[C-Series]] (stochastic DER availability)
