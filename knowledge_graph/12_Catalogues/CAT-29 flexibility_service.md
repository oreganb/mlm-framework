---
type: catalogue
ontology: EIG-MLM
catalogue_no: 29
catalogue_id: EIG-CAT-029
entity: flexibility_service
band: flexibility
status: active
tags: [eig-mlm, catalogue, band:flexibility, entity:flexibility_service]
aliases: ["EIG-CAT-029", "Catalogue 29", "flexibility_service"]
---

# CAT-29 — Flexibility Service

> **EIG-CAT-029** · band: **Flexibility & Platform** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID prefix:** `EIG-FS`
**Catalogue number:** 29 of 94
**Layer:** Flexibility & Platform
**Version:** 1.0.0
**Status:** ACTIVE

---

## 1. Definition

The Flexibility Service Catalogue defines every **flexibility service**: a named, governed offering through which flexibility resources (30) are recruited, activated, dispatched and remunerated for a defined purpose (demand response, frequency regulation, voltage support, reserves, thermal flexibility, behind-the-meter optimisation, market flexibility, P2P flexibility). A service is the operational bridge between the commercial layer (products 22, contracts 23, markets 20) and the physical layer (assets 11, buildings 02): products define *what is sold*, services define *how delivery is organised* — eligibility, enrolment, activation signalling, dispatch logic, measurement, and settlement hand-off.

Service IDs (`EIG-FS-*`) are already woven through upstream catalogues (asset enrolments 11 §65, building capability domains 01, contract serviceEnrolments 23): this catalogue is their authoritative register.

## 2. Scope

**In scope:** service definitions and their lifecycle; eligibility criteria (technical, contractual, data-richness, RD); enrolment process states; activation/dispatch signalling contracts (latencies, channels per 34); delivery measurement hand-off to baselines (27 BL-*) and RD evidence (52/56); service-level constraints (frequency caps, comfort floors, recovery rules); stacking/exclusivity rules across services; sandbox service variants.

**Out of scope:** the tradable product economics (22), the flexibility physics of resources (30), market venue rules (20), settlement computation (27), control execution (62 control_strategy), individual activation events (56).

## 3. Classification Structure

`serviceClass`:

| Code | Class | Description |
|---|---|---|
| FS-DR | demand_response | Load shed/shift on signal (implicit price-based or explicit dispatch) |
| FS-FRQ | frequency_regulation | FFR/POR/SOR-style frequency products delivery organisation |
| FS-VLT | voltage_support | Local voltage management via reactive/active power |
| FS-RSV | reserve_capacity | Standing/replacement reserve availability |
| FS-THM | thermal_flexibility | Heat/cool storage in fabric, DHW, networks (carriers HT/CL, 19) |
| FS-BTM | behind_the_meter | Self-consumption optimisation, peak shaving, tariff response (21) |
| FS-MKT | market_flexibility | Wholesale/LFM-facing aggregated flexibility (20) |
| FS-P2P | p2p_flexibility | Peer-to-peer flexibility exchange within communities (86) |

Secondary axes: `activationMode` (price_signal, explicit_dispatch, autonomous_rule, schedule_based); `aggregationLevel` (asset, building, portfolio, community); `sandbox` (boolean, NEST variants).

## 4. Hierarchy

```
ServiceDefinition (EIG-FS-<CLASS>-<NN>)        ← this catalogue's primary record
├── EligibilityRuleSet (embedded)
├── ActivationContract (embedded: signal, latency, channel)
├── DeliveryRuleSet (embedded: measurement, baseline, grading)
└── Enrolment (EIG-FS-ENR-*)                   ← (participant, resource, service, contract clause)
      └── activations realised as ResponseEvents (56) and dispatch Schedules (24)
```

## 5. Field Groups & Fields

### 5.1 Identity & Classification

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | serviceID | string | — | `EIG-FS-[A-Z]+-[0-9]{2}` | M | Service identifier (e.g. EIG-FS-DR-01) |
| 2 | serviceName | string | — | ≤120 chars | M | Human-readable name |
| 3 | serviceClass | string | — | §3 codes | M | Service class |
| 4 | activationMode | string | — | §3 vocab | M | How activations are triggered |
| 5 | aggregationLevel | string | — | asset, building, portfolio, community | M | Unit of delivery |
| 6 | operatorRef | string | — | EIG-ORG-*/EIG-ACT-* (15/14), typically ACT-AGG/ACT-DSO/ACT-PLT | M | Who runs the service |
| 7 | status | string | — | design, pilot, active, suspended, retired | M | Lifecycle |
| 8 | sandbox | boolean | — | true/false | M | NEST sandbox variant (20 A2 firewall inherits) |

