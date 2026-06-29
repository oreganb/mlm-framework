---
type: catalogue
ontology: EIG-MLM
catalogue_no: 10
catalogue_id: EIG-CAT-010
entity: asset_classification
band: physical
status: active
tags: [eig-mlm, catalogue, band:physical, entity:asset_classification]
aliases: ["EIG-CAT-010", "Catalogue 10", "asset_classification"]
---

# CAT-10 — Asset Classification

> **EIG-CAT-010** · band: **Physical & Spatial Stack** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Asset Classification Catalogue

Catalogue ID prefix: **EIG-AC** · Catalogue number: 10 · Version 1.0 · Status: Active

---

## 1. Definition

The Asset Classification Catalogue is the classification framework for every asset used in buildings, industry, communities and infrastructure within the EIG MLM framework. It defines asset categories, types, subtypes and functions. Every asset instance (catalogue 11) MUST resolve to exactly one leaf node in this classification and one or more functions. No asset may exist outside this scheme.

## 2. Scope

All energy-producing, energy-consuming, energy-storing, energy-converting, energy-transporting, sensing, controlling and computing assets across: residential and non-residential buildings, industrial sites, communities/districts, and utility-scale infrastructure. Excludes building fabric elements (catalogue 05) and pure software (catalogues 31, 73).

## 3. Classification Structure

Three-level taxonomy — Category → Type → Subtype — plus one orthogonal **Function** axis.

### 3.1 Categories (closed, 12)

| Category ID | Category | Definition |
|---|---|---|
| EIG-AC-GEN | Generation | Converts a primary resource to electricity or heat |
| EIG-AC-STO | Storage | Stores energy for later use (electrical, thermal, chemical, mechanical) |
| EIG-AC-HVC | HVAC & Climate | Space heating, cooling, ventilation, humidity control |
| EIG-AC-DHW | Domestic & Process Hot Water | Water heating, storage, distribution |
| EIG-AC-LGT | Lighting | Interior, exterior, emergency lighting |
| EIG-AC-PLG | Plug & Process Loads | Appliances, IT, machinery, process equipment |
| EIG-AC-EVM | E-Mobility | EV charging, V2X infrastructure |
| EIG-AC-DST | Distribution (in-site) | Electrical boards, pipework, ducts, pumps, fans serving distribution |
| EIG-AC-MTR | Metering & Sensing | Meters, sub-meters, sensors, IAQ monitors |
| EIG-AC-CTL | Control & Compute | BMS, controllers, gateways, edge devices, actuators |
| EIG-AC-WTR | Water & Wastewater | Pumps, treatment, rainwater systems (energy-relevant) |
| EIG-AC-NWK | Network Infrastructure | Utility-scale assets: transformers, substations, feeders, district heating plant |

### 3.2 Types and Subtypes (per category; leaf = subtype)

**GEN:** solar_pv (rooftop_pv, ground_mount_pv, bipv, floating_pv) · wind (micro_wind, small_wind, utility_wind) · chp (gas_chp, biomass_chp, micro_chp_stirling, fuel_cell_chp) · hydro (micro_hydro) · solar_thermal (flat_plate, evacuated_tube, pvt_hybrid) · diesel_genset (standby_genset, prime_genset) · biomass_boiler_gen (log, pellet, chip).

**STO:** battery (li_ion_lfp, li_ion_nmc, lead_acid, flow_vanadium, sodium_ion, second_life_ev) · thermal_store (hot_water_cylinder, buffer_tank, pcm_store, ice_store, borehole_ttes, sand_battery) · hydrogen (electrolyser, h2_tank, fuel_cell) · mechanical (flywheel) · ev_as_storage (v2g_unit, v2h_unit).

