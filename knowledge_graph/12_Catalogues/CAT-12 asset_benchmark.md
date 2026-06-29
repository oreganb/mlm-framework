---
type: catalogue
ontology: EIG-MLM
catalogue_no: 12
catalogue_id: EIG-CAT-012
entity: asset_benchmark
band: physical
status: active
tags: [eig-mlm, catalogue, band:physical, entity:asset_benchmark]
aliases: ["EIG-CAT-012", "Catalogue 12", "asset_benchmark"]
---

# CAT-12 — Asset Benchmark

> **EIG-CAT-012** · band: **Physical & Spatial Stack** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Asset Benchmark Catalogue

Catalogue ID prefix: **EIG-ABM** · Catalogue number: 12 · Version 1.0 · Status: Active

---

## 1. Definition

The Asset Benchmark Catalogue defines technology-specific performance benchmarking for assets: efficiency benchmarks, reliability benchmarks and lifecycle benchmarks. It specifies, per asset subtype, which metrics are benchmarked, how cohorts are formed, how statistics are released, and how individual assets receive positions. It mirrors the Building Benchmark Catalogue (06) pattern at asset granularity.

## 2. Scope

All asset subtypes in EIG-AC carrying a benchmarkSchemeRef. Benchmarking is strictly technology-specific: cross-subtype comparison is prohibited except via vector-normalised lifecycle metrics explicitly marked cross_comparable.

## 3. Classification Structure

Three benchmark families:

| Family | Code | Definition |
|---|---|---|
| Efficiency | ABM-EFF | Energy conversion/usage performance vs input |
| Reliability | ABM-REL | Availability, fault behaviour, dependability |
| Lifecycle | ABM-LCY | Degradation, lifetime, whole-life cost & carbon |

Entity types: **Scheme** (EIG-ABM-SCH-*, per technology group) → **Metric** (EIG-ABM-MET-*, closed register) → **Cohort** (EIG-ABM-COH-*) → **StatisticRelease** (immutable) → **AssetPosition**.

## 4. Hierarchy

```
Scheme (per technology, e.g. EIG-ABM-SCH-HP)
 └── Metrics (subset of closed register, with subtype-specific definitions)
      └── Cohorts (subtype × capacityBand × climateZone × [vintageBand])
           └── StatisticReleases (immutable percentile sets per period)
                └── AssetPositions (asset × metric × release → percentile, band)
```

## 5. Field Groups & Fields

### 5.1 Scheme

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | schemeID | string | — | EIG-ABM-SCH-XXX | M | Scheme ID |
| 2 | schemeName | string | — | — | M | e.g. Heat Pump Benchmark Scheme |
| 3 | appliesToSubtypes | array | — | EIG-AC subtype IDs | M | Coverage |
| 4 | metricIDs | array | — | EIG-ABM-MET-* | M | Metrics in scheme (≥1 per family) |
| 5 | cohortKeys | array | — | subset of {subtype, capacityBand, climateZone, vintageBand, usagePatternClass} | M | Stratification keys |
| 6 | minCohortN | integer | — | ≥ 10 | M | Disclosure threshold (default 10) |
| 7 | releaseCadence | enum | — | monthly, quarterly, annual | M | Release rhythm |
| 8 | normalisationRules | array | — | rule refs (weather, occupancy, load) | M | Pre-comparison corrections |
| 9 | status/version/owner/dates | — | — | audit block | M | Audit |

### 5.2 Metric (closed register; representative entries)

