---
type: catalogue
ontology: EIG-MLM
catalogue_no: 61
catalogue_id: EIG-CAT-061
entity: action
band: decision
status: active
tags: [eig-mlm, catalogue, band:decision, entity:action]
aliases: ["EIG-CAT-061", "Catalogue 61", "action"]
---

# CAT-61 — Action

> **EIG-CAT-061** · band: **KPIs, Objectives & Decision Intelligence** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-04 space]] · [[CAT-11 asset]] · [[CAT-34 communication]] · [[CAT-35 edge_infrastructure]] · [[CAT-36 device]] · [[CAT-59 constraint]] · [[CAT-60 decision_intelligence]]
**Used by (downstream):** [[CAT-53 response_behaviour]] · [[CAT-56 response_event]] · [[CAT-62 control_strategy]] · [[CAT-63 event]] · [[CAT-64 workflow]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-061`
**Entity prefixes:** `EIG-ACTN-` (action definitions), `EIG-ACTN-I-` (action instances)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** Operations with domain authorities
**Depends on (upstream):** 04 space, 11 asset, 34 communication, 35 edge_infrastructure, 36 device, 59 constraint, 60 decision_intelligence
**Used by (downstream):** 53 response_behaviour (actions stimulate), 56 response_event, 62 control_strategy, 63 event, 64 workflow

---

## 1. Definition

The **Action Catalogue** registers everything the framework can *do*: executable action definitions (`EIG-ACTN-*`) — named, parameterised operations on devices (36), assets (11), zones (04), markets (25), or systems (31/35) — each with trigger linkage, preconditions, constraint checks, expected outcome, risk class, and fallback; and action instances (`EIG-ACTN-I-*`) — each execution logged with parameters, execution trail, and result. Purpose: **automation and control** — the verb register. Decisions (60) select actions; strategies (62) sequence them; workflows (64) embed them; nothing actuates that isn't defined here (A1).

## 2. Scope
**In scope:** action taxonomy (physical actuation, market, data/system, notification); definition schema (the spec's eight: ID, name, trigger, preconditions, constraints, expected outcome, risk, fallback); parameter schemas with bounds; execution protocol (command → readback → verify → confirm/rollback); idempotency and retry semantics; instance logging; reversibility classification. **Out of scope:** decision logic (60), sequencing/loops (62/64), device protocol details (34/36 — referenced), penalty consequences (68).

## 3. Classification Structure

### 3.1 Action families (`family`)

| Code | Family | Targets | Examples |
|---|---|---|---|
| AF-ACT | Physical actuation | 36 actuation endpoints via 34 | ACT-SHED-HP, ACT-SETPOINT-ADJ, ACT-CHARGE-STO, ACT-VALVE |
| AF-MODE | Mode/schedule change | assets/zones (11/04/24) | ACT-PREHEAT-ENABLE, ACT-SETBACK, ACT-SCHEDULE-SWAP |
| AF-MKT | Market | 25/26 interfaces | ACT-BID-SUBMIT, ACT-BID-WITHDRAW (gate-time constrained CC-MKT) |
| AF-SYS | Data/system | 31/35/37 | ACT-STREAM-QUARANTINE, ACT-DEPLOY-RING-ADVANCE, ACT-TWIN-KILLSWITCH (32 V9) |
| AF-NTF | Notification/engagement | 17/76 channels | ACT-OCCUPANT-NOTIFY (RDD-OCC trigger), ACT-OPS-ALERT |
| AF-PROT | Protective | 36 interlock-adjacent | ACT-ABORT-SAFE, ACT-RESTORE-BASELINE |

### 3.2 Reversibility classes
REV-FULL (cleanly undoable: setpoint changes) · REV-COST (undoable with cost/wear: storage cycles) · REV-TIME (self-reversing on release: shed events) · IRREV (market commitments past gates, notifications sent) — feeds 60 risk scoring.

### 3.3 Execution protocol states
ISSUED → DELIVERED (34 ack) → ACTUATED (36 readback) → VERIFIED (effect observed per expected-outcome check) → CONFIRMED | ROLLED-BACK | FAILED (cause). The full ladder is mandatory for AF-ACT (A4); families declare their applicable subset.

## 4. Hierarchy
```
Family (AF-*) → Action definition (EIG-ACTN-*) → Instance (EIG-ACTN-I-*: parameters + trail + result)
```

## 5. Field Groups & Fields

### 5.1 Action definition (`EIG-ACTN-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | actionID | string | — | `EIG-ACTN-[A-Z0-9-]{2,24}` (alias ACT-* in tables) | M | Spec: Action ID |
| 2 | name / family | string/enum | — | §3.1 | M | Spec: Action name |
| 3 | trigger | json | — | invokers: →60 decision refs / 62 strategies / 64 workflows / manual (role-gated 16) | M | Spec: Trigger — who may invoke (closed list, A2) |
| 4 | targetSelector | expr | — | device/asset/zone classes (36/11/04) the action applies to | M | Where executable |
| 5 | parameters | json | UCUM | schema: name, type, unit, bounds (e.g. kW ∈ envelope, duration ≤ saturation) | M | Bounded inputs (V2) |
| 6 | preconditions | array<expr> | — | machine-evaluable state checks (device online 36, no quarantine 35 A4, mode compatible) | M | Spec: Preconditions — checked at issue |
| 7 | constraintChecks | array<ref> | — | →59 constraints this action must verify (bidirectional, 59 V9) | M | Spec: Constraints |
| 8 | expectedOutcome | json | — | {observable, magnitude expr (often RD-derived, 52), verification window + tolerance} | M | Spec: Expected outcome — basis for VERIFIED |
| 9 | riskClass | enum | — | R1–R4 base (60 §3.3) + reversibility (§3.2) | M | Spec: Risk |
| 10 | fallback | json | — | on FAILED/ROLLED-BACK: safe state, restore action ref, escalation (63/64) | M | Spec: Fallback — never-silent |
| 11 | executionBinding | json | — | message schema (34, e.g. FLEX-DISPATCH-v2), QoS, edge-executable flag (35 localAuthorityScope) | M | Mechanics |
| 12 | idempotency | json | — | {idempotent: bool, dedup key, retry policy} | M | QoS2 alignment (34 A6) |
| 13 | sideEffects | array<ref> | — | events emitted (63), records touched | M | Declared footprint |
| 14 | owner / version / status | — | — | standard | M | Governance |

### 5.2 Action instance (`EIG-ACTN-I-*`)
actionRef@version · invokerRef (→60 instance / 62 / 64 / user) · targetRef · parameters (validated) · protocol trail {state timestamps §3.3, message refs 34, readbacks 36, verification result} · result {CONFIRMED/ROLLED-BACK/FAILED + cause} · linkedEvent (→56 where stimulus) · environment. Immutable.

## 6. Controlled Vocabularies
Families (§3.1), reversibility (§3.2), protocol states (§3.3), failure causes (aligned to 56 void causes + 34/35/36 diagnostics). Parameter expression grammar shared with 59.

## 7. Applicability Rules
- **A1 — Registered verbs only.** Actuation, market submission, and system mutation occur only via defined actions; raw device commands outside this register are a security finding (35 A6 spirit).
- **A2 — Closed trigger lists.** Each definition enumerates its lawful invokers; an action invoked by an unlisted source is rejected and evented (63).
- **A3 — Preconditions and constraints at issue-time.** Both checked before ISSUED (60 EP-PRE handoff); state changes between decision and issue re-check (no stale authorisations).
- **A4 — Full protocol for physical actuation.** AF-ACT requires DELIVERED + ACTUATED (readback, 36 A6) + VERIFIED against expectedOutcome; verification failure triggers fallback within its window — commands are not fire-and-forget.
- **A5 — Bounded parameters.** Parameter bounds bind to live registries (envelope 30, saturation 52, ratings 11); out-of-bounds requests rejected at validation, not clamped silently.
- **A6 — Idempotent or deduplicated.** Retry-safe execution per 34 QoS2 discipline; duplicate suppression keys mandatory for non-idempotent actions.
- **A7 — Stimulus linkage.** Actions constituting response stimuli register their event linkage (56) so behaviour capture (53) and settlement (27) attach correctly.
- **A8 — Edge execution containment.** Edge-executable actions run within localAuthorityScope under cached constraint sets (35 A3, 59 A5) with replay-logged trails.

## 8. Validation Rules
V1 ID/trigger/target refs resolve; trigger lists closed; V2 parameter schemas bounded with registry bindings; V3 precondition/constraint expressions parse (59 grammar); bidirectional links to 59 verified; V4 expectedOutcome verifiable (observable + window + tolerance defined); V5 fallback resolvable (restore actions exist, escalation routes valid); V6 protocol trail completeness per family (AF-ACT full ladder); V7 instances immutable; result causes coded; V8 risk/reversibility consistency (IRREV ⇒ base ≥R2); V9 side-effect declarations match observed emissions (audited); V10 invoker authorisation logged per instance (16 RBAC).

## 9. Relationships to Other Catalogues
60 (selection + parameters) · 62/64 (sequencing/orchestration invokers) · 36/34/35 (execution machinery: endpoints, messages, edge) · 59 (constraint checks; structural exclusions mean some "actions" never exist for some targets) · 56/53 (stimulus events; behaviour capture) · 52 (expected outcomes from RDs; verification feeds deviation analysis 53 §3.3) · 63 (lifecycle + failure events) · 25/26 (market actions, gate-time CC-MKT) · 49 (per-action explanations cite the instance) · 17/76 (notification actions, consent-gated).

## 10. Benchmarking Requirements
Execution reliability per definition: DELIVERED/ACTUATED/VERIFIED rates, fallback invocation rates, latency distributions (vs 29 A8 classes) — feeds device/channel diagnostics (36/34).

## 11. Dataset Requirements
Instance archives with trails: the actuation audit corpus; retention per consequence (settlement-linked ≥7 y).

## 12. Feature Requirements
Action-history features (41): actions_last_24h per target (interacts with 56 fatigue features), time-since-restore.

## 13. Model Requirements
expectedOutcome expressions cite model releases (52 estimators, 44 envelope models) — verification failures feed 53 deviations and ultimately estimator review (47/51).

## 14. KPI Requirements
Action-ops KPIs (57 KF-PLT): verification pass rate, mean execution latency, fallback rate.

## 15. Response Derivative Requirements
AF-ACT dispatch actions are the RD layer's effectors: parameters shaped by 52 A5 arithmetic; VERIFIED-vs-expected residuals are the per-action grain of RD truth-testing (aggregated in 53).

## 16. Decision Requirements
60 hands over {actionRef, parameters} post-gates; issue-time re-checks (A3) protect against race conditions; results return to decision audit chains (60 A1).

## 17. Ontology Mapping
Action ↔ EIG `eig:Action` (PROV Activity); SAREF `saref:Command` alignment for device actuation; protocol states as activity qualifications; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `ActionDefinition`, `ActionInstance`; edges `INVOKED_BY`→60/62/64, `TARGETED`→36/11/04, `CHECKED`→59, `VERIFIED_AGAINST`→52 expectations, `STIMULATED`→56, `EMITTED`→63.

## 19. Digital Twin Mapping
Twins pre-simulate action effects (expectedOutcome dry-runs) and mirror instance trails live; M4 twin actions are real actions under this register (32 — the kill-switch is itself ACT-TWIN-KILLSWITCH).

## 20. Governance
Domain authorities own families (physical: engineering; market: trading; notifications: DPO-reviewed); new AF-ACT definitions require 36-endpoint certification + 59 check review; trigger-list changes are MAJOR.

## 21. Versioning
Definitions semver; instances immutable. Catalogue 1.0.0.

## 22. Example Records

### EIG-ACTN-SHED-HP (ACT-SHED-HP) — heat pump load shed
- family AF-ACT; triggers: [EIG-DEC-FLEX-DISPATCH-RES, 62 supervisory restore]; targets: ASHP actuation endpoints (EIG-DEV-TYP-0012 class)
- parameters: shed_kw ∈ (0, envelope_up(asset)] (30/52-bound); duration ≤ min(saturation p50, inherited 3 h condition); preconditions: device online ∧ no quarantine ∧ comfort headroom > margin
- constraints: [00021 comfort RT-guard, 00134 ramp, 00190 caps]; expectedOutcome: power drop ≥ 0.85 × shed_kw within latency p90 (RD-00184421-derived), window 5 min, tolerance per spec
- risk R2 / REV-TIME; fallback: ACT-RESTORE-BASELINE + 63 event + 60 abstain-mode for subject; binding: FLEX-DISPATCH-v2 QoS2 (34), edge-executable (EIG-EDGE-DEP-00231); idempotent via event-scoped dedup key

### EIG-ACTN-I-0192277 — the instance inside the worked chain
- invoker EIG-DEC-I-00834712; target EIG-DEV-001847; params {2.1 kW, 45 min}; trail: ISSUED 17:30:01 → DELIVERED 17:30:02 → ACTUATED 17:30:18 (readback) → VERIFIED 17:31:12 (drop 2.18 kW ✓) → CONFIRMED; linked event EIG-REV-00337712; explanation EIG-XAI-00412877

### EIG-ACTN-BID-SUBMIT — market bid submission (AF-MKT)
- triggers: [bid-construction decision (25 support)]; preconditions: window OPEN (26 gate state) ∧ prequal ACTIVE (22); IRREV past gate ⇒ base R3 (human-confirm above size threshold); fallback: ACT-BID-WITHDRAW while window permits

## 23. Completion Criteria
☑ All eight spec fields per definition (ID, name, trigger, preconditions, constraints, expected outcome, risk, fallback) ☑ Family/reversibility/protocol taxonomies ☑ Full actuation ladder with verification + rollback ☑ Closed trigger lists + bounded parameters + idempotency ☑ Stimulus/event linkage ☑ Worked shed action closing the dispatch chain end-to-end.
