---
type: catalogue
ontology: EIG-MLM
catalogue_no: 33
catalogue_id: EIG-CAT-033
entity: digital_twin_template
band: flexibility
status: active
tags: [eig-mlm, catalogue, band:flexibility, entity:digital_twin_template]
aliases: ["EIG-CAT-033", "Catalogue 33", "digital_twin_template"]
---

# CAT-33 — Digital Twin Template

> **EIG-CAT-033** · band: **Flexibility & Platform** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-01 building_classification]] · [[CAT-02 building]] · [[CAT-04 space]] · [[CAT-05 building_fabric]] · [[CAT-07 building_archetype]] · [[CAT-10 asset_classification]] · [[CAT-11 asset]] · [[CAT-32 digital_twin]]
**Used by (downstream):** [[CAT-32 digital_twin]] · [[CAT-35 edge_infrastructure]] · [[CAT-37 data_source]] · [[CAT-43 dataset]] · [[CAT-44 model]] · [[CAT-57 kpi]] · [[CAT-73 application]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-033`
**Entity prefix:** `EIG-DTT-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Twin Engineering Lead
**Depends on (upstream):** 01 building_classification, 02 building, 04 space, 05 building_fabric, 07 building_archetype, 10 asset_classification, 11 asset, 32 digital_twin
**Used by (downstream):** 32 digital_twin (instantiation), 35 edge_infrastructure, 37–43 data layer, 44 model, 57 kpi, 73 application

---

## 1. Definition

A **Digital Twin Template** is a versioned, reusable specification that defines everything required to instantiate a Digital Twin (Catalogue 32) for a class of subjects — typically a building classification (Catalogue 01) or archetype (Catalogue 07) — without bespoke engineering. A template declares the required spaces, assets, datasets, models, KPIs, calibration procedure, and target maturity/fidelity, such that twin generation becomes a deterministic, scalable factory process: `template + subject → twin instance`.

Templates are to twins what archetypes (07) are to buildings: the class-level definition from which instances are stamped out. A template never contains subject-specific data; it contains **requirements, defaults, and binding rules**.

## 2. Scope

**In scope:**
- Templates for building twins, zone twins, asset twins, network twins, portfolio twins, and community twins
- Requirement declarations: minimum spaces, mandatory asset coverage, dataset specifications, model bundles, KPI bundles
- Default parameter sets (RC defaults, schedule defaults, fabric assumptions) flagged as CALIBRATED-pending
- Instantiation, binding, and conformance rules
- Template versioning and migration of instantiated twins

**Out of scope:**
- Twin instances themselves (Catalogue 32)
- Subject data (Catalogues 02, 04, 11)
- Model internals (Catalogue 44)
- Edge deployment runtime (Catalogue 35)

## 3. Classification Structure

Template types (`templateType`):

| Code | Type | Subject Catalogue | Description |
|---|---|---|---|
| DTT-BLD | Building twin template | 02 | Whole-building twin per classification/archetype |
| DTT-ZON | Zone twin template | 04 | Thermal-zone or space-cluster twin |
| DTT-AST | Asset twin template | 11 | Single-asset twin (e.g. ASHP, battery) |
| DTT-NET | Network twin template | 13 | Feeder / network-zone twin |
| DTT-PRT | Portfolio twin template | 30 | Aggregated flexibility portfolio twin |
| DTT-COM | Community twin template | 86 | Energy-community twin |

Secondary axes:
- **Target maturity** (from 32): DT-M1..DT-M4 — the maturity the template is designed to reach
- **Target fidelity** (from 32): F1..F4
- **Sector applicability**: list of Catalogue 01 sector codes

## 4. Hierarchy

```
Template family (e.g. EIG-DTT-RES residential)
└── Template (EIG-DTT-RES-01, versioned)
    ├── Space requirement set
    ├── Asset requirement set
    ├── Dataset requirement set
    ├── Model bundle
    ├── KPI bundle
    ├── Default parameter set
    └── Instantiation rule set
        └── (instantiated) Twin instances — recorded in Catalogue 32, back-reference templateID@version
```

A template belongs to exactly one family; a twin instance binds to exactly one template version at instantiation (32 records `templateRef` e.g. `EIG-DTT-RES-01@2.1.0`).

## 5. Field Groups & Fields

### 5.1 Identity & Classification

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | templateID | string | — | `EIG-DTT-[A-Z]{3}-\d{2}` | M | Unique template identifier |
| 2 | templateVersion | string | — | SemVer `MAJOR.MINOR.PATCH` | M | Template version; instances bind to exact version |
| 3 | templateName | string | — | ≤120 chars | M | Human-readable name |
| 4 | templateType | enum | — | §3 codes | M | Template type |
| 5 | templateFamily | string | — | `EIG-DTT-[A-Z]{3}` | M | Family grouping |
| 6 | status | enum | — | draft / active / deprecated / withdrawn | M | Lifecycle status |
| 7 | description | string | — | ≤2000 chars | M | Purpose and intended use |
| 8 | ownerOrgID | ref | — | →15 EIG-ORG-* | M | Owning organisation |
| 9 | stewardRoleID | ref | — | →16 role | M | Steward role per 16 A6 |

### 5.2 Applicability

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 10 | applicableClassifications | array<ref> | — | →01 classification codes | M | Building classifications this template serves |
| 11 | applicableArchetypes | array<ref> | — | →07 EIG-ARC-* | O | Narrower archetype targeting |
| 12 | applicableSectors | array<enum> | — | 01 sector codes | M | Sector list |
| 13 | climateZoneScope | array<ref> | — | →08 EIG-GEO-CZ-* | O | Climate zones template defaults are valid for |
| 14 | floorAreaRangeM2 | range | m² | min/max | O | Valid floor-area band |
| 15 | constructionPeriodScope | array<enum> | — | 07 period codes | O | Construction periods covered by defaults |
| 16 | exclusionRules | array<rule> | — | machine-evaluable | O | Conditions disqualifying a subject (e.g. `mixedUse = TRUE`) |

### 5.3 Target Twin Specification

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 17 | targetMaturity | enum | — | DT-M1..DT-M4 | M | Maturity the template is engineered to reach |
| 18 | targetFidelity | enum | — | F1..F4 | M | Fidelity class (32 §6) |
| 19 | purposeSet | array<enum> | — | 32 purpose codes (monitoring / forecasting / flexibility / settlement_support / planning) | M | Purposes the resulting twin is fit for |
| 20 | calibrationProcedureRef | ref | — | →44 model / procedure doc | C | Mandatory if targetFidelity ≥ F3 |
| 21 | calibrationThresholds | object | — | CVRMSE ≤ %, NMBE ± % | C | Defaults: CVRMSE ≤15%, NMBE ±5% (32 A3); required if targetFidelity ≥ F3 |
| 22 | autonomyEnvelopeTemplate | object | — | guardrail spec | C | Required if targetMaturity = DT-M4; instantiated per 32 V9 incl. kill-switch test ≤90 d |

### 5.4 Required Spaces

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 23 | requiredSpaceTypes | array<object> | — | see sub-fields | M | Space requirement set |
| 23a | — spaceType | enum | — | 04 space-type codes | M | e.g. zone_conditioned, plant_room |
| 23b | — minCount | int | — | ≥0 | M | Minimum instances |
| 23c | — zoningRule | enum | — | per_floor / per_orientation / single_zone / per_dwelling | M | How zones are derived |
| 23d | — rcParameterPolicy | enum | — | from_survey / from_archetype_default / from_calibration | M | Where RC params come from |
| 24 | lifeSafetyExclusionRule | boolean | — | TRUE fixed | M | Life-safety circuits excluded from controllable scope (04 rule) |

### 5.5 Required Assets

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 25 | requiredAssetCoverage | array<object> | — | see sub-fields | M | Asset requirement set |
| 25a | — assetClassRef | ref | — | →10 EIG-AC-* | M | Required asset class |
| 25b | — requirement | enum | — | mandatory / conditional / optional | M | Binding strength |
| 25c | — condition | rule | — | machine-evaluable | C | Required if conditional (e.g. `heatingFuel = electricity`) |
| 25d | — minDataTier | enum | — | T0–T3 (11 tiers) | M | Minimum data-richness tier for the bound asset |
| 25e | — flexEnvelopeRequired | boolean | — | TRUE/FALSE | M | Whether 11 §5.6 envelope must be declared |
| 26 | meterRequirement | object | — | MPRN mandatory; sub-metering rules | M | Metering minimums (e.g. main meter T2, heat-pump circuit sub-meter if FS enrolment intended) |

### 5.6 Required Datasets

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 27 | requiredDatasets | array<object> | — | see sub-fields | M | Dataset requirement set |
| 27a | — datasetSpecRef | ref | — | →43 dataset spec | M | Dataset specification |
| 27b | — variables | array<ref> | — | →38 data variables / 09 EIG-WX-VAR-* | M | Required variables |
| 27c | — minResolution | duration | — | ISO 8601 (e.g. PT15M) | M | Max sampling interval |
| 27d | — minHistoryDays | int | d | ≥0 | M | History needed before instantiation completes |
| 27e | — dataClassFloor | enum | — | REAL / CALIBRATED | M | SYNTHETIC not acceptable for live twins (32 A5); SYNTHETIC permitted only in SBX instantiations |
| 27f | — qualityFloor | ref | — | →40 data_quality rule | M | Minimum quality gate |
| 28 | weatherBindingRule | object | — | station selection rule | M | e.g. nearest 09 station ≤30 km else gridded reanalysis, declared CALIBRATED |

### 5.7 Model Bundle

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 29 | requiredModels | array<object> | — | see sub-fields | M | Model bundle |
| 29a | — modelRole | enum | — | thermal / baseline / forecast_demand / forecast_generation / flex_envelope / anomaly / control | M | Role within twin |
| 29b | — modelSpecRef | ref | — | →44 EIG-MDL-* spec | M | Model specification (not instance) |
| 29c | — requirement | enum | — | mandatory_at_maturity(Mx) / optional | M | Maturity at which the model becomes mandatory |
| 29d | — validationGateRef | ref | — | →48 validation spec | M | Acceptance gate before model activates in twin |
| 30 | rdRequirement | object | — | RD spec ref + min confidence | C | Required if purposeSet ∋ flexibility; aligns with 22 §15 / 29 A2 gates (conf ≥0.8, recency ≤90 d defaults) |

### 5.8 KPI Bundle & Defaults

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 31 | requiredKPIs | array<ref> | — | →57 EIG-KPI-* | M | KPIs every instance must compute |
| 32 | benchmarkBindings | array<ref> | — | →06 / 12 benchmark sets | O | Benchmarks instances report against |
| 33 | defaultParameterSet | object | — | key→value, each tagged | M | Defaults (RC params, schedules, fabric U-values from 05/07); every default tagged `provenance: archetype_default` and dataClass CALIBRATED-pending until calibration replaces it |
| 34 | parameterOverridePolicy | enum | — | survey_overrides_default / calibration_overrides_all | M | Precedence ladder: calibration > survey > archetype default |

### 5.9 Instantiation & Conformance

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 35 | instantiationRuleSet | array<rule> | — | machine-evaluable | M | Binding rules (subject checks, asset binding, dataset binding) |
| 36 | conformanceChecks | array<rule> | — | machine-evaluable | M | Post-instantiation checks; ALL must pass before twin status = active |
| 37 | instantiationModes | array<enum> | — | live / sandbox | M | SBX instantiations inherit sandbox firewall (31 A2 / 32 A6) |
| 38 | estimatedInstantiationEffort | enum | — | automatic / assisted / engineered | M | automatic = zero manual steps |
| 39 | migrationPolicy | object | — | per-version rules | M | How existing instances move to new template versions (§21) |

## 6. Controlled Vocabularies

- `templateType`: §3
- `requirement`: mandatory / conditional / optional
- `rcParameterPolicy`: from_survey / from_archetype_default / from_calibration
- `dataClassFloor`: REAL / CALIBRATED (SYNTHETIC only in sandbox mode)
- `parameterOverridePolicy`: survey_overrides_default / calibration_overrides_all
- `instantiationMode`: live / sandbox
- `estimatedInstantiationEffort`: automatic / assisted / engineered
- Maturity DT-M1..M4, fidelity F1..F4: owned by Catalogue 32; reused here without redefinition

## 7. Applicability Rules

- **A1 — One template per instance.** Every twin instance (32) binds to exactly one `templateID@templateVersion` at instantiation. Template-less twins are permitted only as `engineered` one-offs and must declare `templateRef = NONE` with justification.
- **A2 — Subject must match applicability.** Instantiation MUST fail if the subject's classification (01), archetype (07), floor area, or climate zone falls outside §5.2, unless an explicit waiver (governance §20) is recorded.
- **A3 — Defaults are CALIBRATED-pending.** Default parameters (§5.8 field 33) carry dataClass CALIBRATED only after the template's calibration procedure has validated them against the subject; until then the instance cannot exceed fidelity F2 regardless of targetFidelity.
- **A4 — Maturity gating inherits 32 A2.** A template cannot promise DT-M4 unless its model bundle includes a control model with a validation gate and an autonomy envelope template (field 22).
- **A5 — Sandbox firewall.** `instantiationMode = sandbox` instances are NEST-side (31 EIG-PLT-NEST): SYNTHETIC datasets permitted, no settlement linkage, no live control authority — consistent with 20 A2, 26 A3, 27 A3, 31 A2, 32 A6.
- **A6 — Flexibility purpose requires flex requirements.** If `purposeSet ∋ flexibility`, the template MUST require: at least one asset class with `flexEnvelopeRequired = TRUE`, minDataTier ≥ T2, and an rdRequirement (field 30).
- **A7 — Life-safety exclusion is non-waivable.** Field 24 is fixed TRUE for all templates; no waiver path exists.

## 8. Validation Rules

- **V1** templateID unique; matches pattern; family prefix consistent with templateType.
- **V2** templateVersion is valid SemVer; an `active` template version is immutable — changes require a new version.
- **V3** Every ref field resolves to an existing entity in the named catalogue (dangling refs fail CI).
- **V4** If targetFidelity ≥ F3 then calibrationProcedureRef and calibrationThresholds present (cross-check 32 A3).
- **V5** If targetMaturity = DT-M4 then autonomyEnvelopeTemplate present and includes killSwitchTestIntervalDays ≤ 90.
- **V6** Σ of mandatory dataset history (`minHistoryDays`) consistent with model bundle training requirements (44/50); a model requiring 365 d cannot sit in a template demanding only 90 d.
- **V7** conformanceChecks non-empty and include at minimum: subject-match check, mandatory-asset-bound check, dataset-availability check, model-gate-passed check.
- **V8** All conditional rules (`condition`, `exclusionRules`, `instantiationRuleSet`, `conformanceChecks`) parse in the platform rule grammar (machine-evaluable, 30 A7 grammar reuse).
- **V9** If `purposeSet ∋ settlement_support` then targetFidelity ≥ F3 (32 A4: F2 not settlement-grade).
- **V10** Deprecated template versions cannot be used for new instantiations; existing instances flagged for migration per §21.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 01 building_classification | `defaultTwinTemplateID` in 01 points here (e.g. EIG-DTT-OFF-01); applicability targets 01 codes |
| 02 building | Subjects of DTT-BLD instantiation |
| 04 space | Space requirement sets reference 04 types; zoning rules |
| 05/07 fabric & archetype | Source of default parameters; archetype→template default mapping |
| 10/11 assets | Asset coverage requirements; data tiers; flex envelopes |
| 32 digital_twin | Instances record `templateRef`; maturity/fidelity vocabularies owned by 32 |
| 35 edge_infrastructure | Templates declare edge runtime needs (model bundle → deployment profile) |
| 37–43 data layer | Dataset requirement specs |
| 44/48 model & validation | Model bundle specs and gates |
| 57 kpi | KPI bundles |
| 73 application | Applications consume template-conformant twins |

## 10. Benchmarking Requirements

Templates are benchmarked on: instantiation success rate (target ≥95% automatic for `automatic` templates), mean time-to-active, calibration pass rate at first attempt, and post-calibration parameter drift vs archetype defaults. Cohort minimum n=10 instances before template-level statistics are published (framework rule).

## 11. Dataset Requirements

This catalogue is itself a registry; its records require no datasets. Template dataset requirement sets (§5.6) are the binding contract that Catalogue 43 dataset instances must satisfy at instantiation.

## 12. Feature Requirements

None directly; feature specs enter via the model bundle's modelSpecRefs (44→41).

## 13. Model Requirements

Model bundle (§5.7) declares model **specifications**, never trained instances. Trained instances are bound per twin at instantiation and must pass the declared validation gate (48) before activation.

## 14. KPI Requirements

KPI bundle (§5.8) is mandatory and minimal: every template includes at least `energy_use_intensity`, `comfort_compliance`, and — if flexibility-purposed — `flex_delivery_success` (aligned to EIG-ABM-MET-015).

## 15. Response Derivative Requirements

Flexibility-purposed templates carry an rdRequirement (field 30) specifying the RD spec (52), minimum confidence (default ≥0.8) and recency (default ≤90 d), mirroring product gates (22 §15) so that template-conformant twins are FS-eligible (29 A2) by construction.

## 16. Decision Requirements

Templates targeting DT-M4 must include: control model with validation gate, autonomy envelope template with hard comfort floors (29 A7), override honour rule (29 A4), and kill-switch test schedule (32 V9). Decision logic itself lives in Catalogues 57–65.

## 17. Ontology Mapping

- Template class → `eig:DigitalTwinTemplate` ⊑ `eig:Specification` (MLM ontology)
- `appliesTo` → 01/07 classes; `requiresAssetClass` → 10; `requiresDataset` → 43; `requiresModel` → 44
- SAREF4BLDG / Brick alignment via the space and asset requirement vocabularies inherited from 04/10
- Instantiation relation: `eig:instantiatedFrom(Twin, TemplateVersion)`

## 18. Knowledge Graph Mapping

Nodes: `(:TwinTemplate {templateID, version})`, `(:TemplateFamily)`. Edges: `[:IN_FAMILY]`, `[:APPLIES_TO]→(:BuildingClass|:Archetype)`, `[:REQUIRES_ASSET_CLASS]`, `[:REQUIRES_DATASET_SPEC]`, `[:REQUIRES_MODEL_SPEC]`, `[:REQUIRES_KPI]`, `(:DigitalTwin)-[:INSTANTIATED_FROM {version}]->(:TwinTemplate)`. The INSTANTIATED_FROM edge carries the exact version for migration queries.

## 19. Digital Twin Mapping

This catalogue **is** the twin factory specification layer. Mapping is direct: template → 32 instance fields. Composition templates (DTT-PRT, DTT-COM) compose by reference per 32 A7 — child twins are bound, never copied.

## 20. Governance

Owner: EIG Twin Engineering Lead. Steward, reviewer, approver roles per 16 A6. New template versions require: design review, pilot instantiation on ≥3 subjects, calibration evidence if F3+, and approval record. Waivers (A2) require approver-tier sign-off and expire ≤12 months. Change log mandatory per version.

## 21. Versioning

SemVer. MAJOR = breaking change to requirement sets or conformance checks (instances must migrate or pin); MINOR = additive requirements/defaults (instances may adopt); PATCH = corrections. `migrationPolicy` per version declares: auto_migrate / assisted_migrate / pin_allowed_until(date). Deprecated versions: no new instantiations (V10); withdrawal only after zero active instances remain.

## 22. Example Records

### EIG-DTT-RES-01 @ 2.1.0 — Residential dwelling twin template

- templateName: "Residential Dwelling Operational/Predictive Twin"
- templateType: DTT-BLD; family EIG-DTT-RES; status: active
- applicableClassifications: [01: residential_detached, residential_semi_d, residential_terraced]; applicableArchetypes: [EIG-ARC-RES-SD-1971-1990, EIG-ARC-RES-SD-1991-2010]; sectors: [residential]; climateZoneScope: [EIG-GEO-CZ-IE-ATL, -INL, -ISL]; floorAreaRangeM2: 60–280
- targetMaturity: DT-M3; targetFidelity: F3; purposeSet: [monitoring, forecasting, flexibility]
- calibrationThresholds: CVRMSE ≤15%, NMBE ±5%; calibrationProcedureRef: EIG-MDL-CAL-RES-01
- requiredSpaceTypes: zone_conditioned minCount 2 (zoningRule per_floor, rcParameterPolicy from_calibration); plant_room minCount 0; lifeSafetyExclusionRule TRUE
- requiredAssetCoverage: EIG-AC-HVC-01-01 (ashp) conditional on `heatingFuel = electricity`, minDataTier T2, flexEnvelopeRequired TRUE; EIG-AC-MTR-01-01 (main meter) mandatory T2; EIG-AC-STO-01-01 (li_ion_lfp) optional T2 flexEnvelopeRequired TRUE
- requiredDatasets: dwelling_electricity_15min (PT15M, 180 d history, REAL, quality gate EIG-DQ-R-014); indoor_temperature (PT15M, 90 d, REAL); weather binding: nearest 09 station ≤30 km (EIG-WX-ST-0007 typical for Cork subjects)
- requiredModels: thermal RC model EIG-MDL-SPEC-THERM-R2C2 mandatory_at_maturity(M2), gate EIG-VAL-THERM-01; demand forecast EIG-MDL-SPEC-FCST-RES-01 mandatory_at_maturity(M3); flex_envelope EIG-MDL-SPEC-FLEXENV-01 mandatory if flexibility
- rdRequirement: EIG-RD-SPEC-THERM-SHED, minConfidence 0.8, recency ≤90 d
- requiredKPIs: [EIG-KPI-EUI, EIG-KPI-COMFORT, EIG-KPI-FLEX-DELIV (≡ EIG-ABM-MET-015)]
- defaultParameterSet: R/C from EIG-ARC-RES-SD-1971-1990 (provenance archetype_default); setpoint schedule default 19 °C day / 16 °C night
- parameterOverridePolicy: calibration_overrides_all; estimatedInstantiationEffort: automatic; instantiationModes: [live, sandbox]
- conformanceChecks: subject-match; meter-bound; 180 d data present; thermal model gate passed; if flexibility intended → ASHP envelope declared
- **Used by:** EIG-DT-000877 (IE_B0014, Inis Mór — 32 example) instantiated from this template @2.1.0

### EIG-DTT-OFF-01 @ 1.3.0 — Office building twin template

- templateType: DTT-BLD; family EIG-DTT-OFF; status: active (referenced as defaultTwinTemplateID by 01 office classification)
- applicableClassifications: [office_general, office_public]; sectors: [commercial_office, public]; floorAreaRangeM2: 500–25,000
- targetMaturity: DT-M3; targetFidelity: F3; purposeSet: [monitoring, forecasting, flexibility, planning]
- requiredSpaceTypes: zone_conditioned per_orientation minCount 4; plant_room minCount 1; server_room conditional
- requiredAssetCoverage: AHU class mandatory T2; chiller/heat-pump conditional; BMS point mapping mandatory; main meter + floor sub-meters T2
- requiredDatasets: building_electricity_15min (PT15M, 365 d, REAL); BMS zone temps (PT15M, 180 d); occupancy proxy (PT1H, 90 d, CALIBRATED acceptable)
- requiredModels: multi-zone thermal mandatory_at_maturity(M2); demand forecast M3; anomaly model M2
- requiredKPIs: [EIG-KPI-EUI, EIG-KPI-COMFORT, EIG-KPI-PEAK, EIG-KPI-FLEX-DELIV]
- estimatedInstantiationEffort: assisted (BMS point mapping manual)
- migrationPolicy 1.2→1.3: assisted_migrate (added server_room conditional space)

### EIG-DTT-NET-01 @ 1.0.0 — Network-zone twin template (sandbox-first)

- templateType: DTT-NET; subjects: 13 network zones; targetMaturity DT-M2; targetFidelity F2
- purposeSet: [monitoring, planning]; instantiationModes: [sandbox, live]; sandbox instances on EIG-PLT-NEST may bind SYNTHETIC feeder datasets (A5)
- requiredDatasets: feeder loading (PT15M, 90 d), connection-point register (13 CP-*)
- conformanceChecks: CP register reconciles with MPRN list (pre-check of P14 V10 reconciliation)
- **Used by:** EIG-DT-000301 (NZ-00017 Inis Mór zone twin — 32 example)

## 23. Completion Criteria

- [x] All template types defined with subject catalogue bindings
- [x] Requirement sets cover spaces, assets, datasets, models, KPIs per job spec
- [x] Instantiation/conformance machinery defined and machine-evaluable
- [x] Defaults provenance and override ladder defined (A3)
- [x] Sandbox firewall inherited (A5)
- [x] Cross-refs resolved: EIG-DTT-OFF-01 (01), EIG-DTT-RES-01@2.1 (32 example), EIG-DT-000301 template (32 example)
- [x] Versioning with instance migration policy
- [x] Example records: residential, office, network templates
