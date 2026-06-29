---
type: catalogue
ontology: EIG-MLM
catalogue_no: 64
catalogue_id: EIG-CAT-064
entity: workflow
band: decision
status: active
tags: [eig-mlm, catalogue, band:decision, entity:workflow]
aliases: ["EIG-CAT-064", "Catalogue 64", "workflow"]
---

# CAT-64 — Workflow

> **EIG-CAT-064** · band: **KPIs, Objectives & Decision Intelligence** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-16 role]] · [[CAT-17 user]] · [[CAT-60 decision_intelligence]] · [[CAT-61 action]] · [[CAT-63 event]]
**Used by (downstream):** [[CAT-27 settlement]] · [[CAT-47 validation]] · [[CAT-75 dashboard]] · [[CAT-76 alert]] · [[CAT-93 compliance]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-064`
**Entity prefixes:** `EIG-WF-` (workflow definitions), `EIG-WF-I-` (workflow instances)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** Operations
**Depends on (upstream):** 16 role, 17 user, 60 decision_intelligence, 61 action, 63 event
**Used by (downstream):** 27 settlement (dispute workflows), 47 validation (review workflows), 75 dashboard (task surfaces), 76 alert, 93 compliance

---

## 1. Definition

A **workflow** (`EIG-WF-*`) is a defined, multi-step process orchestrating humans and systems toward an outcome: triggered (typically by events 63, schedules 24, or escalations 60), composed of **steps** — automated actions (61), decision points (60 or human choices), and human tasks with responsible actors (16/17) — with declared inputs and outputs. Instances (`EIG-WF-I-*`) are executions with full step-level state. Purpose: **process orchestration** — the layer where the framework's automation meets organised human work: incident response, approvals, reviews, disputes, commissioning, campaigns. If it takes more than one step or more than one actor, it's a workflow (A1).

## 2. Scope
**In scope:** workflow definition schema (the spec's eight: ID, trigger, steps, decisions, actions, responsible actors, inputs, outputs); step taxonomy (auto-action, decision, human task, wait/timer, gateway, sub-workflow); actor assignment by role (16, never hard-coded persons); SLAs and escalation; state machine semantics; parallelism and joins; instance auditing; versioning with in-flight migration rules. **Out of scope:** action mechanics (61), decision logic (60), alert delivery (76), the business content of each domain process (owned by domain catalogues; workflows orchestrate them).

## 3. Classification Structure

### 3.1 Workflow families (`family`)

| Code | Family | Triggers | Canonical members |
|---|---|---|---|
| WF-INC | Incident response | 63 SEV-1/2 events | WF-SEC-QUARANTINE (METIS-128, 35 A4), WF-COMFORT-REVIEW, stream-outage response |
| WF-APPR | Approval/governance | requests | release promotion sign-off (44 A2), derogation approval (59 A7), dataset RELEASE (43 §20), set/weight changes (58 §20) |
| WF-REV | Review/assurance | calendar/triggers | validation expiry reviews (47 A6), trust-class reviews (37), Goodhart reviews (57 A6), model board prep (70) |
| WF-DSP | Dispute/exception | 27/53 reconciliation | settlement disputes (27 adjustment runs), delivery shortfall reviews |
| WF-COM | Commissioning/change | deployments | edge node commissioning (35), strategy deployment (62 §5.2), device onboarding (36) |
| WF-CMP | Campaign/operations | 56 campaigns, 24 schedules | estimation campaign execution, seasonal revalidation drives |
| WF-REG | Regulatory/compliance | 92/93 deadlines | reporting cycles, audit evidence assembly |

### 3.2 Step types (`stepType`)

| Code | Type | Semantics |
|---|---|---|
| ST-ACT | Automated action | invokes 61 (workflow on its trigger list); result drives transition |
| ST-DEC | Decision point | 60 evaluation OR structured human choice (options + criteria recorded) |
| ST-TASK | Human task | role-assigned work item with SLA, instructions, evidence capture |
| ST-WAIT | Wait/timer | condition or duration (63 subscription or 24 timer) |
| ST-GATE | Gateway | XOR/AND split-join on declared conditions (59 grammar) |
| ST-SUB | Sub-workflow | invokes another EIG-WF (composition; recursion forbidden V5) |

### 3.3 Instance states
TRIGGERED → RUNNING (step states within) → {COMPLETED | CANCELLED (actor+reason) | FAILED (unhandled step failure → incident)} ; step states: PENDING → ACTIVE → {DONE | SKIPPED (gateway) | TIMED-OUT (escalation) | FAILED}.

## 4. Hierarchy
```
Family → Workflow definition (EIG-WF-*: trigger + step graph + actors + I/O)
            └── Instance (EIG-WF-I-*: execution; step-level audit)
                  └── Step instances → 61 actions / 60 decisions / human tasks
```

## 5. Field Groups & Fields

### 5.1 Workflow definition (`EIG-WF-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | workflowID | string | `EIG-WF-[A-Z0-9-]{2,24}` | M | Spec: Workflow ID |
| 2 | family / name / purpose | enum/string | §3.1 | M | Identity |
| 3 | trigger | json | →63 type subscription / →24 schedule / →60 escalation / manual (role-gated) | M | Spec: Trigger (closed list) |
| 4 | inputs | json | typed payload schema (refs to registries; e.g. the triggering 63 instance) | M | Spec: Inputs |
| 5 | steps | array<json> | ordered/graph: [{stepID, stepType §3.2, binding (61/60/task spec/sub-WF ref), transitions, SLA, escalation}] | M | Spec: Steps — the process graph |
| 6 | decisionPoints | array<ref> | subset of steps: ST-DEC entries with criteria/options | M | Spec: Decisions (explicit register) |
| 7 | actionBindings | array<ref> | →61 definitions invocable (bidirectional trigger lists) | M | Spec: Actions |
| 8 | responsibleActors | json | per ST-TASK/approval: →16 roles (RACI: responsible/accountable; consulted/informed routes →76) | M | Spec: Responsible actors — roles, never persons (A3) |
| 9 | outputs | json | typed results schema + records written (registry refs) | M | Spec: Outputs |
| 10 | slaPolicy | json | per-step + end-to-end targets; breach escalation (63 EF-SYS) | M | Timeliness |
| 11 | concurrency | json | instance uniqueness rules (e.g. one open dispute per settlement line), parallel step limits | M | Collision control |
| 12 | migrationPolicy | enum | in-flight on version change: complete-on-old \| migrate-at-step | M | Version safety |
| 13 | owner / version / status | — | standard | M | Governance |

### 5.2 Workflow instance (`EIG-WF-I-*`)
workflowRef@version · triggerEvidence (63 instance / schedule / actor) · inputPayload · step ledger {per step: state, actor (resolved 17 user from 16 role), started/completed, evidence/attachments, action/decision instance refs} · outputs produced · state §3.3 · SLA outcomes · environment. Immutable ledger (append-only).

## 6. Controlled Vocabularies
Families (§3.1), step types (§3.2), instance/step states (§3.3), RACI codes, migration policies. Task evidence types {note, document ref, registry update ref, sign-off}.

## 7. Applicability Rules
- **A1 — Orchestrated, not improvised.** Multi-step/multi-actor processes with consequences (approvals, incidents, disputes, commissioning) run as workflow instances; out-of-band execution of registered processes is an audit finding.
- **A2 — Separation of duties is structural.** Approval steps enforce 16 SoD at assignment time (author ≠ approver, 44/47/48 chains); the engine refuses conflicting assignments rather than trusting convention.
- **A3 — Roles, not persons.** Actor bindings reference roles; resolution to users (17) happens at step activation with availability/delegation rules; person-named workflows break on personnel change and are forbidden.
- **A4 — Evidence at every human step.** ST-TASK completion requires typed evidence (even "reviewed, no findings" is a recorded artefact); sign-offs reference what was signed (version-pinned).
- **A5 — SLAs escalate mechanically.** Timeouts escalate per declared routes (re-assignment, manager role, severity raise via 63) — work items don't silently rot.
- **A6 — Registered effects only.** Workflow steps change the world via 61 actions and registry operations with provenance; side-channel effects are forbidden (the workflow ledger must account for everything the process did).
- **A7 — Triggering record linkage.** Instances bind their triggering records (63 event, 27 line, 47 expiry) bidirectionally; the question "what process handled this?" always answers.

## 8. Validation Rules
V1 step graph well-formed: reachable, terminating, gateway conditions total (59 grammar); V2 trigger/action/decision bindings resolve bidirectionally; V3 actor roles resolve in 16; SoD constraints statically checkable; V4 input/output schemas typed; outputs written verified vs declaration (A6 audit); V5 sub-workflow composition acyclic; V6 SLA + escalation routes complete (no dead-end timeouts); V7 instance ledgers append-only, complete per step; V8 concurrency rules enforced; V9 migration policy applied on version change (in-flight instances accounted); V10 SEV-1 triggered workflows: activation latency monitored (63 V10 receipt chain).

## 9. Relationships to Other Catalogues
63 (primary trigger source; receipt verification) · 61/60 (step bindings) · 16/17 (roles → users; SoD) · 76 (informed routes; task notifications) · 24 (scheduled triggers) · 27 (dispute family; adjustment runs as outcomes) · 47/44/43/58/59 (approval families — promotion, release, derogation, weights) · 35/36/62 (commissioning families) · 56 (campaign execution) · 93 (compliance evidence: workflow ledgers are audit artefacts) · 75 (task inboxes render instance states).

## 10. Benchmarking Requirements
Process metrics per definition: cycle time, SLA attainment, escalation rates, rework loops (steps re-entered) — quarterly ops review; approval-family latency feeds governance health.

## 11. Dataset Requirements
Instance ledgers archived per family retention (compliance/settlement ≥7 y); process-mining extracts packaged (43) for improvement studies.

## 12. Feature Requirements
Workload features (41): open tasks per role, queue ages — for 75 ops dashboards and capacity planning (NEXUS-class estimation interfaces).

## 13. Model Requirements
Workflow-embedded decisions (ST-DEC via 60) carry full decision discipline; process-improvement models (SER-11/12 on cycle times) consume the ledger corpus.

## 14. KPI Requirements
KF-PLT process KPIs (cycle times, SLA %) register in 57; approval-latency KPIs gate governance reviews.

## 15. Response Derivative Requirements
RD-relevant workflows: estimation campaign execution (WF-CMP wrapping 56 lifecycles), estimator validation reviews (WF-REV on 47 expiry), invalidation response (52 A7 consumer notifications spawn review tasks where automated recompute isn't possible).

## 16. Decision Requirements
60 escalations land as workflow instances (humanInterface routes); workflow ST-DEC points invoke decisions under identical gates — the human/automated boundary is explicit per step.

## 17. Ontology Mapping
Workflow ↔ PROV `prov:Plan` / p-plan; steps ↔ plan steps with role qualifications; instances ↔ activities; alignment owned by 79 (BPMN export mapping documented).

## 18. Knowledge Graph Mapping
Nodes `WorkflowDefinition`, `WorkflowInstance`, `StepInstance`; edges `TRIGGERED_BY`→63, `EXECUTED`→61/60, `ASSIGNED_TO`→16/17, `PRODUCED`→outputs, `HANDLED`→domain records. "Who approved this release and on what evidence?" is a two-hop query.

## 19. Digital Twin Mapping
Commissioning/maintenance workflows surface in twin asset views (32); twin-detected divergences (53 §19) spawn WF-REV instances with twin evidence attached.

## 20. Governance
Owner: Operations; definition changes reviewed with affected role owners; approval-family definitions additionally by PI (they *are* governance); annual process review prunes dead workflows.

## 21. Versioning
Definitions semver with migration policies; instances immutable. Catalogue 1.0.0.

## 22. Example Records

### EIG-WF-SEC-QUARANTINE (WF-INC) — METIS-128 quarantine response (resolves 63 routing)
- trigger: EIG-EVT-T-0107 (SEV-1); inputs: event payload {node, signals}
- steps: ST-ACT verify isolation (ACT-STREAM-QUARANTINE confirm, 37/35) → ST-TASK forensic triage (role: security steward, SLA 4 h) → ST-GATE {compromise confirmed?} → [yes: ST-SUB WF-INCIDENT-MAJOR; no: ST-TASK release dossier (evidence per 37 V10)] → ST-DEC release approval (role: platform owner, SoD vs triage actor) → ST-ACT staged rejoin (35 lifecycle Repair→Verify→Rejoin)
- outputs: release/escalation record, 63 closure evidence; end-to-end SLA 24 h

### EIG-WF-MODEL-PROMOTE (WF-APPR) — release promotion (the 44 A2 gate as process)
- trigger: promotion request (manual, AI/ML role); steps: evidence assembly (47/51/39 refs auto-collected) → ST-GATE completeness → ST-TASK reviewer assessment (role per 47 method, SoD) → ST-DEC approver sign-off (version-pinned, A4) → ST-ACT registry promotion + consumer notices (44 A4)
- concurrency: one open per release; outputs: promotion record with full evidence chain

### EIG-WF-I-0084112 — instance (comfort review, the 63 §22 route)
- trigger EIG-EVT-T-0021 instance; ledger: auto context assembly → ops review task (completed, evidence: strategy tuning note →62 parameter override, governed) → closure; cycle 6 h, SLA ✓

## 23. Completion Criteria
☑ All eight spec fields (ID, trigger, steps, decisions, actions, responsible actors, inputs, outputs) ☑ Step taxonomy incl. gateways/sub-workflows with sound graph rules ☑ Role-based assignment with structural SoD ☑ Evidence-at-every-step + registered-effects-only ☑ SLA escalation mechanics ☑ Family register covering incident/approval/review/dispute/commissioning/campaign/regulatory ☑ Worked quarantine + promotion workflows closing 35/44/63 routes.
