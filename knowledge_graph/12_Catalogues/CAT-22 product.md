---
type: catalogue
ontology: EIG-MLM
catalogue_no: 22
catalogue_id: EIG-CAT-022
entity: product
band: markets
status: active
tags: [eig-mlm, catalogue, band:markets, entity:product]
aliases: ["EIG-CAT-022", "Catalogue 22", "product"]
---

# CAT-22 — Product

> **EIG-CAT-022** · band: **Markets, Tariffs & Settlement** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Product Catalogue

## 1. Definition

The Product Catalogue is the authoritative register of all tradable energy, flexibility and service products in the EIG MLM framework. A product is a standardised, tradable specification of a deliverable — energy quantities, flexibility capabilities, reserve and frequency-response services, voltage support, carbon/attribute instruments and community energy products. Products are what markets (20) trade, bids reference, trades (25) instantiate, and settlement (27) verifies.

This catalogue defines product identity, typology, technical specification (the deliverable's physical contract: direction, magnitude, response time, duration, location), eligibility and prequalification requirements, measurement/verification basis, and market bindings. The product specification is the bridge between asset capability (11 §5.6 flexibility envelopes / RD evidence 52) and market commitment.

## 2. Scope

In scope:

- Energy products: settlement-period energy (DAM hourly/half-hourly), intraday contracts, P2P energy lots, community-matched energy.
- Flexibility products: load shed, load shift, turn-up, scheduled flexibility blocks (LFM tender products).
- Reserve products: replacement/operating reserve classes (POR/SOR/TOR/RR family per DS3-style definitions).
- Frequency-response products: FFR, dynamic response classes, ramping products.
- Voltage support products: reactive power/steady-state voltage services.
- Carbon/attribute products: GOs, community green attributes (instrument linkage to 28).
- Community products: shared self-consumption lots, community battery slots, EV-charging flexibility blocks.

Out of scope:

- Market venue rules (20), window timing (26), price levels (formed in markets/tariffs), trade instances (25), service operational orchestration (29 flexibility_service owns activation workflows; products define WHAT is sold, services define HOW it is delivered).

## 3. Classification Structure

Product family (`prdFamily`) — closed vocabulary:

| Code | Family | Examples |
|---|---|---|
| PRD-EN | energy | DAM hourly energy, ID half-hour energy, P2P lot |
| PRD-FLX | flexibility | shed, shift, turn_up, scheduled_block |
| PRD-RSV | reserve | por, sor, tor1, tor2, rr |
| PRD-FRQ | frequency_response | ffr_static, ffr_dynamic, ramping_1/3/8h |
| PRD-VLT | voltage_support | steady_state_reactive, dynamic_reactive |
| PRD-CRB | carbon_attribute | go_certificate, community_green_attribute |
| PRD-CMU | community | shared_self_consumption, community_storage_slot, ev_flex_block |

Product axes (orthogonal): direction (delivery/consumption/bidirectional/upward/downward), firmness (firm, conditional, best_effort), locationality (zonal, feeder_specific, connection_point), activation (scheduled, dispatched, autonomous_frequency_triggered).

## 4. Hierarchy

```text
ProductFamily (7)
└── ProductType (EIG-PRD-<FAM>-<NAME>-NN)   ← the standardised tradable spec
    └── ProductVariant (optional: duration/notice variants within a tender)
```

Product types are market-portable: the same EIG-PRD-FLX-SHED-01 may trade in several MKT-LFM venues; market bindings list which venues admit it (20 V4 symmetric check).

## 5. Field Groups & Fields

### 5.1 Identity & classification

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | productID | string | — | `EIG-PRD-<FAM>-<NAME>-NN` | M | Unique product ID |
| 2 | productName | string | — | snake_case ≤ 60 | M | Canonical name |
| 3 | prdFamily | enum | — | §3 codes | M | Family |
| 4 | carrierID | string | — | EIG-ECR-* (19) | M | Carrier denominated |
| 5 | direction | enum | — | §3 axis | M | Flow/response direction |
| 6 | firmness | enum | — | firm, conditional, best_effort | M | Delivery obligation strength |
| 7 | locationality | enum | — | §3 axis | M | Locational resolution |
| 8 | activation | enum | — | §3 axis | M | How delivery is triggered |
| 9 | allowedMarketTypes | array | — | MKT-* (20) | M | Venue types admitting it |
| 10 | status | enum | — | draft, active, deprecated | M | Lifecycle |

### 5.2 Technical specification (the physical contract)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | quantityUnit | enum | — | MWh, kWh, MW, kW, kvarh, kVAr, certificates | M | Traded unit |
| 2 | minQuantity / maxQuantity | decimal | unit | > 0 | M | Lot bounds |
| 3 | quantityResolution | decimal | unit | tick | M | Divisibility |
| 4 | responseTimeMax | duration | s/min | e.g. FFR ≤ 2 s; LFM shed ≤ 10 min | C | Required for dispatched/triggered products |
| 5 | sustainDurationMin/Max | duration | min/h | product-defined | C | Delivery duration envelope |
| 6 | recoveryTimeMax | duration | min/h | — | C | Max recovery before re-availability |
| 7 | rampRateMin | decimal | kW/min or MW/min | — | C | Ramping products |
| 8 | availabilityWindow | string | — | schedule ref (24) | C | When product must be available |
| 9 | noticePeriodMin | duration | min/h | — | C | Dispatch notice (scheduled/dispatched) |
| 10 | frequencyTrigger | object | — | {threshold_Hz, droop, deadband} | C | Autonomous frequency products |
| 11 | voltageSpec | object | — | {range_kVAr, powerFactor band, node ref} | C | Voltage products |
| 12 | reboundTreatment | enum | — | allowed_metered, capped, forbidden | C | Post-delivery rebound rule (joins 11 reboundFactorPct, 27 verification) |
| 13 | deliveryPointKind | enum | — | connection_point, feeder, zone, registry_account | M | Where delivery is measured |

### 5.3 Eligibility & prequalification

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | eligibleActorTypes | array | — | ACT-* (14) | M | Who may offer |
| 2 | eligibleAssetClasses | array | — | EIG-AC-* / flexibilityClass values (10) | M | Capable asset classes |
| 3 | minAssetTier | enum | — | T0–T3 (11) | M | Data-richness floor (T2+ for RD-verified products) |
| 4 | aggregationRules | object | — | {allowed, minPoolKW?, locationalConstraint?} | M | Pooling rules |
| 5 | prequalification | array | — | [{testCode, description, evidenceKind, validityMonths}] | C | Technical tests (response-time proof, telemetry capability) |
| 6 | rdRequirements | object | — | {required: bool, minConfidence, rdKinds[] (52), recencyMaxDays} | C | RD evidence gates for flexibility products |
| 7 | meteringRequirements | array | — | interval class, sub-metering, telemetry latency | M | M&V instrumentation preconditions |
| 8 | baselineEligibility | array | — | admissible baseline methods (27 registry) | C | Which baselines may verify delivery |

### 5.4 Measurement & verification basis

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | mvBasis | enum | — | metered_energy, baseline_delta, telemetry_power, certificate_registry, allocation_rule | M | How delivery is evidenced |
| 2 | verificationGranularityMin | integer | min | ≤ market settlement period | M | Verification interval |
| 3 | performanceThresholds | object | — | {fullDeliveryPct ≥, partialBand, failBelowPct} | M | Delivery grading (feeds 27 penalties, 12 MET-015) |
| 4 | telemetryRequirements | object | — | {latencyMaxS, resolutionS, signals[]} | C | Real-time evidence (dispatched products) |
| 5 | auditRetention | string | — | 93 schedule ref | M | Evidence retention |

### 5.5 Record metadata

recordVersion, createdDate, modifiedDate, owner, sourceOfTruth (market rulebook/product terms ref), governanceStatus — programme standard.

## 6. Controlled Vocabularies

Closed: prdFamily, direction, firmness, locationality, activation, quantityUnit, reboundTreatment, deliveryPointKind, mvBasis, prequal evidenceKind. External anchors: DS3/System Services product definitions (POR/SOR/TOR/FFR/ramping), SEM energy product conventions, EU GO framework (EECS), OpenADR event/report semantics at dispatch boundary.

## 7. Applicability Rules

| Rule | Statement |
|---|---|
| A1 | A product may be offered only by actors meeting eligibleActorTypes AND holding active market registration for it (20 A1) |
| A2 | Asset-backed offers: every underlying asset satisfies eligibleAssetClasses, minAssetTier, metering requirements; pooled offers satisfy aggregationRules and pool reconciliation (14) |
| A3 | rdRequirements.required products: offer capacity ≤ RD-evidenced capability at stated confidence; declared-vs-observed gap rules (11 §5.6) apply to offers |
| A4 | firmness = firm products carry penalty exposure (23/27); best_effort products cannot trade in markets requiring firm delivery (20 product admission) |
| A5 | Locational products (feeder_specific/connection_point) bind delivery points to network references (13); island_zone context constraints apply |
| A6 | FN-CRITICAL assets are NEVER eligible underlying for curtailment-direction products (10 illegal-pair rule; hard gate) |
| A7 | Carbon/attribute products require registry accounts (20 A7) and instrument linkage (28); no double counting across attribute products |

## 8. Validation Rules

| ID | Rule | Severity |
|---|---|---|
| V1 | productID unique; family prefix matches prdFamily; carrier resolves | error |
| V2 | allowedMarketTypes non-empty; symmetric with market.tradedProductIDs (20 V4) | error |
| V3 | min ≤ max quantity; resolution divides bounds | error |
| V4 | Dispatched/triggered products: responseTimeMax, sustainDuration, notice/trigger spec complete | error |
| V5 | rdRequirements.required ⇒ minAssetTier ≥ T2 and rdKinds resolve (52) | error |
| V6 | mvBasis = baseline_delta ⇒ baselineEligibility non-empty and methods exist (27) | error |
| V7 | performanceThresholds: failBelow < partial ≤ fullDelivery ≤ 100 | error |
| V8 | telemetryRequirements present when mvBasis = telemetry_power | error |
| V9 | Eligible asset classes' flexibilityClass compatible with direction (sheddable→downward, bidirectional→both, none→ineligible) | error |
| V10 | A6 critical-asset exclusion enforced in eligibility expansion | error |
| V11 | Deprecation blocked while any market lists or any open trade references the product | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 10/11 asset | Capability eligibility; flex envelopes back offers; tier gates |
| 13 network | Delivery points, locational binding, island constraints |
| 14 actor | Offer eligibility, pool rules, registrations |
| 19 carrier | Denomination and units |
| 20 market | Venue admission (symmetric binding) |
| 23 contract | Product terms incorporated into participation/flex contracts; penalty regimes |
| 25 trade / 26 window | Trades instantiate products inside windows; window product-eligibility lists |
| 27 settlement | M&V basis, thresholds, baselines, penalties executed |
| 28 carbon | Attribute instruments; avoided-emissions claims for flex products |
| 29/30 flexibility | Services deliver products; resource characteristics map to product specs |
| 52 RD | Capability evidence (A3); product specs define the RD expression frame (response time, duration, magnitude) |
| 12 asset_benchmark | flex_delivery_success (MET-015) measured against product thresholds |

## 10. Benchmarking Requirements

Product-level delivery statistics (acceptance, full/partial/fail rates) per cohort (asset class × product), min n = 10; thresholds from §5.4 define the grading. Cross-product comparability via normalised delivery-success and €/kW-delivered metrics.

## 11. Dataset Requirements

Product registry snapshots versioned; per-product delivery evidence datasets (telemetry windows, baselines, metered deltas) retained per auditRetention; prequalification test artefacts stored with validity tracking.

## 12. Feature Requirements

Features: product-eligibility flags per asset (derived), offerable-capacity (RD-bounded), historical delivery-success by product, product price statistics (from 20/25). Eligibility-derivation features recompute on asset/RD/product version changes.

## 13. Model Requirements

Capacity-estimation models map asset/pool state → offerable quantity per product (respecting A3); delivery-risk models predict performance vs thresholds; portfolio bid optimisers allocate capacity across products subject to conflict rules (20 A6) and recovery constraints (§5.2).

## 14. KPI Requirements

KPIs: offered vs delivered by product, delivery success rate, revenue per product, prequal pass rate, rebound compliance. Product dimension mandatory on all flexibility KPIs (57).

## 15. Response Derivative Requirements

Products define the canonical RD expression frames: an RD supporting EIG-PRD-FLX-SHED-01 must express ΔkW sustained over the product's duration at its response time, with confidence ≥ rdRequirements.minConfidence and recency ≤ recencyMaxDays. RD records (52) tag supported productIDs; product spec changes trigger RD re-expression.

## 16. Decision Requirements

Offer decisions validate: eligibility (A1–A2), RD-backed capacity (A3), conflict-free commitment (20 A6), recovery feasibility vs existing commitments, penalty-adjusted expected value. Dispatch acceptance decisions verify activation feasibility at decision time (asset availability, comfort/authority constraints) before confirming.

## 17. Ontology Mapping

Classes: Product ⊐ {EnergyProduct, FlexibilityProduct, ReserveProduct, FrequencyResponseProduct, VoltageSupportProduct, CarbonAttributeProduct, CommunityProduct}, ProductSpecification, PrequalificationRequirement, MVBasis, PerformanceThreshold.
Object properties: denominatedIn (→ Carrier), admittedTo (→ Market), deliverableBy (→ AssetClass), requiresEvidence (→ RDKind), verifiedBy (→ BaselineMethod), instantiatedAs (→ Trade), deliveredThrough (→ FlexibilityService).
Datatype properties: productID, prdFamily, direction, firmness, responseTimeMax, sustainDurationMin/Max, minConfidence.
Alignment: USEF flexibility product framework; ENTSO-E/DS3 service taxonomies; OpenADR 3 event/report types at dispatch; EECS for attribute certificates.

## 18. Knowledge Graph Mapping

Nodes: Product, PrequalRequirement, PerformanceThreshold. Relationships: DENOMINATED_IN, ADMITTED_TO (→ Market), DELIVERABLE_BY (→ AssetClass), REQUIRES_RD (→ RDKind), VERIFIED_BY (→ BaselineMethod), INSTANTIATED_AS (→ Trade), SUPPORTED_BY (→ RD records, temporal). Keys: productID.

## 19. Digital Twin Mapping

Twins simulate product delivery pre-offer: dispatch the twin against product spec (response, duration, rebound) to estimate deliverable capacity and comfort impact; prequal rehearsals in sandbox; delivered-event replays for dispute support (27).

## 20. Governance

Owner: EIG Catalogue Owner; market-operations co-own venue-linked specs (tracking DS3/LFM tender documents); product additions require M&V design review + settlement owner sign-off. Sandbox product variants flagged and firewalled (20 A2).

## 21. Versioning

Product specs semver: threshold/test changes minor; physical-contract changes (response time, duration, direction) major — open offers grandfather the version pinned at gate closure (26), new offers use current. Trades pin productVersion.

## 22. Example Records

```text
productID: EIG-PRD-FLX-SHED-01 | load_shed_10min | PRD-FLX | carrier: EL | direction: downward | firmness: firm
locationality: feeder_specific | activation: dispatched | allowedMarketTypes: [MKT-LFM]
spec: quantity kW, min 10 / max 2000, tick 1 kW | responseTimeMax 10 min | sustain 30 min–2 h | recovery ≤ 4 h
reboundTreatment: capped (≤ 120% pre-event for 1 h) | deliveryPointKind: connection_point
eligibility: actors [ACT-AGG, ACT-FLX]; assets flexibilityClass ∈ {sheddable, storable, bidirectional}; minAssetTier T2
rdRequirements: {required, minConfidence 0.8, rdKinds [RD-SHED-EL], recency ≤ 90 d}
mvBasis: baseline_delta | baselineEligibility: [BL-LFM-01] | thresholds: full ≥ 95%, partial 70–95%, fail < 70%
telemetry: 1-min power, latency ≤ 60 s | prequal: [{PRQ-LFM-01 response test, witness dispatch, 24 mo}]
```

```text
productID: EIG-PRD-EN-HH-01 | settlement_period_energy | PRD-EN | carrier: EL | direction: bidirectional | firmness: firm
locationality: zonal | activation: scheduled | allowedMarketTypes: [MKT-DAM, MKT-IDM]
spec: quantity MWh, tick 0.1, hourly/half-hourly per window (26) | mvBasis: metered_energy | deliveryPoint: connection_point→BRP perimeter
```

```text
productID: EIG-PRD-FRQ-FFR-01 | ffr_dynamic | PRD-FRQ | carrier: EL | direction: bidirectional | activation: autonomous_frequency_triggered
trigger: {49.8 Hz threshold class, droop per prequal, deadband ±0.015 Hz} | responseTimeMax: 2 s | sustain: ≥ 8 s to POR handover
eligibility: assets [EIG-AC-STO-01-* batteries], minAssetTier T3, telemetry 100 ms class | mvBasis: telemetry_power
```

## 23. Completion Criteria

Complete when: every venue-traded product registered with full physical contract, eligibility, M&V; market↔product bindings symmetric; RD gates resolvable; critical-asset exclusions verified; thresholds executable in settlement; Phase 14 confirms trades/bids reference products solely from this catalogue.