### 5.2 Commercial Binding

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | productRefs | array | — | EIG-PRD-* (22) | C | Products this service delivers into (mandatory for FS-MKT/FS-FRQ/FS-RSV) |
| 2 | marketRefs | array | — | EIG-MKT-* (20) | C | Venues; consistent with product↔market bindings (22 V2) |
| 3 | contractTemplateRef | string | — | EIG-CON-* template/framework (23) | M | Enrolment contract basis; entitlement chain 23 A1 |
| 4 | tariffInteraction | string | — | none, implicit_via_tariff (EIG-TRF-*, 21), stacked | M | Price-signal services bind to dynamic/ToU tariffs |
| 5 | settlementClass | string | — | SET-FLX, SET-SUP, SET-CMU, none (27) | M | Settlement hand-off class |
| 6 | baselineMethodRef | string | — | BL-* (27) | C | Mandatory when settlementClass=SET-FLX |

### 5.3 Eligibility Rules

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | eligibleAssetClasses | array | — | EIG-AC-* subtypes + flexibilityClass values (10) | M | Technical eligibility; FN-CRITICAL assets excluded always (10 illegal pair, 22 A6) |
| 2 | minFlexCapacityKW | decimal | kW | >0 | C | Minimum per-resource envelope (11 §5.6) |
| 3 | dataRichnessTierMin | string | — | T0–T3 (11) | M | T2+ wherever RD evidence is required |
| 4 | rdRequirements | object | — | {minConfidence: 0–1, maxRecencyDays: int, rdKinds: [52 kinds]} | C | RD gates; must be ≥ product gates where productRefs present (22 §15) |
| 5 | controlAuthorityMin | string | — | 02 ladder values (monitor_only … autonomous_within_envelope) | M | Minimum granted authority for activationMode; explicit_dispatch requires ≥ supervised_control |
| 6 | consentScopes | array | — | consent register scopes (14) | M | GDPR-relevant scopes for occupancy-coupled services (03) |
| 7 | zoneEligibility | array | — | EIG-NET-NZ-*/EIG-GEO-* (13/08) | C | Spatial limits (mandatory FS-VLT, locational FS-MKT) |
| 8 | prequalificationTest | object | — | {testProtocol ref, passCriteria} | C | Physical test before first activation (FS-FRQ mandatory) |

### 5.4 Activation Contract

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | signalSchema | string | — | message schema ref (34) | M | Activation payload definition |
| 2 | signalChannel | array | — | protocol/channel refs (34: MQTT topic, HTTPS webhook, LoRaWAN downlink class) | M | Delivery channels with priority order |
| 3 | maxSignalLatencySec | decimal | s | >0 | M | Signal emission→receipt budget; FS-FRQ ≤ 1 s, FS-DR explicit ≤ 60 s, price_signal ≤ 900 s |
| 4 | responseTimeRequirement | string | — | inherited from product (22 §5 physical contract) or stated | M | Receipt→power-change requirement |
| 5 | acknowledgementRequired | boolean | — | true/false | M | Edge ack contract (35) |
| 6 | failsafeBehaviour | string | — | revert_to_schedule, hold_last, local_rule | M | On signal loss; never violates comfort floors (03/23) |
| 7 | overrideHonoured | boolean | — | true/false | M | Occupant override always honoured for residential (03 overrideRate feeds RD discounting) |

