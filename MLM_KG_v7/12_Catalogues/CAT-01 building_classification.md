---
type: catalogue
ontology: EIG-MLM
catalogue_no: 1
catalogue_id: EIG-CAT-001
entity: building_classification
band: physical
status: active
tags: [eig-mlm, catalogue, band:physical, entity:building_classification]
aliases: ["EIG-CAT-001", "Catalogue 01", "building_classification"]
---

# CAT-01 — Building Classification

> **EIG-CAT-001** · band: **Physical & Spatial Stack** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Building Classification Catalogue

## 1. Definition

The Building Classification Catalogue is the authoritative classification framework for every building entity in the EIG MLM framework. It defines the sector → type → subtype → sub-subtype hierarchy, function classifications, occupancy classifications, operational classifications and benchmark classifications against which all buildings, archetypes, benchmarks, datasets, models, KPIs and digital twins are anchored. It is the root catalogue of the dependency chain: no building, occupancy profile, archetype, benchmark cohort or building-level model may reference a classification not defined here.

> **v1.1 (2026-06-13) — representative residential sampling.** The classification taxonomy is unchanged, but the synthetic generator's *sampling prior* over residential subtypes is now anchored to the CSO 2022 national housing stock (*Urban and Rural Life in Ireland 2025*) instead of a uniform prior: detached 0.412, semi-detached 0.254, terraced 0.203, apartment/flat 0.131 (purpose-built 0.104 + converted 0.027). This is a generation-prior calibration recorded here for provenance; it changes which subtypes are drawn, not what subtypes exist.

## 2. Scope

Applies to: building registration, archetype assignment, benchmark cohort assignment, synthetic building generation, model applicability resolution, KPI normalisation (per-classification baselines), digital twin template selection, regulatory mapping (EPC/BER/TM46/NABERS/EN ISO 52000), portfolio aggregation, and classification-conditioned RL contexts.

Out of scope: properties of individual buildings (Building Catalogue, 02), spatial decomposition (Space Catalogue, 04), fabric components (Building Fabric Catalogue, 05).

## 3. Classification Structure

Four mandatory levels plus three orthogonal classification axes.

```text
Level 1: Sector          (EIG-BC-SEC-xx)
Level 2: Type            (EIG-BC-TYP-xxx)
Level 3: Subtype         (EIG-BC-SUB-xxxx)
Level 4: Sub-Subtype     (EIG-BC-SSU-xxxxx)   [optional where granularity exists]

Orthogonal axes (assignable to any node):
Axis A: Function Classification   (EIG-BC-FUN-xx)
Axis B: Occupancy Classification  (EIG-BC-OCL-xx)
Axis C: Operational Classification(EIG-BC-OPC-xx)
Axis D: Benchmark Classification  (EIG-BC-BMC-xx)
```

## 4. Hierarchy

### Sectors (Level 1)

| ID | Sector |
|---|---|
| EIG-BC-SEC-01 | Residential |
| EIG-BC-SEC-02 | Commercial |
| EIG-BC-SEC-03 | Industrial |
| EIG-BC-SEC-04 | Public Administration |
| EIG-BC-SEC-05 | Healthcare |
| EIG-BC-SEC-06 | Education |
| EIG-BC-SEC-07 | Hospitality & Leisure |
| EIG-BC-SEC-08 | Retail |
| EIG-BC-SEC-09 | Transport & Logistics |
| EIG-BC-SEC-10 | Agricultural |
| EIG-BC-SEC-11 | Data & Telecommunications |
| EIG-BC-SEC-12 | Mixed-Use |
| EIG-BC-SEC-13 | Cultural & Religious |
| EIG-BC-SEC-14 | Emergency & Defence |

### Types and Subtypes (Levels 2–3, complete enumeration)

