---
type: catalogue
ontology: EIG-MLM
catalogue_no: 32
catalogue_id: EIG-CAT-032
entity: digital_twin
band: flexibility
status: active
tags: [eig-mlm, catalogue, band:flexibility, entity:digital_twin]
aliases: ["EIG-CAT-032", "Catalogue 32", "digital_twin"]
---

# CAT-32 — Digital Twin

> **EIG-CAT-032** · band: **Flexibility & Platform** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID prefix:** `EIG-DT`
**Catalogue number:** 32 of 94
**Layer:** Flexibility & Platform
**Version:** 1.0.0
**Status:** ACTIVE

---

## 1. Definition

The Digital Twin Catalogue defines **digital twin instances**: governed, synchronised virtual representations of a physical entity (building, asset, space, network zone, community, market system) hosted on a twin platform (31), instantiated from a template (33), fed by declared data sources (37/38), running declared models (44), and carrying explicit synchronisation, fidelity and status declarations. A twin is not a dashboard and not a model: it is a stateful representation contract — *this virtual object tracks that physical object, at this fidelity, with this latency, for these purposes*.

The catalogue covers the full maturity ladder: **static** (geometry/configuration mirror), **operational** (live state synchronisation), **predictive** (forecast/what-if capable), **autonomous** (closed-loop within authority envelopes).

## 2. Scope

**In scope:** twin instance records; twin↔physical-entity binding (exactly one per twin); maturity/type classification; data-source bindings with sync contracts; model bindings; fidelity declarations and verification; state variable registers; status lifecycle; twin-of-twins composition (building twins inside community twins); sandbox twins (NEST).

**Out of scope:** template definitions (33), platform hosting facts (31), the models themselves (44), simulation scenarios (89), control execution authority (granted via 02 ladder + 16 roles, exercised by 62).

## 3. Classification Structure

`twinMaturity` (primary axis, ladder):

| Code | Maturity | Capability |
|---|---|---|
| DT-M1 | static | Configuration/geometry mirror; no live data |
| DT-M2 | operational | Live state sync at declared latency |
| DT-M3 | predictive | M2 + forecasting and what-if simulation |
| DT-M4 | autonomous | M3 + closed-loop action within authority envelope (02 controlAuthorityLevel, never exceeding it) |

`twinSubjectClass`: building, asset, space_zone, network_zone, portfolio, community, district, market_system, edge_fleet. `sandbox` boolean (NEST instances).

## 4. Hierarchy

```
TwinTemplate (33, EIG-DTT-*)
└── TwinInstance (EIG-DT-*)                ← this catalogue
    ├── SubjectBinding (exactly one physical entity ref)
    ├── DataBindings (≥1 for M2+, with sync contracts)
    ├── ModelBindings (≥1 for M3+)
    ├── StateRegister (declared state variables)
    └── ComposedTwins (child twin refs — composition, not duplication)
```

## 5. Field Groups & Fields

### 5.1 Identity & Binding

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | twinID | string | — | `EIG-DT-[0-9]{6}` | M | Twin identifier |
| 2 | twinName | string | — | ≤120 chars | M | Name |
| 3 | templateRef | string | — | EIG-DTT-* (33) + version | M | Generating template; instance must satisfy template requirements (V3) |
| 4 | twinSubjectClass | string | — | §3 vocab | M | Subject kind |
| 5 | subjectRef | string | — | EIG-BLD-*/EIG-AST-*/EIG-SPC-*/EIG-NET-NZ-*/EIG-FLX-PF-*/EIG-CMU-*/EIG-MKT-* | M | Exactly one physical/system entity; one active twin per (subject, purpose) (V2) |
| 6 | purpose | string | — | operations, flexibility, retrofit_planning, market_simulation, research | M | Primary purpose; drives fidelity requirements |
| 7 | twinMaturity | string | — | DT-M1..M4 | M | Maturity level |
| 8 | platformRef | string | — | EIG-PLT-* (31, PLT-TWIN/PLT-SBX) | M | Hosting platform |
| 9 | sandbox | boolean | — | true iff platform is PLT-SBX | M | NEST firewall inheritance (20 A2) |
| 10 | status | string | — | provisioning, syncing, active, degraded, frozen, retired | M | Lifecycle |

