---
type: catalogue
ontology: EIG-MLM
catalogue_no: 30
catalogue_id: EIG-CAT-030
entity: flexibility
band: flexibility
status: active
tags: [eig-mlm, catalogue, band:flexibility, entity:flexibility]
aliases: ["EIG-CAT-030", "Catalogue 30", "flexibility"]
---

# CAT-30 — Flexibility

> **EIG-CAT-030** · band: **Flexibility & Platform** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID prefix:** `EIG-FLX`
**Catalogue number:** 30 of 94
**Layer:** Flexibility & Platform
**Version:** 1.0.0
**Status:** ACTIVE

---

## 1. Definition

The Flexibility Catalogue defines **flexibility resources and their characteristics**: the physical and behavioural capacity of an asset, building, portfolio or community to deviate from its baseline energy trajectory on purpose. Where the asset catalogue (11) holds the per-asset flexibility envelope (§5.6) and the service catalogue (29) holds the operational offering, this catalogue defines the **resource abstraction layer**: typed flexibility resources (electrical, thermal, storage, behavioural), their availability models, their constraint algebra, and the **portfolio** construct that aggregates resources into market-grade capacity with diversity effects.

A FlexResource record answers: what kind of flexibility, how much, in which direction, for how long, how often, with what certainty, under what constraints — in a form that optimisation (60), RD modelling (52) and market offering (20/25) consume identically.

## 2. Scope

**In scope:** flexibility typology (electrical/thermal/storage/behavioural/asset/portfolio); resource records and their envelope algebra; availability models (time-varying, weather-coupled, occupancy-coupled); constraint records (technical, contractual, comfort, network); portfolio composition with diversity factors; state-of-flexibility accounting (committed vs free capacity per window); links to RD characterisation.

**Out of scope:** service operations (29), asset device records (11), market offers (25), control execution (62), RD mathematics (52), storage device specifications (10 EIG-AC-STO-*).

## 3. Classification Structure

`flexType` (primary):

| Code | Type | Physical basis |
|---|---|---|
| FLX-EL | electrical | Direct electrical load/generation modulation (carrier EL, 19) |
| FLX-TH | thermal | Thermal inertia of fabric/zones (05 RC params), DHW tanks, network thermal mass (carriers HT/CL) |
| FLX-ST | storage | Charge/discharge of electrochemical/thermal stores (10 STO-*; conversion CP-05, 19) |
| FLX-BH | behavioural | Voluntary occupant action on signal (03 reboundPropensity/overrideRate) |
| FLX-AS | asset_composite | Single asset combining modes (e.g. ASHP = FLX-EL via FLX-TH buffer) |
| FLX-PF | portfolio | Aggregation of resources with diversity model |

Direction vocabulary (per resource): `up` (reduce import / increase export — turn-down of load or turn-up of generation/discharge) and `down` (increase import / decrease export), consistent with envelope fields flexCapacityUp/Down (11 §5.6).

`certaintyClass`: firm (storage-backed, ≥0.95 availability), conditional (weather/occupancy-coupled), statistical (behavioural, portfolio-only firmness).

## 4. Hierarchy

```
FlexResource (EIG-FLX-R-*)                 ← typed resource, binds to physical anchor
├── EnvelopeDeclaration (inherits/extends 11 §5.6 for asset anchors)
├── AvailabilityModel (embedded or model ref 44)
└── ConstraintSet (embedded constraint records)
Portfolio (EIG-FLX-PF-*)                   ← composition of FlexResources
├── membership rules + diversityModel
└── PortfolioEnvelope (computed, never hand-declared)
StateOfFlexibility (computed per resource/portfolio × window)
```

## 5. Field Groups & Fields

### 5.1 FlexResource — Identity & Anchor

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | flexResourceID | string | — | `EIG-FLX-R-[0-9]{6}` | M | Resource identifier |
| 2 | flexType | string | — | §3 codes (not FLX-PF) | M | Flexibility type |
| 3 | anchorRef | string | — | EIG-AST-* (11), EIG-BLD-* (02), EIG-SPC-* zone (04) | M | Physical anchor; exactly one |
| 4 | carrierRefs | array | — | EIG-ECR-* (19) | M | Carriers affected (FLX-TH lists HT and the driving EL where heat-pump-coupled, CP-01) |
| 5 | ownerActorRef | string | — | EIG-ACT-* (14) | M | Flexibility owner (may differ from asset owner; contracts 23 govern) |
| 6 | certaintyClass | string | — | firm, conditional, statistical | M | Firmness class |
| 7 | status | string | — | declared, characterised, verified, dormant, retired | M | Lifecycle; verified requires ≥1 measured activation (56) |

