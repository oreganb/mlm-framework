---
type: catalogue
ontology: EIG-MLM
catalogue_no: 2
catalogue_id: EIG-CAT-002
entity: building
band: physical
status: active
tags: [eig-mlm, catalogue, band:physical, entity:building]
aliases: ["EIG-CAT-002", "Catalogue 02", "building"]
---

# CAT-02 — Building

> **EIG-CAT-002** · band: **Physical & Spatial Stack** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Building Catalogue

## 1. Definition

The Building Catalogue defines every property of an individual building instance required by any current or foreseeable EIG MLM use case: identification, location, ownership, physical, energy, operational, regulatory and smart-readiness characteristics. It is the master record from which building digital twins, datasets, features, models, KPIs, benchmarks and decision contexts are instantiated.

> **v1.1 (2026-06-13) — geospatial & socioeconomic enrichment.** §5.2 gains six fields (`edID`, `deprivationIndex`, `osmLanduseClass`, `urbanAtlasCode`, `urbanAtlasLabel`, `landCoverCategory`) carrying real-data linkage from the synthetic-data generator's enrichment layer (CSO 2022 small-area centroids, Pobal HP Deprivation Index 2022, OpenStreetMap landuse, EEA Urban Atlas 2018, Tailte Éireann National Land Cover Map). All are CALIBRATED with documented SYNTHETIC fallbacks. The deprivation linkage (`edID → deprivationIndex`) drives socioeconomic variation in technology adoption (catalogue 11), and `landCoverCategory` feeds the PV horizon-shading correction (catalogue 11 `pvShadingFactor`).

## 2. Scope

Applies to: building registration and onboarding, digital twin instantiation, EPC/BER/regulatory reporting, retrofit analysis, simulation, forecasting, optimisation, flexibility assessment, market participation eligibility, benchmark cohort assignment, portfolio aggregation, synthetic building generation.

Out of scope: classification taxonomy (01), spaces within the building (04), fabric component specifications (05), assets (11), occupancy profiles (03) — each referenced, not redefined.

## 3. Classification Structure

A building record is classified by its Building Classification chain (Catalogue 01) and additionally by: tenure class, heritage class, construction era band, and lifecycle stage (all enumerated in §6).

## 4. Hierarchy

```text
Portfolio (EIG-BLD-PRT)
 └─ Site (EIG-SIT, Catalogue 88)
     └─ Building (EIG-BLD)
         └─ Building Component (mixed-use share) (EIG-BLD-CMP)
             └─ Spaces (EIG-SPC, Catalogue 04)
```

## 5. Field Groups & Fields

### 5.1 Identification

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | buildingID | string | — | `EIG-BLD-[0-9]{6}` | Y | Unique internal ID |
| 2 | buildingName | string | — | ≤200 chars | Y | Common name |
| 3 | externalIDs | array[object] | — | {scheme: enum(UPRN, Eircode_building, MPRN_set, GPRN_set, cadastral, client_ref, OSi_ID), value} | N | Cross-system identifiers |
| 4 | classificationID | string | — | EIG-BC Level-3+ node | Y | Classification anchor |
| 5 | siteID | string | — | EIG-SIT ID or null | N | Parent site/campus |
| 6 | portfolioID | string | — | EIG-BLD-PRT ID or null | N | Portfolio membership |
| 7 | twinID | string | — | EIG-DT ID or null | N | Linked digital twin |
| 8 | lifecycleStage | enum | — | planned, design, construction, commissioning, operational, refurbishment, decommissioned, demolished | Y | Current stage |
| 9 | onboardingDate | date | — | ISO 8601 | Y | Date entered MLM management |
| 10 | recordStatus | enum | — | draft, active, archived | Y | Record lifecycle |

