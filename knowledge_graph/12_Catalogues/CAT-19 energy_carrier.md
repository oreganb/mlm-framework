---
type: catalogue
ontology: EIG-MLM
catalogue_no: 19
catalogue_id: EIG-CAT-019
entity: energy_carrier
band: markets
status: active
tags: [eig-mlm, catalogue, band:markets, entity:energy_carrier]
aliases: ["EIG-CAT-019", "Catalogue 19", "energy_carrier"]
---

# CAT-19 — Energy Carrier

> **EIG-CAT-019** · band: **Markets, Tariffs & Settlement** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Energy Carrier Catalogue

## 1. Definition

The Energy Carrier Catalogue is the authoritative register of all energy carriers (vectors) recognised by the EIG MLM framework. An energy carrier is a substance or phenomenon that contains energy and can be converted, stored, transported, metered, traded and accounted. This catalogue closes the vector vocabulary referenced throughout the framework: utility networks (13) carry exactly one carrier per network; assets (10/11) consume/produce/convert/store carriers; tariffs (21), products (22), trades (25), settlement (27) and carbon accounting (28) are all carrier-denominated.

This catalogue defines carrier identity, physical properties, quality parameters, measurement units and conversion bases, carrier-pair conversion processes, storage forms, carbon attributes and market/metering applicability. The nine reserved vectors flagged in Phases 2–3 are formally defined here.

## 2. Scope

In scope:

- The nine primary carriers: electricity, heat, cooling, gas (natural gas/biomethane blends), hydrogen, biomass, steam, compressed air, fuels (liquid fuel family).
- Sub-carriers/grades within each (e.g. LTHW vs HTHW heat grades; H2 purity grades; fuel types kerosene/diesel/HVO).
- Carrier quality parameters (voltage/frequency for electricity; temperature/pressure for thermal; calorific value/Wobbe for gas).
- Conversion processes between carriers (heat pump: electricity→heat; CHP: gas→electricity+heat; electrolysis: electricity→hydrogen).
- Carbon attributes per carrier (links to 28).

Out of scope:

- Network topology (13), asset instances (11), tariff/price structures (21), emission-factor time series values (28 owns values; this catalogue owns the carrier-side hooks).

## 3. Classification Structure

Carrier register (`carrierID` = EIG-ECR-XX) — CLOSED top level:

| carrierID | Carrier | Phase/state | Primary unit | Network type (13) |
|---|---|---|---|---|
| EIG-ECR-EL | electricity | electromagnetic | kWh (kW) | NET-ELC |
| EIG-ECR-HT | heat | thermal, liquid (water) | kWh_th | NET-DHN |
| EIG-ECR-CL | cooling | thermal, liquid | kWh_th (cooling) | NET-DCN |
| EIG-ECR-GA | gas | gaseous (CH₄-dominant) | kWh_hhv (m³ secondary) | NET-GAS |
| EIG-ECR-H2 | hydrogen | gaseous | kWh_hhv (kg secondary) | NET-H2 |
| EIG-ECR-BM | biomass | solid | kWh_ncv (kg, m³ stacked secondary) | — (logistics, not network) |
| EIG-ECR-ST | steam | thermal, vapour | kWh_th (t steam secondary) | — (site networks) |
| EIG-ECR-CA | compressed_air | pneumatic | kWh_el_equiv (Nm³ secondary) | — (site networks) |
| EIG-ECR-FU | fuels | liquid | kWh_ncv (L secondary) | — (logistics) |

Sub-carrier pattern: `EIG-ECR-XX-NN` (e.g. EIG-ECR-HT-01 lthw_55_45, EIG-ECR-HT-02 mthw_80_60, EIG-ECR-HT-03 hthw_110_plus, EIG-ECR-GA-01 natural_gas_h, EIG-ECR-GA-02 biomethane_blend, EIG-ECR-H2-01 green_h2_5_0, EIG-ECR-FU-01 kerosene, EIG-ECR-FU-02 diesel, EIG-ECR-FU-03 hvo, EIG-ECR-BM-01 wood_pellet_enplus_a1, EIG-ECR-BM-02 wood_chip_m30).