**Residential (SEC-01)**
- TYP-101 Single-Family Dwelling → SUB: detached, semi-detached, terraced (mid), terraced (end), bungalow, dormer bungalow, cottage
- TYP-102 Multi-Family Dwelling → SUB: apartment block (low-rise ≤4 storeys), apartment block (mid-rise 5–9), apartment block (high-rise ≥10), duplex, maisonette, converted dwelling
- TYP-103 Communal Residential → SUB: student accommodation, nursing/care home, sheltered housing, hostel, barracks, convent/monastery residence
- TYP-104 Holiday Residential → SUB: holiday home, mobile/park home, serviced apartment

**Commercial (SEC-02)**
- TYP-201 Office → SUB: cellular office, open-plan office, headquarters, serviced/co-working, call centre
- TYP-202 Financial → SUB: bank branch, trading floor, insurance office
- TYP-203 Professional Services → SUB: legal, consultancy, design studio, broadcast/media studio

**Industrial (SEC-03)**
- TYP-301 Manufacturing → SUB: light manufacturing, heavy manufacturing, pharmaceutical/cleanroom, food & beverage processing, electronics/semiconductor fabrication
- TYP-302 Warehouse & Storage → SUB: ambient warehouse, chilled warehouse, cold store (frozen), automated fulfilment centre, bonded warehouse
- TYP-303 Utility & Process → SUB: water treatment, wastewater treatment, energy generation plant building, waste processing/recycling facility, workshop

**Public Administration (SEC-04)**
- TYP-401 Government Office → SUB: national government, local authority, agency office
- TYP-402 Civic → SUB: courthouse, library, community centre, town hall, prison

**Healthcare (SEC-05)**
- TYP-501 Hospital → SUB: acute hospital, teaching hospital, specialist hospital, psychiatric hospital
- TYP-502 Primary & Community Care → SUB: GP surgery/primary care centre, health clinic, dental practice, pharmacy with clinical services
- TYP-503 Laboratory & Research → SUB: clinical laboratory, biomedical research facility

**Education (SEC-06)**
- TYP-601 School → SUB: pre-school/creche, primary school, secondary school, special education school
- TYP-602 Higher & Further Education → SUB: university teaching building, university research building, lecture theatre complex, institute of technology building, training centre

**Hospitality & Leisure (SEC-07)**
- TYP-701 Accommodation → SUB: hotel (full-service), hotel (limited-service), guesthouse/B&B, resort
- TYP-702 Food & Beverage → SUB: restaurant, café, pub/bar, commercial kitchen (dark kitchen)
- TYP-703 Sport & Leisure → SUB: gym/fitness centre, swimming pool, sports hall, stadium/arena, cinema/theatre, GAA/community sports clubhouse

**Retail (SEC-08)**
- TYP-801 Food Retail → SUB: supermarket, convenience store, hypermarket
- TYP-802 Non-Food Retail → SUB: department store, high-street unit, retail warehouse, showroom (incl. automotive)
- TYP-803 Shopping Centre → SUB: enclosed mall, open retail park, market hall

**Transport & Logistics (SEC-09)**
- TYP-901 Passenger Terminal → SUB: airport terminal, rail station, bus station, ferry terminal
- TYP-902 Vehicle Facility → SUB: depot/garage, multi-storey car park, EV charging hub building, maintenance hangar

**Agricultural (SEC-10)**
- TYP-1001 Livestock → SUB: dairy parlour, poultry house, pig unit, cattle housing
- TYP-1002 Horticulture & Crop → SUB: glasshouse, vertical farm, grain store, mushroom house

**Data & Telecommunications (SEC-11)**
- TYP-1101 Data Centre → SUB: enterprise DC, colocation DC, hyperscale DC, edge DC/MEC facility
- TYP-1102 Telecoms → SUB: exchange building, transmission site building

**Mixed-Use (SEC-12)**
- TYP-1201 Mixed-Use Building → SUB: residential-over-retail, office-retail, live-work unit, mixed campus building

**Cultural & Religious (SEC-13)**
- TYP-1301 Cultural → SUB: museum, gallery, heritage/protected structure, archive
- TYP-1302 Religious → SUB: church, mosque, synagogue, temple, parish hall

