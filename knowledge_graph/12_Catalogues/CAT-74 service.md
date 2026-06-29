---
type: catalogue
ontology: EIG-MLM
catalogue_no: 74
catalogue_id: EIG-CAT-074
entity: service
band: applications
status: active
tags: [eig-mlm, catalogue, band:applications, entity:service]
aliases: ["EIG-CAT-074", "Catalogue 74", "service"]
---

# CAT-74 — Service

> **EIG-CAT-074** · band: **Applications & Reporting** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-15 organisation]] · [[CAT-16 role]] · [[CAT-23 contract]] · [[CAT-31 platform]] · [[CAT-64 workflow]] · [[CAT-73 application]]
**Used by (downstream):** [[CAT-27 settlement]] · [[CAT-57 kpi]] · [[CAT-77 report]] · [[CAT-93 compliance]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-074`
**Entity prefix:** `EIG-SVC-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (service portfolio) with Operations
**Depends on (upstream):** 15 organisation, 16 role, 23 contract, 31 platform, 64 workflow, 73 application
**Used by (downstream):** 27 settlement (service charging where applicable), 57 kpi (SLA KPIs), 77 report, 93 compliance

---

## 1. Definition

The **Service Catalogue** registers business services (`EIG-SVC-*`): the externally meaningful units of value delivery — each defined by description, service users (organisations 15 / roles 16 under contracts 23), **SLA**, inputs, outputs, the applications (73) that implement it, and the workflows (64) that operate it. A service is the commitment layer: applications are software, services are promises — availability, quality, support, and remedies, owned and reportable. Purpose: **business service delivery** — what EIG actually offers (to consortium partners, building owners, communities, market actors) is enumerated, SLA-bound, and measured (A1).

## 2. Scope
**In scope:** service schema (the spec's eight: ID, description, users, SLA, inputs, outputs, applications, workflows); service family taxonomy; SLA structure (availability, latency, quality, support, remedies) and measurement bindings (57); consumer onboarding/offboarding (contract-gated); dependency mapping (services → apps → platform); service-level reporting; incident interplay (63/64). **Out of scope:** application internals (73), platform engineering SLOs (31 — internal targets that *support* external SLAs), contract commercial terms (23 — referenced), pricing (21/23).

## 3. Classification Structure

### 3.1 Service families (`family`)

| Code | Family | Consumers | Canonical services |
|---|---|---|---|
| SF-MON | Monitoring & insight | building owners, facility orgs | portfolio monitoring service (apps 01–04), benchmarking service (02/65) |
| SF-FLX | Flexibility services | flexumers, aggregational consumers, market parties | flexibility enablement (RD estimation + prequal, apps 10–12), dispatch operations (app 14), settlement & statements (app 15) |
| SF-OPT | Optimisation & advisory | owners, ESCOs (23 ESCO contracts) | control optimisation (app 16), retrofit advisory (app 17) |
| SF-ENG | Engagement | occupants, communities | occupant service (app 18), community portal service (app 19) |
| SF-DATA | Data services | research partners, public | dataset publication (43 releases incl. hyperGrid-class), API access (31/34 EIG-COM-API-*) |
| SF-RES | Research/sandbox | consortium, innovators | NEST sandbox access (20 EIG-MKT-SBX-01 chain), twin-as-a-service trials (32) |

### 3.2 SLA dimensions (per service, measured)

| Dimension | Form | Measurement (57 binding) |
|---|---|---|
| Availability | % per period + maintenance windows | KF-PLT availability KPIs |
| Latency/freshness | response times; data freshness bounds (46 A2 alignment) | latency KPIs, staleness incidents |
| Quality | accuracy/fitness floors (40 purposes), delivery quality (FLEX-DELIV) | registered KPIs with caveat rules |
| Support | response/resolution per severity (63 SEV alignment) | MTTA/MTTR KPIs |
| Remedies | credits/escalations per 23 clauses | settlement/contract hooks |

### 3.3 Consumer states
ONBOARDING (contract executed 23, access provisioned 16) → ACTIVE → SUSPENDED (contract/compliance cause) → OFFBOARDED (data handling per retention/GDPR duties, 37 A7).

## 4. Hierarchy
```
Family (SF-*) → Service (EIG-SVC-*: promise + SLA)
                    ├── implemented by → 73 applications (+ 31 platform deps)
                    ├── operated by → 64 workflows (support, incident, onboarding)
                    └── consumers (15/16 under 23) with per-consumer SLA instances
```

## 5. Field Groups & Fields

### 5.1 Service (`EIG-SVC-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | serviceID | string | `EIG-SVC-[A-Z0-9-]{3,24}` | M | Spec: Service ID |
| 2 | family / name | enum/string | §3.1 | M | Identity |
| 3 | description | string | ≤2000 chars: the promise in plain language | M | Spec: Service description |
| 4 | serviceUsers | json | consumer classes (→15 org types / 16 roles) + contract gating (→23 templates) | M | Spec: Service users |
| 5 | sla | json | §3.2 dimensions with values, measurement KPI refs (57), reporting cadence, remedies (→23 clauses) | M | Spec: SLA — measured, not asserted (A2) |
| 6 | inputs | json | what consumers provide (data streams →37 registration, consents →03, configurations) | M | Spec: Inputs |
| 7 | outputs | json | what consumers receive (app access, statements 27, datasets 43, reports 77, alerts 76) | M | Spec: Outputs |
| 8 | applications | array<ref> | →73 EIG-APP-* implementing | M | Spec: Applications |
| 9 | workflows | array<ref> | →64 (onboarding, support, incident, offboarding) | M | Spec: Workflows |
| 10 | dependencies | json | →31 platform services, →35 edge (where service includes on-site), upstream data (37) | M | Failure-domain map |
| 11 | environmentScope | enum | production / sandbox (SF-RES) — never mixed (A5) | M | Firewall posture |
| 12 | owner / version / status | — | standard | M | Governance |

### 5.2 Per-consumer service instance
consumerRef (15/23) · state §3.3 · provisioned scopes (16 grants, data scopes) · SLA instance (any contracted deviations from defaults, 23) · onboarding record (64 instance) · SLA attainment history refs.

## 6. Controlled Vocabularies
Families (§3.1), SLA dimensions (§3.2), consumer states (§3.3), remedy forms. Severity alignment to 63 SEV classes for support SLAs.

## 7. Applicability Rules
- **A1 — The promise is the register.** External commitments exist only as registered services; side-letter promises outside SLA structure are governance findings. Contracts (23) reference serviceIDs.
- **A2 — Measured SLAs.** Every SLA dimension binds measurement KPIs (57) computed from the same operational truth as everything else (63 events, 40 fitness, 27 settlement) — attainment is computed, reported (77), and remedy-triggering per 23, never self-graded.
- **A3 — Contract-gated access.** Consumer provisioning flows from executed contracts through onboarding workflows (64) to least-privilege grants (16); offboarding executes data duties (retention, return, deletion per GDPR/23 terms) verifiably.
- **A4 — Dependency honesty.** Services declare their failure domains (platform 31, edge 35, upstream providers 37); SLA values are set within what dependencies support (an SLA exceeding its dependency chain is a design error, V4); upstream incidents propagate to consumer-facing status honestly.
- **A5 — Environment separation.** SF-RES sandbox services are firewall-complete (20 A2 chain): sandbox consumers, sandbox data, sandbox apps — production services never expose sandbox content unbadged, and vice versa.
- **A6 — Occupant/community services carry their ethics.** SF-ENG services embed consent (03), override primacy (29 A4), cohort floors (38 A7), and AU-OCC explanation duties (49) as service features, not optional settings.

## 8. Validation Rules
V1 schema completeness (eight spec fields); refs resolve (73/64/23/15/16); V2 SLA dimensions measured (57 KPI bindings live); remedies resolve to 23 clauses; V3 consumer instances contract-gated; grants least-privilege audited; V4 SLA-vs-dependency consistency (A4 static check at registration); V5 onboarding/offboarding workflows exist and execute (instances logged per consumer transition); V6 environment scope enforced (badging/firewall audits); V7 attainment reporting cadence honoured (77 instances); V8 services immutable per version; SLA changes notify consumers per 23 terms.

## 9. Relationships to Other Catalogues
73 (implementation) · 64 (operation: onboarding, support, incident) · 23 (commercial gating; remedies) · 15/16/17 (consumers; access) · 57 (SLA measurement) · 63 (incident truth) · 27 (statements; service-relevant settlement) · 43/37 (data services; consumer stream registration) · 31/35 (dependency domains) · 77 (service reports) · 93 (compliance: data-handling duties evidence).

## 10. Benchmarking Requirements
Service health: SLA attainment per dimension per service, consumer satisfaction (where surveyed via 18 stakeholder machinery), onboarding cycle time (64 metrics), remedy incidence.

## 11. Dataset Requirements
Attainment computations cite operational corpora; consumer-facing data outputs ship as governed packages (43) under service access rules.

## 12. Feature Requirements
Service-state features (41): per-consumer attainment trends, support load — for capacity planning and 77 reporting.

## 13. Model Requirements
Services embedding model outputs inherit model governance visibility (active releases, validation states) in their quality dimensions — "the forecast service" promises governed forecasts (46), not numbers.

## 14. KPI Requirements
SLA KPIs register in 57 (single-formula rule); service reports (77) render attainment with the same QC caveat machinery.

## 15. Response Derivative Requirements
SF-FLX services are the RD layer commercialised: flexibility enablement (estimation campaigns to RD coverage, prequal), dispatch operations, and settlement statements — service quality dimensions include RD coverage attainment and FLEX-DELIV, making the RD concept's value contractually legible.

## 16. Decision Requirements
Service-embedded automation discloses coupling modes to consumers (60 visibility through 73 A3 badges); consumer-affecting decisions are explainable on request (49 AU-REG/AU-OCC packs per audience).

## 17. Ontology Mapping
Service ↔ EIG `eig:BusinessService` (SEAS/GoodRelations-adjacent) with SLA structures; implementation/operation properties to apps/workflows; alignment owned by 79.

## 18. Knowledge Graph Mapping
Node `Service`; edges `IMPLEMENTED_BY`→73, `OPERATED_BY`→64, `CONSUMED_BY`→15 {contract}, `MEASURED_BY`→57, `DEPENDS_ON`→31/35/37. "What breaks for whom if this platform service degrades?" is the dependency query.

## 19. Digital Twin Mapping
Twin-as-a-service (SF-RES) packages 32 capabilities under SLA; consumer twin views are service outputs with the same fidelity-badge duties.

## 20. Governance
Owner: PI; per-service owners; SLA changes board-reviewed with consumer notice; quarterly attainment reviews; offboarding audits (data duties) with DPO.

## 21. Versioning
Services semver; consumer instances lifecycle-tracked. Catalogue 1.0.0.

## 22. Example Records

### EIG-SVC-FLEX-OPS — Flexibility Operations Service (SF-FLX)
- description: end-to-end flexibility delivery for enrolled portfolios — estimation, prequalification, dispatch, settlement statements
- users: flexumer orgs (15) under flexumer contracts (23 EIG-CON-001207 class); the Aran co-op (EIG-ORG-0044) is the worked consumer
- SLA: dispatch availability 99.5% (KF-PLT), dispatch latency per 29 classes, FLEX-DELIV portfolio floor 90% (measured 57), statement delivery D+5 post-settlement, support SEV-2 ≤1 h
- inputs: stream registration (37 TR-A metering), consents (03), asset declarations (11); outputs: apps 10/12/14/15 access, statements (27), monthly attainment report (77)
- workflows: WF onboarding/support/dispute; dependencies: 31 PLT-MKT/CORE, 35 cluster gateways (failure domains declared); attainment 2026-Q1: all dimensions ✓

### EIG-SVC-DATA-PUB — Public Data Service (SF-DATA)
- hyperGrid-class publication: open datasets (43 A8 floor) with provenance envelopes, SYNTHETIC badging absolute (42); SLA: availability + dataset citation stability; consumers: public/research (open licence, no contract gating beyond terms)

## 23. Completion Criteria
☑ All eight spec fields (ID, description, users, SLA, inputs, outputs, applications, workflows) ☑ Family register spanning monitoring/flex/optimisation/engagement/data/sandbox ☑ Measured SLAs with remedy bindings ☑ Contract-gated lifecycle with verifiable offboarding ☑ Dependency honesty + environment separation ☑ Worked flexibility-operations service making the RD layer contractually real.
