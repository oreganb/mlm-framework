---
type: response_derivative_map
ontology: MLM
version: 4.0
status: active
tags: [mlm, response-derivative, rd, sensitivity, governance]
aliases: [Response Derivative Map, RD Map, RD Engine Reference]
---

# Response Derivative Map v4

## Theoretical Foundation

Response Derivative (RD) — **Brian O'Regan's original theoretical contribution** — extends the mathematical derivative concept to measure conditional responsiveness in energy systems. Rather than measuring static capacity, RD measures *how much useful response* is expected from a given stimulus, under constraints and uncertainty.

**Canonical definition (model C08):**
$$RD_i(t) = \frac{\partial \text{Flex}^{\uparrow}(t)}{\partial x_i(t)} = \lambda_i^*(t)$$

Where $\lambda_i^*$ is the shadow price (dual variable) from the C01 LP optimisation — automatically produced by any LP solver.

## RD Lifecycle Across Layers

| Step | Layer | Action | Series |
|---|---|---|---|
| 1 | Deterministic | Establish hard feasibility bounds (physics, regulation) | T, G, R, L |
| 2 | Stochastic | Estimate probabilistic sensitivities per input variable | W, O, M |
| 3 | AI/ML | Refine non-linear and latent sensitivities | D, F, B |
| 4 | RD Synthesis | Combine into unified RD with uncertainty bands | C08 |
| 5 | RL | Evaluate candidate actions using RD | C05, C09 |
| 6 | RL | Govern: allow (RD≥0.1), derate (RD<0.1), block (RD=0 or storm) | C10 |
| 7 | Feedback | Update trust, weights, model versions via observed outcomes | D08, F01 |

## Canonical RD Object Fields

Every RD estimate produced by C08 carries these fields:

| Field | Type | Description |
|---|---|---|
| `stimulus_id` | string | Which input variable x_i (e.g. `G_POA`, `SoC`, `T_out`, `price_DA`) |
| `response_variable` | string | `Flex_up` or `Flex_down` |
| `expected_response` | float | ∂Flex/∂x_i — shadow price from C01 LP dual |
| `upper_bound` | float | Max RD from deterministic feasibility bounds |
| `lower_bound` | float | Zero (cannot have negative marginal flexibility) |
| `confidence` | float 0–1 | Confidence from D-Series data quality weight |
| `uncertainty` | float | σ from stochastic layer ensemble spread |
| `locality_scope` | string | `building` / `segment` / `community` (G08 fragmentation) |
| `validity_window` | string | `60min` / `3h` / `24h` per determinization mode |
| `constraint_status` | string | `binding` (RD=0) / `slack` (RD>0) / `derating` |
| `action_recommendation` | string | `allow` / `derate(α=0.5)` / `block` |

## Per-Series RD Contribution

| Series | RD Role | Key Variables | Consumed By |
|---|---|---|---|
| W-Series | Weather-conditioned RD | ∂Flex/∂G_POA, ∂Flex/∂T_out, ∂Flex/∂v_wind | C08 synthesis |
| O-Series | Behaviour-conditioned RD | ∂Flex/∂occ_state, ∂Flex/∂comfort_override | C08, C10 |
| M-Series | Market-conditioned RD | ∂Flex/∂price_DA, ∂Flex/∂EF | C08, P07 |
| T-Series | Thermal feasibility bounds | T_in ∈ [T_min,T_max] constrains Flex↑ | C08 upper_bound |
| R-Series | DER-specific RD | ∂Flex/∂P_PV, ∂Flex/∂P_wind | C08 |
| L-Series | Load-specific RD | ∂Flex/∂P_base, ∂Flex/∂P_flex | C08 |
| G-Series | Grid topology RD | Island scope limits RD locality | C09 |
| B-Series | Storage RD | ∂Flex/∂SoC — largest single RD contributor | C08 |
| C08 | RD synthesis engine | Aggregates all above into governed RD object | C05, C09, C10, P07 |
| D-Series | RD confidence weighting | data_quality_flag → confidence field | C08 |
| F-Series | Cross-site RD transfer | Cross-building RD patterns via federated learning | C08 |
| K-Series | Carbon-conditioned RD | ∂Flex/∂EF impacts carbon weight γ | C08, K04 |
| P07 | Portfolio RD KPI | Capacity-weighted community RD score | Dashboard |

## Governance Logic (C10)

```
IF storm_flag OR trip_flag:
    action = BLOCK (u_governed = 0)
ELIF RD_i ≥ 0.10:
    action = ALLOW (u_governed = u_proposed)
ELIF 0.01 ≤ RD_i < 0.10:
    action = DERATE (u_governed = 0.5 × u_proposed)
ELSE (RD_i < 0.01):
    action = BLOCK (asset provides negligible response)
```

## RD in Islanding Context (C09)

Fragmented islanding invalidates community-level RD — G08 identifies which buildings can form viable island segments, and C09 recomputes RD within segment scope. RD is not topology-agnostic (key distinguishing feature from traditional demand response metrics).

## Navigation
- [[02_Models/C_Models/C08]] — RD synthesis engine
- [[02_Models/C_Models/C09]] — Islanding controller consuming RD
- [[02_Models/C_Models/C10]] — Governance layer applying RD rules
- [[02_Models/P_Models/P07]] — Portfolio RD KPI
- [[07_Relationships/Cross-Series Dependencies]] — Full dependency graph
- [[00_Core/Architecture/MLM Architecture v3]] — Four-layer cognitive stack
