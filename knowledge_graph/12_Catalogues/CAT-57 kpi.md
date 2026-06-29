---
type: catalogue
ontology: EIG-MLM
catalogue_no: 57
catalogue_id: EIG-CAT-057
entity: kpi
band: decision
status: active
tags: [eig-mlm, catalogue, band:decision, entity:kpi]
aliases: ["EIG-CAT-057", "Catalogue 57", "kpi"]
---

# CAT-57 — Kpi

> **EIG-CAT-057** · band: **KPIs, Objectives & Decision Intelligence** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-01 building_classification]] · [[CAT-06 building_benchmark]] · [[CAT-12 asset_benchmark]] · [[CAT-38 data]] · [[CAT-40 data_quality]] · [[CAT-41 feature]] · [[CAT-43 dataset]] · [[CAT-51 model_benchmark]] · [[CAT-52 response_derivative]] · [[CAT-65 benchmark]]
**Used by (downstream):** [[CAT-58 objective]] · [[CAT-60 decision_intelligence]] · [[CAT-67 reward]] · [[CAT-75 dashboard]] · [[CAT-77 report]] · [[CAT-91 policy]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-057`
**Entity prefix:** `EIG-KPI-` (definitions), `EIG-KPI-V-` (computed values)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (portfolio) / domain owners per family
**Depends on (upstream):** 01 building_classification, 06/12/51/65 benchmarks, 38 data, 40 data_quality, 41 feature, 43 dataset, 52 response_derivative
**Used by (downstream):** 58 objective, 60 decision_intelligence, 67 reward, 75 dashboard, 77 report, 91 policy

---

## 1. Definition

The **KPI Catalogue** is the single register of Key Performance Indicators: every published, decision-feeding, or reward-feeding performance number in the framework. A KPI definition (`EIG-KPI-*`) fixes: name, machine-evaluable formula, units, thresholds, computation frequency, source data, linked models, and linked decisions. KPI values (`EIG-KPI-V-*`) are computed instances with full provenance. One name, one formula, everywhere (A1): the same KPI rendered on a dashboard (75), cited in a report (77), thresholded in a decision (60), or weighted in a reward (67) computes identically.

## 2. Scope
**In scope:** KPI families across energy, comfort, flexibility, carbon, economic, data/model/platform operations; formula register; threshold semantics (target/alert bands, per-classification overrides 01 §14); normalisation rules; quality caveat codes; aggregation rules (subject → portfolio). **Out of scope:** benchmark cohorts (06/12/65 — KPIs are compared *against* them), reward shaping (67 — consumes KPI values), dashboard rendering (75).

## 3. Classification Structure

### 3.1 KPI families (`family`)

| Code | Family | Canonical members (register) |
|---|---|---|
| KF-EN | Energy | **EIG-KPI-EUI** (kWh/m²/yr), kWh/person, out-of-hours ratio, baseload ratio, HLC-normalised demand |
| KF-CMF | Comfort | **EIG-KPI-COMFORT** (% occupied hours in band, 04), overheating hours, CO₂ adequacy |
| KF-FLX | Flexibility | **EIG-KPI-FLEX-DELIV** (≡ MET-015 flex_delivery_success, 12), activated kWh, RD coverage %, override rate |
| KF-CO2 | Carbon | tCO₂e (28 factors), CI-weighted consumption, **EIG-KPI-PEAK** (peak demand kW, network value) |
| KF-EC | Economic | €/kWh effective, flex revenue, cost avoidance (single pricing truth, 21 V11) |
| KF-DAT | Data ops | fitness pass rates (40), stream availability (37), metadata coverage (39) |
| KF-MDL | Model ops | skill vs baseline (51), validation freshness (47), drift incidents (41 V9) |
| KF-PLT | Platform/edge ops | service availability (31), edge degraded-mode hours (35), dispatch latency (34) |

### 3.2 Threshold semantics
Per definition: `target`, `alertBands` {green/amber/red boundaries, direction}, optional per-classification overrides keyed to 01 nodes (01 §14). Breach handling routes to 63 events / 76 alerts.

### 3.3 Quality caveat codes
QC-OK · QC-DEGRADED (inputs below reporting fitness, 40 §14) · QC-PARTIAL (coverage gap stated) · QC-SANDBOX (synthetic/sandbox badge, 42 §14). Values always carry a code; red-flag rendering rules in 75.

## 4. Hierarchy
```
Family (KF-*) → KPI definition (EIG-KPI-*) → Value (EIG-KPI-V-*: subject × period × provenance)
```

## 5. Field Groups & Fields

### 5.1 KPI definition (`EIG-KPI-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | kpiID | string | — | `EIG-KPI-[A-Z0-9-]{2,20}` | M | ID |
| 2 | name / family | string/enum | — | ≤120 / §3.1 | M | Identity |
| 3 | formula | string | — | machine-evaluable over registered variables (38) / features (41) / settlement quantities (27) | M | The single truth (A1) |
| 4 | unit | string | UCUM | dimensional check V2 | M | Output unit |
| 5 | subjectGrain | enum | — | building/zone/asset/portfolio/network/org/platform | M | Computed per |
| 6 | normalisation | json | — | denominator refs (area 02, occupancy 03, HDD 09) + method | C | Comparability |
| 7 | frequency | duration | — | ISO 8601 (PT30M…P1Y) | M | Computation cadence |
| 8 | sourceData | array<ref> | — | →38 entities / 27 / 53 (fitness floor per 40 §14) | M | Inputs |
| 9 | thresholds | json | — | §3.2 incl. classification overrides | M | Bands |
| 10 | linkedModels | array<ref> | — | →44 (KPIs the model is judged by / feeds) | M | Spec field |
| 11 | linkedDecisions | array<ref> | — | →60 decision IDs thresholding this KPI | M | Spec field |
| 12 | benchmarkBinding | ref | — | →06/12/65 cohort | C | Comparison context |
| 13 | owner / version / status | — | — | standard lifecycle | M | Governance |

### 5.2 Value (`EIG-KPI-V-*`)
kpiRef@version · subjectRef · period · value(+CI where applicable) · qualityCaveat (§3.3) · sourceSnapshot (dataset/entity versions, 43 §14) · computedAt · environment. Immutable; recomputation supersedes.

## 6. Controlled Vocabularies
Families (§3.1), caveat codes (§3.3), subject grains, threshold band directions. KPI naming: registered, unique; aliases via 81 semantic mapping only.

## 7. Applicability Rules
- **A1 — Single formula.** Any surface showing a registered KPI computes via this register; parallel formulas are audit findings.
- **A2 — Fitness floor.** Values from inputs below reporting fitness carry QC-DEGRADED; settlement-derived KPIs (FLEX-DELIV) require settlement fitness (40 §14).
- **A3 — Sandbox badging.** Synthetic/sandbox-sourced values always QC-SANDBOX; excluded from organisational reporting (42 §14).
- **A4 — Normalised where compared.** Cross-subject comparison/publication uses declared normalisation + benchmark cohort (n≥10, 06).
- **A5 — Threshold actions are registered.** Breaches emit 63 events; automated responses route through 60 (no KPI-direct actuation).
- **A6 — Reward linkage discipline.** KPIs feeding 67 rewards must be manipulation-reviewed (Goodhart check recorded): can the agent improve the number without improving the reality? Mitigations declared.

## 8. Validation Rules
V1 ID/name uniqueness; V2 unit dimensional consistency with formula; V3 formula references resolve (38/41/27); V4 thresholds well-ordered per direction; V5 values recomputable from sourceSnapshot; V6 caveat assignment mechanical from input fitness; V7 classification overrides reference valid 01 nodes; V8 linkedDecisions/linkedModels bidirectionally consistent with 60/44 registers; V9 values immutable, supersession chains.

## 9. Relationships to Other Catalogues
38/41/27 (inputs) · 40 (fitness/caveats) · 06/12/65 (benchmark context) · 44/47/51 (model-ops KPIs) · 52/53 (flex KPIs: FLEX-DELIV verifies RD promises) · 58 (objectives target KPIs) · 60 (decision thresholds) · 67 (reward sources, Goodhart-reviewed) · 75/77 (rendering with caveats) · 91/93 (policy/compliance reporting).

## 10. Benchmarking Requirements
Every published comparative KPI binds a cohort (06/12/65); percentile context rendered alongside values.

## 11. Dataset Requirements
Values cite source snapshots (dataset/entity versions); annual KPI publications package as 43 datasets for reproducibility.

## 12. Feature Requirements
KPI formulas reuse registered features (41) where the same quantity exists — dashboard/model identity guaranteed (41 §14).

## 13. Model Requirements
Model-health KPIs (KF-MDL) compute from 47/51/41 registers; models judged only by registered KPIs (44 §14).

## 14. KPI Requirements
(Self) — register completeness: no unregistered numbers on decision paths, rewards, or external publications.

## 15. Response Derivative Requirements
EIG-KPI-FLEX-DELIV is the operational truth-test of RDs (52 §14): delivered vs promised per event series, settlement-grade inputs; RD confidence and delivery KPI rendered together on flex dashboards.

## 16. Decision Requirements
60 consumes thresholds + current values with caveat awareness: QC-DEGRADED values cannot trigger automated actions (advisory only).

## 17. Ontology Mapping
KPI ↔ SEAS/SAREF evaluation concepts + QUDT quantities; values ↔ observations with PROV; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `KPIDefinition`, `KPIValue`; edges `OF_SUBJECT`, `COMPUTED_FROM`→sources, `THRESHOLDED_BY`→60, `REWARDS`→67, `BENCHMARKED_AGAINST`→cohorts.

## 19. Digital Twin Mapping
Twins surface live KPI panels per subject (32); templates (33) declare required KPI sets per fidelity class (resolves 33 forward ref to EIG-KPI-{EUI,COMFORT,FLEX-DELIV,PEAK}).

## 20. Governance
Family owners; formula changes are MAJOR (recompute + consumer notice); Goodhart reviews for reward-linked KPIs signed by PI.

## 21. Versioning
Definitions semver; values immutable. Catalogue 1.0.0.

## 22. Example Records

### EIG-KPI-EUI — Energy Use Intensity
- formula: Σ delivered energy (38 carriers via 19 conversion) / floor area (02) per year, climate-normalised variant via HDD (09); unit kWh/(m²·a); grain building; frequency P1M rolling-annual
- thresholds per 01 node overrides (e.g. open-plan office green <120); benchmark 06 cohorts; consumers: 75 panels, 60 retrofit screening, 33 templates

### EIG-KPI-FLEX-DELIV (≡ MET-015)
- formula: Σ delivered / Σ contracted over settled events (27 four-quantity model), settlement fitness required; thresholds: green ≥95% (22 OB-FULL convention); linked decisions: prequal review (22), dispatch strategy moderation (60); the RD truth-test (§15)

### EIG-KPI-PEAK
- formula: max PT30M demand per period (TS-AVG→kW per 38 semantics); grain building/portfolio/network node; consumers: network value KPIs (13), 60 peak-management decisions, EIG-KPI-V values badge QC codes

## 23. Completion Criteria
☑ Family register with canonical members resolving 33/12 forward refs ☑ Single-formula rule ☑ Fitness caveats + sandbox badging ☑ Threshold/override semantics ☑ Goodhart review for reward links ☑ Provenance-complete values.