### 5.2 Location

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | addressLine1/2/3 | string | — | ≤120 chars each | Y(1) | Postal address |
| 2 | postalCode | string | — | Eircode `^[A-Z][0-9W][0-9A-Z]{5}$` or national format | Y | Postal/Eircode |
| 3 | geographicID | string | — | EIG-GEO ID | Y | Link to Geographic Catalogue (08) |
| 4 | latitude | decimal | °(WGS84) | −90…90, 6 dp | Y | Centroid latitude |
| 5 | longitude | decimal | °(WGS84) | −180…180, 6 dp | Y | Centroid longitude |
| 6 | elevation | decimal | m | −430…9000 | N | Above sea level |
| 7 | climateZoneID | string | — | EIG-WX zone ID | Y | Climate zone (09) |
| 8 | weatherStationID | string | — | EIG-WX station ID | Y | Assigned weather station |
| 9 | footprintGeometry | geojson | — | Polygon/MultiPolygon, WGS84 | N | Building footprint |
| 10 | orientationPrincipal | decimal | ° from N | 0…359.9 | N | Principal façade azimuth |
| 11 | shadingContext | enum | — | open, partially_obstructed, dense_urban, self_shaded | N | Solar context |
| 12 | floodRiskClass | enum | — | none, low, medium, high | N | OPW/flood map class |
| 13 | urbanRuralClass | enum | — | urban, suburban, town, rural | Y | Settlement class |
| 14 | edID | ref | — | CSO Electoral Division ID (→08) | Cond | **(v1.1)** Electoral Division carried from the small-area centroid register; enables ED-level socioeconomic linkage (Pobal). CALIBRATED where SA centroid resolved, else null. |
| 15 | deprivationIndex | decimal | index 0–1 | 0.0…1.0 (0=most deprived, 1=most affluent) | Cond | **(v1.1)** Normalised Pobal HP Deprivation Index for the building's ED (min-max normalised). Drives socioeconomic variation in technology adoption (11). CALIBRATED via ED lookup, else 0.5 (SYNTHETIC fallback). |
| 16 | osmLanduseClass | enum | — | residential, commercial, industrial, retail, office, public, unknown | N | **(v1.1)** OpenStreetMap land-use class at the building location (nearest-polygon). Independent cross-check on sector classification (01/02). |
| 17 | urbanAtlasCode | enum | — | EEA Urban Atlas 2018 5-digit class code, or unknown | N | **(v1.1)** EEA Urban Atlas 2018 land-use class at the location (Cork/Dublin/Galway/Limerick/Waterford footprints). |
| 18 | urbanAtlasLabel | string | — | UA2018 class name, or unknown | N | **(v1.1)** Human-readable label for urbanAtlasCode. |
| 19 | landCoverCategory | enum | — | urban_dense, urban_medium, suburban, rural, unknown | N | **(v1.1)** Tailte Éireann National Land Cover Map settlement-density class at the location; feeds the PV horizon-shading correction (11 pvShadingFactor). |

### 5.3 Ownership & Tenure

| # | Field | Type | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|
| 1 | ownerOrganisationID | string | EIG-ORG ID | Y | Legal owner |
| 2 | operatorOrganisationID | string | EIG-ORG ID | N | Operator if different |
| 3 | occupierOrganisationIDs | array[string] | EIG-ORG IDs | N | Occupying organisations |
| 4 | tenureClass | enum | owner_occupied, leased_single, leased_multi, licensed, public_owned, social_housing, mixed | Y | Tenure structure |
| 5 | leaseExpiry | date | ISO 8601 or null | Cond | Required if leased |
| 6 | splitIncentivePresent | boolean | true/false | Y | Owner/tenant energy incentive split flag |
| 7 | facilitiesManagerActorID | string | EIG-ACT ID | N | Responsible FM |
| 8 | energyManagerActorID | string | EIG-ACT ID | N | Responsible energy manager |
| 9 | decisionAuthorityActorID | string | EIG-ACT ID | Y | Who can approve control actions |

### 5.4 Physical Characteristics

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | grossFloorArea | decimal | m² | >0 | Y | GFA (gross internal) |
| 2 | netLettableArea | decimal | m² | ≤ GFA | N | NLA |
| 3 | treatedFloorArea | decimal | m² | ≤ GFA | Y | Conditioned area (EPC basis) |
| 4 | footprintArea | decimal | m² | >0 | N | Ground footprint |
| 5 | storeysAbove / storeysBelow | integer | — | ≥0 | Y | Storey counts |
| 6 | buildingHeight | decimal | m | >0 | N | To roof level |
| 7 | grossVolume | decimal | m³ | >0 | N | Heated volume preferred |
| 8 | constructionYear | integer | yr | 1000…current+10 | Y | Original construction |
| 9 | constructionEraBand | enum | — | pre1900, 1900_1945, 1946_1970, 1971_1990, 1991_2005, 2006_2010, 2011_2019, 2020_plus | Y | Era band (derivable) |
| 10 | majorRetrofitYears | array[integer] | yr | each ≤ current | N | Deep retrofit history |
| 11 | structuralSystem | enum | — | masonry, concrete_frame, steel_frame, timber_frame, hybrid, modular_prefab | N | Primary structure |
| 12 | thermalMassClass | enum | — | very_light, light, medium, heavy, very_heavy | N | Per ISO 13790 classes |
| 13 | airtightnessTestResult | decimal | m³/h/m² @50Pa | >0 | N | Measured q50 |
| 14 | externalEnvelopeArea | decimal | m² | >0 | N | Total envelope |
| 15 | glazingRatio | decimal | % | 0…95 | N | Glazed % of façade |
| 16 | heritageClass | enum | — | none, protected_structure, conservation_area, NIAH_listed | Y | Heritage constraint |
| 17 | fabricSummaryRef | string | — | EIG-FAB assembly set ID | N | Link to Fabric Catalogue (05) |
| 18 | mixedUseComponents | array[object] | — | {classificationID, floorAreaShare%}; shares sum=100 | Cond | Required if SEC-12 |

