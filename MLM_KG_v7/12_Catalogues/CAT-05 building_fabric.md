---
type: catalogue
ontology: EIG-MLM
catalogue_no: 5
catalogue_id: EIG-CAT-005
entity: building_fabric
band: physical
status: active
tags: [eig-mlm, catalogue, band:physical, entity:building_fabric]
aliases: ["EIG-CAT-005", "Catalogue 05", "building_fabric"]
---

# CAT-05 — Building Fabric

> **EIG-CAT-005** · band: **Physical & Spatial Stack** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Building Fabric Catalogue

## 1. Definition

The Building Fabric Catalogue defines every building envelope and construction component — walls, roofs, floors, windows, doors, insulation layers and their thermal properties — required to support EPC/BER assessment, retrofit analysis, thermal simulation and fabric-related decision-making in the EIG MLM framework. Fabric is specified at three levels: material, construction layer, and assembly (the buildable element bound to spaces).

## 2. Scope

Applies to: BER/DEAP and EPC calculation inputs, NZEB/EPBD compliance, retrofit option modelling and cost-optimal analysis, RC and detailed thermal simulation parameterisation, heat-loss-coefficient derivation, condensation/moisture risk screening, embodied-carbon accounting, synthetic building fabric generation.

Out of scope: structural engineering properties beyond thermal/moisture relevance, building services (11), space definitions (04 — assemblies are referenced by spaces).

## 3. Classification Structure

```text
Element Class:   wall | roof | floor | window | door | rooflight | thermal_bridge | air_barrier
Element Position: external | internal | party | ground_contact | exposed_soffit
Level:           material (EIG-FAB-MAT) → layer (EIG-FAB-LAY) → assembly (EIG-FAB-ASM)
```

## 4. Hierarchy

```text
Material (generic, reusable)
 └─ Layer (material + thickness + role in build-up)
     └─ Assembly (ordered layer stack + bridging + openings handling)
         └─ Assembly Instance (assembly bound to a building/space boundary with area & condition)
                                            (EIG-FAB-INS)
```

## 5. Field Groups & Fields

### 5.1 Material Fields

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | materialID | string | — | `EIG-FAB-MAT-[0-9]{5}` | Y | Unique ID |
| 2 | materialName | string | — | ≤120 chars | Y | Name |
| 3 | materialCategory | enum | — | masonry, concrete, timber, insulation, membrane, board, render_plaster, metal, glass, soil, air_cavity, composite | Y | Category |
| 4 | thermalConductivity | decimal | W/mK | 0.01…500 | Y | λ (design value) |
| 5 | density | decimal | kg/m³ | >0 | Y | ρ |
| 6 | specificHeatCapacity | decimal | J/kgK | >0 | Y | c |
| 7 | vapourResistivity | decimal | MNs/gm | ≥0 | N | Moisture property |
| 8 | embodiedCarbonA1A3 | decimal | kgCO₂e/kg | ≥0 | N | Cradle-to-gate |
| 9 | combustibilityClass | enum | — | A1, A2, B, C, D, E, F (EN 13501-1) | N | Fire class |
| 10 | dataSource | enum | — | EN_ISO_10456, manufacturer_declared, measured, assumed | Y | Provenance of λ |
| 11 | lifecycle block | — | — | status/version/owner/dates/governance | Y | Standard |

### 5.2 Layer Fields

| # | Field | Type | Unit | Allowed Values | Req | Description |
|---|---|---|---|---|---|---|
| 1 | layerID | string | — | `EIG-FAB-LAY-[0-9]{6}` | Y | ID |
| 2 | materialID | string | — | valid material | Y | Material |
| 3 | thickness | decimal | mm | >0 | Y | Layer thickness |
| 4 | layerRole | enum | — | structure, insulation, cavity, finish_internal, finish_external, membrane_vapour, membrane_breather, airtightness, service_void | Y | Function in build-up |
| 5 | thermalResistance | decimal | m²K/W | derived d/λ or declared (cavities) | Y | R-value |
| 6 | bridgedFraction | decimal | — | 0…1 | N | Fraction bridged (e.g. studs) |
| 7 | bridgingMaterialID | string | — | materialID | Cond | If bridgedFraction>0 |