| metricID | Name | Family | Unit | Direction | Subtype scope | Definition basis |
|---|---|---|---|---|---|---|
| EIG-ABM-MET-001 | spf_measured | EFF | — | higher | heat pumps | Seasonal performance factor, SEPEMO H4 boundary, metered |
| EIG-ABM-MET-002 | eta_seasonal | EFF | % | higher | boilers | Seasonal efficiency, metered fuel in / heat out |
| EIG-ABM-MET-003 | specific_yield | EFF | kWh/kWp·yr | higher | solar_pv | Annual AC yield ÷ DC nameplate |
| EIG-ABM-MET-004 | performance_ratio | EFF | % | higher | solar_pv | Yield ÷ irradiance-theoretical |
| EIG-ABM-MET-005 | round_trip_eff | EFF | % | higher | batteries | AC-AC round trip |
| EIG-ABM-MET-006 | sfp | EFF | W/(l/s) | lower | fans/AHU | Specific fan power |
| EIG-ABM-MET-007 | pump_eei | EFF | — | lower | circulators | Energy efficiency index |
| EIG-ABM-MET-008 | lighting_lpd_achieved | EFF | W/m² | lower | lighting | Achieved load density at compliance lux |
| EIG-ABM-MET-009 | charger_efficiency | EFF | % | higher | ev_charger | AC-DC conversion |
| EIG-ABM-MET-010 | availability_pct | REL | % | higher | all telemetered | Uptime ÷ scheduled time |
| EIG-ABM-MET-011 | mtbf_observed | REL | h | higher | all telemetered | Mean time between faults |
| EIG-ABM-MET-012 | mttr_observed | REL | h | lower | all serviced | Mean time to repair |
| EIG-ABM-MET-013 | fault_rate | REL | faults/yr | lower | all | Fault events per operating year |
| EIG-ABM-MET-014 | derating_events | REL | events/yr | lower | HP/chiller/battery | Capacity-limited operation events |
| EIG-ABM-MET-015 | flex_delivery_success | REL | % | higher | FN-FLEX assets | Delivered ÷ committed response events |
| EIG-ABM-MET-016 | capacity_fade_rate | LCY | %/yr | lower | batteries | Usable capacity decline |
| EIG-ABM-MET-017 | efficiency_degradation | LCY | %/yr | lower | HP/PV/boiler | Efficiency decline rate |
| EIG-ABM-MET-018 | cycles_consumed_rate | LCY | %/yr | lower | cycle-limited | cyclesToDate ÷ designCycles per year |
| EIG-ABM-MET-019 | lifetime_achieved_ratio | LCY | — | higher | decommissioned | Actual ÷ design lifetime |
| EIG-ABM-MET-020 | lcoe_asset | LCY | EUR/kWh | lower | generation/storage | Levelised cost (cross_comparable=true) |
| EIG-ABM-MET-021 | lifecycle_carbon_intensity | LCY | kgCO2e/kWh | lower | generation/storage | Embodied+operational ÷ lifetime output (cross_comparable=true) |
| EIG-ABM-MET-022 | maintenance_cost_intensity | LCY | EUR/kW·yr | lower | all serviced | Opex per rated kW |

Metric record fields: metricID, name, family, unit, direction, definitionFormulaRef, dataRequirements (fields from EIG-AST §5 + telemetry vars), minObservationPeriodDays, normalisationApplied (hdd/cdd/irradiance/load/none), crossComparable (bool, default false), validRange, qcRules.

### 5.3 Cohort

cohortID (EIG-ABM-COH-NNNNN), schemeID, metricID, keyValues {subtype, capacityBand, climateZone(EIG-GEO-CZ), vintageBand(era of install: pre2010, 2010_2015, 2016_2020, 2021_plus), usagePatternClass?}, memberCount, memberSelectionRules (tier ≥ T2, meterCoverage=dedicated, observation ≥ minObservationPeriodDays, status=operational), exclusions log.

Capacity bands per technology (closed): HP {≤6, 6–12, 12–20, 20–50, >50 kW_th}; PV {≤4, 4–10, 10–50, 50–250, >250 kWp}; Battery {≤5, 5–15, 15–50, >50 kWh}; boilers {≤20, 20–70, 70–300, >300 kW}.

### 5.4 StatisticRelease (immutable)

releaseID, cohortID, periodStart/End, n, statistics {min, p5, p10, p25, p50, p75, p90, p95, max, mean, std}, weatherVintageRef (EIG-WX), methodVersion, publishedDate, supersedes(null — releases never superseded, only succeeded). Releases with n < minCohortN are computed but embargoed (visible to governance only).

### 5.5 AssetPosition

positionID, assetID, releaseID, metricValueRaw, metricValueNormalised, percentile (0–100), band (A–G mapped: A ≥ p90 … G < p10 for higher-is-better; inverted for lower), confidenceFlags (observation length, gap-fill %, attribution quality), computedDate.

## 6. Controlled Vocabularies

Families (3, closed). Metric register (§5.2, closed; additions minor). Capacity bands (closed per technology). Vintage bands (closed, aligned to building era-band style). Band letters A–G with fixed percentile windows: A [90,100], B [75,90), C [60,75), D [40,60), E [25,40), F [10,25), G [0,10) (direction-adjusted).

