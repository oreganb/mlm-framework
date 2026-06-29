---
type: catalogue
ontology: EIG-MLM
catalogue_no: 13
catalogue_id: EIG-CAT-013
entity: utility_network
band: physical
status: active
tags: [eig-mlm, catalogue, band:physical, entity:utility_network]
aliases: ["EIG-CAT-013", "Catalogue 13", "utility_network"]
---

# CAT-13 — Utility Network

> **EIG-CAT-013** · band: **Physical & Spatial Stack** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Utility Network Catalogue

Catalogue ID prefix: **EIG-NET** · Catalogue number: 13 · Version 1.0 · Status: Active

---

## 1. Definition

The Utility Network Catalogue defines every utility network referenced by the EIG MLM framework — electricity, heat, cooling, gas, hydrogen, water and wastewater — including their topology (nodes, segments, connection points) and operational properties (capacities, constraints, losses, operating envelopes). It is the authority for how energy and water move between buildings, communities and the wider system, and for the network constraints that bound flexibility, trading and control decisions.

## 2. Scope

Networks from in-building boundary outward: LV/MV electricity distribution, transmission interfaces, district heating/cooling, gas distribution, hydrogen pipelines, potable water and wastewater networks. In-building distribution is asset territory (EIG-AC-DST); the boundary is the connection point. Granularity is model-driven: full nodal detail where constraint-aware optimisation runs (e.g. ODEON island feeder); zonal abstraction elsewhere.

## 3. Classification Structure

### 3.1 Network types (closed)

| Type ID | Network | Carrier (→ EIG-ECR) | Notes |
|---|---|---|---|
| NET-ELC | Electricity | electricity | LV 230/400 V, MV 10/20/38 kV, HV interface 110 kV+ |
| NET-DHN | District Heat | heat_lt / heat_ht | 2G–5G generations classified |
| NET-DCN | District Cooling | cooling | — |
| NET-GAS | Natural Gas | natural_gas | Distribution ≤ 4 bar, transmission interface |
| NET-H2 | Hydrogen | hydrogen | Dedicated or blended (blend % attribute) |
| NET-WTR | Potable Water | water | Energy-relevant via pumping |
| NET-WWT | Wastewater | wastewater | Energy-relevant via pumping/treatment; heat recovery |

### 3.2 Entity classes

| Class | Code | Definition |
|---|---|---|
| Network | NW | A named operated network of one type |
| NetworkZone | NZ | Operational/constraint zone within a network (feeder group, pressure zone, DH loop) |
| NetworkNode | NN | Topological node: substation, busbar, pumping station, pressure-reduction, plant, junction |
| NetworkSegment | NS | Edge between two nodes: line, cable, pipe |
| ConnectionPoint | CP | Boundary between network and a customer/building/site (MPRN/GPRN-bearing for ELC/GAS) |
| ConstraintRecord | NC | A declared operational constraint on zone/node/segment |

### 3.3 Voltage / pressure / temperature levels (per type, closed)

ELC: lv_230_400, mv_10kv, mv_20kv, mv_38kv, hv_110kv_plus. GAS: lp_below_75mbar, mp_75mbar_4bar, hp_above_4bar. DHN generation: gen2_steam_ht, gen3_pressurised_ht (>100 °C), gen4_lt (50–70 °C), gen5_ambient (10–25 °C, prosumer). H2: blend_pct (0–100) + pressure tier. WTR/WWT: pressure zone bands.

## 4. Hierarchy

```
Network (NW)
 └── NetworkZone (NZ)            [constraint & settlement granularity]
      ├── NetworkNode (NN) ──┐
      │                       ├── NetworkSegment (NS): fromNode → toNode
      ├── NetworkNode (NN) ──┘
      └── ConnectionPoint (CP) ──connectsTo──▶ EIG-BLD / EIG-SIT / EIG-CMU
ConstraintRecord (NC) ──appliesTo──▶ NZ | NN | NS
```

Topology is a directed multigraph per network; radial (typical LV, DH branches) or meshed (MV rings) — topologyClass attribute. Zones partition nodes; every CP belongs to exactly one zone.

