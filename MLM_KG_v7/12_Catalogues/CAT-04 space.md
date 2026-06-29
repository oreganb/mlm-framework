---
type: catalogue
ontology: EIG-MLM
catalogue_no: 4
catalogue_id: EIG-CAT-004
entity: space
band: physical
status: active
tags: [eig-mlm, catalogue, band:physical, entity:space]
aliases: ["EIG-CAT-004", "Catalogue 04", "space"]
---

# CAT-04 — Space

> **EIG-CAT-004** · band: **Physical & Spatial Stack** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Space Catalogue

## 1. Definition

The Space Catalogue defines every spatial entity within buildings used by the EIG MLM framework: floors, rooms, zones, thermal zones, electrical zones and functional zones. Spaces are the resolution at which thermal models simulate, sensors are located, comfort is measured, occupancy is differentiated and zone-level control acts. Every spatial entity referenced by any digital twin must exist here.

## 2. Scope

Applies to: digital twin spatial structure, thermal zoning for simulation (RC and detailed), sensor and device placement (36), zone-level control strategies (62), space-level occupancy overrides (03), comfort KPI computation per zone, electrical sub-metering topology, lighting/HVAC zone mapping, space utilisation analytics.

Out of scope: building-level properties (02), fabric assemblies bounding spaces (05 — referenced), outdoor spaces beyond curtilage (08).

## 3. Classification Structure

```text
Space Class:        floor | room | zone
Zone Type (if zone): thermal | electrical | functional | lighting | ventilation | security | fire
Space Function:      EIG-SPC-FUN-xx (controlled list, §6)
```

## 4. Hierarchy

```text
Building (EIG-BLD)
 └─ Floor (EIG-SPC, class=floor)
     └─ Room (class=room)            [physical, wall-bounded]
Zones (class=zone) are overlays: a zone aggregates rooms or partial rooms,
and one room may belong to multiple zones of different types
(e.g. thermal zone TZ-2 ∧ electrical zone DB-3 ∧ functional zone "Ward A").
```

Composition rules: every room belongs to exactly one floor; every conditioned room belongs to exactly one thermal zone; every room belongs to exactly one electrical zone (supply topology); functional zones may overlap.

## 5. Field Groups & Fields

### 5.1 Common Space Fields (all classes)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | spaceID | string | — | `EIG-SPC-[0-9]{7}` | Y | Unique ID |
| 2 | spaceName | string | — | ≤120 chars | Y | Name/number |
| 3 | buildingID | string | — | EIG-BLD ID | Y | Parent building |
| 4 | spaceClass | enum | — | floor, room, zone | Y | Class |
| 5 | parentSpaceID | string | — | spaceID or null | Cond | Floor for rooms; null for floors/zones |
| 6 | spaceFunction | enum | — | §6 function list | Y | Functional use |
| 7 | floorArea | decimal | m² | >0 | Y | Net internal area |
| 8 | volume | decimal | m³ | >0 | Y(room/zone) | Internal volume |
| 9 | ceilingHeight | decimal | m | >0 | N | Average |
| 10 | conditioned | boolean | — | true/false | Y | Heated/cooled flag |
| 11 | occupiable | boolean | — | true/false | Y | Designed for occupancy |
| 12 | occupancyProfileOverrideID | string | — | EIG-OCC ID or null | N | Space-level profile (03) |
| 13 | geometryRef | string | — | IFC GUID / GeoJSON ref / null | N | Geometry pointer |
| 14 | boundingAssemblies | array[string] | — | EIG-FAB assembly IDs | N | Fabric boundaries (05) |
| 15 | adjacencies | array[object] | — | {adjacentSpaceID, boundaryType: enum(internal_wall, floor_ceiling, external, ground)} | N | Thermal adjacency graph |
| 16 | status / version / owner / dates / governanceStatus | — | — | standard lifecycle block | Y | Lifecycle |

### 5.2 Floor Fields

| # | Field | Type | Unit | Allowed Values | Req | Description |
|---|---|---|---|---|---|---|
| 1 | floorLevel | integer | — | −10…200 (0=ground) | Y | Level index |
| 2 | floorToFloorHeight | decimal | m | >0 | N | Structural height |
| 3 | grossFloorAreaLevel | decimal | m² | >0 | Y | GFA of level |