## 7. Applicability Rules

1. Only subtype-homogeneous cohorts; no category-level cohorts.
2. Cohort membership requires: dataRichnessTier ≥ T2, meterCoverage = dedicated, observation period ≥ metric minimum, operational status throughout period.
3. Cross-comparable metrics (020, 021) may form vector-level cohorts (e.g. all electricity-producing assets) — the only sanctioned cross-subtype comparison.
4. Weather-sensitive metrics normalised per scheme rules before pooling across climate zones; un-normalised cohorts must be single-zone.
5. flex_delivery_success applies only to assets with ≥ 5 committed events in period.

## 8. Validation Rules

| # | Rule |
|---|---|
| V1 | scheme.appliesToSubtypes ⊆ active EIG-AC leaves; every listed subtype's benchmarkSchemeRef points back (bidirectional integrity) |
| V2 | Scheme has ≥ 1 metric per family (EFF, REL, LCY) |
| V3 | Cohort keyValues complete per scheme cohortKeys; members satisfy selection rules (audited sample 5%) |
| V4 | Release statistics monotonic (p5 ≤ p10 ≤ … ≤ p95); n recorded; embargo applied when n < minCohortN |
| V5 | Position percentile consistent with release statistics (recompute check) |
| V6 | metricValueRaw within metric validRange; normalised value carries normalisation lineage |
| V7 | Releases immutable: any correction = new release + erratum record |
| V8 | Asset appears in ≤ 1 cohort per (metric, period) |

## 9. Relationships to Other Catalogues

| Direction | Catalogue | Relationship |
|---|---|---|
| ▶ depends on | asset_classification (10) | subtype scoping, scheme back-refs |
| ▶ depends on | asset (11) | instance data + telemetry attribution |
| ▶ depends on | geographic (08) / weather_climate (09) | climate cohort keys + normalisation |
| ▶ depends on | data_quality (40) | member eligibility via DQ score |
| ◀ consumed by | kpi (57) | benchmark-relative KPIs (percentile targets) |
| ◀ consumed by | decision_intelligence (60) | underperformance triggers (maintenance, replacement actions) |
| ◀ consumed by | model (44) | benchmark features as model inputs; anomaly priors |
| ◀ consumed by | report (77), dashboard (75) | position rendering |
| peer | building_benchmark (06) | shared release/position pattern; building-level roll-ups may cite asset positions |
| peer | model_benchmark (51) | same statistical machinery applied to models |

## 10. Benchmarking Requirements

(Self-referential core.) Each technology scheme MUST be defined before its subtypes' assets reach 10 telemetered instances. Initial schemes at v1: EIG-ABM-SCH-HP (heat pumps), -BAT (batteries), -PV (solar PV), -BLR (boilers), -VNT (ventilation), -LGT (lighting), -EVC (EV charging), -PMP (pumps/circulators). External anchor datasets (e.g. SEAI HP monitoring, EU Ecodesign declared values) loaded as reference cohorts flagged source=external, never pooled with measured cohorts.

## 11. Dataset Requirements

Inputs: asset master (11), telemetry (38) with attribution, weather (09), fault/event history (63), maintenance records. Outputs: release datasets (Parquet, `EIG_ABM_ROOT/releases/`), position datasets (joined to asset master monthly). All releases reproducible: inputs pinned by dataset version.

## 12. Feature Requirements

FEA-ABM-*: percentile_latest per metric, band, percentile_trend_12m (slope), gap_to_p50, gap_to_p90, peer_rank_within_cohort, underperformance_flag (band ≥ F for 2 consecutive releases). These features feed anomaly detection, maintenance prediction and replacement decision models.

## 13. Model Requirements

Degradation models (lifecycle family) train on release time series; their predictions are validated against subsequent releases (47). Anomaly models use cohort distributions as priors. Benchmark statistics must never leak future information into training (release date alignment enforced by feature pipeline).

## 14. KPI Requirements

Asset KPIs may be absolute (SPF ≥ 3.5) or benchmark-relative (≥ p50 of cohort). Relative KPIs MUST cite releaseID for reproducibility. Portfolio KPIs: % assets in bands A–C, count of underperformance flags, median percentile by subtype.