## 5. Field Groups & Fields

### 5.1 Network (EIG-NET-NW-NNNN)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | networkID | string | — | EIG-NET-NW-NNNN | M | Unique ID |
| 2 | networkName | string | — | — | M | Name |
| 3 | networkType | enum | — | §3.1 | M | Type |
| 4 | operatorOrgID | string | — | EIG-ORG (ESB Networks, GNI, Uisce Éireann, EirGrid, ESCO…) | M | Operator |
| 5 | geoScope | array | — | EIG-GEO IDs | M | Served geography |
| 6 | regulatoryRegime | string | — | EIG-REG ID | M | Governing regulation |
| 7 | topologyClass | enum | — | radial, ring, meshed, point_to_point | M | Structure |
| 8 | dhGeneration / h2BlendPct / pressureTier | enum/dec/enum | — | §3.3 per type | C | Type-specific level |
| 9 | status/version/owner/dates/source | — | — | audit block | M | Audit |

### 5.2 NetworkZone (EIG-NET-NZ-NNNNN)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 10 | zoneID / zoneName / networkID | — | — | — | M | Identity + parent |
| 11 | zoneFunction | enum | — | feeder_group, pressure_zone, dh_loop, settlement_zone, constraint_zone, island_zone | M | Role |
| 12 | firmCapacity | decimal | kW / m³/h | > 0 | M | Firm supply capacity of zone |
| 13 | peakRecorded | decimal | same | ≥ 0 | C | Historical peak |
| 14 | headroomCurrent | decimal | same | — | C | firmCapacity − peak (signed; negative = constrained) |
| 15 | constraintStatus | enum | — | unconstrained, seasonal, constrained, critical | M | DSO-style status |
| 16 | connectionPointCount | integer | count | ≥ 0 | M | CPs in zone |
| 17 | dgPenetrationPct | decimal | % | 0–500 | C (ELC) | Distributed generation vs min load |
| 18 | flexProcurementZoneFlag | boolean | — | — | M (ELC) | Eligible for local flex procurement (links EIG-FS) |

### 5.3 NetworkNode (EIG-NET-NN-NNNNNN)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 19 | nodeID / nodeName / zoneID | — | — | — | M | Identity + zone |
| 20 | nodeFunction | enum | — | substation_primary, substation_secondary, busbar, junction, dh_plant, dh_substation, pump_station, pressure_reduction, storage_node, generation_node, treatment_works, mixing_node | M | Role |
| 21 | lat / lon | decimal(9,6) | deg | WGS84 | M | Location |
| 22 | levelIn / levelOut | enum | — | §3.3 values | C (transforming nodes) | e.g. mv_10kv → lv_230_400 |
| 23 | ratedThroughput | decimal | kVA / kW / m³/h | > 0 | M | Node rating |
| 24 | redundancy | enum | — | none, n_1, n_2 | M | Security standard |
| 25 | linkedAssetIDs | array | — | EIG-AST (NWK category instances) | C | Physical asset records backing this node |
| 26 | monitoringLevel | enum | — | none, scada, smart_secondary, full_pmu | M | Observability |

### 5.4 NetworkSegment (EIG-NET-NS-NNNNNN)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 27 | segmentID / fromNodeID / toNodeID / zoneID | — | — | — | M | Edge identity |
| 28 | segmentMedium | enum | — | overhead_line, underground_cable, preinsulated_pipe_pair, steel_pipe, pe_pipe, gravity_sewer, rising_main | M | Physical medium |
| 29 | lengthM | decimal | m | > 0 | M | Length |
| 30 | capacityRating | decimal | A / kW / m³/h | > 0 | M | Thermal/hydraulic rating |
| 31 | impedanceOhmPerKm | object | Ω/km | {r, x} | C (ELC) | For power flow |
| 32 | diameterMM / insulationClass | dec/enum | mm | — / series_1/2/3 | C (pipes) | Hydraulic + loss params |
| 33 | designFlowTempC / designReturnTempC | decimal | °C | — | C (DHN/DCN) | Temperature regime |
| 34 | lossRate | decimal | %/km or W/m | ≥ 0 | M | Distribution losses |
| 35 | installYear / conditionGrade | int/enum | yr / A–E | — | C | Age & condition |
| 36 | normallyOpenFlag | boolean | — | — | C (ELC switchable) | Backfeed/reconfig capability |

