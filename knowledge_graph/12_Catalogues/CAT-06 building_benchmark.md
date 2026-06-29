---
type: catalogue
ontology: EIG-MLM
catalogue_no: 6
catalogue_id: EIG-CAT-006
entity: building_benchmark
band: physical
status: active
tags: [eig-mlm, catalogue, band:physical, entity:building_benchmark]
aliases: ["EIG-CAT-006", "Catalogue 06", "building_benchmark"]
---

# CAT-06 — Building Benchmark

> **EIG-CAT-006** · band: **Physical & Spatial Stack** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Building Benchmark Catalogue

## 1. Definition

The Building Benchmark Catalogue defines all building benchmark structures used by the EIG MLM framework: external scheme mappings (CIBSE TM46, EPC, SEAI BER, NABERS, DEC) and EIG internal benchmark cohorts. It specifies how a building's performance is positioned against peers, which metrics are benchmarked, how cohorts are constructed, and how percentile statistics are computed, published and versioned.

## 2. Scope

Applies to: peer-group benchmarking, regulatory rating cross-walks, portfolio league tables, anomaly screening (cohort outliers), target setting (percentile-based), retrofit prioritisation, public-sector reporting (SEAI M&R), synthetic data validation (generated buildings must land plausibly within cohorts).

Out of scope: asset-level benchmarks (12), model benchmarks (51), generic cross-domain benchmarks (65 — this catalogue feeds it).

## 3. Classification Structure

```text
Benchmark Family:  external_scheme | internal_cohort
External Schemes:  TM46 | EPC | BER | NABERS | DEC | EnPC_custom
Benchmark Object:  scheme_mapping (EIG-BBM-MAP) | cohort (EIG-BBM-COH)
                   | metric (EIG-BBM-MET) | statistic release (EIG-BBM-REL)
```

## 4. Hierarchy

```text
Scheme / Cohort definition
 └─ Benchmarked Metric (what is measured)
     └─ Statistic Release (versioned percentile tables, dated)
         └─ Building Position (computed, per building per release)
```

## 5. Field Groups & Fields

### 5.1 Scheme Mapping Fields

| # | Field | Type | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|
| 1 | mappingID | string | `EIG-BBM-MAP-[0-9]{5}` | Y | ID |
| 2 | scheme | enum | TM46, EPC, BER, NABERS, DEC, EnPC_custom | Y | External scheme |
| 3 | schemeVersion | string | e.g. TM46:2008, DEAP 4.2 | Y | Pinned version |
| 4 | schemeCategoryCode | string | scheme-native code (e.g. TM46 cat 16) | Y | Category |
| 5 | classificationIDs | array[string] | EIG-BC nodes | Y | Mapped EIG classifications |
| 6 | mappingConfidence | enum | exact, close, approximate | Y | Cross-walk quality |
| 7 | typicalBenchmarkElectrical | decimal kWh/m²/yr | ≥0 | Cond | Scheme typical (TM46/DEC) |
| 8 | typicalBenchmarkFossilThermal | decimal kWh/m²/yr | ≥0 | Cond | Scheme typical |
| 9 | adjustmentRules | array[enum] | weather_degree_day, occupancy_hours, special_energy_use, area_basis | N | Permitted normalisations per scheme |
| 10 | lifecycle block | — | standard | Y | — |

### 5.2 Cohort Fields (internal)

| # | Field | Type | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|
| 1 | cohortID | string | `EIG-BBM-COH-[0-9]{5}` | Y | ID |
| 2 | cohortName | string | ≤120 chars | Y | Name |
| 3 | classificationID | string | EIG-BC Level-3 node | Y | Primary dimension |
| 4 | dimensionFilters | array[object] | {dimension: enum(floor_area_band, occupancy_class, climate_zone, era_band, fuel_mix, sector_obligation), values[]} | Y | Cohort definition |
| 5 | floorAreaBands | array[string] | e.g. <500, 500–2000, 2000–10000, >10000 m² | Cond | If banded |
| 6 | minimumCohortSize | integer | ≥10 (default) | Y | Publication threshold |
| 7 | membershipRule | enum | static_assigned, dynamic_query | Y | How members resolve |
| 8 | memberBuildingIDs | array[string] | EIG-BLD IDs | Cond | If static |
| 9 | refreshFrequency | enum | monthly, quarterly, annual | Y | Recompute cadence |
| 10 | exclusionRules | array[string] | machine-evaluable predicates (e.g. data_coverage<0.9) | Y | Quality gates |

### 5.3 Benchmarked Metric Fields