## 15. Response Derivative Requirements

flex_delivery_success (MET-015) is the bridge metric: RD reliability scores (52) and benchmark positions reconcile — an asset's RD confidence is bounded by its delivery-success benchmark band. Cohort-level response statistics inform aggregation coherence priors for VPP groups (EIG-FLX).

## 16. Decision Requirements

Decision triggers: band G on EFF metric for 2 releases → maintenance investigation action; capacity_fade_rate > p90 of cohort → replacement planning workflow; flex_delivery_success band ≤ E → de-rating of committed flex capacity in service enrolments (29). All triggers route through decision_intelligence (60) with the releaseID as evidence.

## 17. Ontology Mapping

Classes: BenchmarkScheme, BenchmarkMetric, BenchmarkCohort, StatisticRelease, AssetPosition.
Object properties: appliesToSubtype, hasMetric, hasCohort, hasRelease, positionsAsset, normalisedBy, anchoredToClimateZone.
Datatype properties: schemeID, metricID, percentile, band, n, periodStart, periodEnd, direction, crossComparable.
Alignment: QUDT for units; PROV-O for release lineage (prov:wasDerivedFrom dataset versions); SEAS EvaluationOntology (seas:Evaluation ≈ AssetPosition).

## 18. Knowledge Graph Mapping

Nodes: Scheme, Metric, Cohort, Release, Position. Relationships: APPLIES_TO (scheme→subtype), HAS_METRIC, HAS_COHORT, RELEASED (cohort→release), POSITIONS (release→asset via Position node), NORMALISED_BY (→ WX). Positions are first-class nodes (asset, metric, release, percentile) enabling temporal benchmark queries.

## 19. Digital Twin Mapping

Asset twins carry a BenchmarkContext: latest position per metric, band, trend. Twin simulation can project future positions under intervention scenarios (e.g. post-service SPF recovery), writing projected positions flagged scenario=true, never into real releases.

## 20. Governance

Owner: EIG Catalogue Owner; scheme stewards per technology (domain experts). Release sign-off: Validation Reviewer. Embargo rules enforced (n < 10 never published). External reference cohorts reviewed annually for vintage drift. Metric definition changes: major version + historical releases retained under old methodVersion (no retro-restatement).

## 21. Versioning

Scheme/metric semver; releases immutable and dated; methodVersion stamped on every release; position records regenerated only by new releases. Erratum process: flagged release + corrected successor.

## 22. Example Records

```text
schemeID: EIG-ABM-SCH-HP | appliesToSubtypes: [EIG-AC-HVC-01-01..07] | metrics: [001, 010, 011, 013, 014, 015, 017, 022]
cohortKeys: [subtype, capacityBand, climateZone, vintageBand] | minCohortN: 10 | cadence: quarterly
normalisationRules: [hdd_correction_v2, flow_temp_adjustment_v1]
```

```text
cohortID: EIG-ABM-COH-00142 | scheme: SCH-HP | metric: MET-001 (spf_measured)
keys: {subtype: ashp_air_water, capacityBand: 6-12kW, climateZone: EIG-GEO-CZ-IE-ISL, vintage: 2021_plus}
memberCount: 14 | selection: tier≥T2, dedicated metering, ≥330 days observation
```

```text
releaseID: EIG-ABM-REL-2026Q1-00142 | period: 2025-04-01..2026-03-31 | n: 14
stats: {p10: 2.7, p25: 3.0, p50: 3.4, p75: 3.8, p90: 4.2, mean: 3.42, std: 0.51}
weatherVintageRef: EIG-WX-ST-0021/2025-26 | methodVersion: 2.1 | published: 2026-04-15
```

```text
positionID: EIG-ABM-POS-884213 | asset: EIG-AST-004211 | release: REL-2026Q1-00142
raw: 4.45 | normalised: 4.31 | percentile: 93 | band: A | confidence: {obs: 358d, gapfill: 1.2%, attribution: dedicated}
```

## 23. Completion Criteria

Complete when: the 8 v1 schemes defined with full metric bindings; bidirectional subtype↔scheme integrity verified; cohorts auto-form for every eligible (subtype × keys) stratum; first releases published for all cohorts meeting n; every T2+ operational asset holds ≥ 1 position; decision triggers wired into catalogue 60; zero embargo violations.