### 5.5 ConnectionPoint (EIG-NET-CP-NNNNNN)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 37 | cpID / zoneID / nearestNodeID | — | — | — | M | Identity |
| 38 | connectsToType + connectsToID | enum+string | — | building/site/community + ID | M | Customer side |
| 39 | registryID | string | — | MPRN (11-digit) / GPRN / DH customer ID / water account | M (ELC/GAS) | Market registry key — MUST match EIG-BLD energy block |
| 40 | connectionCapacity | decimal | kVA / kW / m³/h | > 0 | M | MIC/MEC or equivalent (import) |
| 41 | exportCapacity | decimal | kVA / kW | ≥ 0 | C (ELC) | MEC export |
| 42 | connectionPhase | enum | — | 1p, 3p, n/a | C (ELC) | Phases |
| 43 | meterAssetID | string | — | EIG-AST fiscal meter instance | M | Fiscal meter |
| 44 | supplierOrgID | string | — | EIG-ORG | C | Current supplier |
| 45 | deMinimisGenFlag / microGenRegistered | boolean | — | — | C (ELC) | Microgen status |

### 5.6 ConstraintRecord (EIG-NET-NC-NNNNN)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 46 | constraintID / appliesToType + appliesToID | — | — | NZ/NN/NS + ID | M | Target |
| 47 | constraintKind | enum | — | thermal_capacity, voltage_band, pressure_band, flow_temp_min, fault_level, export_limit, ramp_limit, n_1_security | M | Kind |
| 48 | limitValue + unit | decimal+str | — | — | M | The limit |
| 49 | direction | enum | — | upper, lower, band | M | Bound type |
| 50 | temporalScope | string | — | always / EIG-SCH ID (seasonal/peak windows) | M | When active |
| 51 | severity | enum | — | advisory, firm, statutory | M | Enforcement class |
| 52 | violationConsequence | string | — | text + penalty ref (EIG-PEN) | M | What breach means |
| 53 | sourceAuthority | enum | — | dso, tso, operator, regulator, internal_model | M | Who declared it |

## 6. Controlled Vocabularies

Network types (7, closed). Entity classes (6, closed). Level vocabularies per type (§3.3, closed). zoneFunction, nodeFunction, segmentMedium, constraintKind, severity, monitoringLevel, redundancy — closed per §5. conditionGrade A–E (A new/excellent … E end-of-life).

## 7. Applicability Rules

1. Every EIG-BLD building with an MPRN MUST have exactly one NET-ELC ConnectionPoint whose registryID equals the building's MPRN; same for GPRN/GAS and DH customer IDs. CP is the authoritative network-side record; building block mirrors it.
2. Constraint-aware optimisation models may only dispatch flexibility within zones where topology detail ≥ node-level and constraints are declared; zonal-only areas restrict to volume-capped advisory flexibility.
3. Island zones (zoneFunction=island_zone, e.g. ODEON Inis Mór feeder) automatically propagate constrained-grid context (EIG-CTX) to all connected buildings, assets and RDs.
4. flexProcurementZoneFlag=true is prerequisite for local flexibility service products (29) in that zone.
5. Wastewater/water networks apply only to energy/pumping models and heat-recovery assessment; no trading applicability.

## 8. Validation Rules