### 5.3 Assembly Fields (opaque elements)

| # | Field | Type | Unit | Allowed Values | Req | Description |
|---|---|---|---|---|---|---|
| 1 | assemblyID | string | — | `EIG-FAB-ASM-[0-9]{5}` | Y | ID |
| 2 | assemblyName | string | — | ≤120 chars | Y | Name |
| 3 | elementClass | enum | — | wall, roof, floor, thermal_bridge, air_barrier | Y | Element |
| 4 | elementPosition | enum | — | external, internal, party, ground_contact, exposed_soffit | Y | Position |
| 5 | layerStack | array[string] | — | ordered layerIDs (outside→inside) | Y | Build-up |
| 6 | uValue | decimal | W/m²K | 0.05…6.0 | Y | Calculated/declared U |
| 7 | uValueMethod | enum | — | EN_ISO_6946, EN_ISO_13370 (ground), measured_in_situ, deemed (age-band default) | Y | Basis |
| 8 | kappaValue | decimal | kJ/m²K | ≥0 | N | Areal heat capacity (mass) |
| 9 | decrementFactor | decimal | — | 0…1 | N | Dynamic property |
| 10 | decrementDelay | decimal | h | ≥0 | N | Dynamic property |
| 11 | psiValueRegister | array[object] | W/mK | {junctionType: enum per ACD set, psiValue, length basis} | N | Linear bridges |
| 12 | airPermeabilityContribution | enum | — | airtight, standard, leaky | N | Qualitative class |
| 13 | constructionEraTypical | array[enum] | — | era bands (01/02) | N | Eras where typical (for deemed values) |
| 14 | retrofitMeasuresApplicable | array[string] | — | measure codes §6 | N | Valid upgrade paths |
| 15 | condensationRiskScreen | enum | — | pass, conditional, fail, not_assessed | N | Glaser screen result |

### 5.4 Glazed/Door Assembly Fields (windows, doors, rooflights)

| # | Field | Type | Unit | Allowed Values | Req | Description |
|---|---|---|---|---|---|---|
| 1 | assemblyID / name / elementClass | — | — | as §5.3 (window, door, rooflight) | Y | Identity |
| 2 | glazingType | enum | — | single, double_air, double_lowE_argon, triple_lowE_argon, vacuum, secondary | Cond | Glazed units |
| 3 | uValueWindow | decimal | W/m²K | 0.4…6.0 | Y | Whole-element Uw/Ud |
| 4 | gValue | decimal | — | 0…0.9 | Cond | Solar factor (glazed) |
| 5 | lightTransmittance | decimal | — | 0…0.95 | N | τv |
| 6 | frameMaterial | enum | — | timber, uPVC, aluminium, alu_clad_timber, steel, composite | Y | Frame |
| 7 | frameFraction | decimal | — | 0.1…0.5 | N | Frame area share |
| 8 | openableFraction | decimal | — | 0…1 | N | For natural ventilation models |
| 9 | shadingDevice | enum | — | none, internal_blind, external_blind, brise_soleil, shutters, overhang | N | Shading |
| 10 | airtightnessClass | enum | — | EN 12207 class 1–4 | N | Joinery airtightness |

### 5.5 Assembly Instance Fields (binding to building)

| # | Field | Type | Unit | Allowed Values | Req | Description |
|---|---|---|---|---|---|---|
| 1 | instanceID | string | — | `EIG-FAB-INS-[0-9]{7}` | Y | ID |
| 2 | assemblyID | string | — | valid assembly | Y | Assembly used |
| 3 | buildingID | string | — | EIG-BLD | Y | Building |
| 4 | boundingSpaceIDs | array[string] | — | EIG-SPC IDs | N | Spaces bounded |
| 5 | area | decimal | m² | >0 | Y | Net element area |
| 6 | orientation | decimal | ° from N | 0…359.9 or null | Cond | External vertical elements |
| 7 | tilt | decimal | ° | 0…180 | N | 90=vertical |
| 8 | conditionClass | enum | — | good, fair, poor, failed | Y | Survey condition |
| 9 | installYear | integer | yr | ≥ construction year | N | When installed |
| 10 | surveyMethod | enum | — | assumed_era_default, visual_survey, drawings, intrusive_survey, thermography, in_situ_u_measurement | Y | Evidence basis |
| 11 | retrofitHistory | array[object] | — | {measureCode, year, costActual €} | N | Applied measures |

