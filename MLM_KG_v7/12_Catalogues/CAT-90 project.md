---
type: catalogue
ontology: EIG-MLM
catalogue_no: 90
catalogue_id: EIG-CAT-090
entity: project
band: governance
status: active
tags: [eig-mlm, catalogue, band:governance, entity:project]
aliases: ["EIG-CAT-090", "Catalogue 90", "project"]
---

# CAT-90 — Project

> **EIG-CAT-090** · band: **Aggregation & Governance** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-15 organisation]] · [[CAT-43 dataset]] · [[CAT-44 model]] · [[CAT-57 kpi]] · [[CAT-77 report]] · [[CAT-78 use_case]] · [[CAT-88 site]] · [[CAT-89 scenario]]
**Used by (downstream):** [[CAT-73 application]] · [[CAT-74 service]] · [[CAT-77 report]] · [[CAT-79 ontology]] · [[CAT-85 semantic_pattern]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-090`
**Entity prefix:** `EIG-PRJ-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (portfolio authority)
**Depends on (upstream):** 15 organisation, 43 dataset, 44 model, 57 kpi, 77 report, 78 use_case, 88 site, 89 scenario
**Used by (downstream):** 77 (RT-PRJ deliverables), 73/74 (project-funded capabilities), 79/85 (project semantic modules), funding-body reporting chains

---

## 1. Definition

The **Project Catalogue** registers projects and pilots (`EIG-PRJ-*`): funded undertakings — Horizon Europe, SEAI RD&D, SFI, Erasmus+ and internal — each defined by objectives, partners (15), sites (88), models (44), data (43), KPIs (57), and **deliverables**. Purpose: **programme governance** — the layer that binds the research portfolio to the operational registers: which project funded which capability, which pilot site produced which evidence, which deliverable froze which results. The portfolio is real and register-typed: the funded set (TIPS4PED, O-CEI, TWINVERSE, BIM4EEB, IREPO, PREDICT, INNOV8HEAT, BLAST!, ODEON-line work) and the active pipeline live here with their site/data/model bindings — attribution becomes a register fact, not a memory exercise (A4: PSR/PULSEse → OPTICS/PULSE, never FLEXUS).

## 2. Scope
**In scope:** project schema (the spec's eight: ID, objectives, partners, sites, models, data, KPIs, deliverables); programme typing (funder, instrument, grant terms); work-package structure (light — enough to anchor deliverables and attribution); pilot-site bindings (88) and evidence flows; deliverable register (77 RT-PRJ instances as the formal outputs); attribution rules (which platform/result belongs to which project — the single most audit-sensitive fact); cross-project capability reuse (funded-once, reused-with-attribution). **Out of scope:** task-level project management (PrisM-class tooling operates the plans; this register holds the governance facts), proposal pipeline content (pre-award), financial administration.

## 3. Classification Structure

### 3.1 Programme types (`programme`)
PG-HE (Horizon Europe) · PG-H2020 (legacy) · PG-SEAI (RD&D) · PG-SFI · PG-ERA (Erasmus+) · PG-INT (internal/strategic). Programme binds reporting cadences (77), open-science obligations (43 A8 interactions), and IP/attribution regimes.

### 3.2 Project lifecycle
PROPOSED → AWARDED → ACTIVE → REPORTING (final period) → CLOSED (obligations: data retention, open deposits, audit windows) → ARCHIVED.

### 3.3 Attribution classes (the audit-sensitive register)
Per capability/output: ATTR-SOLE (one project) · ATTR-CO (declared shares) · ATTR-REUSE (built under X, reused in Y with acknowledgement rules). **Standing rule A4:** PSR (Parametric Simulation Registry) and PULSEse outputs attribute to **OPTICS within the PULSE Programme** — never to FLEXUS or the Tyndall FLEXUS ecosystem; the register encodes this class of binding so misattribution is machine-detectable.

## 4. Hierarchy
```
Programme (PG-*) → Project (EIG-PRJ-*: objectives + partners + WPs)
                       ├── sites (88) → evidence flows (data 43, RD/behaviour chains)
                       ├── funded capabilities (44 models, 73 apps, 79 modules) with attribution
                       └── deliverables (→77 RT-PRJ instances) per WP/milestone
```

## 5. Field Groups & Fields

### 5.1 Project (`EIG-PRJ-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | projectID | string | `EIG-PRJ-\d{4}` (+ external grant ID) | M | Spec: Project ID |
| 2 | name / acronym / programme | string/enum | §3.1 | M | Identity + regime |
| 3 | objectives | array<json> | [{statement, linked UCs →78, target KPIs →57, scenario frame →89 where pathway-bound}] | M | Spec: Objectives — register-bound, verifiable (78 machinery) |
| 4 | partners | array<json> | [{orgRef →15, role: coordinator/beneficiary/associate, WP leads}] | M | Spec: Partners |
| 5 | sites | array<ref> | →88 EIG-SIT-* pilot sites (Aran cluster, Limerick HeatSync-lineage sites, demo sites per project) | M | Spec: Sites — where evidence comes from |
| 6 | models | array<json> | [{modelRef →44, attribution §3.3}] — funded/extended model census | M | Spec: Models |
| 7 | data | array<json> | [{datasetRef →43, role: produced/consumed, open-deposit obligations, embargo →39}] | M | Spec: Data |
| 8 | kpis | array<ref> | →57 project-grain (grant-agreement indicators registered like everything else) | M | Spec: KPIs |
| 9 | workPackages | array<json> | [{wpID, title, lead, milestones, deliverable slots}] | M | Structure anchor |
| 10 | deliverables | array<json> | [{code (D2.3-class), wpRef, due, reportDef →77, instanceRef when issued, status}] | M | Spec: Deliverables — 77 RT-PRJ bindings |
| 11 | attributionRegister | array<json> | capability/output → §3.3 class + acknowledgement text (the A4 rule rows live here) | M | The audit shield |
| 12 | obligations | json | reporting cadence, open-science, audit retention, ethics refs | M | Grant-term hooks |
| 13 | owner / version / status | — | §3.2 lifecycle | M | Governance |

## 6. Controlled Vocabularies
Programmes (§3.1), lifecycle (§3.2), attribution classes (§3.3), partner roles, deliverable statuses. Acknowledgement-text templates per programme.

## 7. Applicability Rules
- **A1 — Objectives are verifiable.** Project objectives bind use cases (78) and KPIs (57) — delivery is judged by the same acceptance machinery as everything else; "the project will demonstrate X" has a UC with criteria.
- **A2 — Evidence flows are register flows.** Pilot results cite the operational records (RD chains, settlements, validations) produced at the bound sites — project claims and operational truth are the same records (the worked Aran chain *is* ODEON-line evidence).
- **A3 — Deliverables are report instances.** Formal deliverables issue as 77 RT-PRJ instances (frozen snapshots, claims discipline, FMT-DATA where data-bearing) — deliverable quality inherits reporting governance.
- **A4 — Attribution is enforced.** The attribution register is checked at publication/report generation (77 approval workflows query it): outputs render their funding acknowledgements and ownership correctly; **the PSR/PULSEse → OPTICS/PULSE rule is a standing register row** — any artefact citing PSR/PULSEse under a FLEXUS banner is a blocking finding.
- **A5 — Reuse with honesty.** Cross-project capability reuse (the FLEXUS-ecosystem economics) records ATTR-REUSE rows: built-under and reused-in both visible — funders see leverage, not double-claiming.
- **A6 — Closure obligations execute.** CLOSED transitions verify open deposits (43), retention configurations, and audit-window readiness — closing a project is a checklist with evidence, not a status flip.

## 8. Validation Rules
V1 schema completeness (eight spec fields); refs resolve (15/88/44/43/57/78/77); V2 objectives→UC bindings live; criteria evaluable; V3 deliverable slots ↔ 77 definitions; issued instances resolve; due-date tracking (63 deadline events); V4 attribution register complete for all funded capabilities; the A4 standing rows present; publication checks wired (77 approval); V5 data obligations tracked (deposits, embargoes per 39); V6 site bindings resolve; evidence-flow joins sampled (A2); V7 lifecycle transitions evidenced (award docs, closure checklists); V8 projects immutable per version.

## 9. Relationships to Other Catalogues
15 (partners) · 88 (pilot sites; the Aran/Limerick anchors) · 44/73/79/85 (funded capabilities with attribution) · 43/39 (data products; open-science; embargoes) · 77 (deliverables as RT-PRJ; acknowledgement enforcement) · 78 (objective verification) · 57 (grant KPIs) · 89 (pathway-framed projects) · 91/92 (policy-aligned objectives; regulatory pilots) · 64 (project workflows: deliverable review, closure).

## 10. Benchmarking Requirements
Portfolio health: deliverable on-time rate, objective-verification rate (UC VERIFIED share), data-obligation compliance, attribution-audit pass rate.

## 11. Dataset Requirements
Project data registers (§5.1.7) bind 43 records; open deposits per programme obligations; evidence corpora retained through audit windows.

## 12. Feature Requirements
Portfolio features (capability reuse counts, evidence productivity per site) for strategic reviews.

## 13. Model Requirements
Funded-model census rows carry attribution; model lineage (44) plus this register answers "who paid for this capability" in one join.

## 14. KPI Requirements
Grant indicators register in 57 (single-formula rule reaches funder reporting); project KPI packs render via 77.

## 15. Response Derivative Requirements
RD-evidence productivity is a pilot deliverable in the ODEON/O-CEI line: coverage attainment at bound sites (52/56 counts) reports as project KPIs — the learning loop's output is fundable, attributed research evidence.

## 16. Decision Requirements
Project steering decisions (scope/resource) stay human-governed (64 workflows); register-side, deliverable risk flags (due-date trajectories) surface via 63/76 to project owners.

## 17. Ontology Mapping
Project ↔ eig:Project (DOAP/EU-vocab alignment candidates per 79 backlog); attribution ↔ funding-acknowledgement pattern; owned by 79.

## 18. Knowledge Graph Mapping
Node `Project`; edges `PARTNERED_WITH`→15, `PILOTS_AT`→88, `FUNDED` {attribution}→44/73/79, `PRODUCED`→43/77, `VERIFIES_BY`→78. "Everything project X funded, produced, and proved" is one traversal; the attribution audit (A4) is a registered query.

## 19. Digital Twin Mapping
Project demo twins (TWINVERSE-line) register under 32 with project attribution; twin-based deliverable evidence labelled per 89/77 rules.

## 20. Governance
Owner: PI; per-project PMs; attribution register changes reviewed (the audit shield is board-visible); portfolio review quarterly; closure sign-offs per A6.

## 21. Versioning
Projects semver; lifecycle per §3.2. Catalogue 1.0.0.

## 22. Example Record

### EIG-PRJ-0011 — ODEON / O-CEI Aran pilot line (PG-SEAI/HE composite per actual grants)
- objectives: residential flexibility evidence at island scale → UC-0014/UC-0007 (78, VERIFIED ✓), RD coverage + delivery KPIs (57); partners: Tyndall/IERC + co-op (EIG-ORG-0044) + technology partners (15)
- sites: EIG-SIT-0007 (88); models funded: FLEXENV line, SER-07 estimators (ATTR-CO with declared shares); data: EIG-DSET-ARAN-ELEC-HH (produced, open-deposit per terms), ODEON-ASSET-TELEM (43)
- deliverables: field-evidence reports as 77 RT-PRJ (frozen snapshots of the worked chains); WPs anchor the Jetson/edge deployment line (35/34 records as implementation evidence)
- attribution register: edge platform ATTR-REUSE rows (built-under/reused-in visible); **standing row: PSR/PULSEse → OPTICS/PULSE (A4)** — enforced at every publication gate

## 23. Completion Criteria
☑ All eight spec fields (ID, objectives, partners, sites, models, data, KPIs, deliverables) ☑ Programme governance binding portfolio to operational registers (evidence = the same records) ☑ Deliverables as frozen report instances ☑ Attribution register with the PSR/PULSEse→OPTICS/PULSE standing rule machine-enforced ☑ Reuse honesty + closure obligations ☑ The ODEON/Aran line worked as the anchor project.