| # | Rule |
|---|---|
| V1 | All IDs unique, pattern-valid; zone→network, node→zone, segment→nodes, CP→zone references resolve |
| V2 | Segment fromNode ≠ toNode; both in same network; graph connected per zone (or declared islands) |
| V3 | radial topologyClass ⇒ zone graph acyclic (normallyOpen segments excluded from cycle check) |
| V4 | CP.registryID format-valid (MPRN 11 digits) and unique per network type |
| V5 | CP.connectionCapacity ≤ serving node ratedThroughput; Σ CP capacities vs firmCapacity logged (diversity factor reported, not hard-failed) |
| V6 | headroomCurrent = firmCapacity − peakRecorded (recomputed, not stored stale > 1 release) |
| V7 | Transforming nodes require levelIn ≠ levelOut; non-transforming require equality or null |
| V8 | DHN segments require flow/return temps consistent with network dhGeneration |
| V9 | Every constraint limitValue unit consistent with constraintKind; temporalScope schedule resolves |
| V10 | Building↔CP registry cross-check (Phase 14): zero mismatched MPRN/GPRN pairs |

## 9. Relationships to Other Catalogues

| Direction | Catalogue | Relationship |
|---|---|---|
| ▶ depends on | geographic (08) | geoScope, node coordinates |
| ▶ depends on | organisation (15) | operators, suppliers |
| ▶ depends on | asset (11) | linkedAssetIDs (NWK instances), meterAssetID |
| ▶ depends on | energy_carrier (19) | carrier binding per type |
| ▶ depends on | schedule (24) | constraint temporal scopes |
| ◀ consumed by | building (02) | CP back-reference; MIC/MEC source of truth |
| ◀ consumed by | market (20), tariff (21), settlement (27) | zones as settlement/locational granularity; network charges |
| ◀ consumed by | flexibility_service (29), flexibility (30) | constraint zones define service need + delivery verification points |
| ◀ consumed by | response_derivative (52) | network context conditioning; locational RD aggregation |
| ◀ consumed by | decision (60–62) | constraints as hard bounds on action spaces |
| ◀ consumed by | community (86), district (87) | shared network context |
| ◀ consumed by | digital_twin (32) | network twins |

## 10. Benchmarking Requirements

Network benchmarks (operator-facing): losses %/km by segment medium & age, availability (SAIDI/SAIFI-style per zone), headroom utilisation, DH linear heat density (MWh/m·yr), pumping energy intensity (kWh/m³). Cohorts by (networkType × medium × vintage); minimum n = 10 segments/zones; same release machinery as EIG-ABM (§12 pattern shared).

## 11. Dataset Requirements

Topology snapshots versioned (graph as node/edge Parquet tables under `EIG_NET_ROOT/topology/<networkID>/<version>/`). Constraint register exported with every snapshot. Zone telemetry (feeder loads, DH flow temps) ingested via data_source (37) keyed on zoneID/nodeID. Synthetic networks (SYNTH_GRID_IE pattern) flagged synthetic=true, never mixed with real topology in operational models.

## 12. Feature Requirements

FEA-NET-*: zone headroom ratio, constraint proximity (load ÷ limit, max over active constraints), electrical distance to primary substation, voltage level, DG penetration, island flag, DH flow temp, loss-adjusted delivery factor, n-1 secure flag, time-to-constraint forecast (derived).

## 13. Model Requirements

Power-flow / hydraulic models bind to topology snapshots by version; results invalid across snapshot boundaries. Constraint forecasting models (zone load vs limit) are mandatory for any zone marked constrained/critical that hosts enrolled flexibility. Network state estimation models declare monitoringLevel prerequisites.

## 14. KPI Requirements

Zone KPIs: peak utilisation %, constraint violation hours, losses %, flexibility activated within zone (kWh), deferred reinforcement value (EUR). Network KPIs roll up zones weighted by energy delivered.

## 15. Response Derivative Requirements

Locational RDs: building/asset RDs aggregate to zone level for DSO-facing services; aggregation MUST respect electrical topology (only CPs in the constrained zone count toward zone response). Network constraints condition RDs (response under constraint-active vs inactive periods differ). Island zones: RD reliability scoring weights local coherence higher (no external balancing).

## 16. Decision Requirements

All dispatch decisions check active constraints for affected zones/nodes/segments before action issuance; statutory-severity constraints are inviolable hard bounds; firm constraints violable only via operator-approved override workflow (64); advisory constraints enter objective as penalties (67). Decision logs record constraint state at decision time.

## 17. Ontology Mapping