### 5.5 Delivery & Constraint Rules

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | maxActivationsPerWeek | integer | — | ≥0 | C | Service-level cap; contractual envelope may tighten (23) |
| 2 | maxEventDurationMin | integer | min | >0 | M | Per-event ceiling |
| 3 | minRecoveryMin | integer | min | ≥0 | M | Between events (asset recovery, 11 §5.6) |
| 4 | comfortFloors | object | — | {tempMinC, tempMaxC, co2MaxPpm, dhwMinC} | C | Mandatory for occupancy-coupled classes (FS-DR/FS-THM/FS-BTM residential) |
| 5 | reboundTreatment | string | — | settled, capped, ignored — consistent with product (22) | M | Rebound accounting |
| 6 | measurementSpec | object | — | {meterResolutionMin, telemetryVars (38), evidenceBundle} | M | Delivery measurement requirements |
| 7 | deliveryGrading | object | — | thresholds (default 22: full ≥95 / partial 70–95 / fail <70) | M | Grade mapping for settlement lines (27 §6 bridge) |
| 8 | stackingRules | array | — | [{otherServiceID, relation: exclusive/stackable/conditional}] | M | Cross-service exclusivity; same capacity never double-committed (20 A6) |

## 6. Controlled Vocabularies

- `serviceClass`, `activationMode`, `aggregationLevel`: §3/§5.
- Enrolment state machine: `applied → eligibility_check → prequalifying → enrolled → suspended ⇄ enrolled → withdrawn | terminated` (eligibility re-checked on contract amendment 23, asset re-declaration 11, RD staleness 52).
- Activation lifecycle (realised in 56, governed here): `signalled → acknowledged → responding → sustaining → released → recovering → evidence_closed`, failure branches `not_acknowledged`, `under_delivered`, `overridden` (occupant), `aborted_safety`.
- `failsafeBehaviour`: revert_to_schedule, hold_last, local_rule.
- `reboundTreatment`: settled, capped, ignored.

## 7. Applicability Rules

| # | Rule |
|---|---|
| A1 | Every enrolment traces to an active contract clause (entitlement chain 23 A1); enrolments without contractual basis are invalid. |
| A2 | Service eligibility may only tighten, never loosen, the gates of any product it delivers (22 §15); rdRequirements here ≥ product rdRequirements. |
| A3 | FN-CRITICAL functions are never enrolled in curtailment-class services (10/22 A6); life-safety circuit exclusions (04) propagate to asset eligibility. |
| A4 | Occupant override is always honoured where overrideHonoured=true; overridden events grade per deliveryGrading but never penalise beyond contract terms, and feed overrideRate/reboundPropensity statistics (03). |
| A5 | Stacking: a resource's flex envelope (11 §5.6) is partitioned across stacked services; Σ committed capacity per direction per window ≤ envelope; exclusivity rules enforced at offer time (20 A6). |
| A6 | Sandbox services activate only sandbox/simulated resources or real resources in declared test mode with consent; outcomes firewalled (20 A2). |
| A7 | Comfort floors are hard constraints: dispatch optimisation (60) and control strategies (62) treat them as inviolable; settlement never rewards floor violations. |
| A8 | Signal latency budgets bind communication (34) and edge (35) designs: a service may not be activated over a channel whose measured p99 latency exceeds maxSignalLatencySec. |

## 8. Validation Rules

| # | Rule | Severity |
|---|---|---|
| V1 | serviceID unique, pattern per §5.1 | error |
| V2 | productRefs/marketRefs consistent with 22 V2 bindings | error |
| V3 | settlementClass=SET-FLX ⇒ baselineMethodRef present and approved (27 A4) | error |
| V4 | rdRequirements ≥ product gates where bound (A2) | error |
| V5 | eligibleAssetClasses contain no FN-CRITICAL∧curtailment combinations (A3) | error |
| V6 | Latency budgets vs serviceClass minima (FS-FRQ ≤ 1 s etc.) | error |
| V7 | comfortFloors present for occupancy-coupled classes at residential aggregation | error |
| V8 | stackingRules symmetric: if A declares exclusive-with-B, B declares exclusive-with-A (Phase 14 check) | error |
| V9 | Enrolment uniqueness: one active enrolment per (resource, service); per (resource capacity direction, window) Σ commitments ≤ envelope (A5) | error |
| V10 | controlAuthorityMin consistent with activationMode (explicit_dispatch ⇒ ≥ supervised_control) | error |
| V11 | sandbox flag consistency with market/product sandbox class | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 30 flexibility | Resources/portfolios enrolled; envelopes partitioned (A5) |
| 22 product | Delivery targets; physical contract inheritance; gate ordering (A2) |
| 20 market | Venues; conflict rules; sandbox firewall |
| 23 contract | Enrolment basis (A1); contractual envelope ≤ technical envelope |
| 21 tariff | Implicit price-signal services |
| 27 settlement | Settlement hand-off; baselines; delivery grading bridge |
| 24 schedule | Dispatch schedules from accepted activations |
| 56 response_event | Activation realisations and evidence |
| 52 RD | Eligibility gates; evidence; observed-performance feedback |
| 11/10 asset | Envelopes, flexibilityClass eligibility, enrolment lists (11 §65) |
| 02/03/04 building/occupancy/space | Capability domains, comfort floors, overrides, circuit exclusions |
| 34/35 communication/edge | Signal channels, latency enforcement, failsafe execution |
| 14/15 actor/org | Operators, participants, consent scopes |
| 62 control_strategy | Activation execution logic |