### 5.5 Energy Characteristics

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | electricityMPRNs | array[string] | — | 11-digit MPRN | Y | Metering point(s) |
| 2 | gasGPRNs | array[string] | — | 7-digit GPRN | N | Gas point(s) |
| 3 | heatNetworkConnection | boolean | — | true/false | Y | DH/DC connection flag |
| 4 | heatNetworkID | string | — | EIG-NET ID | Cond | Required if connected |
| 5 | primaryHeatingFuel | enum | — | electricity, natural_gas, oil, LPG, biomass, district_heat, solid_fuel, hydrogen, none | Y | Main space-heating carrier |
| 6 | secondaryHeatingFuels | array[enum] | — | as above | N | Secondary carriers |
| 7 | primaryHeatingSystem | enum | — | heat_pump_air, heat_pump_ground, heat_pump_water, gas_boiler, oil_boiler, biomass_boiler, electric_resistance, district_heat, CHP, hybrid | Y | Main system family |
| 8 | coolingPresent | boolean | — | true/false | Y | Active cooling flag |
| 9 | coolingSystem | enum | — | dx_split, vrf, chiller_air, chiller_water, absorption, district_cooling, none | Cond | If coolingPresent |
| 10 | ventilationStrategy | enum | — | natural, mechanical_extract, balanced_mvhr, mixed_mode | Y | Ventilation family |
| 11 | onSiteGenerationAssets | array[string] | — | EIG-AST IDs | N | PV, wind, CHP etc. |
| 12 | storageAssets | array[string] | — | EIG-AST IDs | N | Battery/thermal stores |
| 13 | evChargingPoints | integer | — | ≥0 | N | EVSE count |
| 14 | annualElectricityUse | decimal | kWh/yr | ≥0 | N | Latest verified year |
| 15 | annualThermalUse | decimal | kWh/yr | ≥0 | N | Heat (all carriers) |
| 16 | energyUseIntensity | decimal | kWh/m²/yr | ≥0 | N | Total / treated area |
| 17 | peakElectricalDemand | decimal | kW | ≥0 | N | Recorded maximum |
| 18 | connectionCapacityImport | decimal | kVA | >0 | Y | MIC |
| 19 | connectionCapacityExport | decimal | kVA | ≥0 | N | MEC |
| 20 | subMeteringLevel | enum | — | none, main_only, distribution_board, circuit, asset | Y | Metering granularity |
| 21 | dataResolutionElectrical | enum | — | annual, monthly, daily, 30min, 15min, 1min, sub_minute | Y | Best available interval |
| 22 | tariffIDs | array[string] | — | EIG-TRF IDs | Y | Active tariffs |
| 23 | flexibilityRegisteredServices | array[string] | — | EIG-FS IDs | N | Enrolled services |