Classes: UtilityNetwork, NetworkZone, NetworkNode, NetworkSegment, ConnectionPoint, NetworkConstraint.
Object properties: hasZone, hasNode, hasSegment, fromNode, toNode, hasConnectionPoint, connectsTo (→ Building/Site/Community), constrainedBy, operatedBy, carriesCarrier, backedByAsset.
Datatype properties: networkID, firmCapacity, headroomCurrent, capacityRating, lossRate, registryID, limitValue, severity.
Alignment: CIM (IEC 61970/61968) for ELC (cim:Substation, cim:ACLineSegment, cim:EnergyConsumer ≈ CP); SAREF4ENER for flexibility interface; GeoSPARQL for geometry; district heating aligned to SEAS ElectricPowerSystemOntology patterns adapted per carrier.

## 18. Knowledge Graph Mapping

Nodes: Network, Zone, Node, Segment, ConnectionPoint, Constraint. Relationships: HAS_ZONE, IN_ZONE, CONNECTED_BY (node-[segment]-node modelled as relationship with properties or intermediate node for multigraph), SUPPLIES (CP→building), CONSTRAINED_BY, OPERATED_BY, BACKED_BY (→ asset). Graph algorithms supported: shortest electrical path, zone islanding detection, constraint impact radius.

## 19. Digital Twin Mapping

Network twins per zone (minimum) or full nodal (constraint-managed zones): state = loads/flows/temps/pressures at monitored points + estimated elsewhere; constraint margins live; what-if dispatch simulation against topology snapshot. Twin template EIG-DTT per network type. CP state mirrors into connected building twins.

## 20. Governance

Owner: EIG Catalogue Owner; operator liaison per network (data-sharing agreements via EIG-CON contracts). Topology changes: operator-confirmed, snapshot-versioned. Constraint register reviewed with operator quarterly; internal_model-sourced constraints clearly segregated from operator-declared. Registry IDs (MPRN/GPRN) handled under data-protection rules (metadata catalogue 39 sensitivity class).

## 21. Versioning

Topology snapshot versioning (immutable, dated); entity records semver; constraint records effective-dated (start/end) — historical decision audits reproduce the constraint set in force at decision time.

## 22. Example Records

```text
networkID: EIG-NET-NW-0003 | name: Inis Mór LV/MV Feeder Network | type: NET-ELC | operator: EIG-ORG-0003 (ESB Networks)
geoScope: [EIG-GEO-IE-DST-1893] | topologyClass: radial | status: active
zoneID: EIG-NET-NZ-00017 | zoneFunction: island_zone | firmCapacity: 1850 kW | peakRecorded: 1430 kW
headroomCurrent: 420 kW | constraintStatus: seasonal | connectionPointCount: 74 | flexProcurementZoneFlag: true
```

```text
nodeID: EIG-NET-NN-000211 | function: substation_secondary | zone: NZ-00017 | lat: 53.1278 | lon: -9.7044
levelIn: mv_10kv | levelOut: lv_230_400 | ratedThroughput: 400 kVA | redundancy: none | monitoringLevel: smart_secondary
```

```text
cpID: EIG-NET-CP-004388 | zone: NZ-00017 | connectsTo: building EIG-BLD-000872 (IE_B0009)
registryID: 10301234567 (MPRN) | connectionCapacity: 16 kVA | exportCapacity: 6 kVA | phase: 1p
meterAssetID: EIG-AST-006102 | microGenRegistered: true
```

```text
constraintID: EIG-NET-NC-00031 | appliesTo: NZ-00017 | kind: thermal_capacity | limit: 1850 kW | direction: upper
temporalScope: always | severity: firm | consequence: "load shed protocol; penalty ref EIG-PEN-NET-01"
sourceAuthority: dso
```

## 23. Completion Criteria

Complete when: all 7 network types schema-complete; every project-relevant network registered to required granularity (nodal for constraint-managed zones, zonal elsewhere); 100% building MPRN/GPRN ↔ CP reconciliation; constraint register operator-confirmed for all constrained zones; topology snapshots versioned and powering ≥ 1 network twin; zero unresolved graph integrity failures.
