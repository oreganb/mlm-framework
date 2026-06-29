---
type: catalogue
ontology: EIG-MLM
catalogue_no: 58
catalogue_id: EIG-CAT-058
entity: objective
band: decision
status: active
tags: [eig-mlm, catalogue, band:decision, entity:objective]
aliases: ["EIG-CAT-058", "Catalogue 58", "objective"]
---

# CAT-58 — Objective

> **EIG-CAT-058** · band: **KPIs, Objectives & Decision Intelligence** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-21 tariff]] · [[CAT-28 carbon]] · [[CAT-57 kpi]] · [[CAT-59 constraint]]
**Used by (downstream):** [[CAT-12 asset_benchmark]] · [[CAT-44 model]] · [[CAT-60 decision_intelligence]] · [[CAT-62 control_strategy]] · [[CAT-66 reinforcement_learning]] · [[CAT-67 reward]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-058`
**Entity prefixes:** `EIG-OBJ-` (objectives), `EIG-OBJ-SET-` (objective sets)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (objective policy) / domain owners
**Depends on (upstream):** 21 tariff, 28 carbon, 57 kpi, 59 constraint
**Used by (downstream):** 44 SER-12 optimisation, 60 decision_intelligence, 62 control_strategy, 66 reinforcement_learning, 67 reward

---

## 1. Definition

The **Objective Catalogue** registers what the framework optimises *for*: objective definitions (`EIG-OBJ-*`) — machine-evaluable functions over KPIs (57) and registered quantities, each with priority, weight semantics, target, and constraint bindings (59); and **objective sets** (`EIG-OBJ-SET-*`) — the multi-objective bundles that optimisers (SER-12), decision intelligence (60), control strategies (62), and RL reward shaping (66/67) actually consume. Purpose: **optimisation and RL support** — every optimisation run and every reward function traces its goal structure to a versioned, governed set here; "what were we optimising for, with what trade-offs?" is always answerable (A1).

## 2. Scope
**In scope:** objective function schema; sense (min/max) and units; priority/weight semantics (weighted-sum, lexicographic, Pareto/ε-constraint); targets vs open-ended improvement; trade-off declarations; linkage to constraints (hard limits live in 59, never as soft weights here, A3); linkage to rewards (67 derives, never redefines); set composition and conflict analysis; scenario-dependent activation. **Out of scope:** solver mechanics (45 AC-OPT), constraint definitions (59), reward function engineering (67), KPI formulas (57).

## 3. Classification Structure

### 3.1 Objective domains (`domain`)

| Code | Domain | Canonical objectives |
|---|---|---|
| OD-COST | Economic | minimise energy cost (single pricing truth 21 V11), maximise flex revenue (25/27), minimise peak charges |
| OD-CO2 | Carbon | minimise tCO₂e (28 measured factors; marginal-factor dispatch per 28 A3), maximise low-CI consumption share |
| OD-CMF | Comfort/service | maximise comfort compliance (EIG-KPI-COMFORT), minimise service interruptions |
| OD-EN | Energy efficiency | minimise EUI, minimise losses |
| OD-FLX | Flexibility value | maximise deliverable flexibility (55 magnitudes), maximise RD evidence value (56 campaign objective) |
| OD-AST | Asset stewardship | minimise cycling wear (storage cycle cost, 11), maximise asset availability |
| OD-NET | Network | minimise feeder peak (13), voltage-band adherence support |

### 3.2 Combination methods (`combiner`) — for sets

| Code | Method | Semantics |
|---|---|---|
| CB-WS | Weighted sum | normalised objectives × weights, Σw=1; normalisation method declared (V4) |
| CB-LEX | Lexicographic | strict priority order; lower tiers optimised within tolerance of higher |
| CB-EPS | ε-constraint | one primary; others bounded (bounds are soft-targets here, hard limits stay in 59) |
| CB-PAR | Pareto | front generation for human choice (60 advisory mode) |

### 3.3 Activation contexts
Sets activate per context: normal operation, constraint-event (network stress 13), market-event (dispatch active), estimation mode (56 campaigns — evidence value weighted up). Context switching rules are part of the set (A5).

## 4. Hierarchy
```
Objective (EIG-OBJ-*) → Objective set (EIG-OBJ-SET-*: members + combiner + context rules)
                              → consumed by 44 SER-12 / 60 / 62 / 66-67
```

## 5. Field Groups & Fields

### 5.1 Objective (`EIG-OBJ-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | objectiveID | string | — | `EIG-OBJ-[A-Z0-9-]{2,20}` | M | ID |
| 2 | domain / name | enum/string | — | §3.1 | M | Identity |
| 3 | function | string | — | machine-evaluable over →57 KPI refs / 38 variables / 27 quantities | M | The quantity optimised (spec: Function) |
| 4 | sense | enum | — | minimise, maximise | M | Direction |
| 5 | unit | string | UCUM | — | M | Natural units (normalisation handled at set level) |
| 6 | target | json | — | {type: target_value \| open_ended, value?, deadline?} | M | Spec: Target |
| 7 | horizon | duration | — | optimisation window (PT24H dispatch … P10Y planning) | M | Temporal scope |
| 8 | constraintBindings | array<ref> | — | →59 constraint sets that always accompany this objective | M | Spec: Constraints (hard limits live there) |
| 9 | rewardLinkage | array<ref> | — | →67 reward components derived from this objective | M | Spec: Linked rewards (derivation direction: 58→67) |
| 10 | decisionLinkage | array<ref> | — | →60 decisions optimising this | M | Spec: Linked decisions |
| 11 | measurementKPI | ref | — | →57 (the scoreboard for this objective) | M | Outcome verification |
| 12 | owner / version / status | — | — | standard | M | Governance |

### 5.2 Objective set (`EIG-OBJ-SET-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | setID | string | `EIG-OBJ-SET-\d{4}` | M | ID |
| 2 | members | array<json> | [{objectiveRef, priority (int, spec: Priority), weight (spec: Weight, CB-WS), tolerance (CB-LEX), bound (CB-EPS)}] | M | Composition |
| 3 | combiner | enum | §3.2 + normalisation method | M | Trade-off semantics |
| 4 | contextRules | json | §3.3 activation + switching | M | When this set governs |
| 5 | conflictAnalysis | ref | trade-off study (54 sensitivities / Pareto exploration) | M | Documented tensions (A4) |
| 6 | scope | ref | subject/portfolio/platform (02/30/31) | M | Where applied |
| 7 | approvals / version / status | — | per 20 governance | M | Sets are policy — PI/board signed |

## 6. Controlled Vocabularies
Domains (§3.1), combiners (§3.2), senses, target types, activation contexts. Weight semantics fixed per combiner (no mixed interpretations).

## 7. Applicability Rules
- **A1 — Traceable goals.** Every SER-12 run, 60 automated decision, 62 strategy, and 66 reward function cites an ACTIVE set@version; goal-free optimisation is unauditable and forbidden.
- **A2 — Single value truths.** OD-COST uses the pricing library (21 V11); OD-CO2 uses registered factors and the single avoided-emissions method (28 A4); parallel valuations are audit findings.
- **A3 — Hard limits are constraints, not weights.** Safety, comfort floors, contract caps, regulatory bounds live in 59 and bind absolutely; encoding them as large weights is forbidden (weighted violations are still violations).
- **A4 — Trade-offs are explicit.** Sets with tensioned members (cost vs comfort, CO₂ vs cost) carry conflict analysis; weight choices are decisions with owners, not tuning accidents.
- **A5 — Context switching is governed.** Set switches (e.g. constraint-event mode) follow registered rules with logged transitions; mid-horizon silent re-weighting is forbidden.
- **A6 — Reward derivation discipline.** 67 reward components derive from objective functions (declared mapping); a reward without an objective ancestor is unanchored and inadmissible for 66 training.
- **A7 — Outcome verification.** Each objective's measurementKPI is tracked post-hoc: optimised-for vs achieved reported (75/77); persistent gaps trigger set review.

## 8. Validation Rules
V1 function references resolve (57/38/27); V2 unit/sense coherence; V3 set weights Σ=1 (CB-WS) / priorities total order (CB-LEX) / bounds units valid (CB-EPS); V4 normalisation method declared and recomputable; V5 constraintBindings resolve in 59 and accompany every consumption (60/62 runtime check); V6 rewardLinkage bidirectional with 67; V7 conflictAnalysis present for multi-domain sets; V8 context rules deterministic (no ambiguous activation); V9 sets immutable per version; changes supersede with consumer notice.

## 9. Relationships to Other Catalogues
57 (functions over KPIs; scoreboards) · 59 (inseparable constraint companions) · 60 (decision scoring consumes sets) · 62 (MPC/rule objectives) · 66/67 (reward derivation, A6) · 44 SER-12 (solver consumption) · 21/28 (value truths) · 54 (trade-off analysis inputs) · 89 (scenario-dependent sets) · 91 (policy objectives cascade to operational sets).

## 10. Benchmarking Requirements
Achieved-vs-optimised gap tracking per set (A7); cross-portfolio objective attainment league (cohort rules apply).

## 11. Dataset Requirements
Optimisation run logs (inputs, set version, solution) archived for reproducibility; planning-study sets packaged with 89 scenarios.

## 12. Feature Requirements
Objective-relevant signals (price forecasts FC-MKT, CI forecasts FC-CB, 41) feed optimisers; feature freshness rules (46 A2) apply at solve time.

## 13. Model Requirements
SER-12 optimisation models declare consumable combiners; infeasibility handling (59 conflicts) per 45 weakness guards.

## 14. KPI Requirements
measurementKPI mandatory per objective (V-level); set-level attainment dashboards (75) render member trade-offs.

## 15. Response Derivative Requirements
OD-FLX objectives consume RD-derived deliverable quantities (52 A5 arithmetic via 55 profiles); evidence-value objectives (56 campaigns) price RD coverage gaps — the framework optimises its own learning.

## 16. Decision Requirements
60 scoring formulas embed the active set; decisions log set@version + member values at decision time (auditability); Pareto sets route to human choice (advisory mode).

## 17. Ontology Mapping
Objective ↔ EIG `eig:Objective` with QUDT-typed function output; sets ↔ weighted collections; derivesReward → 67; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `Objective`, `ObjectiveSet`; edges `MEASURES_BY`→57, `BOUND_BY`→59, `DERIVES`→67, `GOVERNS`→60/62 decisions {context}. "What was being optimised when action X fired?" is one hop.

## 19. Digital Twin Mapping
Twin what-if optimisation runs (32) select sets explicitly; comparative runs across sets visualise trade-offs (Pareto fronts in twin UI).

## 20. Governance
Sets are policy artefacts: PI/board approval; weight changes logged with rationale; quarterly attainment reviews (A7). Domain objectives owned by domain leads.

## 21. Versioning
Objectives/sets semver; consumption always version-pinned. Catalogue 1.0.0.

## 22. Example Records

### EIG-OBJ-COST-DISPATCH — minimise dispatch-horizon energy cost
- domain OD-COST; function: Σ(consumption × tariff rate) − flex revenue over PT24H (21 pricing library, 27 revenue); sense min; unit EUR; target open_ended
- constraints: →59 comfort floors + contract caps set; reward linkage → 67 cost components; KPI: €/day effective; decisions: 60 dispatch scoring

### EIG-OBJ-SET-0003 — Aran residential dispatch set (governing EIG-EDGE-DEP-00231 strategy)
- members: COST-DISPATCH (priority 2, w 0.45) + CO2-MIN (priority 3, w 0.15) + COMFORT-MAX (priority 1, lexicographic guard: within tolerance 0 of comfort compliance) + AST-CYCLE-MIN (w 0.10) + FLX-REVENUE (w 0.30)
- combiner: CB-LEX(comfort) then CB-WS(rest, min-max normalised); contexts: normal / market-event (FLX weight ↑ per rules) / estimation mode (56 campaigns: evidence value term activates)
- conflict analysis: cost↔comfort study ref (54); approvals PI + co-op board (15 EIG-ORG-0044); scope EIG-FLX-PF-0007

### EIG-OBJ-NET-PEAK — minimise feeder evening peak (NZ-00017)
- function: max PT30M import at CP aggregate (13); ε-constraint companion in constraint-event context; KPI EIG-KPI-PEAK; consumed by 60 network-stress mode

## 23. Completion Criteria
☑ Objective/set split with all spec fields (function, priority, weight, target, constraints, linked rewards, linked decisions) ☑ Combiner semantics incl. lexicographic comfort guards ☑ Hard-limits-are-constraints rule ☑ Reward derivation discipline (58→67) ☑ Context switching governed ☑ Value-truth bindings (21/28) ☑ Worked Aran set chains to edge deployment and RD layer.