Carrier function axes (orthogonal flags): tradable, networkable, storable, meterable_continuous (vs batch-measured), carbon_bearing_at_point_of_use.

## 4. Hierarchy

```text
Carrier (EIG-ECR-XX, 9 fixed)
└── SubCarrier / grade (EIG-ECR-XX-NN)
    └── QualitySpec (named parameter set with limits)
ConversionProcess (EIG-ECR-CP-NN)  ← cross-links carrier pairs, not in the tree
```

Adding a top-level carrier is a MAJOR framework release (touches 13, 21–28, 57+); adding sub-carriers is a minor release.

## 5. Field Groups & Fields

### 5.1 Carrier identity & physics

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | carrierID | string | — | EIG-ECR-XX[-NN] | M | Carrier/sub-carrier ID |
| 2 | carrierName | string | — | snake_case | M | Canonical name |
| 3 | parentCarrierID | string | — | EIG-ECR-XX | C | For sub-carriers |
| 4 | phaseState | enum | — | electromagnetic, liquid, gaseous, solid, vapour, pneumatic | M | Physical form |
| 5 | primaryUnit | enum | — | kWh, kWh_th, kWh_hhv, kWh_ncv, kWh_el_equiv | M | Canonical energy unit |
| 6 | secondaryUnits | array | — | [{unit, toPrimaryFactor or formula, conditionsRef}] | C | m³, kg, L, t, Nm³ with conversion basis |
| 7 | energyDensity | object | — | {value, unit, basis: hhv/ncv, refConditions} | C | E.g. NG ≈ 11.06 kWh_hhv/m³(st); pellets ≈ 4.8 kWh_ncv/kg |
| 8 | hhvNcvRatio | decimal | — | > 1 | C | For combustible carriers (NG ≈ 1.108; H2 ≈ 1.183) |
| 9 | referenceConditions | object | — | {T_degC, p_kPa} | C | Volumetric reference (gas 15 °C/101.325 kPa) |
| 10 | networkType | enum | — | NET-* (13) or none | M | Carrier↔network binding |
| 11 | functionFlags | array | — | §3 axes | M | tradable/networkable/storable/… |
| 12 | status | enum | — | active, deprecated | M | Lifecycle |

### 5.2 Quality parameters

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | qualityParams | array | — | [{paramCode, unit, nominal, min, max, standardRef}] | M | Closed per carrier (below) |
| 2 | qualityMeasurementVars | array | — | EIG-WX-style var refs / metering vars (39) | C | How parameters are observed |

Per-carrier quality parameter register (closed):

- **EL**: voltage_nominal (230/400 V LV; per EN 50160 ±10%), frequency (50 Hz ±0.5), thd_voltage_pct (≤ 8), power_factor (0–1), phase_config (1ph/3ph).
- **HT**: flow_temp_degC, return_temp_degC, delta_t_K, pressure_bar, glycol_pct. Grades: LTHW 55/45 (EIG-ECR-HT-01, heat-pump-ready), MTHW 80/60, HTHW > 110.
- **CL**: flow_temp_degC (6–14 typical), return_temp_degC, delta_t_K.
- **GA**: wobbe_index_MJ_m3 (IE band 47.2–51.4), calorific_value_hhv, biomethane_fraction_pct, odorisation (boolean), pressure_tier (per 13 GAS tiers).
- **H2**: purity_grade (5.0 = 99.999%, fuel-cell ISO 14687), pressure_bar (30/350/700 classes), production_pathway (green/blue/grey — carbon hook §5.4).
- **BM**: moisture_pct (M-class EN ISO 17225), ash_pct, ncv_kWh_kg, grade (ENplus A1/A2/B; chip P/M classes).
- **ST**: pressure_bar_g, temperature_degC, dryness_fraction, superheat_K.
- **CA**: pressure_bar_g (6–10 typical), dewpoint_degC, iso8573_class.
- **FU**: density_kg_L, ncv_kWh_L, sulphur_ppm, bio_blend_pct (e.g. HVO 100).