**Emergency & Defence (SEC-14)**
- TYP-1401 Emergency Services → SUB: fire station, Garda/police station, ambulance base, coastguard station
- TYP-1402 Defence → SUB: military administration, military technical facility

Sub-subtypes (Level 4) are permitted where models require finer granularity (e.g. SUB hyperscale DC → SSU: air-cooled, liquid-cooled, hybrid-cooled) and must be registered here before use.

### Axis A — Function Classification

| ID | Function |
|---|---|
| EIG-BC-FUN-01 | Dwelling |
| EIG-BC-FUN-02 | Workplace |
| EIG-BC-FUN-03 | Production |
| EIG-BC-FUN-04 | Storage |
| EIG-BC-FUN-05 | Care provision |
| EIG-BC-FUN-06 | Teaching & learning |
| EIG-BC-FUN-07 | Assembly & events |
| EIG-BC-FUN-08 | Sales & display |
| EIG-BC-FUN-09 | Transit |
| EIG-BC-FUN-10 | Compute & switching |
| EIG-BC-FUN-11 | Worship & culture |
| EIG-BC-FUN-12 | Mixed function |

### Axis B — Occupancy Classification

| ID | Class | Definition |
|---|---|---|
| EIG-BC-OCL-01 | Continuous (24/7) | Occupied ≥95% of hours |
| EIG-BC-OCL-02 | Extended hours | 12–24 h/day, 6–7 days/week |
| EIG-BC-OCL-03 | Standard working hours | 8–12 h/day, 5 days/week |
| EIG-BC-OCL-04 | Intermittent scheduled | Predictable partial occupancy (e.g. schools, churches) |
| EIG-BC-OCL-05 | Intermittent unscheduled | Stochastic occupancy (e.g. holiday homes) |
| EIG-BC-OCL-06 | Unoccupied / unmanned | Process- or equipment-occupied only |
| EIG-BC-OCL-07 | Seasonal | Occupancy concentrated in defined seasons |

### Axis C — Operational Classification

| ID | Class | Definition |
|---|---|---|
| EIG-BC-OPC-01 | Critical continuous process | No interruption tolerated (hospitals, DCs, fabs) |
| EIG-BC-OPC-02 | Schedulable process | Operations shiftable within constraints |
| EIG-BC-OPC-03 | Comfort-led | HVAC driven by occupant comfort |
| EIG-BC-OPC-04 | Storage-condition-led | Driven by goods condition setpoints |
| EIG-BC-OPC-05 | Event-led | Operation driven by event calendar |
| EIG-BC-OPC-06 | Passive / low-services | Minimal building services |

### Axis D — Benchmark Classification

| ID | Class | External scheme anchor |
|---|---|---|
| EIG-BC-BMC-01 | CIBSE TM46 category (1–29) | TM46 |
| EIG-BC-BMC-02 | EPC category (non-domestic) | EN ISO 52000 / national EPC |
| EIG-BC-BMC-03 | BER dwelling category | SEAI DEAP |
| EIG-BC-BMC-04 | NABERS category | NABERS UK/AUS |
| EIG-BC-BMC-05 | Display Energy Certificate category | DEC |
| EIG-BC-BMC-06 | EIG internal cohort | Building Benchmark Catalogue (06) |

## 5. Field Groups & Fields