## 6. Controlled Vocabularies

Retrofit measure codes (complete set): CWI (cavity wall insulation), EWI (external wall insulation), IWI (internal wall insulation), ATT_INS (attic/ceiling insulation), RAFTER_INS, FLAT_ROOF_INS, FLOOR_INS_SUSPENDED, FLOOR_INS_SOLID, WINDOW_UPGRADE_DOUBLE, WINDOW_UPGRADE_TRIPLE, DOOR_UPGRADE, AIRTIGHTNESS_WORKS, THERMAL_BRIDGE_REMEDIATION, SECONDARY_GLAZING (heritage). Junction types per SEAI/ACD register. All enums governed via (82).

## 7. Applicability Rules

- EN_ISO_13370 method mandatory for ground_contact floors.
- gValue required only for glazed assemblies; psiValueRegister only for external assemblies.
- deemed U-values permitted only where surveyMethod=assumed_era_default and constructionEraTypical matches the building's era band; deemed values flag downstream model confidence as degraded.
- Heritage buildings (02 heritageClass≠none): EWI excluded; SECONDARY_GLAZING and IWI conditionally applicable subject to conservation review.
- Embodied carbon fields required for any assembly entering retrofit option comparison where the objective set (58) includes embodied carbon.

## 8. Validation Rules

- U-value consistency: |declared − Σ-layer-calculated| ≤ 15% unless measured_in_situ.
- Layer stack ordered outside→inside; exactly one airtightness layer position recorded where strategy defined.
- Σ instance areas per element class plausibly ≤ envelope area (02 §5.4.14) × 1.05.
- gValue, frameFraction, decrementFactor within stated ranges; kappa consistent with mass class (02) within one class step.
- Window Uw ≥ centre-pane Ug (where both held).
- conditionClass=failed forces inclusion in retrofit candidate set.
- Every external instance has orientation (vertical) or tilt (non-vertical).

## 9. Relationships to Other Catalogues

Upstream: Building (02), Space (04), Building Classification (01 — era defaults). Lateral: Asset (11 — e.g. dynamic shading as asset), Standard (94 — EN ISO 6946/13370/10456, EN 13501). Downstream: Model (44 — RC parameter derivation, DEAP/SAP engines, simulation), Feature (41), KPI (57 — heat loss indicator), Synthetic Data (42 — fabric sampling by era), Scenario (89 — retrofit scenarios), Carbon (28 — embodied), Decision Intelligence (60 — retrofit option ranking).

## 10. Benchmarking Requirements

Benchmarks: U-value vs regulation backstop and era-typical per element class; heat loss indicator (W/K/m²) vs cohort; airtightness q50 vs era; retrofit cost €/m² per measure vs SEAI grant assumptions. Cohorts: era band × element class × dwelling/building type.

## 11. Dataset Requirements

Fabric survey dataset per building (instances with evidence basis); era-default libraries (TABULA/DEAP defaults) registered as reference datasets (38); in-situ U-value measurement series where performed; thermography artefacts as linked evidence (39 metadata).

## 12. Feature Requirements

Features (41): whole-building heat loss coefficient HLC (W/K) from Σ U·A + Σ ψ·L + ventilation term; fabric heat loss share; glazing-to-wall ratio; mass-weighted kappa; worst-element flag; retrofit headroom (current vs best-feasible U per element).

## 13. Model Requirements

Supplies: DEAP/BER engine inputs (complete element schedule), RC model R derivation, detailed simulation constructions, retrofit option models (measure → ΔU → ΔkWh → € via 44 + 28 + grants), condensation screening. Any model needing an element property absent here must trigger survey workflow, not assume silently.

## 14. KPI Requirements

KPIs: HLC (W/K), fabric energy efficiency (kWh/m²/yr fabric-attributable), BER fabric sub-score trajectory, retrofit measure realised vs predicted saving (%), condensation incident count.

## 15. Response Derivative Requirements

Fabric conditions thermal RDs: coast-down RDs depend on HLC and kappa; setpoint-modulation RDs on decrement properties. RD records must snapshot HLC and mass class at estimation; post-retrofit, affected RDs are invalidated and re-estimated (rule enforced via 52).