### 5.3 Conversion processes (carrier pairs)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | conversionID | string | — | EIG-ECR-CP-NN | M | Process ID |
| 2 | fromCarriers | array | — | carrierID[] | M | Inputs |
| 3 | toCarriers | array | — | carrierID[] | M | Outputs (CHP has two) |
| 4 | assetSubtypes | array | — | EIG-AC-* refs (10) | M | Asset classes performing it |
| 5 | efficiencyMetric | enum | — | cop, spf, eta_th, eta_el, eta_total, ratio_kg_per_kWh | M | Canonical efficiency expression |
| 6 | typicalEfficiencyRange | object | — | {min, max, conditionsNote} | M | E.g. ASHP COP 2.5–4.5 (A7/W35); boiler η_th 0.85–0.97 hhv |
| 7 | reversible | boolean | — | true/false | M | E.g. battery charge/discharge |

Conversion register (closed, v1): CP-01 el→ht heat_pump (cop), CP-02 el→cl chiller/hp_cooling (eer→cop), CP-03 ga→ht boiler (eta_th), CP-04 ga→el+ht chp (eta_el+eta_th), CP-05 el↔el battery_storage (round-trip eta), CP-06 el→h2 electrolysis (kWh/kg ≈ 50–55), CP-07 h2→el fuel_cell (eta_el), CP-08 bm→ht biomass_boiler (eta_th ncv), CP-09 fu→ht oil_boiler (eta_th), CP-10 el→ca compressor (kWh_el/Nm³), CP-11 ht↔ht thermal_storage (standing-loss %/day), CP-12 solar→el pv (performance ratio), CP-13 solar→ht solar_thermal (eta_collector), CP-14 ga→st steam_boiler, CP-15 st→el steam_turbine, CP-16 h2_blend→ga injection (vol % limit per 13 GAS rules).

### 5.4 Carbon & accounting hooks

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | emissionFactorClass | enum | — | grid_dynamic, grid_average, fixed_fuel, biogenic, zero_at_use, pathway_dependent | M | Which 28 factor family applies |
| 2 | defaultEmissionFactorRef | string | — | EIG-CO2-EF-* (28) | M | Default factor record |
| 3 | scopeAtPointOfUse | enum | — | scope1, scope2, scope3, none | M | GHG Protocol scope when consumed |
| 4 | renewableAttributable | boolean | — | true/false | M | Eligible for GO/REGO-style attribution |
| 5 | meteringBasis | enum | — | interval_metered, batch_measured, delivered_quantity, calculated | M | Settlement evidence basis (27) |

Defaults: EL grid_dynamic/scope2; GA fixed_fuel/scope1 (biomethane fraction biogenic); HT/CL/ST/CA pathway_dependent (upstream conversion determines); H2 pathway_dependent; BM biogenic/scope1-biogenic-memo; FU fixed_fuel/scope1.

### 5.5 Record metadata

recordVersion, createdDate, modifiedDate, owner, governanceStatus — programme standard.

## 6. Controlled Vocabularies

Closed: carrierID top level (9), conversion register CP-01..16, phaseState, primaryUnit basis set, efficiencyMetric, emissionFactorClass, scopeAtPointOfUse, meteringBasis, per-carrier quality parameter codes, sub-carrier grades. External standards: EN 50160 (EL quality), EN ISO 17225 (BM), ISO 14687 (H2), ISO 8573 (CA), ISO 6976 (gas calorific/Wobbe), GHG Protocol scopes.

## 7. Applicability Rules

| Rule | Statement |
|---|---|
| A1 | Every network (13) declares exactly one carrierID; carrier networkType must match |
| A2 | Every asset subtype (10) declares input/output carriers consistent with its FN-* functions; conversion assets must map to a CP-* process |
| A3 | Tariffs (21), products (22), trades (25), settlement lines (27) denominate in exactly one carrierID with its primaryUnit; secondary-unit quantities convert via §5.1 factors at recorded reference conditions |
| A4 | Heat grade sub-carriers gate connectability: LTHW networks accept heat-pump sources; HTHW loads cannot connect to LTHW networks (13 connection rule) |
| A5 | H2 blending into GAS networks bounded by CP-16 volumetric limit per network record |
| A6 | Carbon accounting (28) resolves factors via emissionFactorClass; pathway_dependent carriers REQUIRE upstream conversion provenance (no default fudge) |
| A7 | Multi-vector optimisation models must use kWh-basis primary units with declared hhv/ncv basis; mixing bases in one objective is illegal (V8) |

