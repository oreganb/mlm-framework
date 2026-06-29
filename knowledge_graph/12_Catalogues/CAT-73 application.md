---
type: catalogue
ontology: EIG-MLM
catalogue_no: 73
catalogue_id: EIG-CAT-073
entity: application
band: applications
status: active
tags: [eig-mlm, catalogue, band:applications, entity:application]
aliases: ["EIG-CAT-073", "Catalogue 73", "application"]
---

# CAT-73 — Application

> **EIG-CAT-073** · band: **Applications & Reporting** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-16 role]] · [[CAT-17 user]] · [[CAT-31 platform]] · [[CAT-44 model]] · [[CAT-57 kpi]] · [[CAT-60 decision_intelligence]]
**Used by (downstream):** [[CAT-74 service]] · [[CAT-75 dashboard]] · [[CAT-76 alert]] · [[CAT-77 report]] · [[CAT-78 use_case]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-073`
**Entity prefix:** `EIG-APP-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (portfolio) with platform owner (31)
**Depends on (upstream):** 16/17 role/user, 31 platform, 44 model, 57 kpi, 60 decision_intelligence
**Used by (downstream):** 74 service, 75 dashboard, 76 alert, 77 report, 78 use_case

---

## 1. Definition

The **Application Catalogue** registers the framework's applications (`EIG-APP-*`): the user- and system-facing software units through which the MLM stack delivers value — each defined by purpose, inputs, outputs, users (17/16), embedded models (44), embedded decisions (60), and KPIs (57). The register is organised around the **21 action-stage applications**: the canonical set spanning the framework's action pipeline from awareness through dispatch to settlement and learning. Purpose: every application's claims (which models answer, which decisions act, which KPIs prove value) are register-resolvable — applications are assembled from governed parts, never parallel implementations (A1).

## 2. Scope
**In scope:** application schema (the spec's eight: ID, purpose, inputs, outputs, users, models, decisions, KPIs); the 21 action-stage application register (normative census, V8); composition rules (models/decisions/KPIs by reference); user/permission surfaces (16); platform hosting bindings (31); mobile/edge variants; lifecycle. **Out of scope:** service-level wrappers (74), UI composition (75 dashboards are application components), deployment infrastructure (31/35), the embedded artefacts themselves (44/60/57).

## 3. Classification Structure

### 3.1 Action stages and the 21 applications (`EIG-APP-01..21`) — normative census

| Stage | # | Application | Core composition |
|---|---|---|---|
| **Awareness** | 01 | Portfolio Energy Monitor | 38/57 (EUI, PEAK), 75 panels |
| | 02 | Building Performance Profiler | 06/65 benchmarks, 01 classification |
| | 03 | Comfort & IEQ Monitor | 04 zones, EIG-KPI-COMFORT |
| | 04 | Data Quality Console | 40 assessments, 37 streams |
| **Diagnosis** | 05 | Anomaly & Fault Triage | SER-11, 63 EF-AST, 64 WF routing |
| | 06 | Asset Health Manager | 11/12, SER-04, maintenance decisions (DC-OPS) |
| | 07 | Twin Explorer | 32/33 (what-if, calibration views) |
| **Forecasting** | 08 | Demand & Generation Forecaster | 46 definitions, SER-02/03 |
| | 09 | Price & Carbon Outlook | FD-PRC/FD-CI (46), 21/28 |
| **Flexibility** | 10 | Flexibility Profiler | 52/53/55 (RD records, profiles) |
| | 11 | Estimation Campaign Manager | 56 campaigns, EO-EST scheduling (DC-SCHED) |
| | 12 | Prequalification & Product Matcher | 22 gates, 55 matching |
| **Market** | 13 | Bid Construction Desk | 25/26, FC-DEF feeds, portfolio rosters (30) |
| | 14 | Dispatch Operations Console | 60 DC-DISP, 61 trails, 62 modes — the live ops surface |
| | 15 | Settlement & Reconciliation | 27 four-quantity, 53 reconciliation, disputes (WF-DSP) |
| **Optimisation** | 16 | Schedule & Control Optimiser | SER-12, 58 sets, 62 strategy targets |
| | 17 | Retrofit & Investment Ranker | 05 §16, 54 sensitivities, 65 positions (DC-PLAN) |
| **Engagement** | 18 | Occupant App | 17 occupant role, 49 AU-OCC renderings, overrides (29 A4), consent (03) |
| | 19 | Community Energy Portal | 86-scope aggregates, 15 org views, n≥10 cohorts |
| **Governance** | 20 | Model & Policy Board Console | 44/47/51/70/71 (registers, gates, licences) |
| **Learning** | 21 | Learning Loop Monitor | 52 coverage, 56 campaigns, 66/71 fleet, reward accounting (67/68) |

Census is normative: additions/retirements are catalogue MAJOR changes (V8).

### 3.2 Application classes
APP-UI (human-facing), APP-SVC (headless/system-facing — e.g. bid construction core), APP-HYB (both). Mobile variants declared per app (EIG Platform mobile pattern: 5-core-feature subset discipline).

## 4. Hierarchy
```
Action stage → Application (EIG-APP-01..21) → versions → hosting deployments (31) / mobile variants
                    composition: models (44) + decisions (60) + KPIs (57) + dashboards (75) by reference
```

## 5. Field Groups & Fields

### 5.1 Application (`EIG-APP-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | applicationID | string | `EIG-APP-\d{2}` (census §3.1) | M | Spec: Application ID |
| 2 | name / stage / class | string/enum | §3.1/§3.2 | M | Identity |
| 3 | purpose | string | ≤1000 chars | M | Spec: Purpose |
| 4 | inputs | array<ref> | →38 entities / 46 definitions / 52/55/27 registers consumed (provenance envelopes ride through, 31 A3) | M | Spec: Inputs — by reference |
| 5 | outputs | array<ref> | records written (registry refs), exports (43-packaged), notifications (76) | M | Spec: Outputs — registered effects (64 A6 spirit) |
| 6 | users | array<ref> | →16 roles (never persons); permission surface per role | M | Spec: Users |
| 7 | embeddedModels | array<ref> | →44 releases (via 70 slots where adaptive) | M | Spec: Models — resolvable census |
| 8 | embeddedDecisions | array<ref> | →60 definitions (coupling modes visible) | M | Spec: Decisions |
| 9 | kpis | array<ref> | →57 (value proof + in-app rendering with QC badges) | M | Spec: KPIs |
| 10 | dashboards | array<ref> | →75 components composed | C | UI assembly |
| 11 | hosting | json | →31 services, mobile variant flag + feature subset, edge touchpoints (35) | M | Where it runs |
| 12 | environmentSupport | json | production / sandbox (NEST mirrors per app where trial-relevant, firewall A4) | M | Firewall posture |
| 13 | owner / version / status | — | standard | M | Governance |

## 6. Controlled Vocabularies
Stages (§3.1 — frozen seven), classes (§3.2), hosting forms, mobile-subset declaration format. Census IDs EIG-APP-01..21 stable.

## 7. Applicability Rules
- **A1 — Assembled, never forked.** Applications compose registered models/decisions/KPIs by reference; an app-local formula, model, or threshold is an audit finding (57 A1 / 44 A1 closure at the app layer).
- **A2 — Role-gated surfaces.** Every user-visible capability maps to 16 permissions; the occupant app (18) exposes exactly the occupant role surface (overrides, consent, own-data, AU-OCC explanations) — no privileged leakage.
- **A3 — Badge duties propagate.** Apps rendering register content inherit 75 badge duties (QC codes, staleness, SANDBOX, demotions, licence states) — no surface may strip caveats.
- **A4 — Sandbox mirrors are marked.** NEST-mirror app instances run against sandbox feeds, visibly environment-badged end to end; no mixed-environment views (chain 20 A2…).
- **A5 — Census integrity.** New capabilities land inside the 21-app structure (as features/versions) unless the board ratifies census change (V8) — sprawl is governed.
- **A6 — Mobile subsets are declared.** Mobile variants declare their feature subset (the 5-core-feature discipline); parity gaps are documented, not discovered.

## 8. Validation Rules
V1 census complete: 21 ACTIVE applications, IDs stable; V2 composition refs resolve (44 via 70 where adaptive, 60, 57, 75); V3 users resolve to roles; permission surfaces consistent with 16 grants; V4 outputs registered (no side-channel writes); V5 badge propagation verified per surface (sampled UI audits); V6 environment badging on sandbox mirrors; V7 mobile subsets declared and consistent with deployed variants; V8 census changes are catalogue MAJOR with board sign-off.

## 9. Relationships to Other Catalogues
31 (hosting; provenance envelopes) · 44/60/57 (composition) · 70 (adaptive model resolution) · 75/76/77 (UI, alerts, reports as app components/outputs) · 74 (services wrap apps with SLAs) · 78 (use cases validate apps) · 16/17 (roles/users) · 64 (apps surface workflow inboxes) · 49 (explanation renderings per audience).

## 10. Benchmarking Requirements
App health: adoption per role, task completion (where instrumented), badge-compliance audit pass rate, census coverage of operational needs (gap requests tracked).

## 11. Dataset Requirements
App-generated exports ship as 43 packages; usage telemetry (privacy-reviewed) as 38 entities for UX research.

## 12. Feature Requirements
In-app computed quantities resolve to registered features/KPIs (A1); app-specific convenience aggregates register before display.

## 13. Model Requirements
embeddedModels via 70 slots where alternatives exist; app surfaces show active model identity on demand (49 transparency at the UI).

## 14. KPI Requirements
Each app declares its value-proof KPIs (§5.1.9); the census collectively must cover all published KPI families (57) — orphan KPIs without a rendering home are flagged.

## 15. Response Derivative Requirements
Apps 10/11/12/14/21 are the RD layer's human surfaces: RD records with confidence/recency/conditions visible, campaign coverage maps, gate-check outcomes at dispatch — the RD concept's operational legibility lives here.

## 16. Decision Requirements
embeddedDecisions render with coupling-mode visibility (AUTO-ENV vs ADVISORY badges), abstention states, and per-action explanation access (49 A7) — the decision frame is never hidden behind the UI.

## 17. Ontology Mapping
Application ↔ EIG `eig:Application` with composition properties to models/decisions/KPIs; stages as individuals; alignment owned by 79.

## 18. Knowledge Graph Mapping
Node `Application`; edges `EMBEDS`→44/60, `PROVES_BY`→57, `SERVES`→16 roles, `HOSTED_ON`→31, `IN_STAGE`. "Which apps break if this model retires?" is the 44 A4 query at app grain.

## 19. Digital Twin Mapping
Twin Explorer (07) is the twin layer's app; other apps embed twin views by reference (32 components) — one twin truth, many surfaces.

## 20. Governance
Owner: PI; per-app product owners assigned; census reviews annually; badge-compliance audits quarterly with 75.

## 21. Versioning
Applications semver; census changes MAJOR. Catalogue 1.0.0.

## 22. Example Records

### EIG-APP-14 — Dispatch Operations Console (Market stage, APP-UI)
- purpose: live flexibility dispatch operations; inputs: 60 DC-DISP instances, 61 trails, 62 modes, 63 EF-CTL stream, RD gate states (52), portfolio rosters (30)
- users: ops roles (16); embedded decisions: EIG-DEC-FLEX-DISPATCH-RES (AUTO-ENV badge) + manual dispatch (ADVISORY); models via EIG-MS slots; KPIs: FLEX-DELIV, dispatch latency
- badges: demotion states (60 A5), licence states (71), staleness — full 75 duty set; sandbox mirror for NEST trials (environment-badged)

### EIG-APP-18 — Occupant App (Engagement, mobile-first)
- users: occupant role only (A2); surface: own consumption (38, own-data), comfort state, override (29 A4 honoured, logged 53 OB-OVR), event notifications + AU-OCC explanations (49: "your heat pump paused… you earn credit"), consent management (03), flexibility credit statement (27 own-lines)
- mobile subset = full (mobile-first); privacy: own-subject scope structurally (16)

### EIG-APP-21 — Learning Loop Monitor (Learning stage)
- renders the closed loop: RD coverage vs minEvidence (52/56), campaign progress (EIG-REV-CMP), estimator slot states (70), policy licences + confidence (71), reward/penalty accounting (67/68), evidence-value attainment (58 estimation objectives) — the framework watching itself learn

## 23. Completion Criteria
☑ All eight spec fields (ID, purpose, inputs, outputs, users, models, decisions, KPIs) ☑ The 21 action-stage application census defined normatively across seven stages ☑ Assembled-never-forked composition ☑ Role-gated surfaces + badge propagation + sandbox marking ☑ Mobile subset discipline ☑ Worked consoles closing the dispatch, occupant, and learning-loop surfaces.