### 5.6 Operational Characteristics

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | occupancyProfileID | string | — | EIG-OCC ID | Y | Assigned profile (03) |
| 2 | designOccupancy | integer | persons | ≥0 | N | Design capacity |
| 3 | typicalOccupancy | integer | persons | ≥0 | N | Typical weekday peak |
| 4 | operatingScheduleID | string | — | EIG-SCH ID | Y | Operating hours schedule |
| 5 | bmsPresent | boolean | — | true/false | Y | BMS flag |
| 6 | bmsSystem | string | — | vendor/product, ≤80 chars | Cond | If bmsPresent |
| 7 | bmsProtocols | array[enum] | — | BACnet, Modbus, KNX, LonWorks, OPC_UA, MQTT, proprietary | Cond | If bmsPresent |
| 8 | controlAuthorityLevel | enum | — | monitor_only, advisory, supervised_control, autonomous_within_envelope | Y | MLM control permission |
| 9 | setpointHeating / setpointCooling | decimal | °C | 5…35 | N | Default setpoints |
| 10 | comfortBandLower/Upper | decimal | °C | 5…35; upper>lower | Y | Comfort constraint band |
| 11 | criticalLoadRegister | array[string] | — | EIG-AST IDs | Y | Never-curtail assets |
| 12 | maintenanceRegime | enum | — | reactive, planned, condition_based, predictive | N | Maintenance approach |
| 13 | iso50001Certified | boolean | — | true/false | N | EnMS certification |

### 5.7 Regulatory Characteristics

| # | Field | Type | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|
| 1 | berRating | enum | A1…G or null | Cond | Required for residential |
| 2 | berNumber | string | 9-digit | Cond | With berRating |
| 3 | berDate / berExpiry | date | ISO 8601 | Cond | With berRating |
| 4 | epcRating | string | national scale | N | Non-domestic EPC |
| 5 | decRating | string | A…G | N | Display Energy Certificate |
| 6 | nzebCompliant | boolean | true/false | N | NZEB status |
| 7 | epbdRenovationObligation | enum | none, 2030_trigger, 2033_trigger, exempted | N | EPBD recast trajectory |
| 8 | publicSectorObligated | boolean | true/false | Y | SI 426/Public Sector Mandate |
| 9 | sustainabilityCertifications | array[object] | {scheme: enum(LEED,BREEAM,WELL,HPI,DGNB), level, expiry} | N | Voluntary schemes |
| 10 | gdprSensitivityClass | enum | none, occupancy_inferable, individual_inferable | Y | Data protection class for building data |

### 5.8 Smart Readiness Characteristics

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | sriScore | decimal | % | 0…100 | N | Smart Readiness Indicator total |
| 2 | sriAssessmentDate | date | — | ISO 8601 | Cond | With sriScore |
| 3 | sriDomainScores | object | % | 9 SRI domains (heating, cooling, DHW, ventilation, lighting, dynamic_envelope, electricity, EV, monitoring_control) each 0–100 | N | Domain breakdown |
| 4 | sriImpactScores | object | % | 7 SRI impact criteria each 0–100 | N | Impact breakdown |
| 5 | connectivityClass | enum | — | none, intermittent, broadband, fibre, redundant | Y | Site connectivity |
| 6 | edgeNodeIDs | array[string] | — | EIG-EDG IDs | N | Deployed edge infrastructure |
| 7 | iotDeviceCount | integer | — | ≥0 | N | Registered devices (36) |
| 8 | interoperabilityClass | enum | — | closed, partially_open, open_standards | Y | Integration openness |
| 9 | symbioticReadinessLevel | enum | — | isolated, smart, symbiotic_ready, symbiotic, autonomous | Y | EIG maturity ladder |

## 6. Controlled Vocabularies

All enums in §5 are controlled vocabularies governed by the Vocabulary Catalogue (82). Era bands, tenure classes, heritage classes, lifecycle stages, control authority levels and the symbiotic readiness ladder may only be extended via governance (§20).

## 7. Applicability Rules

- berRating fields apply only where Classification berApplicable=true.
- mixedUseComponents required iff sector = SEC-12.
- coolingSystem required iff coolingPresent=true; heatNetworkID iff heatNetworkConnection=true.
- controlAuthorityLevel > monitor_only requires decisionAuthorityActorID and a signed contract record (EIG-CON) granting control rights.
- Flexibility service enrolment requires: subMeteringLevel ≥ distribution_board, dataResolutionElectrical ≤ 30min, and criticalLoadRegister populated.

## 8. Validation Rules