### 5.2 Data Bindings & Synchronisation

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | dataBindingID | string | — | `DB-[0-9]{2}` (twin-scoped) | M | Binding identifier |
| 2 | dataSourceRef | string | — | EIG-DS-* (37) / dataset (43) / device telemetry (36) | M | Source |
| 3 | stateVariables | array | — | state register names (§5.4) fed by this binding | M | What it updates |
| 4 | syncMode | string | — | streaming, periodic, on_change, batch | M | Mechanism |
| 5 | syncLatencyTargetSec | decimal | s | >0 | M | Physical-change→twin-state budget |
| 6 | syncLatencyMeasuredP95Sec | decimal | s | computed | M | Measured; breach ⇒ status=degraded (V6) |
| 7 | stalenessPolicy | string | — | hold_flagged, interpolate_flagged, null_out | M | Behaviour when stale; stale state always flagged, never silently held |
| 8 | dataClass | string | — | REAL, CALIBRATED, SYNTHETIC, COMPUTED | M | Provenance per binding; SYNTHETIC only on sandbox twins (V8) |

### 5.3 Model Bindings

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | modelBindingID | string | — | `MB-[0-9]{2}` | M | Binding identifier |
| 2 | modelRef | string | — | EIG-MOD-* (44) + pinned version | M | Model; version pinning mandatory (31 §13) |
| 3 | roleInTwin | string | — | state_estimation, forecasting, what_if, anomaly, control_recommendation, calibration | M | Function |
| 4 | inputStateVars / outputStateVars | array | — | state register names | M | Wiring |
| 5 | executionTrigger | string | — | on_sync, scheduled (24), on_demand, event (63) | M | When it runs |
| 6 | mlmLayer | string | — | deterministic, stochastic, ai_ml, rl_q | M | Cognitive-stack layer |

### 5.4 State Register

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | stateVarName | string | — | snake_case, twin-scoped unique | M | Variable name |
| 2 | quantityKind | string | — | QUDT-aligned kind + unit (e.g. temperature °C, power kW, SoC %) | M | Semantics |
| 3 | sourceKind | string | — | measured (binding), estimated (model), computed (derivation), simulated (what-if scratch — never persists to live state) | M | Provenance of the value |
| 4 | updateExpectationSec | decimal | s | >0 | M | Freshness expectation |
| 5 | qualityFlag | string | — | live, stale, estimated, fault (40 alignment) | M | Current quality |
| 6 | historyRetention | string | — | dataset ref (43) + retention | M | Where history persists |

### 5.5 Fidelity Declaration

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | fidelityClass | string | — | F1_indicative, F2_engineering, F3_calibrated, F4_verified | M | Declared fidelity |
| 2 | fidelityMetrics | array | — | [{stateVar/output, metric (MAE/MAPE/CVRMSE/NMBE), value, period}] | M | Evidence; F3+ requires calibration metrics meeting declared thresholds (e.g. CVRMSE ≤ 15% hourly, NMBE ≤ ±5% per ASHRAE G14-style criteria) |
| 3 | verificationRef | string | — | validation records (47) | C | Mandatory for F4 |
| 4 | fitnessForPurpose | array | — | purposes the fidelity supports (e.g. F2 supports retrofit_planning, not settlement-grade flexibility) | M | Explicit use limits; consumers must check (A4) |

