---
type: catalogue
ontology: EIG-MLM
catalogue_no: 69
catalogue_id: EIG-CAT-069
entity: context
band: rl_agents
status: active
tags: [eig-mlm, catalogue, band:rl_agents, entity:context]
aliases: ["EIG-CAT-069", "Catalogue 69", "context"]
---

# CAT-69 — Context

> **EIG-CAT-069** · band: **RL, Context & Agents** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-03 occupancy]] · [[CAT-09 weather_climate]] · [[CAT-13 utility_network]] · [[CAT-20 market]] · [[CAT-21 tariff]] · [[CAT-35 edge_infrastructure]] · [[CAT-38 data]] · [[CAT-52 response_derivative]]
**Used by (downstream):** [[CAT-44 model]] · [[CAT-46 forecast]] · [[CAT-55 response_profile]] · [[CAT-58 objective]] · [[CAT-60 decision_intelligence]] · [[CAT-62 control_strategy]] · [[CAT-66 reinforcement_learning]] · [[CAT-70 model_selection]] · [[CAT-71 learning_policy]] · [[CAT-72 agent]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-069`
**Entity prefixes:** `EIG-CTX-` (context definitions), `EIG-CTX-S-` (context state instances)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 03 occupancy, 09 weather_climate, 13 utility_network, 20/21 market/tariff, 35 edge_infrastructure, 38 data, 52 response_derivative (context axes)
**Used by (downstream):** 44 model (applicability), 46 forecast, 55 response_profile, 58 objective (context rules), 60 decision_intelligence, 62 control_strategy, 66 reinforcement_learning, 70 model_selection, 71 learning_policy, 72 agent

---

## 1. Definition

The **Context Catalogue** registers operating contexts: named, machine-evaluable situational states (`EIG-CTX-*`) — each defined by conditions over registered variables with thresholds — and the live context state (`EIG-CTX-S-*`) resolved per subject per moment. Contexts answer "what kind of situation is this?" so that models, decisions, policies, and strategies can be **context-aware** by reference rather than by each re-implementing situational logic: the same "winter evening peak, occupied, network-stressed" determination feeds 60 decisions, 70 model selection, 66 RL state, 62 mode rules, and 52 RD conditioning — computed once, consistently (A1).

This catalogue federates the context axes already in force — RD conditioning axes (52 §3.2), occupancy states (03), network states (13), market/price states (20/21), platform modes (35/62) — into one resolvable register with composition rules.

## 2. Scope
**In scope:** context definition schema (the spec's seven: ID, type, conditions, variables, thresholds, applicable models, applicable actions); context type taxonomy; composition (composite contexts from atomic ones); resolution semantics (per subject, per timestamp, deterministic); hysteresis/dwell rules (no context flapping); applicability registers (which models/actions activate per context); state instance logging; edge-resolvable subsets. **Out of scope:** the source variable definitions (38), domain state machines themselves (03/13/26/62 own their states — contexts reference them, single-source), RD context *gating* rules (52 owns transfer discipline; this catalogue supplies the cells).

## 3. Classification Structure

### 3.1 Context types (`contextType`)

| Code | Type | Source axes | Examples |
|---|---|---|---|
| CT-TMP | Temporal | calendar/TOD (52 CX-TOD) | evening_peak, weekend, holiday (41 is_holiday_ie alignment) |
| CT-CLM | Climatic | 09 bands (52 CX-SEASON) | winter, cold_snap, high_solar |
| CT-OCC | Occupancy | 03 states (52 CX-OCC) | occupied_responsive, unoccupied, guest_mode |
| CT-NET | Network | 13 states | feeder_constrained, normal, outage_adjacent |
| CT-MKT | Market/price | 20/21/26 | peak_price_band, dam_window_open, scarcity_signal |
| CT-AST | Asset state | 11/36 (52 CX-ASSET) | hp_normal_cop, storage_soc_low, device_degraded |
| CT-SYS | System/platform | 35/62/63 | mode_local, quarantine_active, degraded_inputs |
| CT-CMP | Composite | combinations | winter_evening_occupied_stressed (the RD cell pattern) |

### 3.2 Resolution semantics
Deterministic: given variable values at t, exactly one truth value per atomic context per subject; composites resolve from atomics (boolean algebra, 59 grammar). Hysteresis: entry/exit thresholds may differ (declared) with minimum dwell (no flapping — 62 §3.2 alignment). Unknown inputs ⇒ context UNKNOWN (never defaulted true/false) with conservative consumption rules (A4).

### 3.3 Applicability registers
Per context: applicableModels (44 refs whose applicabilityMatrix/assumptions hold here), applicableActions (61 refs sensible here), excludedActions. These are *advisory routing registers* — hard prevention stays in 59 (A3).

## 4. Hierarchy
```
Type (CT-*) → Atomic context (EIG-CTX-*) → Composite context (boolean composition)
                    └── Context state (EIG-CTX-S-*: subject × t → truth values + provenance)
```

## 5. Field Groups & Fields

### 5.1 Context definition (`EIG-CTX-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | contextID | string | `EIG-CTX-[A-Z0-9_-]{3,30}` | M | Spec: Context ID |
| 2 | contextType | enum | §3.1 | M | Spec: Context type |
| 3 | name / description | string | — | M | Identity |
| 4 | conditions | expr | machine-evaluable predicate (59 grammar) over §5.1.5 variables; composites: boolean over context refs | M | Spec: Conditions |
| 5 | variables | array<ref> | →38 EIG-VAR-* / domain state refs (03/13/26/62 — single-source) | M | Spec: Variables |
| 6 | thresholds | json | entry/exit values + units (hysteresis declared); dwell minimums | M | Spec: Thresholds |
| 7 | subjectGrain | enum | building/zone/asset/network/portfolio/platform | M | Resolution grain |
| 8 | applicableModels | array<ref> | →44 (advisory routing, §3.3) | M | Spec: Applicable models |
| 9 | applicableActions / excludedActions | array<ref> | →61 (advisory routing) | M | Spec: Applicable actions |
| 10 | rdAxisBinding | ref | →52 §3.2 axis + band where this context realises an RD conditioning cell dimension | C | RD-cell federation (A2) |
| 11 | edgeResolvable | json | resolvable from edge-local signals during MODE-LOCAL? (35 A3) + cached threshold refs | M | Degraded-mode availability |
| 12 | unknownPolicy | json | consumer behaviour on UNKNOWN (conservative defaults per consumer class) | M | A4 |
| 13 | owner / version / status | — | standard | M | Governance |

### 5.2 Context state (`EIG-CTX-S-*`)
subjectRef · timestamp · contextValues {contextID → TRUE/FALSE/UNKNOWN + entered-at} · inputProvenance (variable values + freshness) · resolverVersion · environment. Logged at state *transitions* (not per-tick) plus snapshots embedded in consumer records (60 instances, 66 tuples, 52 records).

## 6. Controlled Vocabularies
Types (§3.1), truth values {TRUE, FALSE, UNKNOWN}, dwell/hysteresis forms, unknown-policy classes {abstain, conservative_assume_worst, last_known ≤ bound}. Composite algebra: AND/OR/NOT over registered contexts only.

## 7. Applicability Rules
- **A1 — One resolver.** Context resolution is a single platform service (with edge-cached subset); consumers receive resolved states, never re-evaluate conditions locally (drift-by-reimplementation is the failure mode this kills).
- **A2 — RD-cell federation.** Contexts realising RD conditioning dimensions bind their axes (rdAxisBinding); 52 cells reference these contexts — the RD layer and the decision layer condition on *the same* situational truth.
- **A3 — Advisory routing, hard limits elsewhere.** applicableModels/Actions guide selection (70/60); they never substitute for constraints (59) or model applicability verdicts (01/44 A3) — a context can recommend, only constraints forbid.
- **A4 — UNKNOWN is honest.** Missing/stale inputs yield UNKNOWN with per-consumer conservative policies (60: abstain or worst-case; 66: tuple flagged; 62: MODE-CONSERV trigger) — contexts never silently default.
- **A5 — Transitions are events.** Operationally significant context transitions (declared subset) emit 63 events (e.g. feeder_constrained entry) — situational awareness rides the same nervous system.
- **A6 — Edge subset discipline.** Contexts consumed by edge decisions/strategies during MODE-LOCAL must be edge-resolvable (declared, V6) from local signals + cached thresholds; non-resolvable contexts go UNKNOWN locally with conservative handling.
- **A7 — Sandbox parity.** Sandbox resolution runs identical definitions against sandbox feeds (firewall-tagged states).

## 8. Validation Rules
V1 conditions parse (59 grammar); variables resolve (38/domain registries); V2 hysteresis well-ordered (entry/exit consistent with direction); dwell ≥ 0; V3 composites acyclic; reference registered contexts only; V4 rdAxisBinding consistency: bound contexts' bands match 52 spec band definitions (drift detector with 52 versions); V5 resolution determinism: same inputs ⇒ same truth values (property-tested); V6 edge-resolvable claims verified against 35 local signal availability; V7 applicability refs resolve (44/61) bidirectionally where those registers cite contexts; V8 state logging completeness at transitions; consumer snapshots match resolver output (audited); V9 UNKNOWN policies declared per consumer class; V10 definitions immutable per version; threshold changes are MINOR+ with consumer notice (52 cells, 60/62/66 configs).

## 9. Relationships to Other Catalogues
52 (RD cells federate here, A2) · 60/70 (context-aware decisions/selection) · 66/71/72 (RL state composition; policy validity domains) · 62 (mode rules consume CT-SYS; conservative triggers) · 58 (objective-set context switching, 58 A5) · 03/13/20/21/26/35 (source state machines — single-source referenced) · 44 (applicability routing alongside 01 verdicts) · 46 (forecast definitions per context where models differ) · 55 (profile context cells) · 63 (transition events) · 41 (context flags as registered features for models).

## 10. Benchmarking Requirements
Resolver health: resolution latency, UNKNOWN rates per context (input availability proxy), flapping incidents (dwell violations), edge-subset coverage.

## 11. Dataset Requirements
Transition logs as 38 entities; context-state joins packaged with experience/decision corpora (43) — every learning/decision dataset carries its situational truth.

## 12. Feature Requirements
Context truth values register as features (41, one-hot/flags) for model consumption — computed by the resolver, never re-derived in pipelines (A1).

## 13. Model Requirements
Model applicability (44 A3) evaluates conditional verdicts against resolved contexts; assumption monitors (44 A8) bind violatedIf expressions to context transitions where natural.

## 14. KPI Requirements
Context-conditional KPI views (57) slice by resolved contexts (e.g. delivery success in winter_evening cells) using the same truth values as operations — analytics and operations agree on what the situation was.

## 15. Response Derivative Requirements
The RD context schema (52 §3.2) resolves through this register (A2/V4): when an RD record says "winter, occupied, evening, 1–3 h", those are EIG-CTX truths with provenance — making RD conditioning auditable and the no-transfer rules (52 A6) mechanically checkable at citation time (60 A3).

## 16. Decision Requirements
60 evaluation snapshots embed resolved states; context-conditional action tables key on context refs; UNKNOWN triggers per-definition conservative paths (A4).

## 17. Ontology Mapping
Context ↔ EIG `eig:OperatingContext` with condition shapes; truth states ↔ temporal assertions; federation links to 03/13 state individuals; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `ContextDefinition`, transition-logged `ContextState`; edges `CONDITIONS_ON`→38 variables, `REALISES_AXIS`→52, `ROUTES`→44/61, `SNAPSHOT_IN`→60/66/52 records. "Under what situations does this model get selected?" is a register query.

## 19. Digital Twin Mapping
Twins display live context states per subject (32) and replay historical situational truth alongside telemetry; what-if runs set hypothetical contexts explicitly (labelled simulated states).

## 20. Governance
Owner: AI/ML Lead; threshold changes reviewed with affected consumers (52/60/62/66 owners); CT-OCC definitions jointly with occupancy governance (03/DPO); annual register review prunes unused contexts.

## 21. Versioning
Definitions semver; states are logs. Catalogue 1.0.0.

## 22. Example Records

### EIG-CTX-WINTER_EVENING_OCC — the RD-cell composite (CT-CMP)
- composition: CTX-SEASON_WINTER (CT-CLM, 09 band) ∧ CTX-EVENING_PEAK (CT-TMP, 17:00–20:00 local with DST discipline 26 V10) ∧ CTX-OCCUPIED_RESP (CT-OCC, 03 state ref)
- rdAxisBinding: realises CX-SEASON/CX-TOD/CX-OCC bands of EIG-RD-SPEC-THERM-SHED cells (V4 ✓ vs 52 @2.0)
- subjectGrain building; edge-resolvable ✓ (local clock + cached season + occupancy sensor); applicableModels [FLEXENV, THERM-SHED estimators]; transitions: not evented (routine)

### EIG-CTX-FEEDER_CONSTRAINED — network stress (CT-NET)
- conditions: 13 NZ state = constrained (single-source ref); thresholds: entry on DSO signal, exit + PT30M dwell; transitions evented (63 EF-NET); switches 58 sets to constraint-event mode (58 A5); routes 60 to network-priority action tables

### EIG-CTX-S example — embedded in EIG-DEC-I-00834712
- snapshot at 17:29:58Z: {WINTER_EVENING_OCC TRUE (entered 17:00), FEEDER_CONSTRAINED TRUE (entered 16:41), HP_NORMAL_COP TRUE, MODE_LOCAL FALSE}; provenance: inputs fresh ≤ PT2M; resolver @1.0

## 23. Completion Criteria
☑ All seven spec fields (ID, type, conditions, variables, thresholds, applicable models, applicable actions) ☑ Single-resolver rule with deterministic semantics + hysteresis/dwell ☑ UNKNOWN honesty with conservative consumption ☑ RD-cell federation making 52 conditioning auditable ☑ Advisory-routing vs hard-limit boundary ☑ Edge-resolvable subsets ☑ Worked composite chaining into the decision instance already on record.