### 5.2 Envelope Declaration

For asset anchors, fields mirror and extend 11 §5.6 (single source: asset record; this layer adds resource semantics). For building/zone anchors (thermal), the envelope is declared here.

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | flexCapacityUpKW | decimal | kW | ≥0 | M | Max sustained deviation, up direction |
| 2 | flexCapacityDownKW | decimal | kW | ≥0 | M | Down direction |
| 3 | maxDurationMin | object | min | {up, down} | M | Sustain limits per direction |
| 4 | noticeMin | decimal | min | ≥0 | M | Minimum activation notice |
| 5 | recoveryMin | decimal | min | ≥0 | M | Mandatory recovery between activations |
| 6 | reboundFactorPct | decimal | % | ≥0 | M | Expected payback energy as % of delivered (treatment per 22/29) |
| 7 | energyBudgetKWh | object | kWh | {perDay, perWeek} | C | Energy-limited resources (storage, thermal buffers) |
| 8 | rampRateKWPerMin | decimal | kW/min | >0 | C | Mandatory for FS-FRQ-eligible resources |
| 9 | granularityKW | decimal | kW | >0 | O | Smallest dispatchable step (modulating vs on/off, 10 flexibilityClass) |
| 10 | declaredVsObservedGapPct | decimal | % | computed (52 feedback) | M | Live gap; >25% triggers re-declaration (11 rule) |

### 5.3 Availability Model

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | availabilityKind | string | — | constant, schedule_based (24), weather_coupled (09), occupancy_coupled (03), model_based (44) | M | How availability varies |
| 2 | availabilityProfile | string | — | schedule ref / model ref / profile dataset (43) | M | The availability source |
| 3 | weatherSensitivity | object | — | {vars: EIG-WX-VAR-* (09), bands: e.g. temp bands ≤0…>20°C} | C | Mandatory for weather_coupled (heat pumps lose shed capacity in cold bands) |
| 4 | occupancyCoupling | object | — | {occupancyFamilies (03), presenceEffect} | C | Mandatory for occupancy_coupled/FLX-BH |
| 5 | availabilityFloorPct | decimal | % | 0–100 | M | Guaranteed minimum availability for firm class (≥95 for certaintyClass=firm) |

### 5.4 Constraint Set

Constraint records use a uniform algebra so optimisation consumes them mechanically:

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | constraintID | string | — | `C-[0-9]{3}` (scoped to resource) | M | Local identifier |
| 2 | constraintClass | string | — | technical, comfort, contractual, network, safety, energy_budget | M | Origin |
| 3 | expression | object | — | {variable, operator (≤ ≥ = ∈), bound, window} machine-evaluable | M | Constraint as data (joins 59 constraint catalogue conventions) |
| 4 | sourceRef | string | — | 11 (technical), 03/23 (comfort/contractual), 13 (network export/import limits), 04 (safety exclusions) | M | Authority for the constraint |
| 5 | hardness | string | — | hard, soft (penalty ref) | M | Hard constraints inviolable (comfort floors always hard, 29 A7) |

### 5.5 Portfolio (EIG-FLX-PF-*)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | portfolioID | string | — | `EIG-FLX-PF-[0-9]{4}` | M | Portfolio identifier |
| 2 | operatorRef | string | — | EIG-ACT-* (typically ACT-AGG) | M | Portfolio operator |
| 3 | membershipRule | string | — | explicit list ∨ rule (zone, serviceClass, flexType) | M | Composition; members must hold enrolment-compatible contracts (23) |
| 4 | memberResourceIDs | array | — | EIG-FLX-R-* | M | Materialised membership (rule-resolved, dated) |
| 5 | diversityModel | object | — | {method: copula/empirical/factor, simultaneityFactor: 0–1 per direction/window, evidenceRef} | M | Portfolio capacity ≠ Σ member capacity; diversity must be evidenced, not assumed |
| 6 | portfolioEnvelope | object | — | computed {upKW, downKW, duration, notice} per window | M | COMPUTED class; recomputed on membership/availability change |
| 7 | firmnessUpgrade | string | — | statement of how statistical members yield firm portfolio capacity (n, confidence) | C | Statistical→firm aggregation logic (min n=10 members for published firmness, benchmark convention) |
| 8 | zoneRefs | array | — | EIG-NET-NZ-* (13) | C | Locational portfolios (LFM zone alignment, 20) |