### 5.1 Classification Node Fields

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | classificationID | string | — | `EIG-BC-(SEC|TYP|SUB|SSU|FUN|OCL|OPC|BMC)-[0-9]{2,5}` | Y | Unique node identifier |
| 2 | classificationName | string | — | ≤120 chars | Y | Human-readable name |
| 3 | classificationLevel | enum | — | sector, type, subtype, sub_subtype, axis | Y | Hierarchy level |
| 4 | parentClassificationID | string | — | valid classificationID or null (sectors only) | Y | Parent node |
| 5 | classificationDescription | string | — | ≤1000 chars | Y | Scope definition incl. inclusions/exclusions |
| 6 | axisAssignments | array[string] | — | FUN/OCL/OPC/BMC IDs | N | Default axis values inherited by buildings |
| 7 | typicalFloorAreaMin | decimal | m² | ≥0 | N | Typical lower bound (for plausibility checks) |
| 8 | typicalFloorAreaMax | decimal | m² | > min | N | Typical upper bound |
| 9 | typicalEUImin | decimal | kWh/m²/yr | ≥0 | N | Typical energy use intensity lower bound |
| 10 | typicalEUImax | decimal | kWh/m²/yr | > min | N | Typical EUI upper bound |
| 11 | tm46Category | integer | — | 1–29 or null | N | TM46 mapping |
| 12 | epcCategory | string | — | national EPC category code | N | EPC mapping |
| 13 | berApplicable | boolean | — | true/false | Y | Whether SEAI BER applies |
| 14 | nabersCategory | string | — | NABERS category or null | N | NABERS mapping |
| 15 | defaultArchetypeIDs | array[string] | — | EIG-ARC IDs | N | Archetypes seeded from this node |
| 16 | defaultTwinTemplateID | string | — | EIG-DTT ID | N | Default digital twin template |
| 17 | applicableModelFamilies | array[string] | — | EIG-MOD series IDs | N | Model families valid for this node |
| 18 | flexibilityPotentialClass | enum | — | none, low, medium, high, very_high | Y | Prior on demand-side flexibility |
| 19 | classificationStatus | enum | — | draft, active, deprecated, merged | Y | Lifecycle status |
| 20 | supersededByID | string | — | classificationID or null | Cond | Required when status=merged/deprecated |
| 21 | version | string | — | semver `MAJOR.MINOR.PATCH` | Y | Node version |
| 22 | owner | string | — | governance role ID (EIG-ROL) | Y | Steward |
| 23 | createdDate / modifiedDate | date | — | ISO 8601 | Y | Lifecycle dates |
| 24 | governanceStatus | enum | — | proposed, approved, under_review, retired | Y | Governance state |

### 5.2 Applicability Matrix Fields (node × capability)

| # | Field | Type | Allowed Values | Req | Description |
|---|---|---|---|---|---|
| 1 | matrixEntryID | string | `EIG-BC-MTX-[0-9]{5}` | Y | Matrix row ID |
| 2 | classificationID | string | valid node | Y | Subject node |
| 3 | capabilityDomain | enum | occupancy_profile, benchmark_cohort, archetype, asset_class, model, kpi, flexibility_service, market, rd, twin_template | Y | What is being scoped |
| 4 | capabilityID | string | ID in target catalogue | Y | Target entity |
| 5 | applicability | enum | mandatory, applicable, conditional, not_applicable | Y | Applicability verdict |
| 6 | condition | string | machine-evaluable expression or null | Cond | Required when conditional |

## 6. Controlled Vocabularies

- classificationLevel: {sector, type, subtype, sub_subtype, axis}
- applicability: {mandatory, applicable, conditional, not_applicable}
- flexibilityPotentialClass: {none, low, medium, high, very_high}
- classificationStatus: {draft, active, deprecated, merged}
- governanceStatus: {proposed, approved, under_review, retired}
- All sector/type/subtype/axis enumerations in §4 are themselves controlled vocabularies; additions require governance approval (§20).

## 7. Applicability Rules

- Every building (Catalogue 02) must reference exactly one Level-3 subtype; Level 4 where defined.
- Axis B (occupancy) and Axis C (operational) assignments are mandatory at building level; defaults inherit from the classification node.
- Mixed-Use (SEC-12) buildings must additionally declare component classifications with floor-area shares summing to 100% (fields in Catalogue 02).
- tm46Category applies only to non-domestic nodes; berApplicable=true only for SEC-01 and residential components of SEC-12.
- typicalEUI bounds apply for plausibility validation only; they never constrain model outputs.

