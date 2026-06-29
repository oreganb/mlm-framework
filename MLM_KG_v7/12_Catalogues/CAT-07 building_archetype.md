---
type: catalogue
ontology: EIG-MLM
catalogue_no: 7
catalogue_id: EIG-CAT-007
entity: building_archetype
band: physical
status: active
tags: [eig-mlm, catalogue, band:physical, entity:building_archetype]
aliases: ["EIG-CAT-007", "Catalogue 07", "building_archetype"]
---

# CAT-07 — Building Archetype

> **EIG-CAT-007** · band: **Physical & Spatial Stack** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Building Archetype Catalogue

## 1. Definition

The Building Archetype Catalogue defines all archetype structures — energy, occupancy, behaviour and retrofit archetypes — used by the EIG MLM framework. An archetype is a validated, representative parameterisation of a class of buildings (or occupants, behaviours, or retrofit pathways) that supports clustering, transfer learning, synthetic building generation, stock-level modelling and cold-start parameterisation of new buildings.

## 2. Scope

Applies to: clustering pipelines (assignment of real buildings to archetypes), synthetic building/stock generation (42), cold-start model parameterisation for newly onboarded buildings, national/portfolio stock modelling, retrofit pathway analysis, transfer of trained models across similar buildings, scenario population construction (89).

Out of scope: classification taxonomy (01 — archetypes attach to classification nodes), individual building records (02), the clustering algorithms themselves (45).

## 3. Classification Structure

```text
Archetype Family:  energy | occupancy | behaviour | retrofit
Derivation:        expert_defined | cluster_derived | standard_imported (e.g. TABULA) | hybrid
Object:            archetype (EIG-ARC) → parameter block(s) → assignment records (EIG-ARC-ASG)
```

## 4. Hierarchy

```text
Classification Node (01)
 └─ Archetype (scoped to node, one family)
     ├─ Parameter Block (family-specific, §5.2–5.5)
     ├─ Membership Definition (centroid + assignment rule)
     └─ Assignment (building → archetype, with distance/confidence)
Retrofit archetypes additionally chain: state_A → pathway → state_B (both states are energy archetypes).
```

## 5. Field Groups & Fields

### 5.1 Common Archetype Fields

| # | Field | Type | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|
| 1 | archetypeID | string | `EIG-ARC-[0-9]{5}` | Y | Unique ID |
| 2 | archetypeName | string | ≤120 chars | Y | Name |
| 3 | archetypeFamily | enum | energy, occupancy, behaviour, retrofit | Y | Family |
| 4 | classificationID | string | EIG-BC Level-2/3 node | Y | Scope |
| 5 | derivation | enum | expert_defined, cluster_derived, standard_imported, hybrid | Y | Provenance |
| 6 | sourceReference | string | e.g. TABULA IE code, training run ID (EIG-TRN) | Cond | Required unless expert_defined |
| 7 | representativePopulationShare | decimal 0…1 | — | N | Share of stock represented |
| 8 | geographicScopeID | string | EIG-GEO ID | N | Spatial validity |
| 9 | eraBandScope | array[enum] | era bands | N | Temporal validity |
| 10 | centroidFeatureVector | object | {featureID (EIG-FEA): value} | Cond | Required if cluster_derived |
| 11 | assignmentRule | enum | nearest_centroid, rule_based, probabilistic_gmm, manual | Y | How buildings map |
| 12 | assignmentDistanceMetric | enum | euclidean_standardised, mahalanobis, gower, custom | Cond | If centroid-based |
| 13 | assignmentThreshold | decimal | ≥0 | N | Max distance for valid assignment |
| 14 | validationStatus | enum | unvalidated, internally_validated, externally_validated | Y | Evidence level |
| 15 | lifecycle block | — | standard | Y | status/version/owner/dates/governance |

### 5.2 Energy Archetype Parameter Block