### 5.6 State of Flexibility (computed)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | windowTS | datetime | — | grid-aligned (26) | M | Window |
| 2 | freeCapacityUpKW / DownKW | decimal | kW | envelope − Σ commitments (29 enrolments, 25 trades) | M | Available-now capacity |
| 3 | committedRefs | array | — | enrolment/trade refs | M | Where capacity went (stacking ledger, 29 A5) |
| 4 | stateOfChargePct | decimal | % | 0–100 | C | Storage/thermal-buffer resources |
| 5 | dataClass | string | — | COMPUTED | M | Provenance class |

## 6. Controlled Vocabularies

- `flexType`, `certaintyClass`, `availabilityKind`, `constraintClass`, `hardness`: §3/§5.
- Resource lifecycle: `declared → characterised (RD prior exists, 52) → verified (measured activation) → dormant ⇄ verified → retired`.
- Direction semantics fixed framework-wide: `up` = grid-helpful reduction of net import; `down` = increase of net import (absorption). All catalogues (11, 22, 25, 29) share this orientation — Phase 14 consistency check.
- Temperature band vocabulary reused from 09 (≤0 / 0–5 / 5–10 / 10–15 / 15–20 / >20 °C) for weather-coupled availability.

## 7. Applicability Rules

| # | Rule |
|---|---|
| A1 | Asset-anchored envelopes are owned by 11 §5.6; this catalogue references, never duplicates — divergence is a defect (Phase 14 check). Building/zone thermal envelopes are owned here. |
| A2 | Portfolio envelopes are always COMPUTED from members + diversityModel; hand-declared portfolio capacity is forbidden. |
| A3 | Diversity/simultaneity factors require evidence (measured coincidence analysis or cited method); defaulting to 1.0 (no diversity) is the only evidence-free option. |
| A4 | certaintyClass=firm requires availabilityFloorPct ≥ 95 and either storage backing or verified availability history ≥ 90 days. |
| A5 | Behavioural resources (FLX-BH) are statistical-only at resource level; they acquire firmness only inside portfolios meeting firmnessUpgrade criteria (min n=10). |
| A6 | The stacking ledger is authoritative: Σ commitments per (resource, direction, window) ≤ envelope; offers/enrolments check here before formation (29 A5, 20 A6). |
| A7 | Constraints are data, not prose: every constraint machine-evaluable (§5.4 expression); optimisation refuses resources with unparseable constraints. |
| A8 | Network constraints (import/export limits at connection point, 13) bind all resources behind that CP jointly — enforced at building/CP level, not per resource. |

## 8. Validation Rules

| # | Rule | Severity |
|---|---|---|
| V1 | IDs unique per patterns | error |
| V2 | anchorRef exists; one resource per (anchor, flexType) — composites use FLX-AS | error |
| V3 | Asset-anchored envelope fields equal 11 §5.6 values (A1 single source) | error |
| V4 | certaintyClass=firm criteria met (A4) | error |
| V5 | Weather/occupancy coupling present where availabilityKind demands | error |
| V6 | Portfolio members exist, contracts compatible, zone alignment where zoneRefs set | error |
| V7 | diversityModel evidenced or factor=1.0 (A3) | error |
| V8 | Stacking ledger: no window over-commitment (A6) | error |
| V9 | All constraints parse and evaluate against test vectors | error |
| V10 | declaredVsObservedGapPct >25% ⇒ status forced to declared (re-characterisation) and active offers flagged | error |
| V11 | Direction semantics consistent with framework orientation (§6) | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 11 asset | Envelope source for asset anchors; re-declaration loop |
| 10 asset_classification | flexibilityClass eligibility; FN-* exclusions |
| 02/04/05 building/space/fabric | Thermal anchors; RC parameters parameterise FLX-TH availability |
| 03 occupancy | Behavioural coupling; override/rebound statistics |
| 09 weather | Weather-coupled availability bands |
| 13 utility_network | CP-level joint constraints (A8); LFM zones |
| 19 energy_carrier | Carriers affected; conversion coupling (CP-01 heat pump) |
| 29 flexibility_service | Enrolment consumes resources; stacking ledger shared |
| 25 trade | Offered capacity sourced from free capacity |
| 52 RD | Characterisation: RDs quantify the envelope statistically; observed-gap feedback |
| 59 constraint | Constraint algebra conventions |
| 60 decision_intelligence | Optimisation consumer |
| 86 community | Community portfolios |