### 5.6 Autonomy Envelope (DT-M4 only)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | authorityRef | string | — | building controlAuthorityLevel (02) + contract clause (23) | M | Authority basis; twin autonomy ≤ granted authority always |
| 2 | actionScope | array | — | action types (61) permitted autonomously | M | What it may do |
| 3 | guardrails | array | — | constraint refs (30 §5.4 / 59): comfort floors, envelopes, safety exclusions | M | Hard limits evaluated deterministically post-model |
| 4 | escalationRule | string | — | conditions forcing human-in-loop (workflow 64) | M | De-autonomisation triggers |
| 5 | killSwitch | object | — | {channel (34), latencySec, fallback: revert_to_schedule} | M | Immediate revocation path; tested per release |

## 6. Controlled Vocabularies

- `twinMaturity` DT-M1..M4; `twinSubjectClass`; `syncMode`; `stalenessPolicy`; `fidelityClass` F1–F4; `sourceKind`: §5 tables.
- Status machine: `provisioning → syncing → active ⇄ degraded → frozen → retired`; degraded auto-entered on sync/quality breaches; frozen = state preserved, no sync (forensics/decommission); autonomous actions permitted only in active.
- Composition rule vocabulary: child twin state is referenced (read-through), never copied — single source of truth per state variable across the composition tree.

## 7. Applicability Rules

| # | Rule |
|---|---|
| A1 | One active twin per (subject, purpose); multi-purpose needs justified separate instances or one twin with multiple declared purposes — never duplicate state authorities. |
| A2 | Maturity gates: M2 requires ≥1 live data binding meeting latency; M3 requires ≥1 forecasting/what-if model binding with validation (47); M4 requires fidelity F3+, autonomy envelope complete, and kill switch tested. |
| A3 | Twin autonomy never exceeds the subject's granted control authority (02 ladder, contract 23); authority downgrades propagate to the twin immediately (escalation to M3 behaviour). |
| A4 | Consumers (60 decisions, 27 evidence, 52 RD inputs) must check fitnessForPurpose; using an F2 twin for settlement-grade delivery evidence is a defect. |
| A5 | What-if/simulated values never leak into live state (sourceKind=simulated is scratch-only); scenario results persist under 89 scenario records, not twin state. |
| A6 | Sandbox twins (NEST) may consume SYNTHETIC data and replica definitions; their outputs are firewalled (20 A2) — promotion of insights follows the NEST handshake/integrity model via governance, never via data flow. |
| A7 | Composition: community/district twins compose building twins by reference; aggregate state variables are COMPUTED with declared aggregation rules (min cohort n=10 for published aggregates, privacy per 14/18 conventions). |
| A8 | Network-zone twins follow 13 conventions (state at monitored points + estimated elsewhere, constraint margins live); CP state mirrors into connected building twins by reference (A7 pattern). |

## 8. Validation Rules

| # | Rule | Severity |
|---|---|---|
| V1 | twinID unique; subjectRef exists in its catalogue | error |
| V2 | One active twin per (subject, purpose) (A1) | error |
| V3 | Instance satisfies templateRef requirements: required spaces/assets/datasets/models/KPIs present (33) | error |
| V4 | Maturity gate evidence present per A2 | error |
| V5 | All model bindings version-pinned; referenced state vars exist in register | error |
| V6 | Measured sync latency p95 ≤ target, else status=degraded enforced | error |
| V7 | Fidelity metrics meet class thresholds (F3 calibration criteria) | error |
| V8 | SYNTHETIC bindings only on sandbox twins | error |
| V9 | M4: guardrails reference parseable constraints; kill-switch test ≤ 90 days old | error |
| V10 | Composition tree acyclic; no duplicated state authority (A7) | error |
| V11 | Frozen/retired twins emit no actions and accept no sync (state machine) | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 33 template | Generation source; conformance (V3) |
| 31 platform | Hosting; execution context |
| 02/04/11/13/86/87 | Subjects (buildings, spaces, assets, network zones, communities, districts) |
| 37/38/43 data | Bindings, history persistence |
| 36/35 device/edge | Telemetry sources; edge-resident twin shards |
| 44/47 model/validation | Model bindings; fidelity verification |
| 24 schedule | Scheduled executions; what-if on draft schedules |
| 29/30 flexibility | Pre-dispatch simulation; state-of-flexibility variables |
| 52 RD | Twin-derived conditioning variables; RD validation environments |
| 60/61/62 decision/action/control | Recommendation and (M4) closed-loop pathways |
| 89 scenario | What-if persistence |
| 20/26/27 market | Market-system twins (NEST) |