- buildingID unique; classificationID resolves to active Level-3+ node.
- treatedFloorArea ≤ grossFloorArea; netLettableArea ≤ grossFloorArea.
- constructionEraBand consistent with constructionYear.
- latitude/longitude fall within the geographicID boundary (08).
- EUI, where computed, must lie within 0.2×–5× classification typical bounds or carry a reviewed plausibility waiver.
- Every MPRN unique across the catalogue; MPRN checksum valid.
- comfortBandUpper − comfortBandLower ≥ 1.0 °C.
- mixedUseComponents shares sum to 100 ± 0.1%.
- Records with lifecycleStage=operational must have: occupancyProfileID, operatingScheduleID, tariffIDs, weatherStationID.
- **(v1.1)** `deprivationIndex` ∈ [0,1]; where `edID` resolves to a deprivation register entry it is CALIBRATED, otherwise it defaults to 0.5 and is flagged SYNTHETIC (no silent substitution).
- **(v1.1)** `edID`, where present, must resolve to a valid CSO Electoral Division under the building's `geographicID` (08); a mismatch between edID county and geographicID county is a placement defect.
- **(v1.1)** When `latitude`/`longitude` are placed on a CSO 2022 small-area centroid they are CALIBRATED (recorded via the generator's `lat_lon_data_class`); county-bounding-box placement remains SYNTHETIC. dataClass is never upgraded by relabelling.
- **(v1.1)** `pvShadingFactor` (catalogue 11) must lie in [0.90, 1.00]; `landCoverCategory=unknown` ⇒ the 0.97 SYNTHETIC fallback is used downstream.

## 9. Relationships to Other Catalogues

Upstream: Building Classification (01), Geographic (08), Weather/Climate (09). Lateral: Organisation (15), Actor (14), Contract (23), Tariff (21), Schedule (24). Downstream: Space (04), Fabric (05), Asset (11), Occupancy (03), Data Source (37), Dataset (43), Model (44), KPI (57), RD (52), Flexibility (30), Digital Twin (32), Community (86), District (87), Site (88).

## 10. Benchmarking Requirements

Each operational building must be assigned to a benchmark cohort (06) keyed by {classificationID, floor-area band, occupancy class, climate zone}. EUI, peak demand density (W/m²) and carbon intensity (kgCO₂/m²/yr) are the mandatory benchmarked metrics; percentile position recomputed at least annually.

## 11. Dataset Requirements

Minimum dataset bundle per operational building: interval electricity (≤30min), monthly thermal (or interval where metered), weather (assigned station), occupancy proxy (schedule or measured), tariff series. Each registered in Data Catalogue (38) with buildingID lineage. Synthetic counterparts (42) must preserve §8 plausibility relations.

## 12. Feature Requirements

Canonical building features registered in (41): EUI, load factor, base-load ratio, weekday/weekend ratio, heating/cooling degree-day sensitivity, peak-to-average ratio, occupancy-normalised consumption, glazing ratio, era-band encoding, SRI score, flexibility capacity (kW shiftable), thermal time constant (derived from fabric 05 + mass class).

## 13. Model Requirements

This catalogue supplies static inputs to: load forecasting, thermal RC models, retrofit option models, baseline (M&V) models, flexibility capacity models, anomaly detection, archetype clustering. Field absence rules: any model declaring a §5 field as required input must fail gracefully to the classification-level default and flag degraded confidence — never silently impute.

## 14. KPI Requirements

Building-level KPIs (57) computable from this catalogue + datasets: EUI, carbon intensity, cost intensity (€/m²/yr), peak demand, load factor, self-consumption %, self-sufficiency %, comfort compliance %, BER/EPC trajectory, SRI trajectory.

## 15. Response Derivative Requirements

Building-level RDs (52) are contextualised by: thermalMassClass, primaryHeatingSystem, storageAssets, controlAuthorityLevel, comfort band width, criticalLoadRegister. The RD record must cite the buildingID and the field values prevailing at estimation time (RD context snapshot).

## 16. Decision Requirements

Decision Intelligence (60) consumes: controlAuthorityLevel (action gating), comfort bands (constraints), criticalLoadRegister (exclusions), connectionCapacityImport/Export (limits), tariffIDs (cost context), flexibilityRegisteredServices (market context). No action may be issued to a building whose controlAuthorityLevel is monitor_only.

## 17. Ontology Mapping

Classes: Building, BuildingComponent, Location, Tenure, EnergyCharacteristic, OperationalCharacteristic, RegulatoryCharacteristic, SmartReadinessProfile.
Object properties: hasClassification, locatedIn, ownedBy, operatedBy, occupiedBy, hasSpace, hasAsset, hasFabricAssembly, hasOccupancyProfile, hasSchedule, hasTariff, hasTwin, connectedToNetwork, hasMeteringPoint, registeredForService.
Datatype properties: buildingID, grossFloorArea, treatedFloorArea, constructionYear, berRating, sriScore, peakElectricalDemand, connectionCapacityImport, controlAuthorityLevel, symbioticReadinessLevel.
External alignment: SAREF4BLDG, BOT, Brick, ifcOWL IfcBuilding, GeoSPARQL for geometry (via 81).

## 18. Knowledge Graph Mapping

Nodes: Building, MeteringPoint, Owner(Organisation), Operator, Site, ClimateZone, WeatherStation, Tariff, Twin.
Relationships: CLASSIFIED_AS, LOCATED_IN, OWNED_BY, OPERATED_BY, PART_OF_SITE, HAS_METERING_POINT, ASSIGNED_STATION, ON_TARIFF, HAS_TWIN, ENROLLED_IN {serviceID, from, to}, HAS_CRITICAL_LOAD.

## 19. Digital Twin Mapping

Building Twin instantiation contract: twin template selected via classification (01); this catalogue supplies the static property payload; Spaces (04), Fabric (05), Assets (11) supply structure; Data Sources (37) bind live channels. Twin fidelity levels {static, operational, predictive, autonomous} require successively: §5.1–5.4 complete → + live metering bound → + models attached (44) → + controlAuthorityLevel ≥ supervised_control.

## 20. Governance

Roles: Catalogue Owner, Building Data Steward (per portfolio), Energy Domain Expert, Regulatory Compliance Owner, GDPR/Data Protection Officer (for §5.7.10 and occupancy-inferable data), Version Approver. Field additions require schema impact review across forms, APIs, JSON Schemas and twin templates.

## 21. Versioning

Catalogue schema semver; building records carry recordVersion + full audit trail (who/when/what). Lifecycle transitions (§5.1.8) are append-only events. Demolished buildings are archived, never deleted (historical model training integrity).

## 22. Example Records

```text
BuildingID: EIG-BLD-000142 | Name: Lee House Office Block
Classification: EIG-BC-SUB-2011 Open-plan office | Lifecycle: operational
Location: Cork City (EIG-GEO-IE-CO-CRK), 51.8985, -8.4756, climate zone EIG-WX-CZ-IE-S
GFA 8,400 m² | Treated 7,900 m² | Storeys 6/1 | Year 1998 | Era 1991_2005
Tenure: leased_multi | Split incentive: true | Control authority: supervised_control
Heating: gas_boiler (primary), heat_pump_air (secondary) | Cooling: vrf | Vent: mechanical_extract
MPRNs: [10001234567] | MIC 450 kVA | Sub-metering: distribution_board | Resolution: 30min
EUI 142 kWh/m²/yr | Peak 380 kW | Tariff: EIG-TRF-TOU-014 | SRI 41% | Symbiotic level: smart
BER: n/a | EPC: C2 | Public sector obligated: false | GDPR class: occupancy_inferable
```

```text
BuildingID: EIG-BLD-000877 | Name: Inis Mór Community Centre (ODEON pilot IE_B0014)
Classification: EIG-BC-SUB-4023 Community centre | Lifecycle: operational
Location: EIG-GEO-IE-GA-ARN | 53.1219, -9.7027 | Station: EIG-WX-ST-IE-ATH
GFA 620 m² | Year 1986 | Era 1971_1990 | Structure: masonry | Mass: heavy
Heating: heat_pump_air | Vent: natural | MPRN: [10009876543] | MIC 60 kVA
Connectivity: intermittent | Edge node: EIG-EDG-000031 (Jetson Nano) | Control: autonomous_within_envelope
Critical loads: [EIG-AST-007781 defib cabinet circuit] | Comfort band 19–24 °C
Flexibility services: [EIG-FS-DR-02] | Symbiotic level: symbiotic_ready
```

```text
BuildingID: EIG-BLD-001203 | Name: Riverside Mixed Development Block B
Classification: EIG-BC-TYP-1201 | Components: [{SUB-1021 apartments, 70%}, {SUB-8012 high-street unit, 30%}]
Lifecycle: commissioning | NZEB: true | BER (res. component): A2 | Heat network: true (EIG-NET-DH-004)
Storage: [EIG-AST-009912 thermal store 8 m³] | EV points: 24 | SRI 67%
```

## 23. Completion Criteria

Complete when every field in §5 has type, unit, allowed values, requirement status and description; all eight field groups populated; applicability and validation rules machine-checkable; relationships enumerated to exact catalogues; ontology/KG/twin mappings emitted; governance and versioning defined; ≥3 validated examples; and no operational building can present an attribute that has no home in this schema.