## 8. Validation Rules

- classificationID unique across all levels and axes; matches the regex in §5.1.
- Parent chain must resolve to a sector in ≤3 hops; no cycles.
- A subtype may not be active if its parent type is deprecated.
- status=merged/deprecated requires supersededByID resolving to an active node.
- typicalFloorAreaMax > typicalFloorAreaMin; typicalEUImax > typicalEUImin where both present.
- Every active Level-3 node must have ≥1 benchmark mapping (tm46Category, epcCategory, berApplicable=true, or nabersCategory) or an explicit EIG internal cohort (BMC-06).
- Every active Level-3 node must have a defaultTwinTemplateID before any twin instantiation referencing it.
- Matrix entries with applicability=conditional must have a non-null, parseable condition.

## 9. Relationships to Other Catalogues

- **Defines applicability for:** Building (02), Occupancy (03), Building Benchmark (06), Building Archetype (07), Asset Classification (10), Model (44), KPI (57), Flexibility Service (29), Digital Twin Template (33), Response Derivative (52).
- **Consumes:** Vocabulary (82), Taxonomy (83) for term governance; Standard (94) for external scheme references.
- **Constrains:** Synthetic Data (42) building generation; Scenario (89) building populations; Community (86)/District (87)/Site (88) composition rosters.

## 10. Benchmarking Requirements

Each Level-3 node must map to: (a) at least one external benchmark scheme (TM46/EPC/BER/NABERS/DEC) where one exists for the domain, and (b) an EIG internal cohort definition in Catalogue 06 specifying cohort dimensions (floor area band, occupancy class, climate zone) and minimum cohort size n≥10 for percentile publication.

## 11. Dataset Requirements

Datasets keyed by classification must carry classificationID at Level 3 minimum. Synthetic building generation (42) must sample within typicalFloorArea and typicalEUI bounds per node. National stock datasets (e.g. BER research tool, CSO census) must be mapped to this hierarchy via the Semantic Mapping Catalogue (81).

## 12. Feature Requirements

Features derivable from this catalogue: one-hot/ordinal encodings of sector/type/subtype; occupancy-class features; flexibilityPotentialClass prior; EUI-band features; mixed-use share vectors. All registered in the Feature Catalogue (41) with lineage to classificationID.

## 13. Model Requirements

Models must declare applicability against this hierarchy via the matrix (§5.2). Model selection (70) may not propose a model whose matrix verdict is not_applicable for the building's node. Cluster/archetype models (07) train within single Level-2 types unless cross-type training is explicitly approved.

## 14. KPI Requirements

KPIs normalised per classification (e.g. kWh/m²/yr by subtype) must reference the node used for normalisation. KPI thresholds may vary by node; the KPI Catalogue (57) stores per-node threshold overrides keyed by classificationID.

## 15. Response Derivative Requirements

RD priors (expected response magnitude, latency, rebound) are seeded per classification node via flexibilityPotentialClass and refined at building level. The RD Catalogue (52) must record the classification context of every RD to prevent cross-class transfer without validation.

## 16. Decision Requirements

Decision Intelligence (60) context resolution begins with classification: action eligibility tables are filtered by node (e.g. no load-shed actions for OPC-01 critical continuous process). Constraint sets (59) attach defaults by node.

## 17. Ontology Mapping

### Classes
- BuildingClassification, Sector, BuildingType, BuildingSubtype, BuildingSubSubtype, FunctionClassification, OccupancyClassification, OperationalClassification, BenchmarkClassification, ApplicabilityRule

### Object Properties
- hasParentClassification, hasSector, hasType, hasSubtype, hasFunction, hasOccupancyClass, hasOperationalClass, mapsToBenchmarkScheme, applicableTo, supersededBy, hasDefaultArchetype, hasDefaultTwinTemplate

### Datatype Properties
- classificationID, classificationName, classificationLevel, tm46Category, epcCategory, berApplicable, nabersCategory, typicalEUImin, typicalEUImax, flexibilityPotentialClass, classificationStatus, version