## 10. Benchmarking Requirements

Twin quality benchmarks: sync latency attainment, state coverage (% subject state vars live), fidelity metric distributions per template cohort, degraded-time share, autonomous-action override rate (M4). Cohorts per template (33) with min n=10 instances; immutable releases.

## 11. Dataset Requirements

State history persisted per state-var retention declarations (43); what-if scratch isolated; autonomy action logs immutable with full decision provenance (model version, state snapshot, guardrail evaluations) retained ≥ 7 years where actions affect settlement-relevant delivery (27 alignment); GDPR scopes honoured for occupancy-derived state (03/14).

## 12. Feature Requirements

Twins are feature factories: cleaned, synchronised, provenance-tagged state is the preferred feature source for domain models (41) over raw telemetry; features exported declare twin fidelity class so model training can filter (no F1 features in settlement-grade models); simulated values excluded from feature stores (A5).

## 13. Model Requirements

Calibration models maintain F3 fidelity (scheduled recalibration on drift, 47 monitors); state-estimation models declare observability assumptions; what-if models share parameterisation with live models (no divergent physics between simulation and operation — single-physics-truth analogue of 21 V11); RL agents (72) train against sandbox twins, deploy against production twins only through promotion gates.

## 14. KPI Requirements

KPIs (57): twin coverage (% portfolio with M2+ twins), mean fidelity class, sync attainment, what-if utilisation (decisions informed), autonomous action success/override rates, time-degraded. Dimensioned by template, subject class, platform.

## 15. Response Derivative Requirements

Twins serve RD three ways: (1) conditioning context — RD condition variables (temp bands 09, occupancy 03, SoC) read from twin state; (2) counterfactual engines — calibrated twins (F3+) generate admissible baseline counterfactuals where BL-* methods allow (27); (3) validation environments — RD predictions tested against twin-simulated and then measured responses (52 loop). Only F3+ twins back settlement-grade RD evidence (A4).

## 16. Decision Requirements

Decision intelligence (60) consumes twin state/forecasts with fidelity-aware weighting; pre-action simulation through the twin is mandatory for action classes flagged high-impact (61); M4 autonomous decisions log guardrail evaluations; twin status=degraded forces decision flows to conservative fallbacks (declared per consumer).

## 17. Ontology Mapping

Classes: `eig:DigitalTwin` ⊑ `eig:VirtualEntity`; `eig:TwinDataBinding`, `eig:TwinModelBinding`, `eig:TwinStateVariable`, `eig:AutonomyEnvelope`. Object properties: `eig:represents` (→ physical entity classes, functional: exactly one), `eig:instanceOfTemplate` (→ eig:TwinTemplate), `eig:hostedBy` (→ eig:Platform), `eig:boundToSource` (→ eig:DataSource), `eig:runsModel` (→ eig:Model), `eig:composes` (→ eig:DigitalTwin). Datatype: twinMaturity, fidelityClass, syncLatencyTargetSec, sandbox. Alignments: W3C SSN/SOSA (observation feeding state), Brick/SAREF subject alignment, DTDL design-intent mapping for interoperability.

## 18. Knowledge Graph Mapping

Nodes: `(:DigitalTwin {twinID, maturity, fidelity, status, sandbox})`, `(:StateVar)`, `(:TwinBinding)`. Edges: `(:DigitalTwin)-[:REPRESENTS]->(:Building|:Asset|:NetworkZone|…)`, `(:DigitalTwin)-[:INSTANCE_OF]->(:TwinTemplate)`, `(:DigitalTwin)-[:HOSTED_BY]->(:Platform)`, `(:DigitalTwin)-[:COMPOSES]->(:DigitalTwin)`, `(:StateVar)-[:OF_TWIN]->(:DigitalTwin)`, `(:Model)-[:BOUND_TO]->(:DigitalTwin)`. Coverage queries: subjects lacking M2+ twins; fidelity audits filter F-class per consumer requirement.

