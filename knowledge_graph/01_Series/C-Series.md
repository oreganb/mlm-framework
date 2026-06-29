---
type: series_index
ontology: MLM
series_id: C
series_title: Optimisation & Control
layer: rl
status: active
tags: [mlm, series:C, layer:rl, control, optimisation, dispatch]
aliases: [C-Series, Control Series, Optimisation Series]
sub_model_count: 10
id_range: C01–C10
---

# C-Series — Optimisation & Control

## Series Overview
The C-Series is the **decision-making engine** of the MLM. It determines optimal dispatch, scheduling, and coordination at building and community level. It receives all lower-layer outputs and produces control signals.

## Master Formula
$$\min_{\mathbf{x}_t} \alpha C_{energy}(t) + \beta C_{comfort}(t) + \gamma C_{carbon}(t)$$

Subject to: T-Series thermal constraints, B-Series SoC constraints, G-Series grid limits, R-Series generation bounds.

Where weights `α, β, γ` are user/policy preferences and sum to 1.

## Sub-Model Registry

| ID | Title | Method | Key Output |
|---|---|---|---|
| [[C01]] | MILP Flexibility Engine | MILP, LP | Flex↑/↓, dispatch schedule |
| [[C02]] | MPC Controller | MPC (rolling horizon) | u_HVAC, u_bat, setpoints |
| [[C03]] | Determinization Bridge | Quantile/Ensemble/CC | Deterministic inputs to C01 |
| [[C04]] | Q-Table Dispatch Policy | Q-learning | Action policy π(s) |
| [[C05]] | DQN Controller | Deep Q-Network | Continuous action policy |
| [[C06]] | Actor-Critic HVAC | Actor-Critic RL | u_HVAC continuous |
| [[C07]] | Multi-Agent Coordinator | MARL | Community-level dispatch |
| [[C08]] | Response Derivative Engine | RD synthesis | RD(t) per asset |
| [[C09]] | Fragmented Islanding Controller | Island detection + RD | Island dispatch policy |
| [[C10]] | Governance & Safety Layer | Rule-based gating | allow/derate/block signals |

## Series Inputs (from all lower layers)
- [[T-Series]]: T_in(t), comfort_ok, COP
- [[B-Series]]: SoC(t), E_stored, V2G_avail
- [[R-Series]]: P_PV(q), P_wind(q)
- [[W-Series]]: W_SCORE, ensemble Ω_w
- [[O-Series]]: occupancy_state, Q_occ
- [[M-Series]]: price_DA, carbon_intensity
- [[G-Series]]: grid_import_limit, V_bus, island_flag

## Series Outputs
- Dispatch schedule: P_HVAC(t), P_bat_chg(t), P_bat_dis(t), P_shed(t), P_shift(t)
- Flexibility signals: Flex↑(t), Flex↓(t) with uncertainty bands
- Control signals: u_HVAC, setpoints
- Response Derivative: RD(t) per asset
- Governance signals: allow/derate/block per action
