---
type: catalogue
ontology: EIG-MLM
catalogue_no: 75
catalogue_id: EIG-CAT-075
entity: dashboard
band: applications
status: active
tags: [eig-mlm, catalogue, band:applications, entity:dashboard]
aliases: ["EIG-CAT-075", "Catalogue 75", "dashboard"]
---

# CAT-75 — Dashboard

> **EIG-CAT-075** · band: **Applications & Reporting** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-16 role]] · [[CAT-38 data]] · [[CAT-46 forecast]] · [[CAT-57 kpi]] · [[CAT-65 benchmark]] · [[CAT-69 context]] · [[CAT-73 application]]
**Used by (downstream):** [[CAT-18 stakeholder]] · [[CAT-74 service]] · [[CAT-77 report]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-075`
**Entity prefixes:** `EIG-DSH-` (dashboards), `EIG-DSH-W-` (widget types)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** Platform owner (31) with application product owners (73)
**Depends on (upstream):** 16 role, 38 data, 46 forecast, 57 kpi, 65 benchmark, 69 context, 73 application
**Used by (downstream):** 18 stakeholder communication, 74 service outputs, 77 report (shared rendering rules)

---

## 1. Definition

The **Dashboard Catalogue** registers dashboards (`EIG-DSH-*`) — composed visual surfaces of widgets bound to registered data — each defined by users (16), widgets, KPIs (57), data sources, refresh frequency, and permissions. It also owns the framework's **rendering integrity rules**: the badge duties accumulated across the registers (quality caveats, staleness, sandbox/synthetic marking, coupling modes, demotions, derogations, licence states) that every surface must honour. Purpose: **UI and reporting** that can be trusted — a number on a dashboard carries the same provenance, caveats, and freshness truth as the register it came from; rendering never launders (A1).

## 2. Scope
**In scope:** dashboard and widget-type schemas (the spec's seven: ID, users, widgets, KPIs, data sources, refresh frequency, permissions); widget taxonomy; data-binding rules (registered sources only); refresh/freshness semantics; the badge-duty register (normative); drill-down provenance; layout/theme conventions (house style refs); permission-scoped rendering; mobile rendering subsets. **Out of scope:** application logic (73), KPI formulas (57), report documents (77 — shares badge rules), chart implementation details (platform UI stack, 31).

## 3. Classification Structure

### 3.1 Widget types (`EIG-DSH-W-*`)

| Code | Widget | Binds | Notes |
|---|---|---|---|
| W-KPI | KPI tile | →57 value + threshold band + 65 percentile context | QC badge mandatory |
| W-TS | Time-series | →38 entities / 46 runs (forecast fans with UF rendering) | dataClass per-series visible |
| W-MAP | Spatial | →08/13 geographies, subjects | Leaflet-pattern; n≥10 for cohort layers |
| W-TBL | Register table | any catalogue register view | status/lifecycle columns standard |
| W-EVT | Event/timeline | →63 stream, 64 inboxes | severity styling fixed |
| W-FLX | Flexibility | →52 records, 55 profiles, 30 envelopes | confidence/recency/conditions inline |
| W-DEC | Decision/action | →60 instances, 61 trails, 62 modes | coupling-mode badge mandatory |
| W-XAI | Explanation | →49 renderings per audience | confidence label mandatory |
| W-CMP | Comparison | →65 cohort distributions | subject-highlight pattern |

### 3.2 The badge-duty register (normative, framework-wide)

| Badge | Trigger | Source rule |
|---|---|---|
| QC-* quality caveat | KPI/value caveat ≠ OK | 57 §3.3 |
| STALE | past validity/refresh bound | 46 A2, 65 A7 |
| SANDBOX / SYNTHETIC | environment=sandbox or dataClass=SYNTHETIC anywhere in lineage | 42/20 chain (per-series, hyperGrid envelope pattern) |
| AUTO/ADVISORY | decision coupling mode | 60 §3.2 |
| DEMOTED | coupling degradation active | 60 A5 |
| DEROGATION | active constraint derogation in scope | 59 A7 |
| LICENCE-STATE | policy DEGRADED/SUSPENDED in scope | 71 §3.1 |
| FIDELITY | twin F-class on twin-derived views | 32 |
| ESTIMATED | imputed values in view window | 40 §3.3 |
| UNKNOWN-CTX | context UNKNOWN affecting the view | 69 A4 |

Badges are computed from register truth, rendered uniformly (house style), and **non-suppressible** below admin-documented exceptions (none currently authorised) — V5.

### 3.3 Refresh classes
RF-LIVE (≤ PT1M, ops consoles) · RF-FAST (PT5M–PT15M) · RF-STD (PT1H) · RF-DAILY. Declared per widget; freshness indicator rendered; stale-beyond-class triggers STALE badge.

## 4. Hierarchy
```
Widget types (EIG-DSH-W-*) → Dashboard (EIG-DSH-*: layout of bound widget instances)
                                   └── embedded in 73 applications; role-scoped variants
```

## 5. Field Groups & Fields

### 5.1 Dashboard (`EIG-DSH-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | dashboardID | string | `EIG-DSH-\d{4}` | M | Spec: Dashboard ID |
| 2 | name / hostApp | string/ref | →73 EIG-APP-* | M | Identity + home |
| 3 | users | array<ref> | →16 roles; per-role visibility variants | M | Spec: Users |
| 4 | widgets | array<json> | [{widgetType →W-*, binding, layout, refreshClass, drilldown}] | M | Spec: Widgets |
| 5 | kpis | array<ref> | →57 rendered (with threshold/percentile context per W-KPI rules) | M | Spec: KPIs |
| 6 | dataSources | array<ref> | →38/46/52/55/57/60/63/65 register bindings (registered only, A2) | M | Spec: Data sources |
| 7 | refreshFrequency | json | per-widget classes §3.3 + dashboard default | M | Spec: Refresh frequency |
| 8 | permissions | json | 16 expressions per widget where finer than dashboard scope; data-scope filters (own-subject for occupant surfaces) | M | Spec: Permissions |
| 9 | badgeDuties | computed | §3.2 set applicable (derived from bindings; listed for audit) | M | Integrity surface |
| 10 | mobileVariant | json | subset layout or none (73 A6 discipline) | C | Mobile rendering |
| 11 | owner / version / status | — | standard | M | Governance |

### 5.2 Widget type (`EIG-DSH-W-*`)
typeID · binding contract (what register shapes it accepts) · mandatory badges · rendering rules (units via 38 register, direction conventions 30 §6 translated-not-resigned, colour semantics per severity/threshold standards) · drilldown contract (to provenance: source records, 49 explanations, 39 lineage).

## 6. Controlled Vocabularies
Widget types (§3.1), badges (§3.2 — closed; additions are catalogue MINOR with audit), refresh classes (§3.3), drilldown targets. House style tokens referenced (brand register).

## 7. Applicability Rules
- **A1 — Rendering never launders.** Every value renders with its register truth: caveats, class, freshness, conditions; aggregation in the UI cannot strip badges (worst-of propagation for composites — a panel containing one SYNTHETIC series is SYNTHETIC-badged at panel level too).
- **A2 — Registered bindings only.** Widgets bind registers/entities/runs by reference; UI-side computation beyond presentation transforms (unit display, axis scaling) is forbidden — convenience aggregates register first (73 §12).
- **A3 — Permission-scoped truth.** Rendering filters by the viewer's grants (16) and data scopes (own-subject occupant pattern); the same dashboard shows different lawful subsets, never different numbers (the value for a given scope is identical for all who may see it).
- **A4 — Drilldown to provenance.** Every rendered value drills to its sources within two interactions: record → lineage (39) / explanation (49) / event trail (61/63) — "where did this number come from?" is a UI guarantee.
- **A5 — Cohort floors at the glass.** Cohort/comparison widgets enforce n≥10 at render time (38 A7/65 A2) — small cells grey out with the floor stated, never approximate.
- **A6 — Forecast honesty.** Forecast widgets render uncertainty per UF form (fans/intervals), validity boundaries, and confidence — point-only rendering of probabilistic runs is a V-finding (46 A5 at the glass).
- **A7 — Environment separation.** Sandbox views are environment-badged end to end; mixed-environment widgets are impossible by binding rules (69/42 chain).

## 8. Validation Rules
V1 schema completeness (seven spec fields); bindings resolve; V2 widget bindings match type contracts; no UI-side computation beyond declared transforms (code audit); V3 badge computation correct per §3.2 sources (sampled render audits, quarterly with 73); V4 permission scoping verified per role variant; own-subject filters structural; V5 badges non-suppressible (config audit); V6 drilldown contracts satisfied (two-interaction provenance test); V7 refresh/freshness indicators accurate vs declared classes; V8 cohort floor enforcement at render; V9 units/directions per register conventions (38/30); V10 dashboards immutable per version; binding changes are MINOR+ with re-audit.

## 9. Relationships to Other Catalogues
73 (hosting apps; badge propagation 73 A3) · 57/65 (KPI tiles; percentile context) · 38/46 (series; forecast rendering) · 52/55/30 (flexibility widgets) · 60/61/62/63 (ops widgets; coupling badges) · 49 (explanation widgets; drilldowns) · 39 (lineage drilldowns) · 16 (permission scoping) · 69 (UNKNOWN-CTX badge; context filters) · 77 (shared rendering/badge rules for documents) · 42/20 (synthetic/sandbox marking — the hyperGrid envelope at every glass).

## 10. Benchmarking Requirements
Rendering-integrity audit pass rates (V3/V5), drilldown coverage, freshness-indicator accuracy; usage analytics per widget (privacy-reviewed) for UX iteration.

## 11. Dataset Requirements
Dashboard exports (PNG/CSV of views) carry badge text and provenance footers; exported data ships register citations (43-style) — screenshots don't launder either.

## 12. Feature Requirements
Rendered derived quantities resolve to registered features/KPIs (A2); percentile positions via 65 §12 features.

## 13. Model Requirements
Model-output widgets display active release identity on demand (73 §13); demoted/degraded model states surface via badges.

## 14. KPI Requirements
W-KPI is the canonical KPI rendering (threshold bands, percentile context, QC badges); KPI definitions (57) may declare preferred widget configurations — one number, one look.

## 15. Response Derivative Requirements
W-FLX renders RD truth honestly: magnitude with CI, confidence vs the 0.8 gate, recency countdown, inherited conditions, saturation/rebound context (52 A5 arithmetic visible) — the RD concept legible at the glass; profile widgets (55) show axis scores with input tracing.

## 16. Decision Requirements
W-DEC renders the decision frame: coupling badges, gate outcomes, abstentions with reasons, action trails with verification states — automation visible, never hidden (60 §16 at the UI).

## 17. Ontology Mapping
Dashboard/widget ↔ EIG `eig:Dashboard`/`eig:Widget` with binding properties; badges as annotation individuals; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `Dashboard`, `WidgetType`; edges `RENDERS`→register bindings, `SCOPED_TO`→16, `HOSTED_IN`→73, `BADGES` {computed set}. Rendering-impact queries ("which dashboards show this KPI?") support change notices.

## 19. Digital Twin Mapping
Twin views are widget compositions over 32 bindings with FIDELITY badges; twin what-if results render as simulated-state-labelled series (never confusable with measured).

## 20. Governance
Owner: platform owner; widget-type additions reviewed for badge completeness; quarterly render audits with 73; house-style changes via brand governance.

## 21. Versioning
Dashboards/widget types semver. Catalogue 1.0.0.

## 22. Example Records

### EIG-DSH-0014 — Dispatch Operations board (hosted in EIG-APP-14)
- users: ops roles; widgets: W-DEC (live EIG-DEC instances + AUTO badges), W-FLX (portfolio envelope vs commitments, RD gate states), W-EVT (EF-CTL/EF-NET stream), W-TS (delivered vs contracted, RF-LIVE), W-KPI (FLEX-DELIV with QC)
- badges in force: AUTO/ADVISORY, DEMOTED, LICENCE-STATE, STALE, DEROGATION; drilldowns: action→trail→explanation (the EIG-ACTN-I-0192277 chain is two clicks)

### EIG-DSH-0021 — hyperGrid public board (EIG-APP-19/SVC-DATA-PUB surface)
- users: public scope; every series SYNTHETIC-badged per the provenance envelope (A1/A7 — the no-fabrication standard at the glass); cohort layers n≥10; drilldown to generator/scenario provenance (42)

### EIG-DSH-0007 — Occupant home view (EIG-APP-18 mobile)
- own-subject scope structural (A3); widgets: comfort state, own consumption, event notices with AU-OCC explanations (W-XAI), credit statement tile; badges: ESTIMATED where imputed, plain-language freshness

## 23. Completion Criteria
☑ All seven spec fields (ID, users, widgets, KPIs, data sources, refresh frequency, permissions) ☑ Widget taxonomy with binding contracts ☑ The normative badge-duty register (non-suppressible, worst-of propagation) ☑ Registered-bindings-only + drilldown-to-provenance guarantees ☑ Cohort floors, forecast honesty, environment separation at the glass ☑ Worked ops, public-synthetic, and occupant boards closing the rendering-integrity story.