## 8. Validation Rules

| ID | Rule | Severity |
|---|---|---|
| V1 | carrierID pattern-valid; top-level set immutable (9 carriers) | error |
| V2 | Sub-carrier parentCarrierID resolves; quality params subset of parent's register | error |
| V3 | secondaryUnits conversions dimensionally valid with reference conditions stated | error |
| V4 | hhv/ncv basis declared wherever calorific units used; hhvNcvRatio present for combustibles | error |
| V5 | Conversion processes: efficiency ranges physical (COP ≤ Carnot bound flag; eta ≤ 1 on stated basis) | error |
| V6 | Every conversion asset subtype in 10 maps to ≥ 1 CP-*; every CP-* maps to ≥ 1 asset subtype | error |
| V7 | emissionFactorClass/default factor resolve in 28; pathway_dependent carriers carry no fixed default at point of use | error |
| V8 | Cross-carrier model objectives single energy basis (A7) | error |
| V9 | Quality limits within standardRef bounds (EN 50160, Wobbe band, ISO classes) | error |
| V10 | Deprecation: carrier/sub-carrier with live network/tariff/product references cannot deprecate | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 10/11 asset | Asset carrier I/O; CP-* binding; efficiency metrics feed asset benchmarks (12: spf_measured is CP-01's metric) |
| 13 utility_network | One carrier per network; quality params at connection points; H2 blend limits |
| 20–27 market layer | Carrier-denominated markets, tariffs, products, trades, settlement (A3) |
| 28 carbon | Factor classes and scope hooks; pathway provenance for HT/CL/ST/H2 |
| 29/30 flexibility | Flexibility is carrier-specific (electrical/thermal); cross-carrier flex via CP-* (power-to-heat) |
| 39 metadata / 41 feature | Unit registry inherits primary/secondary units; energy features declare carrier + basis |
| 52 RD | RDs are carrier-typed (electrical kW vs thermal kW response); cross-carrier RDs cite CP-* path |
| 89 scenario | Decarbonisation scenarios manipulate carrier mixes and factor trajectories |

## 10. Benchmarking Requirements

Conversion-efficiency benchmarks (12) are CP-indexed: spf cohorts per CP-01, boiler-eta per CP-03, round-trip per CP-05. Carrier-mix benchmarking at building level (06): fossil share, electrification ratio — derived from carrier-resolved consumption.

## 11. Dataset Requirements

Every energy time series (38) carries carrierID + unit + basis in metadata (39); mixed-carrier datasets forbidden without per-series tags. Conversion-condition data (outdoor temp for COP) co-located per CP requirements. Reference-condition normalisation logged in lineage.

## 12. Feature Requirements

Carrier features: carrier one-hot at meter level, electrification ratio, carrier-specific intensities, H2/biomethane fractions, heat-grade class. Conversion features: COP/eta observed vs rated, condition-normalised. All energy features declare basis (hhv/ncv) in the feature registry (41).

## 13. Model Requirements

Multi-vector models (energy-hub formulations) use this catalogue's carrier set and CP-* efficiency envelopes as structural priors. Thermal models bind to heat-grade sub-carriers. Forecast models are carrier-specific; cross-carrier substitution effects (HP vs boiler dispatch) require CP-coupled formulations.

## 14. KPI Requirements

Carrier-resolved KPIs (57): consumption/cost/emissions by carrier, electrification %, renewable share by carrier, conversion-efficiency KPIs per CP-*, district-heat delta-T compliance. All KPI definitions cite carrierID + basis.

## 15. Response Derivative Requirements

RDs (52) declare responseCarrier (what is modulated: EL kW or HT kW_th) and conditioning carrier context. Cross-carrier RDs (power-to-heat shifting) cite the CP path and its efficiency envelope so kW_el ↔ kW_th translation is auditable. Asset flex envelopes (11 §5.6) inherit carrier typing.

## 16. Decision Requirements

Dispatch/optimisation decisions across carriers record the CP efficiencies and prices used; settlement-grade decisions use metered carrier quantities only (meteringBasis). Carrier-switch recommendations (boiler→HP) must present both energy (basis-consistent) and carbon (28) deltas.

## 17. Ontology Mapping

Classes: EnergyCarrier ⊐ {Electricity, Heat, Cooling, Gas, Hydrogen, Biomass, Steam, CompressedAir, Fuel}, SubCarrier, QualityParameter, ConversionProcess, EmissionFactorClass.
Object properties: hasSubCarrier, carriedByNetwork, convertedBy (→ ConversionProcess), convertsFrom/convertsTo, hasQualityParameter, hasDefaultEmissionFactor, measuredIn (→ Unit).
Datatype properties: carrierID, phaseState, primaryUnit, energyDensity, hhvNcvRatio, scopeAtPointOfUse.
Alignment: SAREF saref:Commodity + saref4ener; CIM EnergySchedulingType patterns; QUDT units (kWh, qudt:GrossCalorificValue/NetCalorificValue); Brick substance classes for site media.

## 18. Knowledge Graph Mapping

Nodes: Carrier, SubCarrier, ConversionProcess. Relationships: SUB_OF, CARRIED_BY (→ Network), CONVERTS_FROM/TO, PERFORMED_BY (→ AssetSubtype), HAS_FACTOR_CLASS (→ CarbonFactor), DENOMINATES (→ Tariff/Product/Trade). Keys: carrierID, conversionID.

## 19. Digital Twin Mapping

Twins type every energy flow with carrierID + sub-carrier grade; conversion components simulate via CP efficiency models; multi-vector site twins balance per-carrier with CP coupling; quality excursions (delta-T degradation, voltage) raise twin alerts bound to quality params.

## 20. Governance

Owner: EIG Catalogue Owner; energy-systems lead co-owns physics constants (reviewed against SEAI/ISO updates annually). Top-level carrier additions = framework major release with full impact assessment across 13, 20–28, 52. Quality standards tracked against EN/ISO revisions.

## 21. Versioning

Catalogue semver; constants changes (energy densities, hhv/ncv ratios) minor with evidence; conversion-register additions minor; top-level carrier changes major. Historical settlement recalculation NEVER triggered by constant updates — settlements pin the constant version used (27).

## 22. Example Records

```text
carrierID: EIG-ECR-EL | electricity | phaseState: electromagnetic | primaryUnit: kWh | networkType: NET-ELC
functionFlags: [tradable, networkable, storable(via CP-05), meterable_continuous]
quality: voltage 230/400 V ±10% (EN 50160), 50 Hz ±0.5, THD ≤ 8%
emissionFactorClass: grid_dynamic | defaultEmissionFactorRef: EIG-CO2-EF-IE-GRID | scopeAtPointOfUse: scope2
meteringBasis: interval_metered | status: active
```

```text
carrierID: EIG-ECR-HT-01 | lthw_55_45 | parent: EIG-ECR-HT | phaseState: liquid | primaryUnit: kWh_th
quality: flow 55 °C, return 45 °C, ΔT 10 K nominal | heat-pump-compatible grade (CP-01 source eligible)
emissionFactorClass: pathway_dependent | meteringBasis: interval_metered (heat meter, EN 1434)
```

```text
conversionID: EIG-ECR-CP-01 | heat_pump | from: [EIG-ECR-EL] | to: [EIG-ECR-HT]
assetSubtypes: [EIG-AC-HVC-01-01 ashp_air_water, EIG-AC-HVC-01-02 gshp, …] | efficiencyMetric: cop (spf seasonal, SEPEMO H4 per 12 MET spf_measured)
typicalEfficiencyRange: {2.5, 4.5, A7/W35–A7/W55 envelope} | reversible: false (cooling via CP-02)
```

## 23. Completion Criteria

Complete when: all nine carriers + v1 sub-carriers and CP-01..16 fully specified; every network, asset subtype, tariff, product and settlement line resolves its carrier reference; basis declarations universal; carbon hooks resolve into 28; quality registers standard-anchored; Phase 14 confirms no carrier vocabulary exists outside this catalogue.