| # | Field | Type | Unit | Allowed Values | Req | Description |
|---|---|---|---|---|---|---|
| 1 | metricID | string | — | `EIG-BBM-MET-[0-9]{4}` | Y | ID |
| 2 | metricName | enum | — | EUI_total, EUI_electrical, EUI_thermal, carbon_intensity, cost_intensity, peak_demand_density, load_factor, baseload_ratio, out_of_hours_ratio, self_consumption_pct, comfort_compliance_pct, kwh_per_person, water_intensity | Y | Closed metric list |
| 3 | unit | string | per metric | kWh/m²/yr, kgCO₂e/m²/yr, €/m²/yr, W/m², %, — | Y | Unit |
| 4 | normalisationBasis | enum | — | treated_area, gross_area, nla, per_person, per_occupied_hour | Y | Denominator |
| 5 | weatherNormalised | boolean | — | true/false | Y | Degree-day corrected flag |
| 6 | weatherNormalisationMethod | enum | — | none, HDD_15.5, HDD_18_CDD_18, regression_based | Cond | If normalised |
| 7 | sourceKPIID | string | — | EIG-KPI ID | Y | Computation source (57) |
| 8 | directionPreferred | enum | — | lower_better, higher_better | Y | Interpretation |

### 5.4 Statistic Release Fields

| # | Field | Type | Allowed Values | Req | Description |
|---|---|---|---|---|---|
| 1 | releaseID | string | `EIG-BBM-REL-[0-9]{6}` | Y | ID |
| 2 | cohortID / mappingID | string | one of | Y | Subject |
| 3 | metricID | string | valid metric | Y | Metric |
| 4 | periodStart / periodEnd | date | ISO 8601 | Y | Data period |
| 5 | sampleSize | integer | ≥ minimumCohortSize | Y | n |
| 6 | percentiles | object | {p5,p10,p25,p50,p75,p90,p95: decimal} | Y | Distribution |
| 7 | mean / stdDev | decimal | per unit | Y | Moments |
| 8 | bestPracticeThreshold | decimal | typically p10/p25 per direction | Y | "Good" line |
| 9 | typicalThreshold | decimal | p50 | Y | "Typical" line |
| 10 | methodologyNote | string | ≤500 chars | Y | Computation notes |
| 11 | releaseDate / supersedesReleaseID | date/string | — | Y/N | Versioning |

### 5.5 Building Position Fields (computed)

| # | Field | Type | Allowed Values | Req | Description |
|---|---|---|---|---|---|
| 1 | buildingID / releaseID / metric value / percentileRank (0–100) / ratingBand (A–G internal) / dataCoverage (0–1) / outlierFlag (boolean) / computedDate | — | — | Y | Position record per building per release |

## 6. Controlled Vocabularies

Metric list (§5.3.2) is closed; schemes, dimensions, normalisation bases, adjustment rules as enumerated. Internal rating bands A–G map to percentile windows: A≤p10, B≤p25, C≤p40, D≤p55, E≤p70, F≤p85, G>p85 (direction-aware). Governed via (82).

## 7. Applicability Rules