**HVC:** heat_pump (ashp_air_water, ashp_air_air, gshp_brine_water, wshp, exhaust_air_hp, hthp_industrial, hybrid_hp_gas) · boiler (gas_condensing, gas_non_condensing, oil, biomass_pellet, electric, hydrogen_ready) · direct_electric (storage_heater_hhr, storage_heater_legacy, panel_heater, infrared_panel, underfloor_electric) · chiller (air_cooled, water_cooled, absorption, free_cooling) · ventilation (mvhr, mev, dcv_ahu, natural_vent_actuated, erv) · distribution_emitters (radiator_hydronic, ufh_hydronic, fan_coil, chilled_beam, air_diffuser) · humidity (humidifier, dehumidifier) · district_interface (heat_interface_unit, substation_dh).

**DHW:** cylinder (vented, unvented, heat_pump_cylinder, smart_immersion) · instantaneous (electric_shower, combi_function, pou_heater) · circulation (dhw_recirc_pump) · legionella_control_unit.

**LGT:** luminaire (led_panel, led_highbay, led_exterior, legacy_fluorescent, legacy_hid) · lighting_control (presence_sensor_lc, daylight_dimmer, dali_controller) · emergency_lighting.

**PLG:** white_goods (wet_appliance_dw, wet_appliance_wm, wet_appliance_td, cold_appliance_fridge, cold_appliance_freezer) · cooking (hob_induction, hob_gas, oven_electric) · it_load (server, network_switch, workstation, pos) · industrial_process (compressor_air, motor_drive_vsd, motor_dol, furnace, kiln, refrigeration_rack, cold_room, conveyor) · misc_plug.

**EVM:** ev_charger (ac_7kw, ac_22kw, dc_50kw, dc_150kw_plus, wireless) · v2x_charger (v2g_dc, v2h_ac) · load_management_unit.

**DST:** electrical (main_board, sub_board, ups, pfc_unit, transformer_lv) · hydronic (circulator_pump_fixed, circulator_pump_vsd, mixing_valve, zone_valve, heat_exchanger_plate) · air (supply_fan, extract_fan, vav_box, damper_actuated).

**MTR:** fiscal_meter (electricity_smart_esbn, gas_smart, heat_meter_mid, water_meter) · sub_meter (ct_meter_1p, ct_meter_3p, modbus_meter, mbus_heat) · sensor (temp_sensor, rh_sensor, co2_sensor, pm_sensor, voc_sensor, lux_sensor, occupancy_pir, occupancy_count, door_window_contact, flow_sensor, pressure_sensor, current_clamp) · weather_local (see WX building_local).

**CTL:** supervisory (bms_head_end, bems_cloud, home_hub) · controller (plc, ddc_controller, thermostat_smart, thermostat_legacy, trv_smart, relay_module) · gateway (protocol_gateway_modbus, protocol_gateway_bacnet, lorawan_gateway, m2m_router) · edge_compute (jetson_class, rpi_class, industrial_pc) · actuator (valve_actuator, damper_actuator, contactor).

**WTR:** pump_water (borehole, booster, sewage), treatment_unit, rainwater_harvest_unit.

**NWK:** transformer_mv_lv, substation_primary, feeder_segment, dh_plant (heat_only_boiler_plant, chp_plant_dh, large_hp_plant), dh_pipe_segment, h2_pipeline_segment, gas_pressure_reduction.

### 3.3 Function axis (multi-select, FN-*)

| Function ID | Function | Definition |
|---|---|---|
| FN-PRODUCE | Produces energy | Net energy output capability |
| FN-CONSUME | Consumes energy | Net consumption |
| FN-STORE | Stores energy | Charge/discharge capability |
| FN-CONVERT | Converts vector | e.g. electricity→heat (HP), gas→heat+elec (CHP) |
| FN-TRANSPORT | Moves energy | Pumps, fans, feeders, pipes |
| FN-MEASURE | Senses/measures | Data origination |
| FN-CONTROL | Controls/actuates | Changes another asset's state |
| FN-COMPUTE | Computes | Runs models/agents locally |
| FN-FLEX | Provides flexibility | Schedulable/sheddable/shiftable (drives RD eligibility) |
| FN-CRITICAL | Life-safety/critical | Never sheddable (overrides FN-FLEX) |