## 10. Benchmarking Requirements

Service performance benchmarks: activation success rate (delivered_full share), mean response time vs requirement, override rate (residential), availability attainment, enrolment churn, kW delivered per enrolled kW. Min cohort n=10 enrolments/instances; immutable releases; ties to EIG-ABM-MET-015 flex_delivery_success at asset level (12).

## 11. Dataset Requirements

Enrolment registers and activation evidence retained ≥ 7 years (settlement audit alignment 27); signal logs with emission/receipt/ack timestamps (latency verification A8/V6); consent-scoped occupancy data handled per GDPR classes (03/14); sandbox activation data partitioned.

## 12. Feature Requirements

Features: rolling activation success per enrolment, fatigue indicators (success decay vs activation frequency — feeds maxActivationsPerWeek tuning), override probability by context (03), available-now capacity per service (envelope minus commitments), latency p99 per channel. Sandbox features firewalled.

## 13. Model Requirements

Dispatch-selection models (60/72) rank enrolments by RD confidence × availability × cost; override-prediction models use consented behavioural features only; availability forecasting per portfolio (30) validated against realised events (56); no model may bypass comfort floors or stacking checks (A5/A7 enforced post-model in deterministic guards).

## 14. KPI Requirements

KPIs (57): delivered/contracted % per service, response-time p95, override rate, comfort-floor violation count (target 0), revenue per enrolled kW (joins 27), recruitment funnel conversion. All dimensioned by serviceClass, aggregationLevel, zone.

## 15. Response Derivative Requirements

Services are the primary RD consumers and producers: eligibility gates use RD confidence/recency (§5.3); every activation generates RD observation evidence (56→52) updating observed performance; declared-vs-observed gaps propagate to asset re-declaration (11 >25% rule); service-level RD priors aggregate enrolment RDs respecting portfolio diversity (30). Price-signal services use price-elasticity RDs (52 kinds) rather than dispatch RDs.

## 16. Decision Requirements

Decisions: enrolment acceptance (eligibility engine, deterministic); dispatch selection (optimisation 60 under stacking/envelope/comfort constraints); suspension on repeated under-delivery (threshold per service, workflow 64); service design changes (status transitions) require T3+ authority (16). Activation abort decisions (safety) execute locally at edge (35) without round-trip.

## 17. Ontology Mapping

Class: `eig:FlexibilityService` ⊑ `eig:Service`; `eig:ServiceEnrolment` ⊑ `eig:Agreement-linked entity`. Object properties: `eig:deliversProduct` (→ eig:Product), `eig:operatedBy` (→ eig:Actor), `eig:enrols` (→ eig:FlexResource), `eig:activatesVia` (→ eig:CommunicationChannel), `eig:settledAs` (→ eig:SettlementClass), `eig:usesBaseline` (→ eig:BaselineMethodology). Datatype: serviceID, serviceClass, activationMode, maxSignalLatencySec, maxActivationsPerWeek. Alignments: SAREF4ENER flexibility concepts, USEF flexibility value chain roles, OpenADR event/report model — design-intent level per SHIFT practice.

## 18. Knowledge Graph Mapping

