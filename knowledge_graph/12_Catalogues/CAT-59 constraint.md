---
type: catalogue
ontology: EIG-MLM
catalogue_no: 59
catalogue_id: EIG-CAT-059
entity: constraint
band: decision
status: active
tags: [eig-mlm, catalogue, band:decision, entity:constraint]
aliases: ["EIG-CAT-059", "Catalogue 59", "constraint"]
---

# CAT-59 — Constraint

> **EIG-CAT-059** · band: **KPIs, Objectives & Decision Intelligence** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-04 space]] · [[CAT-11 asset]] · [[CAT-13 utility_network]] · [[CAT-20 market]] · [[CAT-23 contract]] · [[CAT-29 flexibility_service]] · [[CAT-30 flexibility]] · [[CAT-36 device]] · [[CAT-92 regulation]]
**Used by (downstream):** [[CAT-12 asset_benchmark]] · [[CAT-44 model]] · [[CAT-58 objective]] · [[CAT-60 decision_intelligence]] · [[CAT-61 action]] · [[CAT-62 control_strategy]] · [[CAT-66 reinforcement_learning]] · [[CAT-68 penalty]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-059`
**Entity prefixes:** `EIG-CST-` (constraints), `EIG-CST-SET-` (constraint sets)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (constraint policy); domain authorities per class
**Depends on (upstream):** 04 space, 11 asset, 13 utility_network, 20 market, 23 contract, 29 flexibility_service, 30 flexibility, 36 device, 92 regulation
**Used by (downstream):** 44 SER-12, 58 objective, 60 decision_intelligence, 61 action, 62 control_strategy, 66 reinforcement_learning, 68 penalty

---

## 1. Definition

The **Constraint Catalogue** is the single register of binding limits: machine-evaluable conditions (`EIG-CST-*`) that decisions, actions, optimisations, and learned policies MUST satisfy, organised into **constraint sets** (`EIG-CST-SET-*`) resolved per subject and context. The seven mandated classes — physical, asset, comfort, market, regulatory, contract, safety — are enumerated with their authoritative sources. Purpose: **safe decision-making** — no optimiser solution, dispatch, control move, or RL action executes outside its resolved constraint set (A1), and violations are structurally prevented where possible, detected and penalised (68) where not.

Constraints bind absolutely. They are never weights (58 A3), never negotiable by confidence, and never relaxed by automation — relaxation is a governed human change to the register.

## 2. Scope
**In scope:** the seven constraint classes with field schemas; expression grammar (machine-evaluable, side-effect-free); enforcement points (structural exclusion, pre-action check, runtime guard, post-hoc audit); resolution (subject × context → effective set); precedence and conflict handling; temporary derogations (governed); violation handling handoff (63 events, 68 penalties). **Out of scope:** objective trade-offs (58), penalty magnitudes (68), action definitions (61), the source registries themselves (constraints *reference* 04/11/13/23/29/36/92 values, single-source rule A2).

## 3. Classification Structure

### 3.1 Constraint classes (`class`) — the mandated seven

| Code | Class | Authoritative source | Canonical examples |
|---|---|---|---|
| CC-PHY | Physical | physics/network state (13) | power balance; feeder thermal limits; voltage bands; storage SoC ∈ [min,max] |
| CC-AST | Asset | 11 registries + 36 device specs | rated power ceilings; ramp limits (maxRamp); min on/off times; cycle budgets; temperature operating windows |
| CC-CMF | Comfort | 04 zone bands + 23 contracted floors + 29 A7 | zone temp ≥ comfort floor (e.g. 19 °C EIG-CON-001207); CO₂ ceilings; DHW availability |
| CC-MKT | Market | 20/26 rules | gate-time feasibility; min/max bid sizes; prequalification states (22) |
| CC-REG | Regulatory | 92 register | metering/settlement rules; grid code obligations; GDPR processing bounds (37 A7) |
| CC-CON | Contract | 23 terms | event-frequency caps (≤2/week); notice periods; entitlement chain (23 A1); estimation allowances (56 A2) |
| CC-SAF | Safety | 36 interlocks + life-safety registers (04) | life-safety circuits never shed (lifeSafetyFlag, 04/36 V8); device interlocks on-device; lone-asset minimum service; METIS-128 quarantine exclusions (35 A4) |

Precedence (conflict resolution, V6): CC-SAF > CC-REG > CC-CMF ≥ CC-CON > CC-PHY/CC-AST > CC-MKT — safety and law outrank economics always; comfort and contract co-rank with contract unable to waive regulatory comfort minima.

### 3.2 Enforcement points (`enforcement`)

| Code | Point | Mechanism |
|---|---|---|
| EP-STRUCT | Structural | excluded from action/option space entirely (e.g. life-safety circuits absent from sheddable registers, 04 §5.5) |
| EP-PRE | Pre-action check | 60/61 gate before execution; fail ⇒ no action |
| EP-RT | Runtime guard | continuous monitor during action (62 supervisory layer; edge-local 35 A3); breach ⇒ abort + safe state |
| EP-POST | Post-hoc audit | settlement/operational audit (27/63); breach ⇒ 68 penalty + review |

Every constraint declares its strongest feasible enforcement point; EP-POST-only is permitted solely where physics prevents earlier enforcement (declared).

### 3.3 Hardness
HARD (absolute) is the default and the norm. SOFT exists only as *target bands* delegated to 58 objectives — anything registered here is HARD (the catalogue is the hard-limit register by definition; soft preferences do not belong here, A3).

## 4. Hierarchy
```
Class (CC-*) → Constraint (EIG-CST-*) → Set resolution (EIG-CST-SET-*: subject × context → effective list)
                                              → enforced at EP-* by 60/61/62/35/36
```

## 5. Field Groups & Fields

### 5.1 Constraint (`EIG-CST-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | constraintID | string | — | `EIG-CST-\d{5}` | M | ID |
| 2 | class | enum | — | §3.1 | M | Class |
| 3 | name / description | string | — | — | M | Human identity |
| 4 | expression | string | — | machine-evaluable predicate over registered variables/states (38/04/11/13/30), side-effect-free | M | The limit (16-grammar style) |
| 5 | sourceRef | ref | — | →04/11/13/20/23/29/36/92 record (single source, A2) | M | Authority; constraint mirrors, never redefines |
| 6 | parameters | json | UCUM units | bound values + refs (e.g. comfortFloor →23 clause) | M | Dimensioned |
| 7 | appliesTo | expr | — | subject selector (classification 01 / asset class 10 / contract 23) | M | Scope |
| 8 | contextActivation | json | — | always \| conditional (occupied state 03, season, network state 13) | M | When binding |
| 9 | enforcement | enum | — | §3.2 strongest feasible | M | How prevented |
| 10 | violationSeverity | enum | — | critical, major, minor → 63 severity / 68 penalty class | M | Breach handling |
| 11 | derogationPolicy | json | — | permitted? (CC-SAF: never), approver role, max duration, logging | M | Governed exceptions |
| 12 | owner / version / status | — | — | standard; class authority owns | M | Governance |

### 5.2 Constraint set resolution (`EIG-CST-SET-*`)
Resolved view per subject × context: effective constraints (refs@versions), resolution timestamp, source registry snapshot hashes. Resolution is deterministic and cached with invalidation on any source change (V5). Consumers (60/61/62/66) receive sets, never hand-pick constraints.

## 6. Controlled Vocabularies
Classes (§3.1 — closed), enforcement points (§3.2), severities, precedence order (frozen). Expression grammar shared with 16 permissions / 30 constraint algebra (machine-evaluable predicates, declared function whitelist).

## 7. Applicability Rules
- **A1 — Nothing executes outside its set.** 60 decisions, 61 actions, 62 strategies, SER-12 solutions, and 66 policies are feasible only within the resolved set; infeasible optimiser solutions are rejected, not "closest-fixed" silently.
- **A2 — Single source.** Constraints mirror authoritative registry values by reference; duplicated literals drift and are forbidden — comfort floors point at 23 clauses, ramp limits at 11 fields, life-safety at 04/36 registers.
- **A3 — Hard means hard.** No weight encoding (58 A3), no confidence-based relaxation, no RL exploration through constraints (66: action masking/shielding mandatory — constraint violation is not a learnable trade-off).
- **A4 — Safety is structural where possible.** CC-SAF prefers EP-STRUCT (option-space exclusion) and on-device interlocks (36 A6) over software checks; CC-SAF derogations: never.
- **A5 — Degraded-mode conservatism.** Edge degraded mode (35 A3) enforces the cached resolved set; staleness tightens, never loosens (unknown state ⇒ conservative bounds).
- **A6 — Conflict resolution by precedence.** Set conflicts resolve per §3.1 order with logged resolution; CC-MKT yielding to CC-CMF (e.g. comfort floor curtails a committed dispatch) triggers proper market handling (partial delivery per 22/27), never comfort violation.
- **A7 — Derogations are loud.** Temporary derogations: approver per policy, max duration, 63 event emitted, visible on dashboards (75), auto-expiry; silent exceptions don't exist.

## 8. Validation Rules
V1 expression parses, side-effect-free, whitelist-only functions; V2 parameters dimensioned and consistent with referenced registry units; V3 sourceRef resolves and values match (drift detector, A2); V4 appliesTo/contextActivation deterministic; V5 set resolution reproducible from snapshots; cache invalidation ≤ 5 min from source change; V6 precedence conflicts resolved and logged; V7 CC-SAF: EP-STRUCT/on-device where technically feasible, derogation=never; V8 derogations carry approver+expiry+event; V9 every 61 action definition references its constraint checks (bidirectional); V10 violation events (EP-RT/EP-POST) emit 63 + route 68.

## 9. Relationships to Other Catalogues
04/11/13/20/23/29/36/92 (authoritative sources) · 58 (objectives always travel with constraint bindings) · 60/61/62 (enforcement consumers) · 66 (action masking/shielding) · 68 (violation penalties) · 63 (violation/derogation events) · 30 (envelope algebra interoperates: envelopes are CC-AST/CC-CMF-derived feasible regions) · 27 (post-hoc settlement audits) · 35/36 (edge/device enforcement machinery).

## 10. Benchmarking Requirements
Violation-rate KPIs by class/enforcement point (EP-RT aborts, EP-POST findings); derogation frequency; resolution-cache latency.

## 11. Dataset Requirements
Resolved-set snapshots logged per automated decision (60 audit); violation event archives retained per regulatory horizons.

## 12. Feature Requirements
Constraint headroom features (41 FC-FLX: distance-to-limit) computed from resolved sets — decision-relevant, registered.

## 13. Model Requirements
SER-12 formulations import sets as solver constraints; SER-13/66 policies trained with masking; 44 V7 automated-coupling evidence includes constraint-adherence (47 MT-ENV).

## 14. KPI Requirements
Constraint-adherence KPIs (57 KF-PLT/KF-FLX); comfort-floor compliance is both CC-CMF enforcement and EIG-KPI-COMFORT measurement (same source values, A2).

## 15. Response Derivative Requirements
RD estimation events (56) run inside full sets (A1 — estimation never violates comfort/contract); RD saturation bounds (52) often *are* constraint-derived (comfort-floor-bound sustainable duration) — the linkage is recorded.

## 16. Decision Requirements
60: resolved set retrieval → EP-PRE gates → feasible option space → scoring (58) → action (61) with EP-RT guards; per-action audit logs set snapshot (49 explanations cite binding constraints — "shed limited to 2.1 kW by ramp limit").

## 17. Ontology Mapping
Constraint ↔ EIG `eig:Constraint` subclassed per CC-*; expressions ↔ SHACL-style shapes where applicable; sources via `prov:wasQuotedFrom`; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `Constraint`, `ConstraintSet`; edges `SOURCED_FROM`→registries, `BINDS`→subjects, `ENFORCED_AT` {EP}, `VIOLATED_IN`→63 events, `MASKS`→66 actions. "What limited this dispatch?" is the 49-explanation query.

## 19. Digital Twin Mapping
Twins render resolved sets as feasible-region overlays (envelope visualisations, 30); what-if runs flag constraint-binding segments; M4 twins carry the same enforcement stack as production (32 kill-switch interplay).

## 20. Governance
Class authorities own their classes (e.g. safety: engineering authority; contract: 23 owner); register changes follow source-registry governance; derogation policies reviewed annually; precedence order changes are constitutional (PI + board).

## 21. Versioning
Constraints semver tracking their sources; sets resolved-on-demand with snapshot provenance. Catalogue 1.0.0.

## 22. Example Records

### EIG-CST-00021 — comfort floor (CC-CMF, the EIG-CON-001207 floor)
- expression: `zone_temp_c(subject, t) >= comfort_floor_c` ; parameters: comfort_floor_c →23 clause (19 °C); appliesTo: contract-enrolled dwellings; activation: occupied state (03)
- enforcement EP-RT (62 supervisory + edge-local 35) with EP-PRE feasibility in dispatch planning; severity critical; derogation: occupant-initiated only (their own override ≠ derogation)

### EIG-CST-00088 — life-safety circuit exclusion (CC-SAF)
- expression: structural — life_safety circuits absent from sheddable option space (04 §5.5 register; 36 lifeSafetyFlag V8); enforcement EP-STRUCT + on-device interlock; derogation never; severity critical

### EIG-CST-00134 — heat pump ramp limit (CC-AST)
- expression: `|Δpower| / Δt <= maxRamp(asset)`; source →11 EIG-AST-004211 field; enforcement EP-RT on-device (36) + dispatch shaping (62); severity major → 68 wear-penalty class

### EIG-CST-00190 — event-frequency cap (CC-CON)
- expression: `events(subject, rolling P7D) < 2` (→23 EIG-CON-001207); enforcement EP-PRE at 56 ARMED (V1 there) and 60 scheduling; edge-side cache enforcement (EIG-EDGE-DEP-00231, 35)

## 23. Completion Criteria
☑ All seven mandated classes with authoritative-source bindings ☑ Precedence order (safety>regulatory>comfort≥contract>physical/asset>market) ☑ Enforcement-point taxonomy with structural-safety preference ☑ Hard-means-hard + RL masking + no weight encoding ☑ Single-source mirroring with drift detection ☑ Governed, loud derogations ☑ Examples chain to the contract, asset, and life-safety registers already in force.