## 4. Hierarchy

```
Category (12) → Type (closed per category) → Subtype (leaf, ~140)
Function axis: leaf nodes carry defaultFunctions; instances may add/remove per rules §7.
```

Node ID pattern: `EIG-AC-<CAT>-<TYPE_NN>-<SUB_NN>` e.g. EIG-AC-HVC-01-02 = heat_pump / ashp_air_air. Names are the canonical join key for humans; IDs for machines.

## 5. Field Groups & Fields (per classification node)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | nodeID | string | — | pattern above | M | Unique node ID |
| 2 | nodeName | string | — | snake_case | M | Canonical name |
| 3 | nodeLevel | enum | — | category, type, subtype | M | Level |
| 4 | parentNodeID | string | — | valid nodeID | C (type, subtype) | Parent |
| 5 | definition | string | — | ≤ 500 chars | M | Unambiguous definition |
| 6 | defaultFunctions | array | — | FN-* | M (subtype) | Default function set |
| 7 | flexibilityClass | enum | — | none, sheddable, shiftable, modulating, storable, bidirectional | M (subtype) | Default flex capability (drives RD eligibility) |
| 8 | energyVectorsIn / energyVectorsOut | array | — | EIG-ECR IDs (electricity, natural_gas, heat_lt/ht, hydrogen, biomass, oil, solar, wind, water) | M (subtype) | Vector interfaces (forward ref → 19) |
| 9 | typicalCapacityRange | object | unit per vector | {min, max, unit} | M (subtype) | Plausibility band for instance validation |
| 10 | typicalLifetimeYears | object | yr | {design, economic} | M (subtype) | Lifecycle defaults |
| 11 | maintenanceClass | enum | — | none, annual, biannual, quarterly, condition_based, statutory | M (subtype) | Default regime |
| 12 | standardsRefs | array | — | EIG-STD IDs | O | Governing standards (forward ref → 94) |
| 13 | modelRelevance | array | — | model family tags (thermal, electrical, forecast, rd, comfort, iaq, lifecycle) | M (subtype) | Which model families need this subtype's instances |
| 14 | benchmarkSchemeRef | string | — | EIG-ABM scheme ID | C | Default benchmark scheme (→ 12) |
| 15 | status / version / owner / dates / source | — | — | standard audit block | M | Audit |

## 6. Controlled Vocabularies

Categories (§3.1, closed). Types/subtypes (§3.2, closed; extension = minor version). Functions (§3.3, closed). flexibilityClass (closed, 6 values). maintenanceClass (closed). Vector list defers to energy_carrier (19) as authority; until Phase 4, the 9 vectors named in §5#8 are reserved.

## 7. Applicability Rules

1. Asset instances bind to subtype leaves only — never to category/type nodes.
2. Instance functions = defaultFunctions ± declared deltas; removing FN-CRITICAL from a subtype that defaults to it requires governance sign-off; adding FN-FLEX to an instance whose subtype defaults flexibilityClass=none requires an RD eligibility assessment record.
3. FN-CRITICAL ∧ FN-FLEX is illegal at instance level: FN-CRITICAL wins and the instance is excluded from all shed/shift action spaces (consistent with space catalogue circuitRegister life-safety exclusions).
4. Building-type applicability: subtypes may declare excludedBuildingSectors (e.g. hthp_industrial excluded from Residential); validated against EIG-BC at instance creation.
5. NWK category applies only to assets owned/operated at network level (EIG-ORG operator role), never to building instances.

## 8. Validation Rules