| # | Field | Type | Unit | Allowed Values | Req | Description |
|---|---|---|---|---|---|---|
| 1 | typicalGFA | decimal | m² | >0 | Y | Representative size |
| 2 | typicalEUItotal / electrical / thermal | decimal | kWh/m²/yr | ≥0 | Y | Intensity set |
| 3 | typicalPeakDensity | decimal | W/m² | ≥0 | Y | Peak demand density |
| 4 | loadProfileShapeRef | string | — | normalised 8760/17520 profile dataset ID (43) | Y | Canonical shape |
| 5 | fabricAssemblySet | array[string] | — | EIG-FAB-ASM IDs | Y | Typical fabric |
| 6 | typicalHLC | decimal | W/K per m² | >0 | Y | Heat loss coefficient density |
| 7 | systemSet | object | — | {heating: enum, cooling: enum, ventilation: enum, dhw: enum} per (02) vocab | Y | Typical systems |
| 8 | fuelMixShares | object | % | {carrier: share}, Σ=100 | Y | Carrier split |
| 9 | onSiteGenerationTypical | object | — | {pv_kwp, battery_kwh, other} | N | DER endowment |
| 10 | flexibilityCapacityTypical | decimal | kW/1000m² | ≥0 | N | Shiftable density |
| 11 | parameterUncertainty | object | — | {field: cv%} | N | Spread per parameter |

### 5.3 Occupancy Archetype Parameter Block

| # | Field | Type | Allowed Values | Req | Description |
|---|---|---|---|---|---|
| 1 | occupancyProfileID | string | EIG-OCC ID | Y | Canonical profile (03) |
| 2 | densityRange | object | {min, typical, max m²/person} | Y | Density envelope |
| 3 | scheduleVariability | enum | deterministic…stochastic_event (per 03) | Y | Predictability |
| 4 | annualOccupiedHoursTypical | decimal h/yr | 0…8784 | Y | Hours |

### 5.4 Behaviour Archetype Parameter Block

| # | Field | Type | Allowed Values | Req | Description |
|---|---|---|---|---|---|
| 1 | behaviourComponentID | string | EIG-OCC-BEH ID | Y | Canonical behaviour (03) |
| 2 | flexEngagementDistribution | object | {class: share}, Σ=100% over (03 §5.5.6) classes | Y | Engagement mix |
| 3 | overrideRateTypical | decimal 0…1 | — | Y | DR override prior |
| 4 | reboundFactorTypical | decimal | ≥0 | Y | kWh rebound per kWh shifted |
| 5 | priceElasticityPrior | decimal | ≤0 | N | Demand/price prior |

### 5.5 Retrofit Archetype Parameter Block

| # | Field | Type | Unit | Allowed Values | Req | Description |
|---|---|---|---|---|---|---|
| 1 | fromEnergyArchetypeID | string | — | EIG-ARC (energy) | Y | Start state |
| 2 | toEnergyArchetypeID | string | — | EIG-ARC (energy) | Y | End state |
| 3 | measurePackage | array[string] | — | measure codes (05 §6) | Y | Bundle |
| 4 | indicativeCost | decimal | €/m² | >0 | Y | Capex density |
| 5 | costBasisYear | integer | yr | — | Y | Price base |
| 6 | grantEligibility | array[object] | — | {scheme: enum(SEAI_indiv, SEAI_OSS, EXEED, BEC, none), rate%} | N | Supports |
| 7 | predictedSavingThermal / Electrical | decimal | kWh/m²/yr | ≥0 | Y | Modelled deltas |
| 8 | predictedCarbonSaving | decimal | kgCO₂e/m²/yr | ≥0 | Y | Carbon delta |
| 9 | embodiedCarbonCost | decimal | kgCO₂e/m² | ≥0 | N | Embodied (05/28) |
| 10 | simplePaybackTypical | decimal | yr | >0 | Y | Indicative payback |
| 11 | disruptionClass | enum | — | none, low, occupied_works, decant_required | Y | Delivery impact |
| 12 | sequencingConstraints | array[string] | — | e.g. "fabric_before_heatpump" rule codes | N | Pathway logic |
| 13 | berBandShift | object | — | {from: A1…G, to: A1…G} | N | Rating effect |

### 5.6 Assignment Record Fields

| # | Field | Type | Allowed Values | Req | Description |
|---|---|---|---|---|---|
| 1 | assignmentID | string | `EIG-ARC-ASG-[0-9]{7}` | Y | ID |
| 2 | buildingID / archetypeID | string | valid IDs | Y | Pair |
| 3 | assignmentDate | date | ISO 8601 | Y | When |
| 4 | distance / probability | decimal | ≥0 / 0…1 | Cond | Per assignment rule |
| 5 | confidenceClass | enum | high, medium, low, forced_manual | Y | Quality |
| 6 | featureSnapshotRef | string | dataset ref (43) | Y | Features at assignment |
| 7 | supersedesAssignmentID | string | or null | N | Reassignment chain |

