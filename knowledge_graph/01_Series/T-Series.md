---
type: series_index
ontology: MLM
series_id: T
series_title: Thermal Envelope & HVAC/Plant
layer: deterministic
status: active
tags: [mlm, series:T, layer:deterministic, thermal, hvac]
aliases: [T-Series, Thermal Series]
sub_model_count: 20
id_range: T01–T20
---

# T-Series — Thermal Envelope & HVAC/Plant

## Series Overview

The T-Series models heat transfer, HVAC efficiency, and thermal system response using RC (Resistor-Capacitor) network analogies. It is the **primary deterministic layer series** for building physics.

## Master Formula

**RC Thermal Network (generic):**
$$C \frac{dT_{in}}{dt} = \sum_k \frac{T_{in,k} - T}{R_k} + Q_{int} + Q_{solar} + Q_{HVAC}$$

Where:
- `C` = thermal capacitance (J/K), from building envelope properties
- `R_k` = thermal resistance of zone k (K/W)
- `Q_int` = internal heat gains (from O-Series)
- `Q_solar` = solar gains (from W-Series, W01)
- `Q_HVAC` = HVAC output (controlled variable)

## Sub-Model Registry

| ID | Title | Method | Key Output |
|---|---|---|---|
| [[T01]] | Building Thermal RC Model | RC network, ODE | T_in(t) zone temperatures |
| [[T02]] | HVAC COP Model | COP equations, regression | COP(T_in, T_out) |
| [[T03]] | Heat Pump Thermal Model | RC + COP combined | Q_HP, P_HP |
| [[T04]] | Solar Thermal Gain Model | RC + irradiance | Q_solar per zone |
| [[T05]] | Infiltration & Ventilation Model | RC infiltration | Q_inf, ACH |
| [[T06]] | Radiator Hydronic Model | Hydronic RC | Q_rad, T_flow |
| [[T07]] | PCM Thermal Storage Model | RC + phase change NTU | T_PCM, E_stored |
| [[T08]] | Hot Water Cylinder Model | RC stratified | T_DHW(z), Q_DHW |
| [[T09]] | Underfloor Heating Model | RC slab | T_floor, Q_UFH |
| [[T10]] | Thermal Comfort Index Model | PMV/PPD, operative temp | PMV, PPD |
| [[T11]] | Building Fabric Archetype Model | RC parameter lookup | R, C per archetype |
| [[T12]] | Inter-Zone Heat Transfer Model | Multi-zone RC | T_zone[n] |
| [[T13]] | High-Temp Heat Pump Model (HTHP) | COP high-T + RC | COP_HTHP, Q_HTHP |
| [[T14]] | Chiller Thermal Model | COP_chill + RC | Q_chill, P_chill |
| [[T15]] | Boiler Thermal Model | η-boiler + RC | Q_boiler, η |
| [[T16]] | Heat Exchanger Model (NTU-ε) | NTU-ε method | ε, Q_HX, T_out_HX |
| [[T17]] | Thermal Inertia & Lag Model | RC time-constant | τ_thermal, T_lag |
| [[T18]] | Setpoint Optimisation Thermal | LP with RC constraints | T_setpoint_optimal |
| [[T19]] | Building EPC Classification Model | RC → EPC label | EPC_label, U-values |
| [[T20]] | Surrogate Thermal Model | ANN replacing EnergyPlus | T_in(t) fast approximation |

## Series Inputs

| Variable | Symbol | Source Series | Dataset |
|---|---|---|---|
| Solar irradiance | G_POA | [[W-Series]] W01 | DS-WEATHER |
| Outdoor temperature | T_out | [[W-Series]] W02 | DS-WEATHER |
| Occupancy heat gain | Q_occ | [[O-Series]] O01 | DS-OCCUPANCY |
| Equipment heat gain | Q_equip | [[L-Series]] L04 | DS-ENERGY |
| Building parameters | R, C, U | — | DS-BUILDING |
| HVAC control signal | u_HVAC | [[C-Series]] C01 | — |

## Series Outputs → Consumed By

| Output | Symbol | Consumed By |
|---|---|---|
| Indoor temperature | T_in(t) | [[C-Series]] (comfort constraint), [[O-Series]] (occupant response) |
| HVAC power demand | P_HVAC | [[L-Series]] L03, [[B-Series]] (net load) |
| COP | COP(T_in,T_out) | [[C-Series]] (efficiency in objective) |
| Thermal comfort index | PMV, PPD | [[C-Series]], [[P-Series]] (KPI) |
| EPC label | EPC | [[K-Series]] (carbon compliance) |

## Dataset Dependencies
- **[[DS-BUILDING]]** — Fabric archetypes, U-values, R/C parameters, orientation, insulation
- **[[DS-WEATHER]]** — T_out, G_POA time series
- **[[DS-HVAC]]** — Equipment specs, rated COP, flow rates

## Related Series
- feeds_into: [[C-Series]], [[L-Series]], [[K-Series]]
- receives_from: [[W-Series]], [[O-Series]]
- bounded_by: [[G-Series]] (power limits)