| # | Rule |
|---|---|
| V1 | nodeID unique, pattern-valid; level consistent with ID depth |
| V2 | Tree acyclic; every subtype has type parent; every type has category parent |
| V3 | Subtype must carry defaultFunctions, flexibilityClass, vectors, capacity range, lifetime, maintenanceClass, modelRelevance |
| V4 | FN-PRODUCE ⇒ energyVectorsOut non-empty; FN-CONSUME ⇒ energyVectorsIn non-empty; FN-CONVERT ⇒ both non-empty and different |
| V5 | flexibilityClass ≠ none ⇒ FN-FLEX ∈ defaultFunctions (and conversely) |
| V6 | typicalCapacityRange.min < max; units UCUM-valid |
| V7 | No two subtypes within a type share identical (vectors, functions, definition) — prevents duplicate leaves |
| V8 | Deprecated nodes carry successorNodeID; instances on deprecated nodes flagged for migration |

## 9. Relationships to Other Catalogues

| Direction | Catalogue | Relationship |
|---|---|---|
| ◀ consumed by | asset (11) | every instance.classificationNodeID → subtype leaf |
| ◀ consumed by | asset_benchmark (12) | benchmark schemes keyed per subtype |
| ◀ consumed by | flexibility (30), flexibility_service (29) | eligibility via flexibilityClass + FN-FLEX |
| ◀ consumed by | response_derivative (52) | RD definitions scoped to subtypes |
| ◀ consumed by | model (44) | modelRelevance routing |
| ◀ consumed by | device (36) | MTR/CTL subtypes are the device superset |
| ▶ depends on | energy_carrier (19) | vector vocabulary (reserved pending Phase 4) |
| ▶ depends on | standard (94) | standardsRefs |
| peer | building_classification (01) | excludedBuildingSectors validation |

## 10. Benchmarking Requirements

Benchmarking is technology-specific: benchmark schemes (12) key on subtype, never category (an ASHP and a gas boiler share a category-adjacent space but incomparable metrics). Each subtype with benchmarkSchemeRef must have ≥ 1 efficiency metric, ≥ 1 reliability metric, ≥ 1 lifecycle metric defined in catalogue 12.

## 11. Dataset Requirements

Any dataset of asset instances/telemetry MUST carry classificationNodeID per row or per asset join. Synthetic asset generation (42) samples subtypes per building archetype using installed-base priors; priors are versioned datasets referenced from archetype catalogue (07) retrofit pathways.

## 12. Feature Requirements

