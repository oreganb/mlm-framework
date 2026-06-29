---
type: catalogue
ontology: EIG-MLM
catalogue_no: 11
catalogue_id: EIG-CAT-011
entity: asset
band: physical
status: active
tags: [eig-mlm, catalogue, band:physical, entity:asset]
aliases: ["EIG-CAT-011", "Catalogue 11", "asset"]
---

# CAT-11 — Asset

> **EIG-CAT-011** · band: **Physical & Spatial Stack** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Asset Catalogue

Catalogue ID prefix: **EIG-AST** · Catalogue number: 11 · Version 1.1 · Status: Active

> **v1.1 (2026-06-13) — generator enrichment.** §5.2 gains `pvShadingFactor` (PV-asset horizon-shading correction sourced from the host building's land cover, →02 `landCoverCategory`). Separately, technology adoption for PV / battery / EV-charger / heat-pump assets in the synthetic generator is now modulated by the host building's `deprivationIndex` (→02, Pobal-derived): richer Electoral Divisions receive proportionally higher adoption, deprived EDs lower, with per-technology gradients. The weighted national adoption mean must still reconcile to the SEAI/SIMI anchor (validation rule below) — the gradient redistributes, it does not inflate.

---

## 1. Definition

The Asset Catalogue defines every asset property required by any model in the EIG MLM framework: technical specifications, operational characteristics, maintenance characteristics, energy characteristics and flexibility characteristics. It is the instance register — each record is one physical asset (or asset bank treated as a unit) installed at a building, site, community or network location, classified by the Asset Classification Catalogue (10).

## 2. Scope

All instances of subtypes in EIG-AC across all sectors. A missing field here is a missing model input: every field exists because at least one model family (thermal, electrical, forecast, RD, comfort, IAQ, lifecycle, market) consumes it.

## 3. Classification Structure

Instances classify by: (a) classificationNodeID → EIG-AC subtype (primary); (b) ownership class (owner_occupier, landlord, esco, dso_tso, community, third_party_paas); (c) operational role (primary, backup, redundant_n1, seasonal, decommission_pending); (d) data richness tier (T0 nameplate-only, T1 +commissioning data, T2 +telemetry, T3 +validated digital twin).

## 4. Hierarchy

```
Asset (EIG-AST-NNNNNN)
 ├── locatedAt → EIG-BLD building / EIG-SPC space / EIG-SIT site / EIG-NET segment (exactly one host)
 ├── classifiedAs → EIG-AC subtype leaf
 ├── partOfSystem → AssetSystem (EIG-AST-SYS-NNNN, e.g. "heating system 1") 
 └── componentAssets → child assets (e.g. HP outdoor unit + cylinder + buffer as components of system)
```

AssetSystem is a lightweight grouping record (systemID, systemName, systemFunction enum {heating, cooling, dhw, ventilation, pv_storage, ev, lighting_zone, process_line}, memberAssetIDs, leadAssetID).

## 5. Field Groups & Fields

### 5.1 Identification & classification

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | assetID | string | — | EIG-AST-NNNNNN | M | Unique asset ID |
| 2 | assetName | string | — | UTF-8 | M | Human label |
| 3 | classificationNodeID | string | — | EIG-AC subtype leaf | M | Classification binding |
| 4 | functions | array | — | FN-* (subtype defaults ± deltas) | M | Effective functions |
| 5 | flexibilityClassEffective | enum | — | as EIG-AC §5#7 | M | Effective flex class after instance assessment |
| 6 | manufacturer / modelNumber / serialNumber | string | — | — | M/M/O | Identity triple |
| 7 | hostType + hostID | enum+string | — | building/space/site/network + valid ID | M | Exactly one host |
| 8 | systemID | string | — | EIG-AST-SYS-* | C | Parent system |
| 9 | ownershipClass / ownerOrgID | enum/string | — | §3(b) / EIG-ORG | M | Ownership |
| 10 | operationalRole | enum | — | §3(c) | M | Role |
| 11 | dataRichnessTier | enum | — | T0–T3 | M | Drives model eligibility |
| 12 | installDate / commissionDate / decommissionDate | date | — | ISO 8601 | M/C/O | Lifecycle dates |
| 13 | status | enum | — | planned, installed, commissioned, operational, faulted, dormant, decommissioned | M | Lifecycle status |

### 5.2 Technical specifications (model-dependent core; nameplate)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 14 | ratedCapacity | decimal | per-vector unit | within subtype plausibility band | M | Primary rating (kW_th, kW_e, kWh, m³/h, lm as appropriate) |
| 15 | ratedCapacityVector | string | — | EIG-ECR ID | M | Vector of #14 |
| 16 | secondaryRatings | object | — | {key: {value, unit}} e.g. kW_e for CHP | C | Additional ratings |
| 17 | ratedEfficiency | object | — | metric-keyed: {scop, seer, eta_seasonal, round_trip_pct, cop_a7w35, eer, lm_per_w} | M | Subtype-appropriate efficiency set |
| 18 | modulationRange | object | % | {minPct, maxPct} of rated | C (modulating) | Turndown envelope |
| 19 | rampRate | decimal | %/min or kW/min | > 0 | C (modulating/bidirectional) | Max rate of change |
| 20 | startupTimeS / shutdownTimeS | integer | s | ≥ 0 | C | Transition times |
| 21 | minOnTimeS / minOffTimeS | integer | s | ≥ 0 | C (cycling-limited) | Cycling constraints |
| 22 | storageCapacity | decimal | kWh / L / kg_H2 | > 0 | C (FN-STORE) | Usable stored energy |
| 23 | maxChargeRate / maxDischargeRate | decimal | kW | > 0 | C (FN-STORE) | C-rate envelope |
| 24 | standingLossRate | decimal | %/day or W | ≥ 0 | C (stores) | Self-discharge / standing loss |
| 25 | voltagePhase | enum | — | 1p_230, 3p_400, dc, mv | C (electrical) | Connection |
| 26 | refrigerant / gwp | string/int | — / — | F-gas code / GWP value | C (HP, chiller) | F-gas compliance inputs |
| 27 | soundPowerDb | decimal | dB(A) | 0–120 | C (HP outdoor, fans) | Acoustic constraint input |
| 28 | physicalDims / weightKg | object/decimal | m / kg | — | O | Spatial planning |
| 29 | ipRating | string | — | IPNN | O | Environment rating |
| 30 | pvShadingFactor | decimal | ratio | 0.90–1.00 (1.0 = no shading) | C (PV) | **(v1.1)** Horizon/urban shading correction for PV generation assets, derived from the host building's land-cover settlement density (→02 `landCoverCategory`). Applied multiplicatively to pvlib AC output in the synthetic generator. 0.97 SYNTHETIC fallback when land cover absent. |

### 5.3 Energy characteristics (operational energy behaviour)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 30 | annualEnergyConsumptionKWh | decimal | kWh/yr | ≥ 0 | C (T1+) | Measured or estimated; basis flagged |
| 31 | annualEnergyGenerationKWh | decimal | kWh/yr | ≥ 0 | C (FN-PRODUCE) | Yield |
| 32 | baseloadW | decimal | W | ≥ 0 | C | Standby draw |
| 33 | loadProfileRef | string | — | EIG-DSP dataset ID or EIG-OCC-linked profile | C (T2+) | Characteristic profile binding |
| 34 | meteringPointID | string | — | EIG-AST sub_meter ID or MPRN-level | C | Where this asset is measured (self-ref to MTR instance) |
| 35 | meterCoverage | enum | — | dedicated, shared_circuit, unmetered_estimated | M | Telemetry attribution quality |
| 36 | weatherSensitivityClass | enum | — | none, temperature, irradiance, wind, multi | M | Which WX conditioning applies |
| 37 | performanceCurveRefs | array | — | curve IDs: {cop_vs_oat_flow, eff_vs_load, soc_vs_voltage, fan_curve} | C (T2+) | Part-load/condition curves (stored as datasets) |

### 5.4 Operational characteristics

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 38 | controlInterface | enum | — | none, relay_on_off, modbus_rtu, modbus_tcp, bacnet_ip, knx, mbus, api_cloud, ocpp, sg_ready, eebus, proprietary_gateway | M | How it can be controlled |
| 39 | controlAuthorityGranted | enum | — | monitor_only, advisory, supervised_control, autonomous_within_envelope | M | Per-asset authority (≤ building's controlAuthorityLevel) |
| 40 | controllerAssetID | string | — | EIG-AST CTL instance | C | Controlling device |
| 41 | integrationAccessories | array | — | free text + part codes | O | e.g. NIBE MODBUS 40, Madoka/BACnet gateway (ODEON pattern) |
| 42 | operatingSchedule | string | — | EIG-SCH ID | C | Bound schedule (forward ref → 24) |
| 43 | setpointEnvelope | object | unit | {min, max, default, unit} | C (controllable) | Allowed setpoint band |
| 44 | criticalLoadFlag | boolean | — | true/false | M | Mirrors FN-CRITICAL; excluded from shed |
| 45 | redundancyGroup | string | — | group key | C (N+1 sets) | Mutual backup grouping |
| 46 | faultStateBehaviour | enum | — | fail_safe_off, fail_safe_on, fail_last_state | C (actuating) | Safety behaviour |

### 5.5 Maintenance characteristics

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 47 | maintenanceRegime | enum | — | inherits EIG-AC default; overridable | M | Effective regime |
| 48 | lastServiceDate / nextServiceDue | date | — | ISO 8601 | C (regime ≠ none) | Service cycle |
| 49 | serviceProviderOrgID | string | — | EIG-ORG | O | Maintainer |
| 50 | warrantyEndDate | date | — | ISO 8601 | O | Warranty |
| 51 | mtbfHours / mttrHours | decimal | h | > 0 | C (T2+, reliability-modelled) | Reliability parameters (observed or subtype prior) |
| 52 | conditionScore | decimal | — | 0–100 | C (condition_based) | Latest condition assessment |
| 53 | faultHistoryRef | string | — | EIG-EVT query ref | C | Fault event linkage (forward ref → 63) |
| 54 | cyclesToDate | integer | count | ≥ 0 | C (cycle-limited: batteries, compressors) | Wear counter |
| 55 | designCycles | integer | count | > 0 | C with #54 | Cycle life |
| 56 | degradationModelRef | string | — | EIG-MOD ID | C (T3) | Bound degradation model |

### 5.6 Flexibility characteristics (RD-critical block)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 57 | flexCapacityUpKW / flexCapacityDownKW | decimal | kW | ≥ 0 | M (FN-FLEX) | Max deliverable response each direction |
| 58 | flexDurationMaxMin | integer | min | > 0 | M (FN-FLEX) | Sustainable response duration |
| 59 | flexNoticeMinS | integer | s | ≥ 0 | M (FN-FLEX) | Minimum activation notice |
| 60 | flexRecoveryMin | integer | min | ≥ 0 | M (FN-FLEX) | Recovery before re-activation |
| 61 | reboundFactorPct | decimal | % | 0–300 | M (FN-FLEX) | Expected post-event rebound vs delivered |
| 62 | comfortCouplingClass | enum | — | none, weak, strong | M (FN-FLEX) | Whether response degrades comfort (gates RD by occupancy) |
| 63 | flexAvailabilityScheduleRef | string | — | EIG-SCH ID | C | When flex is offerable |
| 64 | responseDerivativeIDs | array | — | EIG-RD IDs | C (T2+) | Computed RDs attached to this asset |
| 65 | flexibilityServiceEnrolments | array | — | EIG-FS IDs (e.g. EIG-FS-DR-01/02) | C | Enrolled services |
| 66 | aggregationGroupID | string | — | EIG-FLX group | C | VPP/aggregation membership |

### 5.7 Commercial & lifecycle economics

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 67 | capexEUR / installCostEUR | decimal | EUR | ≥ 0 | O | Cost decomposition (PSR-aligned) |
| 68 | grantRefs | array | — | SEAI grant codes | O | Subsidies applied |
| 69 | opexAnnualEUR | decimal | EUR/yr | ≥ 0 | O | Operating cost |
| 70 | residualValueEUR | decimal | EUR | ≥ 0 | O | End-of-life value |
| 71 | embodiedCarbonKgCO2e | decimal | kgCO2e | ≥ 0 | O | A1–A3 embodied carbon |

### 5.8 Audit

| 72 | version / owner / createdDate / modifiedDate / source / dataQualityScore | standard audit block + DQ score (0–100, from EIG-DQ rules) | M |

## 6. Controlled Vocabularies

ownershipClass, operationalRole, dataRichnessTier, status, controlInterface, controlAuthorityGranted, weatherSensitivityClass, meterCoverage, comfortCouplingClass, faultStateBehaviour — all closed per §5. Efficiency metric keys closed: scop, seer, eta_seasonal, round_trip_pct, cop_a7w35, cop_a2w35, eer, lm_per_w, spf_measured.

## 7. Applicability Rules

1. Field applicability is driven by classification: conditional blocks activate per subtype functions (FN-STORE → §5.2#22–24; FN-FLEX → §5.6 entire block mandatory).
2. controlAuthorityGranted ≤ host building's controlAuthorityLevel; an asset can never exceed its building's authority ceiling.
3. dataRichnessTier gates model eligibility: T0 assets serve only static/archetype models; T2+ required for RD computation; T3 required for autonomous control envelope participation.
4. criticalLoadFlag=true ⇒ §5.6 block must be absent or all-zero; asset excluded from action spaces.
5. Assets with meterCoverage=unmetered_estimated cannot contribute to settlement-grade flexibility services (29) — advisory services only.

## 8. Validation Rules

| # | Rule |
|---|---|
| V1 | assetID unique; classificationNodeID is an active subtype leaf |
| V2 | ratedCapacity within subtype typicalCapacityRange × [0.5, 2.0] (warn) / × [0.2, 5.0] (hard) |
| V3 | functions ⊆ subtype defaults ∪ approved deltas; FN-CRITICAL ∧ FN-FLEX rejected |
| V4 | Host resolves; space host must belong to building host chain; exactly one host |
| V5 | commissionDate ≥ installDate; operational ⇒ commissionDate present |
| V6 | FN-FLEX ⇒ flexCapacityUp+Down > 0, duration, notice, rebound, comfortCoupling all present |
| V7 | maxChargeRate/DischargeRate ≤ ratedCapacity (electrical stores) |
| V8 | modulationRange.minPct < maxPct ≤ 100 |
| V9 | setpointEnvelope.min < default < max |
| V10 | flexibilityServiceEnrolments require controlInterface ≠ none and controlAuthorityGranted ≥ supervised_control |
| V11 | conditionScore present if maintenanceRegime=condition_based and tier ≥ T1 |
| V12 | cyclesToDate ≤ designCycles × 1.5 (else force status review) |
| V13 | Efficiency metric keys appropriate to subtype (scop only for HPs, round_trip only for stores, …) |
| V14 | dataQualityScore ≥ 60 required for tier T2 designation |
| V15 | **(v1.1)** `pvShadingFactor` (PV assets) ∈ [0.90, 1.00]; absent host `landCoverCategory` ⇒ 0.97 SYNTHETIC fallback, flagged not relabelled |
| V16 | **(v1.1, G-ADOPTION-RECON)** After deprivation-gradient adjustment, the expansion-weighted national adoption rate for each of {pv, battery, ev_charger, heat_pump} must remain within ±5% of its SEAI/SIMI national anchor — the socioeconomic gradient redistributes adoption across EDs without changing the national total |

## 9. Relationships to Other Catalogues

| Direction | Catalogue | Relationship |
|---|---|---|
| ▶ depends on | asset_classification (10) | classificationNodeID |
| ▶ depends on | building (02), space (04), site (88), utility_network (13) | host |
| ▶ depends on | organisation (15) | owner, service provider |
| ▶ depends on | schedule (24), flexibility_service (29), energy_carrier (19) | forward bindings |
| ◀ consumed by | asset_benchmark (12) | benchmark positions per instance |
| ◀ consumed by | device (36) | MTR/CTL instances detailed as devices |
| ◀ consumed by | data (38), feature (41) | telemetry attribution via meteringPointID |
| ◀ consumed by | model (44), forecast (46) | instance parameters as model inputs |
| ◀ consumed by | response_derivative (52) | §5.6 envelope is RD prior + constraint |
| ◀ consumed by | action (61), control_strategy (62) | actionable asset register |
| ◀ consumed by | digital_twin (32) | one twin per T2+ asset (policy) |

## 10. Benchmarking Requirements

Every operational asset with benchmarkSchemeRef on its subtype receives benchmark positions (12) when cohort n ≥ 10 within (subtype × capacity band × climate zone). In-situ efficiency (e.g. SPF measured) benchmarks take precedence over nameplate; both stored, basis flagged.

## 11. Dataset Requirements

Asset master dataset: one row per asset, all §5 fields, snapshot-versioned monthly. Telemetry datasets join on assetID via meteringPointID; attribution quality (meterCoverage) propagates into dataset metadata. Performance curves stored as small datasets (EIG-DSP) with axes declared.

## 12. Feature Requirements

FEA-AST-*: capacity-normalised consumption (kWh/kW·yr), load factor, utilisation hours, efficiency-vs-nameplate ratio, age fraction, cycles fraction, condition score, flex availability ratio (offered hours ÷ total), rebound factor, response notice class, control interface capability flags.

## 13. Model Requirements

Models declare required fields by §5 group; the registry validates instance completeness before inference (tier gating §7.3). Degradation models bind via degradationModelRef; RD models consume §5.6 as priors and update responseDerivativeIDs. Thermal models require performanceCurveRefs for T2+ accuracy claims.

## 14. KPI Requirements

Per-asset KPIs: SPF/SCOP achieved, availability %, MTBF observed, fault rate, flex delivery success rate, rebound observed vs declared, energy cost per output unit. Roll-ups follow classification hierarchy (§ EIG-AC 14).

## 15. Response Derivative Requirements

§5.6 is the RD contract: declared envelope (capacity, duration, notice, recovery, rebound) is the prior; observed RDs (52) measure actual conditional responsiveness and are reconciled against the declaration. Persistent gap declared-vs-observed > 25% triggers re-declaration workflow. comfortCouplingClass=strong RDs must be occupancy-conditioned (catalogue 03 flexibilityEngagementClass interaction).

## 16. Decision Requirements

The decision layer treats this catalogue as the actuator registry: action feasibility checks read controlInterface, authority, setpointEnvelope, cycling constraints, criticalLoadFlag, redundancyGroup (never shed both members), faultStateBehaviour. Every executed action logs assetID + pre/post state.

## 17. Ontology Mapping

Classes: Asset, AssetSystem, FlexibilityEnvelope, MaintenanceRecordSet, PerformanceCurve.
Object properties: classifiedAs (→ AC subtype), locatedAt, partOfSystem, controlledBy, metersAsset/meteredBy, enrolledIn (→ FS), hasResponseDerivative, ownedBy, maintainedBy.
Datatype properties: assetID, ratedCapacity, scop, flexCapacityUpKW, flexNoticeMinS, reboundFactorPct, criticalLoadFlag, dataRichnessTier.
Alignment: SAREF saref:Device + saref:Profile (flex envelope ≈ s4ener:PowerProfile), Brick equipment instances, IFC IfcDistributionElement occurrences.

## 18. Knowledge Graph Mapping

Nodes: Asset, AssetSystem. Relationships: INSTANCE_OF (→ subtype), LOCATED_AT, PART_OF, CONTROLLED_BY, METERED_BY, ENROLLED_IN, HAS_RD, OWNED_BY, BACKS_UP (redundancy), FEEDS_MODEL. Keys: assetID. Telemetry stays in lakehouse; KG holds identity, topology and capability.

## 19. Digital Twin Mapping

T2+ assets get twins from subtype templates (33), specialised with: nameplate block, performance curves, flex envelope, control interface binding, degradation state. Twin state sync cadence per asset class (battery: 1 min; boiler: 15 min). T3 = twin validated against telemetry (validation record in 47).

## 20. Governance

Owner: EIG Data Management Lead; flexibility block co-owned by RD Owner (changes to §5.6 semantics require RD sign-off). Instance data stewardship per ownerOrgID; EIG validates on ingest. Decommissioned assets retained (status transition), never deleted — historical model lineage depends on them.

## 21. Versioning

Record-level semver; monthly master snapshots; envelope re-declarations are minor record versions with effective-date ranges so historic RD computations remain reproducible against the envelope in force at the time.

## 22. Example Records

```text
assetID: EIG-AST-004211 | name: ASHP — IE_B0009 | classificationNodeID: EIG-AC-HVC-01-01 (ashp_air_water)
manufacturer: NIBE | model: F2040-12 | host: building EIG-BLD-000872 (IE_B0009, Inis Mór)
ratedCapacity: 12 kW_th | ratedEfficiency: {scop: 4.6, cop_a7w35: 4.8} | modulationRange: {25, 100}
controlInterface: modbus_rtu | integrationAccessories: [NIBE MODBUS 40] | controlAuthorityGranted: supervised_control
flex: {up: 0, down: 3.5 kW, durationMax: 90 min, notice: 300 s, recovery: 60 min, rebound: 140%, comfortCoupling: strong}
weatherSensitivityClass: temperature | dataRichnessTier: T2 | enrolments: [EIG-FS-DR-01]
maintenanceRegime: annual | status: operational | dataQualityScore: 87
```

```text
assetID: EIG-AST-005102 | name: Battery — community hall | classificationNodeID: EIG-AC-STO-01-01 (li_ion_lfp)
ratedCapacity: 5 kW | storageCapacity: 13.5 kWh | maxCharge/Discharge: 5/5 kW | roundTrip: 91%
standingLossRate: 0.8 %/day | cyclesToDate: 612 | designCycles: 6000 | controlInterface: api_cloud
flex: {up: 5, down: 5 kW, durationMax: 160 min, notice: 10 s, recovery: 0, rebound: 105%, comfortCoupling: none}
controlAuthorityGranted: autonomous_within_envelope | tier: T3 | twin: active | status: operational
```

## 23. Completion Criteria

Complete when: every physical asset in scope registered with tier ≥ T0; all FN-FLEX assets carry complete §5.6 envelopes; all controllable assets carry interface + authority + envelope; capacity plausibility validated against subtype bands; meter attribution declared for 100% of operational assets; RD reconciliation loop active for all T2+ flex assets; zero validation failures outstanding.