## 10. Benchmarking Requirements

Resource benchmarks: realised vs declared capacity, availability attainment vs floor, rebound factor measured vs declared, portfolio diversity factor stability. Cohorts by flexType × anchor class × climate zone (08); min n=10; immutable releases; asset-level metrics flow through 12 (EIG-ABM-MET-015 family).

## 11. Dataset Requirements

State-of-flexibility series stored at window resolution (26 grids) as COMPUTED datasets (43); availability model inputs (weather 09, occupancy 03) referenced with consent scopes; commitment ledger immutable event log; characterisation activations retained with full telemetry (56 evidence standard).

## 12. Feature Requirements

Features: free capacity now/next-24h per direction, SoC trajectories, weather-conditional capacity (per temp band), portfolio simultaneity realised, fatigue/recovery state, constraint-slack features (distance to comfort floor). All features carry dataClass; portfolio features recompute on membership events.

## 13. Model Requirements

Availability models (model_based kind) registered in 44 with validation against realised availability (47); thermal flexibility models parameterised from fabric RC (05) and calibrated per building (CALIBRATED class); behavioural models use consented features only; diversity models re-estimated quarterly or on ≥10% membership change.

## 14. KPI Requirements

KPIs (57): exploitable flexibility (kW, kWh) per building/zone/portfolio, utilisation rate (delivered/available), firmness attainment, constraint-binding frequency (which constraints actually limit dispatch — design feedback), flexibility value €/kW/yr (joins 27).

## 15. Response Derivative Requirements

This catalogue is RD's physical substrate: the envelope is the *declared* prior; RDs (52) provide the *measured conditional* characterisation (ΔkW response under conditions c with confidence γ); status=characterised requires an RD record; verified requires RD updated from ≥1 real activation. Weather/occupancy availability couplings define the RD conditioning variables. The declared-vs-observed gap is computed RD-side and written back here (V10).

## 16. Decision Requirements

Optimisation (60) consumes: free capacity, constraints (hard/soft), availability forecasts, rebound factors — and returns commitments to the stacking ledger atomically (no partial writes). Portfolio recomposition decisions require operator authority (T2+); retiring a resource with active commitments requires unwind workflow (64).

## 17. Ontology Mapping

Classes: `eig:FlexResource` ⊑ `eig:EnergyResource`; subtypes per flexType; `eig:FlexPortfolio` ⊑ `eig:FlexResource` (composite pattern); `eig:FlexConstraint`, `eig:AvailabilityModel`, `eig:StateOfFlexibility`. Object properties: `eig:anchoredTo` (→ eig:Asset ∪ eig:Building ∪ eig:Zone), `eig:affectsCarrier`, `eig:memberOf` (→ eig:FlexPortfolio), `eig:constrainedBy`, `eig:characterisedBy` (→ eig:ResponseDerivative). Datatype: flexCapacityUpKW/DownKW, noticeMin, recoveryMin, reboundFactorPct, certaintyClass, simultaneityFactor. Alignments: SAREF4ENER PowerProfile/FlexOffer concepts, USEF flexibility specifications — design-intent level.

## 18. Knowledge Graph Mapping

