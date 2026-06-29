---
type: catalogue
ontology: EIG-MLM
catalogue_no: 62
catalogue_id: EIG-CAT-062
entity: control_strategy
band: decision
status: active
tags: [eig-mlm, catalogue, band:decision, entity:control_strategy]
aliases: ["EIG-CAT-062", "Catalogue 62", "control_strategy"]
---

# CAT-62 — Control Strategy

> **EIG-CAT-062** · band: **KPIs, Objectives & Decision Intelligence** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-11 asset]] · [[CAT-24 schedule]] · [[CAT-35 edge_infrastructure]] · [[CAT-44 model]] · [[CAT-58 objective]] · [[CAT-59 constraint]] · [[CAT-60 decision_intelligence]] · [[CAT-61 action]] · [[CAT-66 reinforcement_learning]]
**Used by (downstream):** [[CAT-32 digital_twin]] · [[CAT-53 response_behaviour]] · [[CAT-63 event]] · [[CAT-70 model_selection]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-062`
**Entity prefixes:** `EIG-CTL-` (strategies), `EIG-CTL-I-` (strategy deployments)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** Operations with EIG AI/ML Lead
**Depends on (upstream):** 11 asset, 24 schedule, 35 edge_infrastructure, 44 model, 58 objective, 59 constraint, 60 decision_intelligence, 61 action, 66 reinforcement_learning
**Used by (downstream):** 32 digital_twin (M4), 53 response_behaviour, 63 event, 70 model_selection

---

## 1. Definition

A **control strategy** (`EIG-CTL-*`) is a standing, closed-loop policy that continuously governs target assets/zones: the layer that runs *between* discrete decisions (60) — sensing inputs, computing control moves at control timescales, and invoking actions (61) under constraint guards (59). The catalogue supports the three mandated control types — **rule-based**, **MPC (model predictive control)**, and **RL** — under one schema: strategy ID, control type, target assets, inputs, constraints, optimisation logic, and fallback logic, plus the supervisory architecture that keeps all three safe. Deployments (`EIG-CTL-I-*`) bind a strategy version to targets at a site/edge node (35).

## 2. Scope
**In scope:** control type taxonomy and their specific obligations (rule sets, MPC formulations, RL policy bindings); the universal supervisory layer (constraint guards, watchdogs, mode ladder); input/actuator bindings; timescale discipline; fallback hierarchies; deployment and handover records; interaction with discrete decisions (60 owns event-scale choices, strategies own continuous regulation — boundary explicit); bumpless transfer between modes. **Out of scope:** action mechanics (61), policy training (66/50 RK-RL), MPC solver internals (45 AC-OPT), thermal models themselves (44 SER-01).

## 3. Classification Structure

### 3.1 Control types (`controlType`) — the mandated three

| Code | Type | Logic core | Obligations |
|---|---|---|---|
| CT-RULE | Rule-based | ordered condition→move rules (59 grammar) | complete state coverage (V4); rules inspectable (49 XM-INTR) |
| CT-MPC | Model predictive | receding-horizon optimisation: model (44 SER-01/06) + objective set (58) + constraints (59) over horizon | model fidelity coupling (32/47); infeasibility handling declared; solve-time budget |
| CT-RL | Reinforcement learning | promoted policy (66) with action masking | sandbox-trained only (66 gates); frozen exploitation in production; shielded by supervisory layer always |

Hybrid stacks declare layers (e.g. RL setpoint selection over MPC tracking over rule safety net).

### 3.2 Mode ladder (universal, per deployment)

| Mode | Authority | Trigger |
|---|---|---|
| MODE-FULL | strategy logic active | normal, all evidence fresh |
| MODE-CONSERV | tightened bounds, simplified logic | input quality drop (40), forecast staleness (46), comms degradation (34) |
| MODE-FALLBACK | declared fallback logic (typically CT-RULE safety set) | logic failure, solve timeout (MPC), policy anomaly (RL), evidence expiry (47) |
| MODE-LOCAL | edge degraded-autonomous | platform unreachable (35 A3): cached constraints, localAuthorityScope |
| MODE-SAFE | hold safe state, actions suspended except AF-PROT | guard breach, watchdog, quarantine (35 A4), kill-switch |

Transitions logged (63); downward transitions automatic, upward require condition clearance + dwell time (no flapping, V7). Bumpless transfer: state handover defined per transition (A5).

### 3.3 Timescales
Control loop periods: TS-FAST (PT1S–PT1M, frequency/power tracking), TS-STD (PT1M–PT15M, thermal/storage), TS-SLOW (PT15M–PT1H, scheduling adherence). Loop period ≤ ⅒ dominant process time constant where applicable (04 τ); declared per strategy.

## 4. Hierarchy
```
Strategy (EIG-CTL-*: type + logic + supervisory spec)
  └── Deployment (EIG-CTL-I-*: strategy@version × targets × node 35)
        └── Mode state (ladder §3.2) → action invocations (61) → behaviour capture (53)
```

## 5. Field Groups & Fields

### 5.1 Strategy (`EIG-CTL-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | strategyID | string | — | `EIG-CTL-[A-Z0-9-]{2,24}` | M | Spec: Strategy ID |
| 2 | controlType | enum | — | §3.1 (+ hybrid layer declaration) | M | Spec: Control type |
| 3 | targetSelector | expr | — | asset/zone classes (10/04) + per-deployment binding | M | Spec: Target assets |
| 4 | inputs | array<ref> | — | →38 variables (live, 36 sensors), →46 forecasts (freshness rules), →59 resolved set, →52/55 (envelope/profile state) | M | Spec: Inputs — registered only |
| 5 | constraintSetRule | ref | — | →59 resolution; supervisory guards derive from it | M | Spec: Constraints |
| 6 | optimisationLogic | json | — | per type: CT-RULE ruleset ref (versioned); CT-MPC {modelRefs 44, objectiveSet 58@v, horizon, period, solver 45, infeasibility policy}; CT-RL {policyRef 66@v, mask spec, anomaly detector} | M | Spec: Optimisation logic |
| 7 | controlOutputs | array<ref> | — | →61 action definitions invocable (closed list; strategy listed in their triggers) | M | Effectors |
| 8 | timescale | enum+json | — | §3.3 + loop period | M | Discipline |
| 9 | supervisorySpec | json | — | guards (59 EP-RT monitors), watchdogs (loop liveness, output sanity bounds), mode ladder parameters (§3.2 thresholds, dwell times) | M | The safety frame (A1) |
| 10 | fallbackLogic | json | — | MODE-FALLBACK ruleset (self-contained, no external dependencies) + MODE-LOCAL subset (35-cached) | M | Spec: Fallback logic |
| 11 | decisionInterface | json | — | which 60 decisions set strategy targets/modes (e.g. dispatch decisions set shed targets the strategy tracks) | M | 60/62 boundary (A2) |
| 12 | edgeProfile | json | — | edge-executable? resource envelope (35 A7), cached-set refresh cadence | C | Edge fit |
| 13 | validationBinding | ref | — | →47 VM-CL evidence (mandatory for production deployment) | M | Safety evidence |
| 14 | owner / version / status | — | — | standard | M | Governance |

### 5.2 Deployment (`EIG-CTL-I-*`)
strategyRef@version · targets (resolved) · node (→35 EIG-EDGE-NODE / 31 service) · activeMode + mode history (63-evented) · parameter overrides (bounded, governed) · commissionedAt / handover record (commissioning checklist: sensor verification 36, guard tests, fallback drills) · status. Mode/state telemetry streams to 38.

## 6. Controlled Vocabularies
Control types (§3.1), modes (§3.2 — frozen ladder), timescales (§3.3), infeasibility policies {hold, shrink-horizon, fallback}, RL anomaly responses. Rule grammar = 59 expression grammar.

## 7. Applicability Rules
- **A1 — Supervisory layer is universal.** Every strategy, regardless of type, runs inside the guard/watchdog/mode-ladder frame; CT-RL especially never actuates unshielded (59 A3 masking + supervisory guards — defence in depth).
- **A2 — Decision/strategy boundary.** 60 makes event-scale choices (dispatch yes/no, targets, mode requests); strategies regulate continuously toward those targets. Strategies never originate market commitments or contract-relevant events — they execute and track.
- **A3 — Registered inputs and outputs only.** Inputs from 38/46/59/52/55 registers; outputs via 61 actions with the strategy on their trigger lists; side-channel sensing/actuation forbidden.
- **A4 — MPC fidelity coupling.** CT-MPC model references couple to fidelity status (32/47): model degradation ⇒ MODE-CONSERV; solve timeout ⇒ declared infeasibility policy then MODE-FALLBACK; horizons respect forecast validity (46 A2).
- **A5 — Bumpless transfer.** Mode and version transitions hand over state (integrator states, setpoint trajectories) per declared transfer rules — no actuator steps from bookkeeping.
- **A6 — Fallback self-sufficiency.** MODE-FALLBACK logic is dependency-free (no forecasts, no platform calls, no learned components): cached constraints + local sensing + rule set, provably within comfort/safety bounds (47 drill evidence).
- **A7 — Version promotion = VM-CL.** New strategy versions on production targets require closed-loop validation (47), staged rings (35 A6 pattern), and shadow comparison where feasible (run-in-parallel scoring before authority transfer).
- **A8 — Sandbox parity.** NEST strategies run identical stacks against sandbox twins (32 A6); promotion moves logic via the handshake, never trained-on-production shortcuts for CT-RL (66 chain).

## 8. Validation Rules
V1 type-specific logic completeness (CT-RULE coverage; CT-MPC formulation refs resolve; CT-RL policy promoted+masked); V2 inputs/outputs registered, bidirectional trigger links (61); V3 supervisory guards derived from the constraint set (no unguarded constraints at EP-RT); V4 rule/state coverage total (no undefined regions); V5 loop period vs process τ discipline; V6 fallback dependency-freedom statically verified; V7 mode transitions: dwell times enforced, flapping detector; V8 deployments carry commissioning records + VM-CL refs; V9 mode/version history complete (63 events); V10 edge deployments within resource envelopes, cached-set freshness monitored (59 A5).

## 9. Relationships to Other Catalogues
60 (boundary A2: targets/mode requests in, tracking out) · 61 (effectors; trigger lists) · 59 (guards; cached sets) · 44/45 (MPC models/solvers; SER-01 thermal cores) · 66 (RL policies under shields) · 46 (forecast inputs with freshness) · 35/36 (edge execution; sensors/actuators; degraded mode alignment) · 47 (VM-CL promotion; fallback drills) · 53 (control behaviour observed; oscillation/wear analysis) · 32 (twin-hosted strategies; M4 = production strategy on twin authority) · 24 (schedule adherence strategies) · 63 (mode/guard events).

## 10. Benchmarking Requirements
Control performance per deployment: tracking error, constraint-guard activation rate, mode dwell distribution, actuator duty (11 wear interface), comfort compliance under control (57). Strategy version comparisons via shadow scoring (A7).

## 11. Dataset Requirements
Control telemetry (setpoints, moves, modes) as 38 entities; commissioning/drill records retained for deployment lifetime.

## 12. Feature Requirements
Control-state features (41): mode flags, tracking error stats, guard-activation counts — for 60 context and 70 reviews.

## 13. Model Requirements
CT-MPC internal models are 44 releases under full discipline (47/50/51); strategy-embedded models never fork from the register.

## 14. KPI Requirements
EIG-KPI-COMFORT under control authority; energy/cost attainment vs 58 set; guard-activation KPI as leading safety indicator.

## 15. Response Derivative Requirements
Strategies execute dispatch tracking shaped by RD arithmetic (52 A5 via 60 targets); control telemetry during events is the raw material of 53 observations; strategy-induced oscillations contaminating RD evidence are a V-finding (event windows flag controller-mode context).

## 16. Decision Requirements
Per A2: 60 sets targets/modes; strategies report attainment + abstain-equivalent (MODE-FALLBACK) states back into decision context; protective decisions (DC-PROT) may command MODE-SAFE directly.

## 17. Ontology Mapping
Strategy ↔ EIG `eig:ControlStrategy` with type individuals; modes ↔ state machine pattern; SAREF `saref:Profile/Command` alignment at the effector edge; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `ControlStrategy`, `StrategyDeployment`; edges `CONTROLS`→targets, `SENSES`→38, `INVOKES`→61, `GUARDED_BY`→59, `EMBEDS`→44/66, `IN_MODE` {history}→63, `VALIDATED_BY`→47.

## 19. Digital Twin Mapping
Twins host strategy shadows (A7 comparison) and pre-deployment soak tests; M4 twins (32) are deployments where the twin holds control authority — same register, same supervisory frame, kill-switch live.

## 20. Governance
Owners: Operations (deployments) + AI/ML Lead (logic); commissioning sign-off per deployment; fallback drills semi-annual (47 evidence refresh); CT-RL deployments additionally reviewed at the model board.

## 21. Versioning
Strategies semver (logic ⇒ MINOR+, guard/fallback changes ⇒ MAJOR); deployments track version history. Catalogue 1.0.0.

## 22. Example Records

### EIG-CTL-RES-THERM-01 — residential thermal control (hybrid, the EIG-EDGE-DEP-00231 strategy)
- type: CT-MPC core (R2C2 EIG-MDL-SPEC-THERM-R2C2 + envelope FLEXENV-01; objective EIG-OBJ-SET-0003; horizon PT12H, period PT5M, TS-STD) over CT-RULE safety net
- inputs: zone temps (36 sensors), weather forecast FC (46), tariff (21), resolved set (00021/00134/00190), dispatch targets from EIG-DEC-FLEX-DISPATCH-RES
- outputs: [ACT-SETPOINT-ADJ, ACT-SHED-HP tracking, ACT-RESTORE-BASELINE]; supervisory: comfort RT-guard, ramp guard, loop watchdog 3×period
- fallback: rule set {if T < floor+0.5 → heat full; schedule-following otherwise} — dependency-free ✓; MODE-LOCAL subset cached on edge (35 A3)
- validation: VM-CL within EIG-VAL-00077 scope; deployment: node EIG-EDGE-NODE-0007, commissioned with sensor verification + fallback drill; mode history evented

### EIG-CTL-STO-SITE-01 — site battery (CT-RL over rules)
- RL policy 66-promoted, masked to SoC/ramp/CC-MKT-feasible actions; supervisory caps cycling per AST-CYCLE-MIN objective interplay; MODE-FALLBACK: tariff-band rule charging; shadow-scored 30 d before authority (A7)

### Mode transition example
- 2026-02-07: Starlink failover lag (34 EIG-COM-CHN-00041) → MODE-LOCAL 41 min (cached set, scope-limited shed tracking continued) → upward transition after dwell PT10M clearance; 63 events + replay log (35) complete

## 23. Completion Criteria
☑ All spec fields (strategy ID, control type, targets, inputs, constraints, optimisation logic, fallback logic) ☑ Rule/MPC/RL under one supervisory frame ☑ Mode ladder with bumpless transfer + flapping control ☑ Dependency-free fallbacks, drilled ☑ Decision/strategy boundary explicit ☑ Edge degraded-mode alignment ☑ Worked hybrid residential strategy closing the Aran control chain.