### 5.3 Room Fields

| # | Field | Type | Unit | Allowed Values | Req | Description |
|---|---|---|---|---|---|---|
| 1 | roomNumber | string | — | ≤20 chars | N | Door number |
| 2 | designOccupancyRoom | integer | persons | ≥0 | N | Capacity |
| 3 | externalWallExposure | decimal | m² | ≥0 | N | External envelope area |
| 4 | glazedArea | decimal | m² | ≥0 | N | Window area |
| 5 | orientationPrimary | decimal | ° from N | 0…359.9 or null | N | Main façade azimuth |
| 6 | dhwOutletsPresent | boolean | — | true/false | Y | Hot water draw point |
| 7 | wetRoom | boolean | — | true/false | Y | Moisture class |

### 5.4 Thermal Zone Fields

| # | Field | Type | Unit | Allowed Values | Req | Description |
|---|---|---|---|---|---|---|
| 1 | memberSpaceIDs | array[string] | — | room spaceIDs | Y | Constituent rooms |
| 2 | hvacSystemAssetID | string | — | EIG-AST ID | Y | Serving system |
| 3 | terminalUnitAssetIDs | array[string] | — | EIG-AST IDs | N | Terminal units |
| 4 | heatingSetpoint / coolingSetpoint | decimal | °C | 5…35 | Y | Zone setpoints |
| 5 | setbackSetpointHeating / Cooling | decimal | °C | 5…35 | N | Unoccupied setbacks |
| 6 | comfortBandLower / Upper | decimal | °C | within building band | Y | Zone comfort band |
| 7 | thermalCapacitanceEstimate | decimal | kWh/K | >0 | N | Lumped C (RC model) |
| 8 | thermalResistanceEstimate | decimal | K/kW | >0 | N | Lumped R to ambient |
| 9 | timeConstantEstimate | decimal | h | >0 | N | τ = RC (derivable) |
| 10 | zoneAirSensorDeviceIDs | array[string] | — | EIG-DEV IDs | N | Temp/RH/CO₂ sensors |
| 11 | controllableFlag | boolean | — | true/false | Y | Independent control possible |
| 12 | flexibilityEligible | boolean | — | true/false | Y | May participate in thermal flex |
| 13 | criticalEnvironment | boolean | — | true/false | Y | e.g. cleanroom, ward — blocks flex |
| 14 | designVentilationRate | decimal | l/s/person or ACH | >0 | N | Ventilation basis (state unit in subfield ventRateUnit) |

### 5.5 Electrical Zone Fields

| # | Field | Type | Unit | Allowed Values | Req | Description |
|---|---|---|---|---|---|---|
| 1 | distributionBoardRef | string | — | board label ≤40 chars | Y | DB identity |
| 2 | parentBoardZoneID | string | — | spaceID(zone) or null | N | Supply topology parent |
| 3 | subMeterDeviceID | string | — | EIG-DEV ID or null | N | Metering device |
| 4 | memberSpaceIDs | array[string] | — | room spaceIDs | Y | Supplied rooms |
| 5 | circuitRegister | array[object] | — | {circuitRef, loadType: enum(lighting, small_power, hvac, dhw, process, ev, life_safety), ratedCurrent A, sheddable: boolean} | Y | Circuit inventory |
| 6 | lifeSafetyCircuitsPresent | boolean | — | true/false | Y | Never-shed flag at zone level |

### 5.6 Functional / Other Zone Fields

| # | Field | Type | Allowed Values | Req | Description |
|---|---|---|---|---|---|
| 1 | zoneType | enum | thermal, electrical, functional, lighting, ventilation, security, fire | Y | Overlay type |
| 2 | memberSpaceIDs | array[string] | room spaceIDs | Y | Members |
| 3 | functionalPurpose | string | ≤200 chars | Cond | For functional zones |
| 4 | controlGroupRef | string | BMS group ref | N | Control system grouping |

## 6. Controlled Vocabularies