Classification-derived features (FEA-AC-*): subtype one-hot/embedding, flexibilityClass ordinal, function flags, vector interface flags, capacity-normalised features (value ÷ ratedCapacity using §5#9 plausibility check), asset-age-fraction (age ÷ design lifetime).

## 13. Model Requirements

Models declare applicability by subtype list or modelRelevance tag; the model registry (44) refuses deployment of a model to an instance whose subtype is outside the declared set. Subtype-level transfer (model trained on ashp_air_water applied to gshp_brine_water) requires a transfer validation record (47).

## 14. KPI Requirements

KPIs computed per asset roll up by subtype → type → category. Efficiency KPIs use subtype-appropriate definitions (SCOP for HPs, seasonal efficiency for boilers, round-trip for storage) — the KPI catalogue (57) must bind metric definitions to subtypes via this catalogue's nodeIDs.

## 15. Response Derivative Requirements

RD eligibility derives from flexibilityClass: sheddable→shed RDs, shiftable→shift RDs, modulating→modulation RDs, storable/bidirectional→charge/discharge RDs. Subtypes with flexibilityClass=none are excluded from RD computation entirely. RD definitions (52) MUST reference subtype nodeIDs in their applicability blocks; aggregation across subtypes requires homogeneity tests.

## 16. Decision Requirements

Action spaces (61) enumerate allowed actions per subtype (e.g. trv_smart: setpoint_adjust; battery li_ion: charge/discharge/idle; FN-CRITICAL: none). Control strategies (62) bind to subtypes; deploying a strategy to a non-listed subtype is blocked at decision-engine level.

## 17. Ontology Mapping

Classes: AssetCategory, AssetType, AssetSubtype (⊑ skos:Concept), AssetFunction, FlexibilityClass.
Object properties: hasParentNode, hasDefaultFunction, hasFlexibilityClass, consumesVector, producesVector, governedByStandard, benchmarkedBy.
Datatype properties: nodeID, nodeName, definition, typicalLifetimeDesignYears, capacityMin, capacityMax.
Alignment: SAREF saref:Device + SAREF4ENER/SAREF4BLDG device hierarchies (skos:closeMatch per subtype), Brick equipment classes (skos:closeMatch), IFC IfcDistributionElement types for HVC/DST.

## 18. Knowledge Graph Mapping

Nodes: AssetCategory, AssetType, AssetSubtype, AssetFunction. Relationships: PARENT_OF, HAS_FUNCTION, HAS_FLEX_CLASS, IN_VECTOR/OUT_VECTOR, ALIGNED_TO (external class), INSTANCE_OF (asset→subtype, from 11). Keys: nodeID. The taxonomy loads as a static reference subgraph; instance subgraph (11) hangs off it.

## 19. Digital Twin Mapping

Twin templates (33) are authored per subtype (or subtype group) — the template registry keys on nodeID. A twin instantiated for an asset inherits the subtype's default behaviour model set (via modelRelevance) and flexibility envelope (via flexibilityClass), then specialises with instance data.

## 20. Governance

Owner: EIG Catalogue Owner (PI). Domain experts per category (HVAC, electrical, storage, networks). Subtype additions: proposal → domain review → minor release. Function/flexibilityClass changes on existing subtypes: major release + downstream impact scan (RD, action space, twin templates).

## 21. Versioning

Catalogue semver; node-level deprecation with successor pointers; instances never orphaned (migration job required before node retirement). Changelog per release enumerates affected downstream catalogues.

## 22. Example Records

```text
nodeID: EIG-AC-HVC-01-01 | nodeName: ashp_air_water | level: subtype | parent: EIG-AC-HVC-01 (heat_pump)
definition: Air-source heat pump delivering heat via hydronic circuit (space ± DHW).
defaultFunctions: [FN-CONSUME, FN-CONVERT, FN-FLEX] | flexibilityClass: modulating
vectorsIn: [electricity] | vectorsOut: [heat_lt] | typicalCapacityRange: {4, 16, kW_th} (residential band)
typicalLifetimeYears: {design: 15, economic: 12} | maintenanceClass: annual
modelRelevance: [thermal, forecast, rd, lifecycle] | benchmarkSchemeRef: EIG-ABM-SCH-HP
standardsRefs: [EN 14511, EN 14825, EU 813/2013]
```

```text
nodeID: EIG-AC-STO-01-01 | nodeName: li_ion_lfp | level: subtype | parent: EIG-AC-STO-01 (battery)
defaultFunctions: [FN-STORE, FN-FLEX] | flexibilityClass: bidirectional
vectorsIn: [electricity] | vectorsOut: [electricity] | typicalCapacityRange: {2.5, 500, kWh}
typicalLifetimeYears: {design: 12, economic: 10} | maintenanceClass: condition_based
modelRelevance: [electrical, forecast, rd, lifecycle] | benchmarkSchemeRef: EIG-ABM-SCH-BAT
```

```text
nodeID: EIG-AC-CTL-04-01 | nodeName: jetson_class | level: subtype | parent: EIG-AC-CTL-04 (edge_compute)
definition: GPU-accelerated edge computer (Jetson Nano/Orin class) running local inference.
defaultFunctions: [FN-COMPUTE, FN-CONSUME] | flexibilityClass: none
vectorsIn: [electricity] | typicalCapacityRange: {5, 60, W} | lifetime: {design: 7, economic: 5}
maintenanceClass: condition_based | modelRelevance: [rd] | note: ODEON IE_B0001–IE_B0020 edge nodes
```

## 23. Completion Criteria

Complete when: all 12 categories, all types, ~140 subtypes enumerated with full §5 field sets; every asset instance in catalogue 11 resolves to a leaf; flexibilityClass assigned to 100% of subtypes; vector interfaces reconciled against energy_carrier (19) in Phase 4; benchmark scheme coverage confirmed against catalogue 12; zero instances on deprecated nodes.
