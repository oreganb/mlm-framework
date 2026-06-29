---
type: series_index
ontology: MLM
series_id: G
series_title: Grid, Microgrid & Protection
layer: deterministic
status: active
tags: [mlm, series:G, layer:deterministic, grid, microgrid, protection]
aliases: [G-Series, Grid Series]
sub_model_count: 12
id_range: G01–G12
---

# G-Series — Grid, Microgrid & Protection

## Master Formula
$$V_{drop} = I \cdot Z_{line} = I(R + jX)$$
Voltage drop using feeder impedance. Used for island detection and stability.

## Sub-Model Registry

| ID | Title | Key Output |
|---|---|---|
| [[G01]] | Feeder Power Flow Model | V_bus, P_grid_max |
| [[G02]] | Grid Import/Export Limits | grid_limit[direction] |
| [[G03]] | Frequency Deviation Model | Δf(t) |
| [[G04]] | Voltage Stability Model | V_pu, PQ_feasibility |
| [[G05]] | Power Factor Model | PF(t), reactive_Q |
| [[G06]] | Network Loss Model | P_loss(t) |
| [[G07]] | Microgrid Islanding Model | island_mode_flag |
| [[G08]] | Fragmented Islanding Model | island_segment_id |
| [[G09]] | Protection Scheme Model | trip_flag, recloser_state |
| [[G10]] | EV Grid Impact Model | V_impact(EV_charge) |
| [[G11]] | DER Hosting Capacity | max_DER_kW per feeder |
| [[G12]] | Community Energy Balance | EC_balance, sharing_ratio |

## Outputs → Consumed By
- P_grid_max → [[C01]] (hard import constraint)
- island_flag → [[C09]] (islanding controller)
- V_bus → [[G04]], [[G06]]