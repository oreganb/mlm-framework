---
type: series_index
ontology: MLM
series_id: L
series_title: Load & End-Use Decomposition
layer: deterministic
status: active
tags: [mlm, series:L, layer:deterministic, load, nilm]
aliases: [L-Series, Load Series]
sub_model_count: 13
id_range: L01–L13
---

# L-Series — Load & End-Use Decomposition

## Master Formula
$$P_{total}(t) = \sum_i P_i(t) + \epsilon_{NILM}(t)$$
Aggregated from appliance sub-models via NILM or sensor decomposition.

## Sub-Model Registry

| ID | Title | Key Output |
|---|---|---|
| [[L01]] | Net Load Model | P_net(t) = P_total - P_PV |
| [[L02]] | NILM Disaggregation | P_appliance[i](t) |
| [[L03]] | HVAC Load Model | P_HVAC(t) |
| [[L04]] | Appliance Load Model | P_appliance_total(t) |
| [[L05]] | Lighting Load Model | P_lighting(t) |
| [[L06]] | DHW Load Model | P_DHW(t) |
| [[L07]] | EV Charging Load | P_EV(t) |
| [[L08]] | Base Load Model | P_base(t) (non-controllable) |
| [[L09]] | Flexible Load Identification | flex_load_flags |
| [[L10]] | Load Forecasting Model | P_forecast[t+H] |
| [[L11]] | Peak Load Model | P_peak, peak_time |
| [[L12]] | Load Profile Clustering | archetype_load_profile |
| [[L13]] | Demand Response Load Model | P_DR_adjusted(t) |

## Outputs → Consumed By
- P_net(t) → [[C01]] (net PCC power), [[G01]] (grid loading)
- P_base(t) → [[C01]] (non-controllable baseline)
- flex_load_flags → [[C01]] (which loads can be shifted)