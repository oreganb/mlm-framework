---
type: catalogue
ontology: EIG-MLM
catalogue_no: 77
catalogue_id: EIG-CAT-077
entity: report
band: applications
status: active
tags: [eig-mlm, catalogue, band:applications, entity:report]
aliases: ["EIG-CAT-077", "Catalogue 77", "report"]
---

# CAT-77 — Report

> **EIG-CAT-077** · band: **Applications & Reporting** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-39 metadata]] · [[CAT-43 dataset]] · [[CAT-57 kpi]] · [[CAT-65 benchmark]] · [[CAT-75 dashboard]] · [[CAT-92 regulation]] · [[CAT-93 compliance]]
**Used by (downstream):** [[CAT-18 stakeholder]] · [[CAT-74 service]] · [[CAT-91 policy]] · [[CAT-93 compliance]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-077`
**Entity prefixes:** `EIG-RPT-` (report definitions), `EIG-RPT-I-` (report instances)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (reporting policy); domain owners per type
**Depends on (upstream):** 39 metadata, 43 dataset, 57 kpi, 65 benchmark, 75 dashboard (shared rendering rules), 92 regulation, 93 compliance
**Used by (downstream):** 18 stakeholder, 74 service (attainment reports), 91 policy, 93 compliance evidence

---

## 1. Definition

The **Report Catalogue** governs formal reporting: report definitions (`EIG-RPT-*`) — each with report type, audience, frequency, data sources, KPIs, format, and distribution — and instances (`EIG-RPT-I-*`): the generated documents with frozen source snapshots. Reports are dashboards' formal siblings: where dashboards (75) render live truth, reports freeze it for the record — versioned documents with citable provenance, the same badge/caveat integrity, and distribution control. Purpose: **reporting and compliance** — every formal claim EIG makes (to partners, funders, regulators, communities, the public) is a registered report instance whose every number reproduces from cited snapshots (A1).

## 2. Scope
**In scope:** definition schema (the spec's eight: ID, type, audience, frequency, data sources, KPIs, format, distribution); report type taxonomy; source-snapshot freezing; rendering integrity inheritance (75 badge rules in document form); format register (docx/pdf/md house-pattern, machine-readable companions); distribution control and acknowledgement; regulatory report bindings (92/93); narrative governance (claims vs evidence). **Out of scope:** dashboard mechanics (75), document tooling (the skills/templates pipeline — house style refs), KPI/benchmark content (57/65), compliance obligation tracking (93 — reports are evidence artefacts there).

## 3. Classification Structure

### 3.1 Report types (`reportType`)

| Code | Type | Audience class | Cadence norm |
|---|---|---|---|
| RT-OPS | Operational | internal ops/management | weekly/monthly |
| RT-SLA | Service attainment | service consumers (74 A2) | per SLA reporting terms |
| RT-PRJ | Project/funder | consortium, funding bodies (90 projects) | per grant agreement (deliverable-pattern) |
| RT-REG | Regulatory/statutory | regulators (92 instruments) | per instrument deadlines (93 tracked) |
| RT-CMU | Community/stakeholder | communities, occupants (18) | quarterly/annual, plain-language |
| RT-PUB | Public/transparency | open | annual (hyperGrid-class disclosures, 43 A8 floor) |
| RT-GOV | Governance/board | model board, PI | monthly (model/decision/learning health) |

### 3.2 Integrity inheritance
Reports inherit the 75 badge-duty register in document form: caveat footnotes (QC codes), SYNTHETIC/SANDBOX marking, staleness statements, coupling-mode disclosure where automation is discussed, estimated-data disclosure (40), cohort floors (n≥10). Aggregation in prose cannot launder (75 A1 worst-of propagation applies to narrative claims too, A4).

### 3.3 Formats
FMT-DOCX / FMT-PDF (house templates per writing-style governance) · FMT-MD (internal) · FMT-DATA (machine-readable companion: the figures behind every chart/table as a 43-style package, mandatory for RT-REG/RT-PUB, V6).

## 4. Hierarchy
```
Type (RT-*) → Report definition (EIG-RPT-*: sources × KPIs × audience × cadence)
                  └── Instance (EIG-RPT-I-*: period → frozen snapshot → document(s) → distribution record)
```

## 5. Field Groups & Fields

### 5.1 Report definition (`EIG-RPT-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | reportID | string | `EIG-RPT-\d{4}` | M | Spec: Report ID |
| 2 | reportType / name | enum/string | §3.1 | M | Spec: Report type |
| 3 | audience | json | →16 roles / 15 orgs / 18 stakeholder groups / public; classification (internal/external/open) | M | Spec: Audience |
| 4 | frequency | json | cadence + deadline rules (RT-REG: 92 instrument deadlines via 93 tracking) | M | Spec: Frequency |
| 5 | dataSources | array<ref> | →57 values, 65 sets, 27 lines, 63/64 metrics, 43 datasets — registered only (75 A2 symmetry) | M | Spec: Data sources |
| 6 | kpis | array<ref> | →57 with rendering configs (75 W-KPI conventions in print) | M | Spec: KPIs |
| 7 | sections | json | outline: per section {content contract, evidence bindings, narrative owner} | M | Claims map (A4) |
| 8 | format | array<enum> | §3.3 (+ FMT-DATA mandate by type) | M | Spec: Format |
| 9 | distribution | json | channels (delivery, portal 73, publication), recipients (role/org-resolved), acknowledgement requirements, embargo rules (39-aligned) | M | Spec: Distribution |
| 10 | approvalWorkflow | ref | →64 (review/sign-off per type; RT-REG: compliance owner sign-off 93) | M | Release gate |
| 11 | retentionPolicy | duration | per type (RT-REG ≥ regulatory horizon; settlement-touching ≥7 y) | M | Record keeping |
| 12 | owner / version / status | — | standard | M | Governance |

### 5.2 Report instance (`EIG-RPT-I-*`)
definitionRef@version · period · **sourceSnapshot** {every cited value's register version: KPI values (57 V-refs), dataset versions (43), benchmark sets (65), event/workflow metrics windows} · generatedAt/by · documents {format, hash} + FMT-DATA package ref · badge/caveat register (computed, §3.2) · approval record (64 instance) · distribution log {recipients, deliveredAt, acks where required} · supersedes (corrections republish, never edit — 27 A6 discipline). Immutable.

## 6. Controlled Vocabularies
Types (§3.1), formats (§3.3), audience classifications, distribution channel forms, acknowledgement requirements. Narrative claim classes: {computed (snapshot-backed), interpreted (owner-attributed analysis), forward-looking (labelled, never computed-styled)}.

## 7. Applicability Rules
- **A1 — Reproducible numbers.** Every figure/table in an instance reproduces from its sourceSnapshot; a number without a register citation does not appear (75 A2/A4 in document form). The FMT-DATA companion makes this machine-verifiable where mandated.
- **A2 — Freeze at generation.** Snapshots pin versions at generation time; later register changes don't silently alter issued reports — corrections are superseding instances with change notes.
- **A3 — Integrity badges in print.** §3.2 inheritance: caveats as footnotes, SYNTHETIC marking on any synthetic-lineage content (the hyperGrid standard in documents — 42 chain), estimated/coverage disclosures (40/43 §11), cohort floors.
- **A4 — Claims discipline.** Narrative sections map to claim classes: computed claims cite snapshots; interpretations carry their author; forward-looking statements are labelled — prose cannot launder what tables cannot (the writing-style governance handles voice; this rule handles truth).
- **A5 — Audience-fit without truth-fit.** Plain-language variants (RT-CMU/occupant-facing) simplify presentation, never numbers: the same snapshot underlies every audience rendering of a period (75 A3 symmetry — different lawful views, identical values).
- **A6 — Regulatory reports are compliance evidence.** RT-REG instances bind to 93 obligations (instrument, deadline, submission proof in the distribution log); late/missing instances are 93 findings with 63 deadline events upstream.
- **A7 — Open publications meet the open floor.** RT-PUB content satisfies 43 A8 (no personal grain, cohort floors, licence clarity, SYNTHETIC badging) — publication is a dataset-release-grade act.

## 8. Validation Rules
V1 schema completeness (eight spec fields); sources/KPIs resolve; V2 instances carry complete snapshots (every rendered value traceable — sampled reproduction audits); V3 badge/caveat computation per §3.2 (print audits with 75 quarterly); V4 claims map coverage: sections' computed claims all snapshot-bound; V5 approval workflow executed pre-distribution (64 instance attached); SoD where type demands; V6 FMT-DATA companions present where mandated; hashes recorded; V7 distribution logs complete; acknowledgements where required; embargo rules honoured; V8 RT-REG: 93 obligation bindings + deadline attainment; V9 instances immutable; supersession with change notes; retention enforced; V10 audience variants share snapshots (A5 check).

## 9. Relationships to Other Catalogues
75 (shared rendering integrity; dashboards↔reports as live/frozen siblings) · 57/65 (content: KPIs, benchmark context) · 43/39 (snapshots; FMT-DATA packaging; embargo) · 64 (approval workflows; distribution tasks) · 93/92 (regulatory bindings; deadlines; evidence) · 74 (SLA attainment reports per service terms) · 90 (project deliverable reports) · 18 (stakeholder communication plans consume RT-CMU) · 27 (settlement-derived figures at settlement fitness) · 49 (automation disclosures cite explanation packs where decisions are discussed).

## 10. Benchmarking Requirements
Reporting-ops metrics: on-time rate per type (RT-REG = 100% target), reproduction-audit pass rate, correction/supersession rate, acknowledgement completion.

## 11. Dataset Requirements
FMT-DATA companions are 43-grade packages (manifest, checksums); RT-PUB data meets the open floor (A7); snapshots retained per retention policies.

## 12. Feature Requirements
Report-derived aggregates register (41/57) before appearing — period-over-period deltas, attainment ratios — so dashboards and reports agree (75 §14 symmetry).

## 13. Model Requirements
Reports discussing model performance cite validation/benchmark records (47/51), never ad-hoc evaluations (47 §14); model-card summaries embed by reference.

## 14. KPI Requirements
KPI renderings follow 57/75 conventions (thresholds, percentile context, QC footnotes); the period's KPI value set in a report is the same V-records analytics sees.

## 15. Response Derivative Requirements
Flexibility reporting (RT-SLA/RT-PRJ/RT-PUB) presents the RD layer honestly: delivery vs promise (FLEX-DELIV with settlement fitness), RD coverage progress, confidence distributions — and never presents synthetic or sandbox trial results as field performance (A3, the no-fabrication standard's reporting clause).

## 16. Decision Requirements
Reports discussing automated operation disclose coupling modes, demotion incidents, and abstention statistics (60 audit aggregates) — automation is reported as governed, with the governance visible.

## 17. Ontology Mapping
Report ↔ EIG `eig:Report` (dct/DCAT document pattern) with snapshot qualifications (PROV); regulatory bindings → 92 instruments; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `ReportDefinition`, `ReportInstance`; edges `SNAPSHOTS`→57/43/65 versions, `APPROVED_BY`→64, `DISTRIBUTED_TO`→15/16/18, `EVIDENCES`→93 obligations, `SUPERSEDES`. "What did we tell the regulator, from what data?" is one query.

## 19. Digital Twin Mapping
Twin-derived figures in reports carry FIDELITY context (32) and calibration-evidence citations; what-if/scenario content is labelled simulated (never measured-styled).

## 20. Governance
Owner: PI (policy); type owners per §3.1; approval workflows enforce SoD (author ≠ approver for external types); annual reporting-calendar review aligned with 92/93 and grant agreements; house templates via writing-style governance.

## 21. Versioning
Definitions semver; instances immutable with supersession. Catalogue 1.0.0.

## 22. Example Records

### EIG-RPT-0012 — Flexibility Service Attainment Report (RT-SLA, the 74 EIG-SVC-FLEX-OPS output)
- audience: consumer org (EIG-ORG-0044) per contract terms; frequency monthly D+10
- sources: SLA KPI values (57 V-refs), 27 statement aggregates, 63 incident metrics; sections: attainment table (computed), incident narrative (interpreted, ops owner), outlook (forward-looking labelled)
- formats [PDF + FMT-DATA]; approval: service owner sign-off (64); distribution: portal + email, ack required; instance 2026-05: all dimensions ✓, snapshot complete, reproduction audit passed

### EIG-RPT-0031 — Annual Public Flexibility & Data Report (RT-PUB)
- the hyperGrid-standard disclosure: portfolio outcomes (cohort-floored), open-data census (43 releases), SYNTHETIC content explicitly badged and quarantined to its own labelled section; FMT-DATA mandatory; A7 floor checks in approval workflow

### EIG-RPT-0044 — Model & Learning Governance Report (RT-GOV, monthly board pack)
- sources: 44 census health, 47 freshness, 51 leaderboard movements, 70 slot health, 71 licence states, 66 containment telemetry, 67/68 incentive accounting; the board reads the registers, frozen

## 23. Completion Criteria
☑ All eight spec fields (ID, type, audience, frequency, data sources, KPIs, format, distribution) ☑ Frozen-snapshot reproducibility with machine-readable companions ☑ Rendering-integrity inheritance in print (badges, claims discipline, audience-fit-not-truth-fit) ☑ Regulatory bindings as compliance evidence ☑ Open-publication floor ☑ Supersede-never-edit corrections ☑ Worked SLA, public, and governance reports closing the formal-record chain.