## 6. Controlled Vocabularies

Families, derivations, assignment rules, distance metrics, disruption classes, grant schemes, confidence classes as enumerated; measure codes inherited from (05 §6); all governed via (82). Sequencing rule codes registered here (closed list: fabric_before_heatpump, airtightness_with_ventilation, roof_before_pv, dhw_with_heating).

## 7. Applicability Rules

- An archetype applies only to buildings whose classification falls under its classificationID scope and (where set) geographic/era scopes.
- Cold-start parameterisation: a new building with assignment confidence high/medium inherits archetype parameter blocks as priors; low/forced_manual inherits with degraded-confidence flag propagated to all consuming models.
- Retrofit archetypes apply only where fromEnergyArchetype matches the building's current assignment and heritage exclusions (05 §7) pass.
- Transfer learning across buildings permitted only within the same energy archetype unless cross-archetype transfer is validated (47).

## 8. Validation Rules

- archetypeID unique; from/to retrofit states distinct and same classification scope.
- fuelMixShares and flexEngagementDistribution sum to 100 ± 0.1%.
- cluster_derived archetypes must reference a training run (50) and carry centroid vectors over registered features (41) only.
- Each building has exactly one active assignment per archetype family; reassignments chain via supersedesAssignmentID.
- typicalEUItotal within classification typical bounds (01) × [0.5, 2.0].
- Retrofit predicted savings ≤ from-state EUI (cannot save more than is used, per vector).
- Population shares per (classification × family) sum ≤ 1.0.
- standard_imported archetypes pin source version (e.g. TABULA release) in sourceReference.

## 9. Relationships to Other Catalogues

Upstream: Building Classification (01), Building (02), Occupancy (03), Fabric (05), Feature (41), Training (50). Lateral: Geographic (08), Weather (09 — profile shapes are climate-scoped), Carbon (28), Standard (94 — TABULA). Downstream: Synthetic Data (42 — generation sampling), Dataset (43), Model (44 — priors, transfer), Scenario (89 — stock construction), Decision Intelligence (60 — retrofit pathways), KPI (57 — archetype-relative KPIs), Benchmark (06 — cohort cross-checks), Digital Twin Template (33 — template seeding).

## 10. Benchmarking Requirements

Archetype-relative benchmarking: building vs its archetype centroid (deviation per feature); archetype population statistics cross-validated against benchmark cohort releases (06) — systematic divergence triggers archetype review.

## 11. Dataset Requirements

Per energy archetype: canonical normalised load profile dataset (8760/17520 points, registered in 43); per cluster-derived archetype: training dataset lineage; assignment feature snapshots stored as datasets for reproducibility.

## 12. Feature Requirements

Clustering feature sets registered in (41) with explicit list per archetype family (energy: EUI set, load shape stats, HLC, fuel mix; occupancy: schedule stats; behaviour: override/rebound/engagement). Assignment must use the registered set version — feature drift forces re-clustering review.

## 13. Model Requirements

Consumers: synthetic generators (sample parameter blocks + uncertainty), stock models (population shares × parameters), cold-start forecasters (archetype priors), transfer-learning pipelines (archetype-gated), retrofit option models (pathway packages). Clustering models (44/45) produce candidate archetypes → governance promotes to active.

## 14. KPI Requirements

KPIs: assignment coverage % (buildings with high/medium confidence), archetype fidelity (mean member-to-centroid distance trend), retrofit archetype realisation gap (predicted vs measured savings across applications), population share drift.

## 15. Response Derivative Requirements

Archetypes carry RD priors: flexibilityCapacityTypical, overrideRateTypical, reboundFactorTypical seed building-level RD estimation (52). RD transfer across buildings is archetype-gated (same rule as model transfer, §7).

## 16. Decision Requirements

Decision uses (60): retrofit pathway selection per building = applicable retrofit archetypes ranked multi-objective; portfolio planning = archetype-level rollout optimisation; cold-start control = conservative action envelopes until building-specific models replace archetype priors (confidence-based handover rule).

## 17. Ontology Mapping

