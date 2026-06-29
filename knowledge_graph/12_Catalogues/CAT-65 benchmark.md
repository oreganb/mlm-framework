---
type: catalogue
ontology: EIG-MLM
catalogue_no: 65
catalogue_id: EIG-CAT-065
entity: benchmark
band: rl_agents
status: active
tags: [eig-mlm, catalogue, band:rl_agents, entity:benchmark]
aliases: ["EIG-CAT-065", "Catalogue 65", "benchmark"]
---

# CAT-65 — Benchmark

> **EIG-CAT-065** · band: **RL, Context & Agents** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-01 building_classification]] · [[CAT-06 building_benchmark]] · [[CAT-08 geographic]] · [[CAT-12 asset_benchmark]] · [[CAT-38 data]] · [[CAT-43 dataset]] · [[CAT-51 model_benchmark]] · [[CAT-57 kpi]]
**Used by (downstream):** [[CAT-57 kpi]] · [[CAT-60 decision_intelligence]] · [[CAT-75 dashboard]] · [[CAT-77 report]] · [[CAT-89 scenario]] · [[CAT-91 policy]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-065`
**Entity prefixes:** `EIG-BM-` (benchmark definitions), `EIG-BM-V-` (benchmark value sets)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Data Management Lead (register) with domain owners
**Depends on (upstream):** 01 building_classification, 06 building_benchmark, 08 geographic, 12 asset_benchmark, 38 data, 43 dataset, 51 model_benchmark, 57 kpi
**Used by (downstream):** 57 kpi (comparison context), 60 decision_intelligence (screening), 75 dashboard, 77 report, 89 scenario, 91 policy

---

## 1. Definition

The **Benchmark Catalogue** is the framework's umbrella benchmark register: the cross-domain system of comparison references — **benchmark definitions** (`EIG-BM-*`) fixing family, metric, scope, cohort, and source; and **value sets** (`EIG-BM-V-*`) carrying the percentile distributions and best-practice levels per cohort per period. It federates the specialised benchmark catalogues — building (06), asset (12), model (51) — under one schema and adds the families they don't cover (operational, process, data, flexibility, carbon, economic), enabling **cross-domain benchmarking**: any registered KPI (57) can answer "compared to what?" with a governed reference (A1).

Specialised catalogues remain systems of record for their domains (06/12/51 own their cohort mechanics); this register indexes them and owns the rest.

## 2. Scope
**In scope:** the federation schema (family/metric/scope/cohort/source/percentiles/best-practice — the spec's eight); benchmark families across all domains; cohort definition rules (internal cohorts vs external references); source classes (internal computed, national statistics, standards/literature, regulatory targets); percentile and best-practice semantics; refresh cadences; comparability rules (normalisation prerequisites); cross-register indexing of 06/12/51. **Out of scope:** cohort computation mechanics owned by 06/12/51; KPI formulas (57); benchmark-driven decision logic (60).

## 3. Classification Structure

### 3.1 Benchmark families (`family`)

| Code | Family | System of record | Examples |
|---|---|---|---|
| BF-BLD | Building performance | **06** (indexed) | EUI bands A–G, comfort compliance cohorts |
| BF-AST | Asset performance | **12** (indexed) | EIG-ABM-MET-001..022 incl. MET-015 |
| BF-MDL | Model performance | **51** (indexed) | task leaderboards, baseline envelopes |
| BF-FLX | Flexibility | here | RD coverage rates, deliverable kW/dwelling by archetype (55 censuses), delivery success distributions |
| BF-CO2 | Carbon | here | tCO₂e/m² cohorts, CI-weighted consumption references, national grid CI context (28) |
| BF-EC | Economic | here | €/kWh effective cohorts, flex revenue per enrolled kW |
| BF-DAT | Data operations | here | fitness pass-rate references (40), stream availability norms (37), metadata coverage (39 §10) |
| BF-OPS | Process/platform | here | MTTA/MTTR references (63), cycle-time norms (64), dispatch latency (34 classes as targets) |
| BF-EXT | External references | here | SEAI/CSO national statistics, EN/ISO standard values, regulatory targets (92), literature values — provenance-classed |

### 3.2 Source classes (`sourceClass`)

| Code | Class | Trust handling |
|---|---|---|
| SC-INT | Internal computed | from 38/43 corpora under cohort rules (n≥10, 06 discipline) |
| SC-NAT | National/official statistics | 37-registered public sources (SEAI, CSO, EirGrid — the 25-source register lineage) |
| SC-STD | Standards/literature | citation-pinned (version/year); review-dated |
| SC-REG | Regulatory targets | 92-linked; compliance semantics (93) |
| SC-SYN | Synthetic-derived context | 42-sourced (e.g. SYNTH_GRID_IE distributions) — SANDBOX-badged, context only, never compliance/disclosure comparison (42 A3) |

### 3.3 Reference semantics
Per value set: percentile ladder {p10, p25, p50, p75, p90}, best-practice level (definition: top-decile of cohort OR standard-defined target — declared which), and threshold bindings (57 bands may anchor here). Direction (lower/higher-is-better) fixed per metric.

## 4. Hierarchy
```
Family (BF-*) → Benchmark definition (EIG-BM-*: metric × scope × cohort × source)
                    └── Value set (EIG-BM-V-*: period × percentiles + best practice)
06/12/51 entries appear as indexed definitions with passthrough refs.
```

## 5. Field Groups & Fields

### 5.1 Benchmark definition (`EIG-BM-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | benchmarkID | string | `EIG-BM-[A-Z0-9-]{3,24}` | M | Spec: Benchmark ID |
| 2 | family | enum | §3.1 | M | Spec: Benchmark family |
| 3 | metric | ref | →57 EIG-KPI-* (preferred) or registered formula (38/41) + unit + direction | M | Spec: Metric — single-formula rule inherited (57 A1) |
| 4 | scope | json | subject grain + spatial bounds (→08) + sector/class bounds (→01/10) | M | Spec: Scope |
| 5 | cohortDefinition | json | SC-INT: selector + n≥10 floor + exclusions; others: reference population statement | M | Spec: Cohort |
| 6 | source | json | sourceClass §3.2 + provenance refs (43 dataset / 37 stream / citation / 92 instrument) | M | Spec: Source |
| 7 | normalisationPrereq | json | required normalisations for valid comparison (area, climate HDD 09, occupancy 03 — per 57 §5.1.6) | M | Comparability gate (A3) |
| 8 | refreshCadence | duration | P1M–P1Y per family | M | Currency |
| 9 | indexedFrom | ref | →06/12/51 entry for federated rows | C | Passthrough marker |
| 10 | owner / version / status | — | standard | M | Governance |

### 5.2 Value set (`EIG-BM-V-*`)
benchmarkRef@version · period · cohortRealised {n, selector snapshot} (SC-INT) · percentiles {p10..p90} · bestPractice {value, definition basis} · qualityNote (coverage, caveats; SC-SYN ⇒ SANDBOX badge) · sourceSnapshot (43 versions / citations) · computedAt. Immutable; refresh supersedes.

## 6. Controlled Vocabularies
Families (§3.1), source classes (§3.2), percentile ladder (fixed), best-practice bases {top_decile, standard_target, regulatory_target}, directions. Family additions: MINOR with owner assignment.

## 7. Applicability Rules
- **A1 — Compared-to-what is governed.** Published comparisons (75/77), screening decisions (60 DC-PLAN), and policy reporting (91) cite registered benchmarks; ad-hoc references in outputs are audit findings.
- **A2 — Cohort floors.** SC-INT cohorts: n≥10 (06 discipline), exclusion rules declared, selector snapshots retained; small-cohort requests return the nearest valid parent cohort, never a thin slice.
- **A3 — Normalise before comparing.** Subject values compare only after the definition's normalisation prerequisites (and the same formula, 57 A1); raw-vs-normalised mismatches are blocked at render time (75).
- **A4 — Source honesty.** Value sets carry provenance; SC-STD citations are year-pinned (standards move); SC-SYN context never appears in compliance, disclosure, or contractual comparisons (42 chain) and always carries the badge.
- **A5 — Federation, not duplication.** 06/12/51 rows are indexed passthroughs (single source, 59 A2 spirit); their values never fork here.
- **A6 — Best practice is defined, not vibes.** Every best-practice level states its basis (§3.3); shifting bases between periods is a versioned change.
- **A7 — Refresh discipline.** Stale value sets (past cadence) render with staleness badges; decisions consuming them note it (60 input quality).

## 8. Validation Rules
V1 metric refs resolve (57 preferred); direction declared; V2 SC-INT cohorts: floor + selector reproducibility from snapshots; V3 percentile monotonicity; best practice consistent with basis and direction; V4 normalisation prerequisites consistent with metric definition (57); V5 indexed rows: passthrough integrity vs 06/12/51 (drift detector); V6 SC-SYN badging mechanical; compliance-context exclusion enforced; V7 value sets immutable; supersession chains; V8 refresh cadence monitoring; staleness badges automatic; V9 source snapshots resolve (43 versions, citations well-formed); V10 cross-family metric collisions (same quantity, different definitions) rejected — one metric, one register entry.

## 9. Relationships to Other Catalogues
06/12/51 (federated systems of record) · 57 (metrics; threshold anchoring; comparison context for values) · 60 (screening/planning decisions consume references) · 75/77 (rendering with badges; percentile context) · 91/93 (policy targets, compliance comparisons — SC-REG rows) · 28 (carbon references; disclosure rules) · 55 (flexibility censuses feed BF-FLX) · 42/43 (synthetic context rules; source snapshots) · 89 (scenario baselines anchor to benchmark periods) · 08/01 (scope bounds).

## 10. Benchmarking Requirements
(Self) — register health: family coverage vs published-KPI census (every externally published KPI has a comparison reference or a documented absence), staleness rates, passthrough-drift incidents.

## 11. Dataset Requirements
SC-INT value sets cite source dataset versions (43); annual benchmark publications package as open datasets where cohort floors permit (43 A8).

## 12. Feature Requirements
Percentile-position features (41): subject's cohort percentile per metric — registered, decision-consumable (60 screening).

## 13. Model Requirements
BF-MDL passthrough from 51; benchmark-position features feed SER-12 ranking models (retrofit screening uses BF-BLD positions).

## 14. KPI Requirements
57 threshold bands may anchor to value-set percentiles (e.g. amber = below cohort p50) — anchoring declared in the KPI definition; re-anchoring on refresh is governed (57 §20).

## 15. Response Derivative Requirements
BF-FLX references contextualise RD-layer outputs: deliverable-kW distributions by archetype (55 §10 censuses), delivery-success distributions (MET-015 cohorts via 12) — "is this building's flexibility normal for its class?" gets a governed answer.

## 16. Decision Requirements
60 screening decisions (DC-PLAN: retrofit ranking, prequal reviews, campaign targeting 56 A6) consume percentile positions with staleness awareness (A7); references inform, thresholds and constraints still govern (59).

## 17. Ontology Mapping
Benchmark ↔ EIG `eig:Benchmark` with QUDT metric typing; value sets ↔ statistical distributions (percentile individuals); sources via PROV/citation; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `BenchmarkDefinition`, `BenchmarkValueSet`; edges `MEASURES`→57, `SCOPED_TO`→01/08, `SOURCED_FROM` {class}, `INDEXES`→06/12/51, `ANCHORS`→57 thresholds, `POSITIONS`→subjects {percentile}. Cross-domain comparison dashboards are KG views.

## 19. Digital Twin Mapping
Twin subject views render benchmark positions alongside live KPIs (32); portfolio twins show cohort distributions with the subject highlighted.

## 20. Governance
Register owner: Data Management Lead; family owners maintain definitions; SC-EXT source admissions reviewed (provenance + licence); annual register review aligns with 06/12/51 cycles; SC-REG rows track 92 instrument changes.

## 21. Versioning
Definitions semver; value sets immutable per period. Catalogue 1.0.0.

## 22. Example Records

### EIG-BM-FLX-DELIV-RES — residential delivery success reference (BF-FLX)
- metric EIG-KPI-FLEX-DELIV (57); scope: residential flex-enrolled, IE; cohort SC-INT: enrolled dwellings n≥10 per archetype cell, exclusions {commissioning month}
- value set 2026-Q1: n=214; percentiles {p10 0.86, p25 0.91, p50 0.95, p75 0.97, p90 0.99}; best practice 0.99 (top_decile); refresh P3M
- consumers: prequal reviews (22 via 60), 75 flex dashboards (EIG-AST-004211 renders at p62)

### EIG-BM-EUI-OFF-IE — office EUI reference (BF-BLD, indexed from 06)
- indexedFrom 06 cohort register (A–G band machinery passthrough); normalisation prereq: climate (09 HDD) + area; SC-INT + SC-NAT context (SEAI statistics overlay, provenance-classed separately)

### EIG-BM-MTTR-SEV2 — operational MTTR reference (BF-OPS)
- metric: MTTR SEV-2 (63 §10); scope: platform ops; cohort SC-INT rolling-year; best practice standard_target 4 h (declared basis); anchors 57 KF-PLT amber band

## 23. Completion Criteria
☑ All eight spec fields (ID, family, metric, scope, cohort, source, percentiles, best practice) ☑ Cross-domain family register federating 06/12/51 without duplication ☑ Source classes with synthetic/compliance exclusions ☑ Cohort floors + normalise-before-compare ☑ Defined best-practice bases + staleness discipline ☑ Examples spanning flexibility, building, and operational domains.