Nodes: `(:FlexResource)`, `(:FlexPortfolio)`, `(:FlexConstraint)`, `(:StateOfFlex {window})`. Edges: `(:FlexResource)-[:ANCHORED_TO]->(:Asset|:Building|:Zone)`, `(:FlexResource)-[:MEMBER_OF]->(:FlexPortfolio)`, `(:FlexResource)-[:CONSTRAINED_BY]->(:FlexConstraint)`, `(:FlexResource)-[:CHARACTERISED_BY]->(:ResponseDerivative)`, `(:Enrolment|:Trade)-[:COMMITS {kW, window, direction}]->(:FlexResource)`. Free-capacity queries aggregate COMMITS against envelopes; portfolio queries roll up membership with diversity factors.

## 19. Digital Twin Mapping

Twins maintain live state-of-flexibility (SoC, thermal state, constraint slack) as twin state variables; pre-dispatch simulation tests activations against the constraint set; NEST sandbox composes virtual portfolios over the synthetic grid to study diversity and firmness at scales beyond the physical pilot (Aran 20-building fleet → island-scale portfolios).

## 20. Governance

Owner: Flexibility Modelling Lead. Steward: data/ML engineer (availability models, diversity estimation). Reviewer: RD methodology owner (52 alignment). Approver: T3+ for firmness-class changes and portfolio publication (16). Duties per 16 A6. Diversity-factor evidence reviewed before any market-facing portfolio capacity publication.

## 21. Versioning

Resources: stateful with immutable event history; envelope changes are re-declarations (dated, gap-triggered or owner-initiated). Portfolios: versioned on membership-rule change (MAJOR), diversity model re-estimation (MINOR). Catalogue semver as standard. ChangeLog mandatory.

## 22. Example Records

```yaml
flexResourceID: EIG-FLX-R-001482
flexType: FLX-AS (heat pump via thermal buffer) | anchorRef: EIG-AST-004211 (NIBE ASHP @ IE_B0009)
carrierRefs: [EIG-ECR-EL (driven), EIG-ECR-HT (buffered, CP-01)]
ownerActorRef: EIG-ACT-00214 (flexumer) | certaintyClass: conditional | status: verified
envelope: {upKW: 2.4, downKW: 3.1, maxDurationMin: {up: 120, down: 90}, noticeMin: 15,
           recoveryMin: 240, reboundFactorPct: 35, rampRateKWPerMin: 1.2, gapPct: 11}
availability: {kind: weather_coupled, vars: [EIG-WX-VAR-001 dry-bulb],
               bands: {≤0°C: 0.3×, 0–5: 0.6×, 5–10: 0.85×, >10: 1.0×}, floorPct: 30}
constraints:
  - {C-001, comfort, zoneTemp ≥ 19.0 °C all windows, source: EIG-CON-001207, hard}
  - {C-002, technical, compressor min-off 10 min, source: EIG-AST-004211, hard}
  - {C-003, network, CP import ≤ MIC, source: EIG-NET CP-004388, hard}
characterisedBy: EIG-RD-… (dispatch_shed, conf 0.84, recency 22 d)
```

```yaml
portfolioID: EIG-FLX-PF-0007
operatorRef: EIG-ACT-00031 (Aran Community Energy Aggregation CLG)
membershipRule: zone = EIG-NET-NZ-00017 ∧ flexType ∈ {FLX-AS, FLX-ST} ∧ status = verified
memberResourceIDs: [EIG-FLX-R-001482, … n=14]
diversityModel: {method: empirical coincidence (90 d telemetry), simultaneityFactor: {up: 0.72, down: 0.68}, evidenceRef: EIG-DSP-…}
portfolioEnvelope: {upKW: 31.4 (COMPUTED), downKW: 28.9, notice: 15 min}  # ≠ Σ members (43.6)
firmnessUpgrade: statistical members n=14 ≥ 10 ⇒ firm at 0.9 confidence for ≤60 min events
zoneRefs: [EIG-NET-NZ-00017] | status: verified
```

## 23. Completion Criteria

Complete when: every enrolled or offered capacity in 29/25 resolves to a FlexResource with parseable constraints; asset-anchored envelopes reconcile zero-diff with 11; all portfolios carry evidenced diversity models; stacking ledger live with atomic commitment writes; weather/occupancy couplings reference valid 09/03 structures; RD characterisation loop (52→gap→re-declaration) operational; Phase 14 confirms direction semantics uniform across 11/22/25/29/30.