## 16. Decision Requirements

Retrofit decisions (60): option set generated from retrofitMeasuresApplicable minus heritage exclusions; ranked on multi-objective (cost, kWh, carbon incl. embodied, comfort); failed-condition instances escalate priority. Operational decisions: pre-heating viability gated on HLC and τ.

## 17. Ontology Mapping

Classes: FabricMaterial, FabricLayer, FabricAssembly, GlazedAssembly, AssemblyInstance, ThermalBridge, RetrofitMeasure.
Object properties: madeOfMaterial, hasLayer (ordered), instanceOfAssembly, boundsSpace, partOfBuilding, upgradableBy, evidencedBy.
Datatype properties: materialID, thermalConductivity, density, specificHeatCapacity, thickness, uValue, gValue, kappaValue, psiValue, area, orientation, conditionClass, embodiedCarbonA1A3.
External alignment: ifcOWL IfcMaterialLayerSet/IfcWall etc., SAREF4BLDG building objects, EN ISO 10456 material identifiers (via 81).

## 18. Knowledge Graph Mapping

Nodes: Material, Layer, Assembly, AssemblyInstance, Building, Space, RetrofitMeasure.
Relationships: HAS_LAYER {order}, USES_MATERIAL, INSTANCE_OF, BOUNDS, PART_OF, UPGRADABLE_BY {ΔU, indicativeCost}, RETROFITTED_WITH {year, cost}, EVIDENCED_BY {surveyMethod}.

## 19. Digital Twin Mapping

Static twins carry the full instance register (geometry-linked where geometryRef exists); predictive twins derive RC parameters from assemblies; retrofit "what-if" twins clone the instance register and apply measure deltas; autonomous twins monitor in-situ U drift (where instrumented) against declared values and raise condition alerts.

## 20. Governance

Roles: Catalogue Owner, Fabric/Building-Physics Domain Expert, BER Assessor (registered, for DEAP-facing data), Conservation Officer (heritage applicability), Validation Reviewer, Version Approver. Era-default libraries updated only on national methodology releases (DEAP/TABULA versions pinned).

## 21. Versioning

Materials/assemblies semver; instances carry survey-event audit trail. DEAP/SAP methodology version pinned per assessment record. Superseded era defaults retained for reproducing historical BERs.

## 22. Example Records

```text
AssemblyID: EIG-FAB-ASM-00031 | Name: 300mm Cavity Wall + Full-Fill Bead (retrofit)
Class: wall | Position: external | Method: EN_ISO_6946
Stack (out→in): brick 102.5mm → bonded bead 150mm (λ 0.033) → block 100mm → plaster 13mm
U: 0.21 W/m²K | κ: 145 kJ/m²K | Decrement: f 0.42, delay 9.1 h
ψ register: [lintel 0.30, jamb 0.05, sill 0.04 W/mK] | Condensation: pass
Era typical: 1971_1990 (pre-retrofit variant ASM-00030, U 0.55) | Measures applied: CWI
```

```text
AssemblyID: EIG-FAB-ASM-00077 | Name: Triple-Glazed Alu-Clad Timber Window
Class: window | Uw: 0.85 W/m²K | g: 0.50 | τv: 0.69
Frame: alu_clad_timber, fraction 0.28 | Openable: 0.6 | Airtightness: class 4
```

```text
InstanceID: EIG-FAB-INS-0021045 | Assembly: EIG-FAB-ASM-00031
Building: EIG-BLD-000877 | Bounds: [EIG-SPC-0007802, ...] | Area: 212 m²
Orientation: 270° | Condition: good | Survey: in_situ_u_measurement (U_meas 0.23)
Retrofit history: [{CWI, 2024, €4,800}]
```

## 23. Completion Criteria

Complete when material→layer→assembly→instance levels are fully fielded with EN-standard methods cited; deemed-value era libraries registered; retrofit measure vocabulary closed; validation rules enforce U-value, area and ordering consistency; EPC/BER, retrofit and simulation model requirements traceable to fields; ontology/KG/twin/governance/versioning populated; ≥3 examples spanning opaque, glazed and instance levels.