Nodes: `(:FlexService {serviceID, serviceClass, status, sandbox})`, `(:Enrolment {state})`. Edges: `(:Enrolment)-[:IN_SERVICE]->(:FlexService)`, `(:Enrolment)-[:OF_RESOURCE]->(:Asset|:Building|:Portfolio)`, `(:Enrolment)-[:UNDER_CONTRACT]->(:Contract)`, `(:FlexService)-[:DELIVERS]->(:Product)`, `(:FlexService)-[:EXCLUSIVE_WITH|STACKABLE_WITH]->(:FlexService)`, `(:ResponseEvent)-[:ACTIVATION_OF]->(:Enrolment)`. Capacity-commitment queries traverse enrolment edges with window filters.

## 19. Digital Twin Mapping

Twins (32) simulate activations pre-dispatch (feasibility vs comfort/envelope), shadow-grade delivery in real time, and host sandbox service variants in NEST where new service designs are trialled against the virtual grid before pilot status. Template twins (33) declare which services a twin class can simulate.

## 20. Governance

Owner: Flexibility Services Lead. Steward: operations engineer (enrolment register, signal infrastructure). Reviewer: market/DSO liaison. Approver: T3+ (16); new service classes T4. Duties per 16 A6. Comfort floor and override policies reviewed with consumer-protection lens (CRU sandbox conditions where applicable, 15 EIG-ORG-0102).

## 21. Versioning

Semver per service definition. MAJOR: eligibility tightening affecting existing enrolments, activation contract changes; existing enrolments re-validated with transition window. MINOR: new channels, widened eligibility. PATCH: editorial. Enrolments are stateful records with immutable event history. ChangeLog mandatory.

## 22. Example Records

```yaml
serviceID: EIG-FS-DR-01
serviceName: Residential Load Shed (Explicit Dispatch)
serviceClass: FS-DR | activationMode: explicit_dispatch | aggregationLevel: asset
operatorRef: EIG-ORG-0044 (Aran co-op aggregation) | status: active | sandbox: false
productRefs: [EIG-PRD-FLX-SHED-01] | marketRefs: [EIG-MKT-LFM-01]
contractTemplateRef: EIG-CON flexumer enrolment framework (e.g. EIG-CON-001207)
settlementClass: SET-FLX | baselineMethodRef: BL-LFM-01
eligibility:
  eligibleAssetClasses: [EIG-AC-HVC-01-01 ashp (modulating), EIG-AC-STO-01-01 li_ion (bidirectional)]
  minFlexCapacityKW: 2.0 | dataRichnessTierMin: T2
  rdRequirements: {minConfidence: 0.8, maxRecencyDays: 90, rdKinds: [dispatch_shed]}
  controlAuthorityMin: supervised_control | consentScopes: [occupancy_indicators, hvac_control]
activation:
  signalSchema: EIG-COM-SCH-FLEX-DISPATCH-v2 (34) | signalChannel: [MQTT primary, HTTPS fallback]
  maxSignalLatencySec: 60 | responseTimeRequirement: ≤10 min (22)
  acknowledgementRequired: true | failsafeBehaviour: revert_to_schedule | overrideHonoured: true
delivery:
  maxActivationsPerWeek: 2 (contract may tighten) | maxEventDurationMin: 120 | minRecoveryMin: 240
  comfortFloors: {tempMinC: 19.0, dhwMinC: 50.0}
  reboundTreatment: capped | deliveryGrading: {full: ≥95, partial: 70–95, fail: <70}
  stackingRules: [{EIG-FS-FRQ-01, exclusive}, {EIG-FS-BTM-01, conditional: non-overlapping windows}]
```

```yaml
serviceID: EIG-FS-DR-02
serviceName: Commercial ToU Shift (Price Signal)
serviceClass: FS-DR | activationMode: price_signal | aggregationLevel: building
operatorRef: EIG-ACT-00031 | status: active
tariffInteraction: implicit_via_tariff (EIG-TRF-0007 Smart ToU) | settlementClass: SET-SUP
rdRequirements: {rdKinds: [price_elasticity], minConfidence: 0.6}
maxSignalLatencySec: 900 | overrideHonoured: true
```

## 23. Completion Criteria

Complete when: all EIG-FS-* IDs cited upstream (01/02/11/14/23) resolve to definitions here; every active service has executable eligibility, activation and delivery rule sets; stacking symmetry verified (V8); latency budgets verified against measured channel performance (34/35); sandbox variants registered for services under NEST trial; Phase 14 confirms enrolment↔contract↔asset↔settlement chains resolve with zero orphans.