External alignment: SAREF4BLDG bldg:Building subclassing; BOT bot:Building; Brick brick:Building; EU EPBD building categories via Semantic Mapping (81).

## 18. Knowledge Graph Mapping

### Nodes
- Sector, BuildingType, BuildingSubtype, SubSubtype, FunctionClass, OccupancyClass, OperationalClass, BenchmarkClass

### Relationships
- CHILD_OF, HAS_FUNCTION, HAS_OCCUPANCY_CLASS, HAS_OPERATIONAL_CLASS, MAPS_TO_BENCHMARK, APPLICABLE_TO {capabilityDomain, verdict, condition}, SUPERSEDED_BY, DEFAULT_ARCHETYPE, DEFAULT_TWIN_TEMPLATE

Provenance: every node carries version, owner, governanceStatus as properties.

## 19. Digital Twin Mapping

Every Building Twin instance resolves its classification chain at instantiation and inherits: default twin template, default occupancy profile family, default constraint set, model applicability matrix slice, and benchmark cohort. Classification changes on a live twin trigger re-resolution and are versioned twin events.

## 20. Governance

Roles: Catalogue Owner; Building Domain Expert; Benchmark Owner (for Axis D); Ontology Steward; Validation Reviewer; Version Approver. Adding/merging/deprecating any node requires impact analysis across the applicability matrix and approval by Owner + Version Approver. National scheme changes (TM46 revisions, DEAP updates) trigger a mandatory review cycle.

## 21. Versioning

Semver per node and per catalogue. MAJOR: hierarchy restructure or node merge/split; MINOR: new nodes or axis values; PATCH: descriptions, typical bounds. Deprecated nodes are retained for 24 months minimum for historical dataset integrity; datasets never re-key silently.

## 22. Example Records

```text
ClassificationID: EIG-BC-SUB-2011
Name: Open-Plan Office
Level: subtype | Parent: EIG-BC-TYP-201 (Office) | Sector: EIG-BC-SEC-02
Axes: FUN-02 Workplace | OCL-03 Standard hours | OPC-03 Comfort-led | BMC-01 TM46
TM46: 16 (General office) | EPC: B1 office | BER applicable: false
Typical floor area: 500–25,000 m² | Typical EUI: 95–220 kWh/m²/yr
Flexibility potential: high | Twin template: EIG-DTT-OFF-01
Status: active | Version: 1.2.0 | Governance: approved
```

```text
ClassificationID: EIG-BC-SUB-1013
Name: Hyperscale Data Centre
Level: subtype | Parent: EIG-BC-TYP-1101 | Sector: EIG-BC-SEC-11
Axes: FUN-10 | OCL-01 Continuous | OPC-01 Critical continuous | BMC-06 EIG cohort
TM46: n/a | BER applicable: false
Typical EUI: 800–2,500 kWh/m²/yr | Flexibility potential: medium (UPS/genset/cooling thermal mass)
Sub-subtypes: SSU-10131 air-cooled, SSU-10132 liquid-cooled, SSU-10133 hybrid
Status: active | Version: 1.0.0 | Governance: approved
```

```text
MatrixEntryID: EIG-BC-MTX-00412
Classification: EIG-BC-SUB-5011 (Acute hospital)
CapabilityDomain: flexibility_service | Capability: EIG-FS-DR-01 (Demand response — load shed)
Applicability: conditional
Condition: affected_circuits NOT IN critical_load_register AND backup_generation.status = available
```

## 23. Completion Criteria

Complete when: every building type and subtype in the operational and foreseeable portfolio is represented to Level 3 (Level 4 where models require); all four axes enumerated; every active Level-3 node has benchmark mapping, twin template, typical bounds and flexibility prior; applicability matrix populated for occupancy, benchmark, archetype, model, KPI, flexibility-service and twin-template domains; ontology/KG mappings emitted; governance roles assigned; ≥3 example records validated against §8.