## 19. Digital Twin Mapping

Self-referential anchor: this catalogue *is* the twin registry. Cross-twin conventions: composition by reference (A7), network↔building mirroring (A8), NEST as market-system twin (31), template conformance (33). Twin-of-the-framework (meta-twin for platform health) is a PLT-monitoring concern (31 §12), not a subject twin.

## 20. Governance

Owner: Digital Twin Lead. Steward: data engineering (bindings, sync). Reviewer: model owner (44) for M3+, safety reviewer for M4. Approver: T3+ for M3 activation; T4 + documented kill-switch test for M4 (16). Duties per 16 A6. Fidelity claims audited against 47 records; degradation incidents reviewed per platform security baseline (31).

## 21. Versioning

Twin instances: configuration-versioned (binding/model/envelope changes bump config version; state itself is unversioned time series). Template upgrades re-validate instances (V3). MAJOR: subject/purpose/maturity changes. MINOR: binding/model additions. PATCH: editorial. ChangeLog mandatory; M4 envelope changes logged with approver identity.

## 22. Example Records

```yaml
twinID: EIG-DT-000877
twinName: IE_B0014 Operational Twin (Inis Mór)
templateRef: EIG-DTT-RES-01 v2.1 | twinSubjectClass: building | subjectRef: EIG-BLD-000877
purpose: flexibility | twinMaturity: DT-M3 | platformRef: EIG-PLT-FLEXUS | sandbox: false
status: active | fidelityClass: F3_calibrated
fidelityMetrics: [{zone_temp_living, CVRMSE, 11.2%, 2026-Q1}, {hp_power, MAPE, 7.8%, 2026-Q1}]
dataBindings:
  - {DB-01, EIG-DS edge telemetry via EdgeWare (Jetson node), [zone_temp_*, hp_power, soc_dhw], streaming, target 30 s, p95 12 s, hold_flagged, REAL}
  - {DB-02, EIG-WX-ST-0021 Mace Head (09), [ext_temp, wind], periodic 600 s, CALIBRATED}
modelBindings:
  - {MB-01, EIG-MOD-RC-CALIB v1.4, state_estimation, deterministic}
  - {MB-02, EIG-MOD-HP-FC v2.0, forecasting, ai_ml, on_sync}
stateRegister: [zone_temp_living (°C, measured, 60 s), hp_power (kW, measured, 30 s),
                soc_dhw (%, estimated, 300 s), flex_up_now (kW, computed, 300 s)]
fitnessForPurpose: [flexibility (settlement-grade with 27 BL-LFM-01), operations]
```

```yaml
twinID: EIG-DT-000301
twinName: NZ-00017 Network Zone Twin (Inis Mór feeder)
twinSubjectClass: network_zone | subjectRef: EIG-NET-NZ-00017 | purpose: operations+flexibility
twinMaturity: DT-M3 | platformRef: EIG-PLT-FLEXUS | fidelityClass: F2_engineering
stateRegister: [feeder_load (kW, measured @ head), cp_loads (estimated elsewhere per 13), constraint_margin (kW, computed, live)]
composes: [EIG-DT-000877, … building twins by reference (A7/A8)]
fitnessForPurpose: [operations, LFM dispatch feasibility — NOT settlement evidence at F2]
```

## 23. Completion Criteria

Complete when: every subject demanding a twin per template applicability (33) has an instance passing V3; all M2+ twins meet sync targets; fidelity evidence on file per class; M4 instances (if any) carry tested kill switches; composition trees resolve acyclically; sandbox/production partition verified; Phase 14 confirms twin references in 13/29/30/52/60 resolve here and fitness-for-purpose checks are encoded in consumers.
