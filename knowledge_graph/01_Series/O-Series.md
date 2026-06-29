---
type: series_index
ontology: MLM
series_id: O
series_title: Occupancy, Behaviour & DHW
layer: stochastic
status: active
tags: [mlm, series:O, layer:stochastic, occupancy, behaviour]
aliases: [O-Series, Occupancy Series]
sub_model_count: 15
id_range: O01–O15
---

# O-Series — Occupancy, Behaviour & DHW

## Master Formula
$$P_{occ}(t) = \sum_j \pi_j S_j(t)$$
Hidden Markov Model occupancy probability. `π_j` = transition probabilities, `S_j` = states from PIR/BLE/WiFi sensors.

## Sub-Model Registry

| ID | Title | Method | Key Output |
|---|---|---|---|
| [[O01]] | Occupancy Probability Model | HMM, PIR/BLE | P_occ(t), occ_state {0,1,2} |
| [[O02]] | Occupant Schedule Model | Markov chain | schedule_type, peak hours |
| [[O03]] | Thermal Comfort Feedback | PMV → override | comfort_override_flag |
| [[O04]] | DHW Demand Model | Statistical | Q_DHW(t), V_DHW(t) |
| [[O05]] | Appliance Use Model | Markov + weighting | P_appliance(t) |
| [[O06]] | Lighting Demand Model | Occupancy × daylight | P_lighting(t) |
| [[O07]] | EV Departure/Return Model | Markov | EV_state, SoC_on_return |
| [[O08]] | Occupant Heat Gain Model | Activity × count | Q_occ(W) |
| [[O09]] | Natural Ventilation Model | Window state Markov | ACH_natural |
| [[O10]] | Smart Appliance Response | Price signal → shift | P_shifted(t) |
| [[O11]] | Behavioural Flexibility Model | Survey data | flex_willingness score |
| [[O12]] | Holiday/Event Pattern Model | Calendar + events | holiday_flag, event_factor |
| [[O13]] | Household Archetype Model | Cluster classification | archetype_label |
| [[O14]] | Income-Energy Correlation | Regression | income_adjustment_factor |
| [[O15]] | Occupant Override Model | HMI signals | override_type, duration |

## Datasets
- **[[DS-OCCUPANCY]]** — PIR, BLE, WiFi occupancy readings; schedule surveys
- **[[DS-SMART-METER]]** — Consumption patterns as occupancy proxy
- **[[DS-CALENDAR]]** — Public holidays, school terms, sports events

## Population Weightings (for DS-OCCUPANCY generation)
| Attribute | Distribution |
|---|---|
| Household size | 1p:25%, 2p:35%, 3p:15%, 4p:15%, 5+:10% |
| Income | Low(<€30k):20%, Medium:50%, High(>€60k):30% |
| Employment | 9-5 worker:60%, Shift:30%, Retired:10% |
| Weekday pattern | Morning&Evening peak:50%, Steady:30%, Irregular:20% |

## Outputs → Consumed By
- P_occ(t) → [[T01]] (Q_int), [[L01]] (load scaling), [[C01]] (comfort gate)
- Q_occ(W) → [[T01]] (internal heat gain)
- comfort_override_flag → [[C10]] (governance)
- EV_state → [[B03]], [[B04]]