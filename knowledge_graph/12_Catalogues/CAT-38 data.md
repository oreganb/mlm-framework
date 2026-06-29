---
type: catalogue
ontology: EIG-MLM
catalogue_no: 38
catalogue_id: EIG-CAT-038
entity: data
band: data
status: active
tags: [eig-mlm, catalogue, band:data, entity:data]
aliases: ["EIG-CAT-038", "Catalogue 38", "data"]
---

# CAT-38 — Data

> **EIG-CAT-038** · band: **Data, Features & Datasets** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-09 weather_climate]] · [[CAT-19 energy_carrier]] · [[CAT-36 device]] · [[CAT-37 data_source]]
**Used by (downstream):** [[CAT-39 metadata]] · [[CAT-40 data_quality]] · [[CAT-41 feature]] · [[CAT-43 dataset]] · [[CAT-44 model]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-038`
**Entity prefixes:** `EIG-VAR-` (variable register), `EIG-DE-` (data entities)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Data Management Lead
**Depends on (upstream):** 09 weather_climate, 19 energy_carrier, 36 device, 37 data_source
**Used by (downstream):** 39 metadata, 40 data_quality, 41 feature, 43 dataset, 44 model

---

## 1. Definition

The **Data Catalogue** defines the framework's raw and processed data entities at two levels:

- **Variable** (`EIG-VAR-*`): the canonical definition of a measurable/derivable quantity — name, physical meaning, unit (UCUM), datatype, valid range, temporal semantics (instantaneous / interval-average / cumulative), and carrier/domain binding. The variable register is the single naming authority: one quantity, one variable ID, framework-wide. (Weather variables EIG-WX-VAR-001..022 from Catalogue 09 are incorporated by reference as the meteorological subset.)
- **Data entity** (`EIG-DE-*`): a logical table/series definition — a structured collection of variables with keys, grain, partitioning, and processing level — the unit from which datasets (43) are packaged.

Processing levels: **L0** raw-as-landed (immutable), **L1** validated/cleaned (quality-flagged, gap-marked), **L2** harmonised (resampled, unit-normalised, calendar-aligned), **L3** derived/aggregated (COMPUTED). Each level is a distinct data entity with lineage; lower levels are never overwritten.

## 2. Scope

**In scope:** variable register with full semantic definition; data entity definitions (schema, keys, grain, level, storage class); temporal semantics and resampling rules; unit normalisation rules; linkage to sources (37), features (41), models (44), datasets (43).

**Out of scope:** metadata standards (39), quality rules (40 — flags applied here, rules defined there), feature formulas (41), file packaging (43), storage technology choices (31 platform concern; lakehouse pattern referenced only).

## 3. Classification Structure

### 3.1 Variable domains (`variableDomain`)

| Code | Domain | Examples |
|---|---|---|
| VD-EL | Electrical | active_power_w, active_import_kwh, voltage_v, frequency_hz |
| VD-TH | Thermal | flow_temp_c, heat_energy_kwh, cop_ratio |
| VD-EN | Environmental indoor | air_temp_c, rh_pct, co2_ppm, occupancy_state |
| VD-WX | Meteorological | EIG-WX-VAR-001..022 (by reference, 09) |
| VD-MK | Market/price | dam_price_eur_mwh, imbalance_price, tariff_rate |
| VD-CB | Carbon | grid_ci_gco2_kwh (28 EIG-CO2-CI), avoided_emissions |
| VD-OP | Operational/state | device_status, setpoint_c, dispatch_state, override_flag |
| VD-FX | Flexibility | flex_capacity_up_kw, delivered_flex_kwh, baseline_kwh |

### 3.2 Temporal semantics (`temporalSemantics`)

| Code | Semantics | Resampling rule |
|---|---|---|
| TS-INST | Instantaneous sample | Downsample: mean/last (declared per variable); never sum |
| TS-AVG | Interval average | Time-weighted mean |
| TS-CUM | Cumulative register | Difference to interval; rollover/reset handling mandatory (36 registerCumulative) |
| TS-INT | Interval total (energy) | Sum on aggregation; pro-rata on splitting |
| TS-EVT | Event/state change | Never resampled; forward-fill for state reconstruction only with flag |

### 3.3 Processing levels

L0 raw / L1 validated / L2 harmonised / L3 derived — each entity declares exactly one.

## 4. Hierarchy

```
Variable register (EIG-VAR-*)  ── one quantity, one ID
Data entity (EIG-DE-*)         ── schema over variables
├── L0 entity (per stream family)
├── L1 entity (validated)      ── lineage → L0 + 40 profile
├── L2 entity (harmonised)     ── lineage → L1 + resampling/unit rules
└── L3 entity (derived)        ── lineage → L2 + transformation ref (41/44)
Dataset (43) packages entities for release/training.
```

## 5. Field Groups & Fields

### 5.1 Variable (`EIG-VAR-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | variableID | string | — | `EIG-VAR-\d{4}` (or EIG-WX-VAR-* by ref) | M | Variable ID |
| 2 | canonicalName | string | — | snake_case, unique | M | Canonical name |
| 3 | variableDomain | enum | — | §3.1 | M | Domain |
| 4 | definition | string | — | ≤1000 chars, physical meaning | M | Unambiguous definition |
| 5 | unit | string | — | UCUM (e.g. kW, kW.h, Cel, %) | M | Canonical unit; storage ALWAYS in canonical unit |
| 6 | datatype | enum | — | decimal / int / boolean / enum / string | M | Type |
| 7 | enumValues | array | — | closed list | C | Required if datatype enum |
| 8 | validRange | range | — | min/max physical plausibility | M | Hard bounds (40 plausibility baseline) |
| 9 | temporalSemantics | enum | — | §3.2 | M | Time meaning + resampling rule binding |
| 10 | downsampleRule | enum | — | mean / last / sum / twa / none | M | Per §3.2 constraint |
| 11 | carrierRef | ref | — | →19 EIG-ECR-* | C | For energy variables |
| 12 | precisionScale | object | — | decimals stored/displayed | M | Numeric discipline |
| 13 | sourceVariableAliases | array<string> | — | provider-side names | O | Mapping aid (37 schema mapping) |
| 14 | piiSensitivity | enum | — | none / indirect / direct | M | Privacy class (granular consumption = indirect) |
| 15 | status | enum | — | active / deprecated (with successor ref) | M | Lifecycle |

### 5.2 Data Entity (`EIG-DE-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 16 | entityID | string | — | `EIG-DE-[A-Z0-9-]+-L[0-3]` | M | Entity ID with level suffix |
| 17 | entityName | string | — | ≤160 chars | M | Name |
| 18 | processingLevel | enum | — | L0/L1/L2/L3 | M | Level |
| 19 | grain | object | — | key set + time resolution (e.g. {buildingID, ts} @ PT15M) | M | One row = one (keys, ts) |
| 20 | variables | array<object> | — | variableRef + nullability + quality-flag column pairing | M | Schema |
| 21 | keys | array<ref> | — | entity keys (→02 buildingID, →11 assetID, →36 deviceID, MPRN, →08 geo) | M | Identity columns |
| 22 | timezonePolicy | enum | — | utc_stored_local_derived | M | Fixed: store UTC, derive local (DST-safe; 21 V6 / 26 V10 alignment) |
| 23 | sourceLineage | array<ref> | — | →37 streams (L0) / →parent entity + transform ref (L1+) | M | Lineage (PROV) |
| 24 | dataClassProfile | object | — | per-variable dataClass column or entity-constant | M | Provenance carried per value where mixed (31 A3) |
| 25 | qualityRegime | ref | — | →40 profile | M | Quality rules applied (L1+) |
| 26 | partitioning | object | — | e.g. by month + buildingID | M | Storage partitioning (lakehouse pattern: Parquet/DuckDB convention) |
| 27 | retentionPolicy | object | — | duration per level; L0 settlement-relevant ≥7 y | M | Retention (27 alignment) |
| 28 | linkedFeatures | array<ref> | — | →41 EIG-FT-* | O | Features computed from entity |
| 29 | linkedModels | array<ref> | — | →44 EIG-MDL-* | O | Models consuming entity (maintained from 44 side too) |
| 30 | accessClass | enum | — | open / internal / restricted / personal | M | Drives 43 access rules |
| 31 | environment | enum | — | live / sandbox | M | Firewall consistency |
| 32 | status | enum | — | active / frozen / deprecated | M | Lifecycle |

## 6. Controlled Vocabularies

Domains, temporal semantics, levels as §3; UCUM units exclusively; timezonePolicy single value (fixed); dataClass REAL/CALIBRATED/SYNTHETIC/COMPUTED. Gap markers: explicit NULL + gap-flag column (never zero-filled); quality flags from 40 vocabulary {valid, suspect, estimated, missing, quarantined}.

## 7. Applicability Rules

- **A1 — One quantity, one variable.** New ingestions map provider fields to existing variables (aliases) before any new variable is minted; duplicate semantics are a registry error resolved by deprecation+successor.
- **A2 — Canonical units at rest.** All storage in canonical UCUM units; conversion at ingestion with declared factors (19 conversion register for carrier-energy conversions); display conversions downstream only.
- **A3 — Levels are append-only and ordered.** L0 immutable as landed; corrections create L1+ versions with lineage — never in-place edits (mirrors 27 A6 supersede discipline). A level-n entity derives only from level ≤ n sources.
- **A4 — Temporal semantics govern math.** Aggregation/resampling strictly per temporalSemantics: cumulative registers differenced with rollover handling; instantaneous never summed; energy pro-rated on split. Violations are pipeline defects, not data.
- **A5 — Gaps are explicit.** Missing intervals carry NULL + gap flag; imputation only at L2+ with method recorded and values flagged `estimated` (dataClass CALIBRATED) — settlement and RD use never consume imputed values silently (27/52 evidence rules).
- **A6 — Mixed provenance is per-value.** Entities mixing dataClasses carry a per-value dataClass column; entity-constant shortcut allowed only when provably uniform.
- **A7 — Privacy at grain.** piiSensitivity ≥ indirect variables at sub-hourly building grain are accessClass personal; aggregation to cohort n≥10 required before open/internal exposure (framework cohort rule; 03/37 A7).

## 8. Validation Rules

- **V1** Variable IDs/names unique; UCUM units parse; validRange sane (min<max).
- **V2** Entity grain keys resolve to registered key domains; (keys, ts) uniqueness enforced.
- **V3** downsampleRule consistent with temporalSemantics matrix (§3.2).
- **V4** L1+ entities reference a qualityRegime; L0 reference streams with matching variables (37 V2 reciprocal).
- **V5** Lineage acyclic and level-ordered (A3).
- **V6** No zero-fill: statistical screen for suspicious zero-runs in gap windows (pipeline test).
- **V7** Per-value dataClass column present where lineage mixes classes (A6).
- **V8** Retention ≥ floors (settlement 7 y; RD evidence 13 months min; personal data ≤ purpose limit per DPIA).
- **V9** Sandbox entities derive only from sandbox/one-way-copied sources (firewall).
- **V10** Deprecated variables carry successorRef; no active entity schema uses deprecated variables after grace window.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 09 weather | EIG-WX-VAR-* subset by reference; harmonised weather entities |
| 19 energy_carrier | carrierRef; conversion factors at ingestion |
| 27 settlement | Metered-quantity entities (TR-A lineage); retention floors |
| 36/37 device & source | L0 lineage to streams/devices; alias mapping |
| 39 metadata | Entity/variable metadata records |
| 40 data_quality | Quality regimes, flags vocabulary |
| 41 feature | Features compute from L2/L3 entities |
| 42 synthetic | SYNTHETIC entities sandbox-side |
| 43 dataset | Packaging of entities |
| 44 model | Input bindings; dependency notifications |
| 52 RD | Evidence-grade entity requirements |

## 10. Benchmarking Requirements

Entity health: completeness %, estimated-value share, latency from source to L2. Variable register health: alias coverage, duplicate-candidate audit quarterly.

## 11. Dataset Requirements

This catalogue defines the entities datasets package; its own register snapshots daily.

## 12. Feature Requirements

Features (41) declare input entities at L2+ only (no features on raw L0); lineage chain variable→entity→feature is mandatory for model audit.

## 13. Model Requirements

Models bind entities (via datasets) with declared minimum quality (40) and freshness; entity schema changes propagate notifications to bound models (44 dependency graph).

## 14. KPI Requirements

`entity_completeness_pct`, `estimated_share_pct`, `pipeline_latency_p95`, `schema_drift_incidents`.

## 15. Response Derivative Requirements

RD-grade entities: REAL/CALIBRATED only, source timestamps, no silent imputation, PT15M or finer for thermal/electrical response measurement; flagged per 52 evidence spec.

## 16. Decision Requirements

Decision inputs consume L2+ entities with freshness checks; stale critical entities trigger declared fallbacks (37 §16 chain).

## 17. Ontology Mapping

- `eig:Variable` ⊑ `qudt:Quantity` pattern with `qudt:unit` (UCUM bridge); `eig:DataEntity` ⊑ `dcat:Dataset` component; PROV-O lineage (`prov:wasDerivedFrom` between levels); SOSA `sosa:ObservableProperty` for measured variables.

## 18. Knowledge Graph Mapping

Nodes: `(:Variable)`, `(:DataEntity {level})`. Edges: `[:HAS_VARIABLE]`, `[:DERIVES_FROM]` (level chain), `[:SOURCED_FROM]->(:DataStream)`, `[:FEEDS_FEATURE]`, `[:CONSUMED_BY]->(:Model)`. Impact analysis: "what breaks if variable X deprecates" traverses HAS_VARIABLE→FEEDS_FEATURE→CONSUMED_BY.

## 19. Digital Twin Mapping

Twin dataset requirements (33) cite variables + resolutions from this register; twin state stores are L2 entities with twin-specific grain.

## 20. Governance

Owner: Data Management Lead. New variables: registry review (duplicate check, unit/semantics sign-off). Entity schema changes: versioned migration with consumer notification ≥30 d. Quarterly variable-duplicate audit.

## 21. Versioning

Variables: deprecate+successor, never mutate semantics. Entities: schema SemVer; MAJOR = breaking (column removal/retype), MINOR = additive columns, PATCH = docs. Data itself: append-only per A3.

## 22. Example Records

### EIG-VAR-0102 — active_import_kwh

- domain VD-EL; unit kW.h; datatype decimal(12,3); validRange 0–10,000 per interval (building grain)
- temporalSemantics TS-INT (interval total); downsampleRule sum; carrierRef EIG-ECR-EL
- aliases: ["AI_KWH" (HES csv), "import_energy" (EdgeWare)]; piiSensitivity indirect (A7 grain rule applies)

### EIG-VAR-0240 — hp_power_w

- domain VD-EL; unit W; TS-INST; downsample mean; range 0–25,000; aliases ["compressor_power"]; source devices type EIG-DEV-TYP-0012

### EIG-DE-ARAN-ELEC-HH-L1 @ 1.2.0 — Aran half-hourly consumption, validated

- level L1; grain {mprn_pseudonym, ts} @ PT30M (→PT15M transition tracked); keys mprn_pseudonym→02 buildings
- variables: [EIG-VAR-0102 + quality flags]; lineage: EIG-DS-00211 (37) → L0 EIG-DE-ARAN-ELEC-HH-L0
- qualityRegime EIG-DQ-R-014; dataClass REAL (entity-constant); accessClass personal; retention 7 y (settlement lineage)
- timezone: UTC stored, Europe/Dublin derived

### EIG-DE-ODEON-ASSET-TELEM-L2 @ 2.0.0 — Harmonised asset telemetry

- level L2; grain {assetID, ts} @ PT1M; variables [EIG-VAR-0240 hp_power_w, EIG-VAR-0241 flow_temp_c, …]
- lineage L1 ← L0 ← streams EIG-DS-04471/04472; unit-normalised, UTC; gap flags explicit; estimated values flagged CALIBRATED
- linkedFeatures: thermal-response features (41); linkedModels: EIG-MDL-FLEXENV-01; RD-grade ✓ (§15)

### EIG-DE-IE-GRID-CI-L2 — National grid carbon intensity

- variables [EIG-VAR-0501 grid_ci_gco2_kwh ← 28 EIG-CO2-CI]; PT30M; lineage EirGrid stream (37); consumed by avoided-emissions COMPUTED L3 entities (28 A4 single-truth method)

## 23. Completion Criteria

- [x] Dataset identity (entities), variables, source, frequency, units, quality, coverage, linked features, linked models — all per job spec
- [x] Variable register as single naming authority with aliases, UCUM, temporal semantics, resampling math (A1/A2/A4)
- [x] L0–L3 level model, append-only, lineage-ordered (A3)
- [x] Explicit gaps, flagged imputation, per-value provenance (A5/A6)
- [x] Privacy grain rule with cohort n≥10 (A7)
- [x] Cross-refs: EIG-DS-00211/04471/04472, EIG-WX-VAR-*, EIG-ECR-EL, EIG-CO2-CI, EIG-MDL-FLEXENV-01
- [x] Example variables and entities at multiple levels