Space functions (EIG-SPC-FUN, complete list): office_open, office_cellular, meeting_room, circulation, stairs_lift, toilets, kitchen_tea, commercial_kitchen, dining, retail_floor, storage, plant_room, server_room, electrical_room, workshop, laboratory, cleanroom, classroom, lecture_theatre, library_space, ward, treatment_room, operating_theatre, waiting_area, bedroom, living_space, bathroom, utility_room, gym_space, pool_hall, atrium, reception, loading_bay, parking_internal, data_hall, telecom_room, cold_store_space, growing_area, livestock_space, worship_hall, auditorium, archive_space, cell_custody, external_canopy.

boundaryType, loadType, zoneType, spaceClass enums as in §5; governed via (82).

## 7. Applicability Rules

- Thermal-zone fields apply only where conditioned=true members exist.
- criticalEnvironment=true forces flexibilityEligible=false (auto-rule).
- occupancyProfileOverrideID permitted only where spaceFunction differs materially from building function (validated by domain expert).
- Volume required for rooms and thermal zones (RC models); optional for floors.
- Circuit register mandatory before any electrical-zone shed action is authorised (61).

## 8. Validation Rules

- Σ room floorArea per floor ≤ grossFloorAreaLevel × 1.02.
- Σ thermal zone areas of conditioned rooms = Σ conditioned room areas (full partition, no overlap).
- Every conditioned room appears in exactly one thermal zone; every room in exactly one electrical zone.
- Adjacency graph symmetric: A adjacent B ⇒ B adjacent A with same boundaryType.
- comfort band within building band (02); setbacks outside comfort band by ≥1 °C.
- timeConstantEstimate ≈ R×C within ±10% where all three present.
- Life-safety circuits sheddable=false invariably.
- spaceID unique; parent chains acyclic; zone members reference rooms in the same building.

## 9. Relationships to Other Catalogues

Upstream: Building (02), Fabric (05), Occupancy (03). Lateral: Asset (11 — serving systems), Device (36 — sensors/meters), Schedule (24). Downstream: Model (44 — zone thermal models), Feature (41), KPI (57 — zone comfort), Control Strategy (62), Action (61 — zone-scoped actions), Digital Twin (32 — spatial skeleton), Data (38 — zone-level series), RD (52 — zone thermal RDs), Synthetic Data (42).

## 10. Benchmarking Requirements

Zone-level benchmarks: W/m² lighting and small power by space function; comfort compliance % by zone; ventilation rate vs design; space utilisation (occupied hours × density vs capacity). Cohorts keyed by spaceFunction.

## 11. Dataset Requirements

Per controllable thermal zone: zone air temperature series (≥15min), setpoint series, valve/terminal state where available; per metered electrical zone: interval kWh. Registered in (38) with spaceID lineage. Synthetic twins (42) must generate per-zone series respecting adjacency thermal coupling.

## 12. Feature Requirements

Features (41): zone temperature deviation from setpoint, zone thermal time constant, solar-exposed glazing area × orientation factor, occupancy-weighted zone comfort, electrical zone load shares by loadType, sheddable capacity per zone (kW).

## 13. Model Requirements

Zone RC thermal models require: volume, C, R (or estimation from fabric 05 + mass class), adjacency graph, internal gains (03 density), HVAC capacity (11). Detailed simulation requires geometryRef + boundingAssemblies. Electrical disaggregation models require circuitRegister. Missing C/R triggers estimation workflow, never silent defaults.

## 14. KPI Requirements

KPIs: zone comfort compliance %, zone kWh/m² by loadType, ventilation adequacy (CO₂ < threshold % of occupied hours), shed capacity delivered vs declared, space utilisation %.

## 15. Response Derivative Requirements

Zone thermal RDs: ∂T_zone/∂P_hvac, ∂P/∂setpoint, coast-down duration per °C (function of τ). RD records cite spaceID, τ estimate, occupancy state, season. Electrical RDs: sheddable kW per zone with restoration profile.

## 16. Decision Requirements

Action scoping: every control action (61) targets a spaceID (zone); decision intelligence (60) filters eligible zones by controllableFlag, flexibilityEligible, criticalEnvironment, and occupancy state; life-safety circuits excluded structurally.

## 17. Ontology Mapping