Classes: Archetype, EnergyArchetype, OccupancyArchetype, BehaviourArchetype, RetrofitArchetype, ParameterBlock, ArchetypeAssignment, RetrofitPathway.
Object properties: scopedToClassification, hasParameterBlock, assignedTo (Building), derivedFromTrainingRun, fromState, toState, includesMeasure, seedsTwinTemplate, supersedes.
Datatype properties: archetypeID, archetypeFamily, derivation, populationShare, typicalEUItotal, typicalHLC, indicativeCost, simplePaybackTypical, distance, confidenceClass.
External alignment: TABULA/EPISCOPE typology concepts; alignment recorded via (81).

## 18. Knowledge Graph Mapping

Nodes: Archetype, ParameterBlock, Building, ClassificationNode, RetrofitMeasure, TrainingRun.
Relationships: SCOPED_TO, HAS_BLOCK, ASSIGNED_TO {distance, confidence, date}, DERIVED_FROM, PATHWAY_FROM/PATHWAY_TO, INCLUDES_MEASURE {cost share}, SEEDS_TEMPLATE, SUPERSEDED_BY, VALIDATED_AGAINST {cohortReleaseID}.

## 19. Digital Twin Mapping

Twin templates (33) are seeded from energy archetypes (default fabric set, systems, profiles); cold-start twins run on archetype parameters until building-specific calibration replaces them (handover logged as twin event); stock/district twins (87) instantiate archetype populations.

## 20. Governance

Roles: Catalogue Owner, Archetype Domain Expert (per family), Data Scientist Owner (cluster-derived promotion), Retrofit/QS Expert (cost validation), Validation Reviewer, Version Approver. Promotion rule: cluster outputs become active archetypes only after internal validation against ≥1 benchmark release and expert sign-off.

## 21. Versioning

Archetype semver; parameter block updates MINOR, scope or centroid changes MAJOR (force reassignment run). Assignments are append-only events. Cost bases re-indexed annually (costBasisYear pinned per record).

## 22. Example Records

```text
ArchetypeID: EIG-ARC-00214 | Family: energy | Name: 1971–1990 Semi-D, Gas, Pre-Retrofit
Scope: EIG-BC-SUB-1012 (semi-detached) | Era: 1971_1990 | Geo: EIG-GEO-IE | Derivation: standard_imported (TABULA IE.N.SFH.04)
GFA 110 m² | EUI total 210 (thermal 175, elec 35) | Peak 5.2 W/m² | HLC 2.9 W/K/m²
Fabric: [ASM-00030 cavity uninsulated, ASM-00102 attic 100mm, ASM-00150 double-air windows]
Systems: {heating: gas_boiler, vent: natural, dhw: boiler_cylinder} | Fuel: {gas 78, elec 22}
Population share: 0.11 of subtype | Validation: externally_validated | v2.0.1
```

```text
ArchetypeID: EIG-ARC-00498 | Family: retrofit | Name: Semi-D Deep Retrofit Pathway B
From: EIG-ARC-00214 → To: EIG-ARC-00231 (post-retrofit HP variant)
Package: [CWI, ATT_INS, WINDOW_UPGRADE_TRIPLE, AIRTIGHTNESS_WORKS, DOOR_UPGRADE] + HP install
Cost: €620/m² (2026 base) | Grants: [{SEAI_OSS, 45%}] | Savings: thermal 120, elec −8 (HP shift), carbon 38 kgCO₂e/m²/yr
Embodied: 41 kgCO₂e/m² | Payback 13.5 yr (post-grant) | Disruption: occupied_works
Sequencing: [fabric_before_heatpump, airtightness_with_ventilation] | BER: D2 → A3
```

```text
AssignmentID: EIG-ARC-ASG-0103377 | Building: EIG-BLD-002455 → EIG-ARC-00214
Rule: nearest_centroid (mahalanobis) | Distance 1.7 (threshold 2.5) | Confidence: high
Feature snapshot: EIG-DSP-009912 | Date 2026-03-02 | Supersedes: none
```

## 23. Completion Criteria

Complete when all four families are fully fielded with family-specific parameter blocks; assignment mechanics (rules, distances, thresholds, snapshots) specified; retrofit pathway chaining validated; promotion governance defined; relationships to clustering, synthesis, transfer and decision layers explicit; ontology/KG/twin mappings emitted; ≥3 examples covering energy, retrofit and assignment objects.