- TM46/DEC mappings: non-domestic only; BER: residential and residential components of mixed-use.
- weatherNormalised mandatory for EUI_thermal and EUI_total cross-year comparisons.
- special_energy_use adjustment (TM46 separables, e.g. server rooms) requires sub-metering evidence (02 §5.5.20 ≥ distribution_board).
- Buildings failing exclusionRules are positioned but flagged excluded_from_statistics (they see the benchmark; they don't move it).
- Synthetic buildings never enter statistic releases (provenance gate via 42/43).

## 8. Validation Rules

- Percentiles monotone: p5≤p10≤…≤p95; mean within [p5,p95].
- sampleSize ≥ minimumCohortSize; releases below threshold are blocked, not published with caveats.
- A building belongs to exactly one cohort per (classification, metric) at a time; dynamic membership re-evaluated each refresh with membership-change audit.
- mappingConfidence=approximate prohibits regulatory use (reporting flag).
- dataCoverage ≥0.9 for inclusion in statistics (default; per-cohort override allowed downward never below 0.75).
- Direction-aware band assignment verified (lower_better vs higher_better).

## 9. Relationships to Other Catalogues

Upstream: Building Classification (01), Building (02), KPI (57 — metric computation), Weather/Climate (09 — degree days), Data Quality (40 — coverage), Geographic (08 — climate zone dimension). Downstream: Benchmark (65 — federation), Dashboard (75), Report (77), Decision Intelligence (60 — outlier-triggered investigation), Scenario (89 — target setting), Synthetic Data (42 — plausibility envelopes).

## 10. Benchmarking Requirements

This catalogue *is* the benchmarking specification for buildings. Mandatory cohort coverage: every active Level-3 classification with ≥10 operational buildings must have ≥1 cohort with EUI_total, carbon_intensity and peak_demand_density releases at least annually.

## 11. Dataset Requirements

Inputs: verified annual/interval consumption (38), treated areas (02), degree days per station (09), occupancy hours (03). Releases themselves are registered datasets (43) with full lineage to member buildings and KPI versions.

## 12. Feature Requirements

Features (41): percentileRank per metric, band, distance-to-best-practice, cohort z-score, rank trajectory (Δpercentile/yr). Used by anomaly models and retrofit prioritisation.

## 13. Model Requirements

Models consuming benchmarks: anomaly detection (cohort-relative), target-setting optimisation, retrofit prioritisation ranking, synthetic data validators (generated cohort distribution vs real release KS-test). Models must cite releaseID — never "latest" implicitly.

## 14. KPI Requirements

Every benchmarked metric maps 1:1 to a KPI (57) via sourceKPIID; KPI formula/version changes invalidate open releases for recomputation. Benchmark-derived KPIs: percentile rank, band, peers-beaten %.

## 15. Response Derivative Requirements

Benchmarks contextualise RD value: flexibility RDs are prioritised for buildings in poor percentile positions (high headroom). RD catalogue (52) may reference cohort position as context but RD estimation itself is building-specific.

## 16. Decision Requirements

Decision triggers (60): outlierFlag → investigation workflow (64); band ≤ F → retrofit option generation (05/60); percentile regression > threshold quarter-on-quarter → alert (76). Targets set as "reach p25 of cohort by date" are machine-resolvable via releases.

## 17. Ontology Mapping

Classes: BenchmarkScheme, SchemeMapping, BenchmarkCohort, BenchmarkedMetric, StatisticRelease, BuildingPosition.
Object properties: mapsClassification, hasMetric, hasRelease, positions (Building), supersedes, computedFromKPI, normalisedBy.
Datatype properties: scheme, schemeVersion, cohortID, minimumCohortSize, percentile values, bestPracticeThreshold, percentileRank, ratingBand, dataCoverage, releaseDate.
External alignment: TM46 category scheme, EPBD EPC scales, SEAI BER scale as SKOS concept schemes (via 81/83).

## 18. Knowledge Graph Mapping

Nodes: Scheme, Cohort, Metric, Release, Building, ClassificationNode.
Relationships: MAPS_TO {confidence}, MEMBER_OF {from, to}, BENCHMARKED_BY, HAS_RELEASE {period}, POSITIONED {rank, band, coverage}, SUPERSEDED_BY, COMPUTED_FROM {kpiVersion}.

## 19. Digital Twin Mapping

Building twins carry current position records per metric as twin state; portfolio/district twins (87/88) aggregate cohort statistics; what-if twins recompute hypothetical position under scenario consumption.

## 20. Governance

Roles: Catalogue Owner, Benchmark Owner (statistics sign-off), Building Domain Expert, Regulatory Compliance Owner (scheme mappings), Data Quality Steward (coverage gates), Version Approver. External scheme version changes trigger mapping review within one quarter.

## 21. Versioning

Scheme mappings pinned to scheme versions; releases immutable once published (supersede, never edit); cohort definition changes are MINOR (membership) or MAJOR (dimension change — breaks comparability; old releases tagged non-comparable).

## 22. Example Records

```text
CohortID: EIG-BBM-COH-00012 | Name: Open-Plan Offices 2k–10k m², Climate Zone IE-S
Classification: EIG-BC-SUB-2011 | Dimensions: {floor_area_band: 2000–10000, climate_zone: IE-S, occupancy_class: OCL-03}
Membership: dynamic_query | Min size: 10 | Refresh: quarterly
Exclusions: [data_coverage<0.9, lifecycleStage≠operational]
```

```text
ReleaseID: EIG-BBM-REL-002341 | Cohort: EIG-BBM-COH-00012 | Metric: EUI_total (kWh/m²/yr, treated_area, weather HDD_15.5)
Period: 2025-01-01…2025-12-31 | n=34
Percentiles: p5 84, p10 92, p25 108, p50 139, p75 171, p90 204, p95 226 | mean 143, σ 41
Best practice: 108 (p25) | Typical: 139 | Released: 2026-02-15
```

```text
Position: EIG-BLD-000142 @ REL-002341 → value 142 kWh/m²/yr, percentile 53, band D,
coverage 0.97, outlier: false, computed 2026-02-16
```

## 23. Completion Criteria

Complete when: all five external schemes mapped for every applicable classification; internal cohort definitions exist per §10 coverage rule; the metric list is closed and KPI-linked; release/position schemas validated; direction-aware banding implemented; relationships/ontology/KG/twin/governance/versioning populated; ≥3 examples covering cohort, release and position objects.