Classes: Space, Floor, Room, Zone, ThermalZone, ElectricalZone, FunctionalZone, Adjacency, Circuit.
Object properties: partOfBuilding, onFloor, memberOfZone, adjacentTo, boundedBy (FabricAssembly), servedBy (Asset), sensedBy (Device), suppliedFrom (ElectricalZone), hasCircuit.
Datatype properties: spaceID, spaceClass, spaceFunction, floorArea, volume, conditioned, heatingSetpoint, comfortBandLower/Upper, timeConstantEstimate, controllableFlag, criticalEnvironment.
External alignment: BOT (bot:Storey, bot:Space, bot:adjacentZone), Brick (brick:HVAC_Zone, brick:Room), ifcOWL IfcSpace/IfcZone, SAREF4BLDG (via 81).

## 18. Knowledge Graph Mapping

Nodes: Floor, Room, ThermalZone, ElectricalZone, FunctionalZone, Circuit, Device, Asset.
Relationships: PART_OF, MEMBER_OF, ADJACENT_TO {boundaryType}, BOUNDED_BY, SERVED_BY, SENSED_BY, SUPPLIED_FROM, HAS_CIRCUIT {loadType, sheddable}, OVERRIDES_OCCUPANCY.

## 19. Digital Twin Mapping

The space hierarchy is the spatial skeleton of every Building Twin: floors→rooms as structure, zones as control/simulation overlays. Twin fidelity: static twins need §5.1–5.3; operational twins bind zone sensors; predictive twins require thermal zone RC parameters; autonomous twins require controllableFlag mapping to actuator devices (36).

## 20. Governance

Roles: Catalogue Owner, Spatial Data Steward (per building/portfolio), HVAC Domain Expert (thermal zoning sign-off), Electrical Domain Expert (circuit register sign-off), Validation Reviewer, Version Approver. Re-zoning a live twin is a MAJOR building-record event requiring impact analysis on models and historical series continuity.

## 21. Versioning

Space records semver; zone membership changes are MINOR, re-partitioning thermal zones is MAJOR (breaks series continuity — old zone series archived under superseded zone IDs, never re-keyed).

## 22. Example Records

```text
SpaceID: EIG-SPC-0004471 | Class: zone (thermal) | Name: TZ-2 North Open Plan
Building: EIG-BLD-000142 | Members: 12 rooms, 1,140 m², 3,420 m³
HVAC: EIG-AST-002201 (AHU-2) | Terminals: 14 VAV boxes
Setpoints: 21/24 °C | Setbacks: 16/28 °C | Comfort band: 20–24.5 °C
C: 6.2 kWh/K | R: 1.9 K/kW | τ: 11.8 h | Sensors: 6 (temp/CO₂)
Controllable: true | Flex eligible: true | Critical: false | Version 1.3.0
```

```text
SpaceID: EIG-SPC-0004502 | Class: zone (electrical) | Name: DB-3 Level 2 East
Building: EIG-BLD-000142 | Board: DB-L2-E | Parent: EIG-SPC-0004490 (MDB)
Sub-meter: EIG-DEV-001882 | Members: 9 rooms
Circuits: [L2E-1 lighting 16A sheddable, L2E-4 small_power 32A sheddable,
L2E-7 hvac 32A sheddable, L2E-9 life_safety 10A NOT sheddable]
Life safety present: true | Version 1.1.0
```

```text
SpaceID: EIG-SPC-0007810 | Class: room | Name: Server Room 1.04
Building: EIG-BLD-000877 | Floor: level 1 | Function: server_room
Area 18 m² | Vol 49 m³ | Conditioned: true | Occupiable: false
Occupancy override: EIG-OCC-00007 (data_centre_unmanned)
Thermal zone: EIG-SPC-0007811 (TZ-SR, criticalEnvironment=true → flex ineligible)
```

## 23. Completion Criteria

Complete when every spatial entity class is fully fielded; the function vocabulary covers all portfolio space uses; partition/overlay rules are machine-validated; thermal and electrical zone parameters sufficient for RC modelling and shed decisions respectively; relationships/ontology/KG/twin/governance/versioning populated; ≥3 examples covering thermal, electrical and override cases.
