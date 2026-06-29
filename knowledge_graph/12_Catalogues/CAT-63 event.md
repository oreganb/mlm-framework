---
type: catalogue
ontology: EIG-MLM
catalogue_no: 63
catalogue_id: EIG-CAT-063
entity: event
band: decision
status: active
tags: [eig-mlm, catalogue, band:decision, entity:event]
aliases: ["EIG-CAT-063", "Catalogue 63", "event"]
---

# CAT-63 — Event

> **EIG-CAT-063** · band: **KPIs, Objectives & Decision Intelligence** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-34 communication]] · [[CAT-35 edge_infrastructure]] · [[CAT-37 data_source]] · [[CAT-40 data_quality]] · [[CAT-59 constraint]] · [[CAT-60 decision_intelligence]] · [[CAT-61 action]] · [[CAT-62 control_strategy]]
**Used by (downstream):** [[CAT-56 response_event]] · [[CAT-64 workflow]] · [[CAT-70 model_selection]] · [[CAT-76 alert]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-063`
**Entity prefixes:** `EIG-EVT-T-` (event types), `EIG-EVT-` (event instances)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** Operations
**Depends on (upstream):** 34 communication, 35 edge_infrastructure, 37 data_source, 40 data_quality, 59 constraint, 60 decision_intelligence, 61 action, 62 control_strategy
**Used by (downstream):** 56 response_event (cross-registered kind), 64 workflow, 70 model_selection, 76 alert

---

## 1. Definition

The **Event Catalogue** defines the framework's operational event system: the typed register (`EIG-EVT-T-*`) of everything notable that can *happen* — state changes, threshold breaches, failures, lifecycle transitions, security signals — and the instance stream (`EIG-EVT-*`) of occurrences, each carrying type, trigger, severity, duration, affected entities, and required actions. Events are the nervous system: they trigger workflows (64), raise alerts (76), drive event-triggered decisions (60), and form the operational audit timeline. Purpose: **workflows and alerts** — anything that must provoke a response is an event of a registered type with a registered response (A1); response events (56) are a specialised kind cross-registered here.

## 2. Scope
**In scope:** event type taxonomy and schema (the spec's seven: ID, type, trigger, severity, duration, affected entities, required actions); severity model and escalation; emission discipline (who may emit what); correlation and deduplication; lifecycle (raised → acknowledged → responding → resolved → closed); routing matrix (events → workflows/alerts/decisions); retention. **Out of scope:** workflow step logic (64), alert channel mechanics (76), response-event evidence semantics (56 — linked), data-quality rule internals (40 — breaches emit here).

## 3. Classification Structure

### 3.1 Event type families (`family`)

| Code | Family | Sources | Examples |
|---|---|---|---|
| EF-DAT | Data | 37/38/40 | stream silence, quality regime breach (40 escalation), schema drift (39 V3) |
| EF-AST | Asset/device | 11/36/53 | fault codes, readback failures, anomaly flags, calibration due |
| EF-NET | Network/comms | 13/34/35 | channel failover (EIG-COM-CHN-00041 class), feeder constraint active, LoRaWAN outage |
| EF-SEC | Security | 35 METIS-128 | Scan→Detect→Report→Quarantine lifecycle signals, quarantine entry/release |
| EF-CTL | Control/decision | 60/61/62 | mode transitions, guard activations, action failures, demotions (60 A5) |
| EF-MKT | Market | 25/26/27 | gate transitions, delivery shortfalls, settlement disputes |
| EF-CST | Constraint/compliance | 59/93 | violations (EP-RT/EP-POST), derogations (59 A7), regulatory deadline events |
| EF-KPI | Performance | 57 | threshold band transitions (57 A5) |
| EF-SYS | Platform | 31/35 | service degradation, deployment ring advances, kill-switch |
| EF-FLX | Flexibility | 56 | response-event lifecycle cross-registrations (56 §9) |

### 3.2 Severity model

| Level | Meaning | Default response window |
|---|---|---|
| SEV-1 Critical | safety/regulatory/major-loss exposure | immediate; 64 workflow + 76 page |
| SEV-2 Major | service/settlement/evidence impact | ≤1 h acknowledge |
| SEV-3 Minor | degraded but contained | ≤1 business day |
| SEV-4 Info | audit/lifecycle record | no response required |

Severity is type-defined with instance escalation rules (duration/recurrence raise severity, V6).

### 3.3 Lifecycle
RAISED → ACKED → RESPONDING (64 workflow ref) → RESOLVED (condition cleared) → CLOSED (post-review where required). Auto-resolution permitted for self-clearing types (declared); SEV-1/2 require human closure.

## 4. Hierarchy
```
Family (EF-*) → Event type (EIG-EVT-T-*: schema + severity + routing) → Instance (EIG-EVT-*: occurrence + lifecycle)
```

## 5. Field Groups & Fields

### 5.1 Event type (`EIG-EVT-T-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | eventTypeID | string | `EIG-EVT-T-\d{4}` | M | Spec: Event ID (type level) |
| 2 | family / name | enum/string | §3.1 | M | Spec: Event type |
| 3 | triggerDefinition | json | machine-evaluable condition OR emitting-system declaration (closed emitter list) | M | Spec: Trigger |
| 4 | severityDefault | enum | §3.2 + escalation rules | M | Spec: Severity |
| 5 | payloadSchema | json | typed fields (refs to affected registries) | M | Instance content contract |
| 6 | affectedEntityTypes | array<enum> | which registries instances reference | M | Spec: Affected entities (typing) |
| 7 | requiredActions | json | routing: →64 workflow ref / →76 alert channel (EIG-ALERT-CHN-STD etc.) / →60 decision trigger / none(SEV-4) | M | Spec: Required actions — registered responses only (A1) |
| 8 | correlationKeys | array<string> | dedup/correlation fields (subject, cause) | M | Storm control |
| 9 | autoResolve | json | self-clearing condition or null | C | Lifecycle shortcut |
| 10 | retention | duration | per family (security/settlement ≥7 y) | M | Audit horizon |
| 11 | owner / version / status | — | standard | M | Governance |

### 5.2 Event instance (`EIG-EVT-*`)
typeRef@version · raisedAt (source-disciplined timestamps, 36 V11) · trigger evidence (payload per schema) · severity (default or escalated, with rule cite) · duration {onset, resolvedAt} · affectedEntities (resolved refs) · correlationGroup (dedup key + parent/child storm links) · lifecycle states + actors (16) · responseRefs {→64 workflow instance, →76 alerts sent, →60 decision instances triggered} · environment. Immutable payload; lifecycle appends.

## 6. Controlled Vocabularies
Families (§3.1), severities (§3.2), lifecycle states (§3.3), escalation rule forms, auto-resolve forms. Emitter authorisation lists per type (closed, V2).

## 7. Applicability Rules
- **A1 — Registered responses.** Every type's requiredActions resolve to registered workflows/alerts/decisions; "someone should look at this" without a route is not a type. SEV-4 may route to none (audit-only).
- **A2 — Closed emitters.** Types declare lawful emitting systems; instances from unlisted emitters are themselves SEV-2 security events (EF-SEC).
- **A3 — Storm discipline.** Correlation keys + dedup windows mandatory; child events fold under a parent within correlation groups; alert flooding (76) is prevented here, at the source.
- **A4 — Severity honesty.** Escalation rules are mechanical (duration/recurrence/blast-radius based); manual severity inflation/deflation is logged with actor and reason.
- **A5 — Edge buffering.** Edge-raised events during MODE-LOCAL (35 A3) buffer and replay with original timestamps on reconnection; the timeline never has silent gaps.
- **A6 — Cross-registration, not duplication.** Response events (56), quality breaches (40), guard activations (59/62) keep their domain records as the system of record; the EVT instance carries the reference, not a copy (single-source, 59 A2 spirit).
- **A7 — Closure discipline.** SEV-1/2 closure requires resolution evidence + post-review where the type demands (e.g. EF-SEC quarantine releases per 37 A4/V10); auto-resolve never applies to SEV-1.

## 8. Validation Rules
V1 type IDs/routing resolve (64/76/60 refs valid); V2 emitter authorisation enforced; V3 payload validates against schema; affected refs resolve; V4 correlation keys present; dedup window respected; V5 lifecycle monotone with actor logging; V6 escalation rules mechanical and recomputable; V7 SEV-1/2 human closure; evidence attached; V8 edge replay completeness (35 logs reconcile); V9 retention enforced per family; V10 routing executions confirmed (workflow/alert delivery receipts) — an event whose required action didn't fire is itself an event (EF-SYS).

## 9. Relationships to Other Catalogues
64 (workflow triggers — the primary consumer) · 76 (alert routing) · 60 (event-triggered decisions; demotion events) · 56 (flexibility events cross-registered) · 40/37 (quality/stream events) · 59 (violations/derogations) · 61/62 (action failures, mode transitions) · 35/34 (edge/comms events; METIS-128 lifecycle) · 27 (settlement disputes) · 57 (KPI band transitions) · 93 (compliance evidence timeline).

## 10. Benchmarking Requirements
Event-ops metrics: MTTA/MTTR per severity, storm-fold ratios, auto-resolve accuracy, routing delivery success (V10), per-family volumes (trend anomalies reviewed).

## 11. Dataset Requirements
The event stream is an L1 entity (38) feeding operational analytics; incident corpora packaged (43) for reliability studies.

## 12. Feature Requirements
Event-derived features (41): faults_last_30d per asset, mode-transition counts — for 11 diagnostics and 60 context.

## 13. Model Requirements
Anomaly models (SER-11) emit through registered types; event streams train them (closed loop with A2 discipline).

## 14. KPI Requirements
KF-PLT operational KPIs compute from instance streams; KPI threshold events (EF-KPI) are themselves typed here (57 A5).

## 15. Response Derivative Requirements
RD-relevant happenings (event VOIDs 56, invalidation triggers 52 A7, estimator demotions 47) flow as typed events so the RD layer's state changes are on the same operational timeline as everything else.

## 16. Decision Requirements
Event-driven decision triggers (60 §5.1.13) subscribe by type; DC-PROT decisions consume SEV-1 directly; decision demotions (60 A5) emit EF-CTL types visibly.

## 17. Ontology Mapping
Event ↔ EIG `eig:OperationalEvent` (PROV Activity/InstantaneousEvent pattern); severity/lifecycle as individuals; cross-registration via `eig:recordsSameOccurrenceAs`; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `EventType`, `EventInstance`; edges `AFFECTS`→entities, `TRIGGERED`→64/76/60, `CORRELATES_WITH` {group}, `CROSS_REGISTERS`→56/40/59 records. Incident timelines are KG path queries.

## 19. Digital Twin Mapping
Twins render entity event timelines in context (asset view shows its EF-AST history); twin kill-switch and fidelity degradations emit EF-SYS/EF-CTL types.

## 20. Governance
Owner: Operations; type additions reviewed for routing completeness (A1) and storm design (A3); quarterly event-ops review (volumes, MTTA/MTTR, unrouted findings).

## 21. Versioning
Types semver; instances immutable payloads with lifecycle appends. Catalogue 1.0.0.

## 22. Example Records

### EIG-EVT-T-0021 — comfort guard activation (EF-CST)
- trigger: 59 EP-RT monitor on EIG-CST-00021; severity SEV-2 (escalate SEV-1 if >15 min or recurrence >2/day); payload {subject, zone temp trace ref, controlling strategy 62, suppressed action 61}
- requiredActions: →64 WF-COMFORT-REVIEW + →76 EIG-ALERT-CHN-STD; correlation key {subject}; retention 7 y (contract-relevant)

### EIG-EVT-00921473 — instance (the Starlink failover episode, 62 example)
- type EF-NET channel-failover; raised 2026-02-07T18:02:11Z (gateway clock); affected {EIG-COM-CHN-00041, EIG-EDGE-NODE-0007, 14 subjects}; severity SEV-3
- correlation: folded 14 child stream-silence events; responses: →76 ops notice; lifecycle: auto-resolved 18:43 on link restore (declared self-clearing), replay reconciliation ✓ (V8); child MODE-LOCAL transition events (EF-CTL) linked

### EIG-EVT-T-0107 — METIS-128 quarantine entry (EF-SEC)
- trigger: 35 A4 lifecycle signal (closed emitter: security service); SEV-1; requiredActions: →64 WF-SEC-QUARANTINE + page; closure: human, with release evidence per 37 V10; retention 7 y

## 23. Completion Criteria
☑ All seven spec fields typed (ID, type, trigger, severity, duration, affected entities, required actions) ☑ Family taxonomy covering data/asset/network/security/control/market/constraint/KPI/system/flex ☑ Severity + escalation mechanics ☑ Storm correlation + edge replay ☑ Registered-response and closed-emitter rules ☑ Cross-registration single-source pattern ☑ Examples tie to the comfort guard, failover, and METIS-128 chains already in force.
