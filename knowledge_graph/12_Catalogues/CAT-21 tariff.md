---
type: catalogue
ontology: EIG-MLM
catalogue_no: 21
catalogue_id: EIG-CAT-021
entity: tariff
band: markets
status: active
tags: [eig-mlm, catalogue, band:markets, entity:tariff]
aliases: ["EIG-CAT-021", "Catalogue 21", "tariff"]
---

# CAT-21 — Tariff

> **EIG-CAT-021** · band: **Markets, Tariffs & Settlement** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Tariff Catalogue

## 1. Definition

The Tariff Catalogue is the authoritative register of all tariff structures used in the EIG MLM framework: static, time-of-use, dynamic, real-time, critical-peak, export, network and community tariffs. A tariff is a priced rule structure that maps metered or allocated energy quantities (and capacity/standing elements) to monetary amounts over time. Tariffs are the price interface between suppliers/network operators/communities and end parties; they drive cost modelling, price-response analysis, optimisation objectives and market-facing decisions.

This catalogue defines tariff identity, typology, rate structures (time bands, blocks, indexed formulas), applicability (who/where/which meter class), composition (energy + network + levies), validity, and the machine-executable rate-resolution contract: given (carrier, quantity, timestamp, location, customer class), every tariff must deterministically resolve to a price.

## 2. Scope

In scope:

- Supply tariffs: flat/standard, day/night, smart ToU (per ESB Networks smart-meter time bands), dynamic (DAM-indexed), real-time pricing pilots, critical-peak pricing/rebates.
- Export tariffs: CEG-style export remuneration, community export arrangements.
- Network tariffs: DUoS/TUoS structures by DSO customer group (DG1–DG10 style classes), capacity charges (MIC-based), standing charges.
- Levies/obligations as tariff components: PSO, electricity tax — as named components with current-value references.
- Community tariffs: internal CEC pricing, sharing-scheme allocation prices (binds 20 MKT-CMU).
- Multi-carrier tariffs: gas, district heat, water where billed.

Out of scope:

- Market clearing prices (20 publishes; dynamic tariffs INDEX them).
- Contract terms beyond pricing (23). Settlement computation (27 executes tariffs; this catalogue defines them).
- Tax law interpretation (component values referenced with effective dates; authority is the levy issuer).

## 3. Classification Structure

Tariff type (`tariffType`) — closed vocabulary:

| Code | Type | Price determination |
|---|---|---|
| TRF-FLAT | static_flat | Single rate per kWh |
| TRF-TOU | time_of_use | Fixed calendar/clock bands (day/night/peak) |
| TRF-DYN | dynamic_indexed | Formula on market reference price (e.g. DAM hourly + margin) |
| TRF-RTP | real_time | Interval-level price published near-real-time |
| TRF-CPP | critical_peak | ToU base + event-triggered peak price/rebate |
| TRF-EXP | export | Remuneration for exported energy |
| TRF-NET | network | DUoS/TUoS/capacity/standing structures |
| TRF-LEV | levy_component | PSO/tax components |
| TRF-CMU | community | Intra-community pricing/allocation |
| TRF-BLK | block_volumetric | Rising/declining block rates (water/gas patterns) |

Direction: import, export, bidirectional. Charge basis (`chargeBasis`, per component): per_kWh, per_kW_capacity, per_day_standing, per_event, per_kVArh, percent_of_energy_cost, fixed_per_period.

## 4. Hierarchy

```text
TariffPlan (EIG-TRF-NNNN)                ← the published, subscribable plan
└── TariffComponent (1..n)               ← energy | network | levy | standing | capacity | event
    └── RateStructure                    ← flat | bands[] | blocks[] | formula | priceFeed
        └── RateValue(s) with validity   ← effective-dated, versioned
```

A customer-facing bill price = Σ components resolved at each interval. Composite plans REFERENCE shared components (e.g. one DUoS component reused across supplier plans) — components are first-class records (`EIG-TRF-CMP-NNNN`).

## 5. Field Groups & Fields

### 5.1 Tariff plan identity

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | tariffID | string | — | `EIG-TRF-NNNN` | M | Unique plan ID |
| 2 | tariffName | string | — | ≤ 120 chars | M | Commercial/official name |
| 3 | tariffType | enum | — | §3 TRF-* | M | Primary type (dominant energy component) |
| 4 | direction | enum | — | import, export, bidirectional | M | Flow direction priced |
| 5 | carrierID | string | — | EIG-ECR-* (19) | M | Single carrier per plan |
| 6 | issuerActorID | string | — | EIG-ACT (ACT-SUP/DSO/CEC/PLT) | M | Issuing party |
| 7 | currency | string | — | EUR | M | Currency |
| 8 | vatTreatment | object | — | {ratePct, basis} | M | VAT application |
| 9 | validFrom / validTo | date | — | ISO 8601 | M | Plan validity (open-ended allowed) |
| 10 | status | enum | — | draft, published, closed_to_new, withdrawn | M | Lifecycle |
| 11 | componentRefs | array | — | EIG-TRF-CMP-NNNN[] ordered | M | Composition |

### 5.2 Applicability

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | customerClasses | array | — | residential, sme, commercial, industrial, public_sector, community_member | M | Eligible classes |
| 2 | dsoGroupCodes | array | — | DG-style network groups | C | Network tariff applicability |
| 3 | meterRequirements | array | — | interval_30min, interval_15min, smart_capable, export_registered, mic_declared | M | Metering preconditions (joins 13 CP / 11 MTR assets) |
| 4 | geographicScope | object | — | EIG-GEO/NET refs or national | M | Where offered |
| 5 | contractRequirement | string | — | EIG-CON template ref (23) | C | Plan offered only under contract template |
| 6 | maxMIC_kVA / minMIC_kVA | decimal | kVA | ≥ 0 | C | Capacity band gates |
| 7 | enrolmentConstraints | array | — | e.g. requires CEG registration, community membership (86) | C | Additional gates |

### 5.3 Tariff components (EIG-TRF-CMP records)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | componentID | string | — | EIG-TRF-CMP-NNNN | M | Component ID |
| 2 | componentKind | enum | — | energy, network_duos, network_tuos, capacity, standing, levy_pso, levy_tax, event_peak, event_rebate, export_remuneration, community_allocation | M | Kind |
| 3 | chargeBasis | enum | — | §3 bases | M | Unit basis |
| 4 | rateStructureKind | enum | — | flat, time_bands, blocks, formula_indexed, price_feed, event_schedule | M | Structure |
| 5 | rateStructure | object | — | kind-specific (below) | M | The executable structure |
| 6 | issuerActorID | string | — | component issuer (DSO for DUoS) | M | Authority for values |
| 7 | valueVersions | array | — | [{values, effectiveFrom, effectiveTo, sourceRef}] | M | Effective-dated values; append-only |

Rate-structure schemas:

- **flat**: `{rate}`.
- **time_bands**: `{calendar: {timezone: Europe/Dublin, dstRule: local_clock}, bands: [{bandCode, name, clockRanges[], dayTypes[], months[], rate}]}`. Band codes closed: NIGHT, DAY, PEAK, EV_NIGHT, WEEKEND. Bands MUST tile the full year with no gaps/overlaps (V6). Smart-standard bands: day 08–23, night 23–08, peak 17–19 (per ESBN ToU convention; versioned).
- **blocks**: `{period: billing_month, blocks: [{upTo_kWh, rate}], finalRate}` — monotone thresholds (V7).
- **formula_indexed**: `{formula: "max(floor, a*REF + b)", refPriceCode (20 §5.4), floor?, cap?, resolutionMin}` — references published market prices; lag/averaging windows explicit.
- **price_feed**: `{feedRef (37 source), resolutionMin, publicationLatencyMax, fallbackRule}` — RTP; fallback MANDATORY (V9).
- **event_schedule**: `{trigger: issuer_declared, noticeMinH, maxEventsPerYear, maxDurationH, eventRate or rebateRate, windowConstraints}` — CPP/CPR; caps mandatory (V10).

### 5.4 Price-resolution contract

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | resolutionGranularityMin | integer | min | ≤ settlement period (27) | M | Finest interval the plan resolves to |
| 2 | resolutionFunction | string | — | normative pseudocode/ref | M | Deterministic: (carrier, qty, ts, location, class, events) → €; total = Σ components |
| 3 | dstHandling | enum | — | local_clock_bands, utc_fixed | M | DST rule (local_clock for IE ToU) |
| 4 | roundingRule | object | — | {perInterval: 4dp €, perBill: 2dp, method: half_even} | M | Deterministic rounding |
| 5 | priceCalendarPublication | string | — | where resolved calendars publish (37) | C | For DYN/RTP transparency |

### 5.5 Record metadata

recordVersion, createdDate, modifiedDate, owner, sourceOfTruth (issuer publication ref), governanceStatus — programme standard.

## 6. Controlled Vocabularies

Closed: tariffType, direction, componentKind, chargeBasis, rateStructureKind, band codes, customerClasses, meterRequirements, dstHandling. External anchors: ESBN ToU band conventions and DUoS group structures (versioned by issuer publications), CRU decisions (PSO), Revenue (electricity tax), CEG framework (export).

## 7. Applicability Rules

| Rule | Statement |
|---|---|
| A1 | Every supply point (13 CP) under management resolves to exactly one active import plan and ≤ 1 export plan per carrier at any time |
| A2 | Dynamic/RTP plans require interval metering ≥ plan resolution; smart ToU requires smart_capable meter (meterRequirements enforced at enrolment) |
| A3 | Network components are issuer-authoritative: supplier plans embed DUoS components BY REFERENCE — never copy values (single source, V8) |
| A4 | CPP events: notice, frequency and duration caps enforced; event declarations are events (63) feeding alerts (76) and decision layer |
| A5 | Community tariffs apply only to community members (86 membership check); allocation prices must reconcile with supplier/BRP settlement boundary (20 A4) |
| A6 | Tariff switch events take effect at interval boundaries; mid-interval switches forbidden |
| A7 | Optimisation objectives (60) must use the same resolution function as settlement (27) — one pricing truth (V11) |

## 8. Validation Rules

| ID | Rule | Severity |
|---|---|---|
| V1 | tariffID/componentID unique; references resolve | error |
| V2 | Plan has ≥ 1 energy-kind component (except pure TRF-NET/TRF-LEV registrations) | error |
| V3 | Carrier consistency: all components same carrier as plan | error |
| V4 | validFrom < validTo; component value versions contiguous, non-overlapping, append-only | error |
| V5 | Status transitions draft→published→{closed_to_new→withdrawn}; published plans never edit historical values | error |
| V6 | time_bands tile 8760/8784 h exactly (DST-aware local clock); no gap/overlap | error |
| V7 | blocks monotone increasing thresholds; rates non-negative (rebates use event_rebate kind) | error |
| V8 | Network/levy components referenced not duplicated (A3); issuer = authority for kind | error |
| V9 | price_feed has fallbackRule + latency bound | error |
| V10 | event_schedule has maxEventsPerYear, maxDurationH, noticeMinH all set | error |
| V11 | resolutionFunction identity verified: optimisation pricing library version = settlement pricing library version (hash check) | error |
| V12 | resolutionGranularityMin ≤ market settlementPeriodMin where plan indexes that market | error |
| V13 | Export plan direction = export; bidirectional plans define both resolution paths | error |
| V14 | VAT/rounding rules present; bill reproduction test (sample) passes against issuer reference | warning |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 13 utility_network | DUoS groups, MIC capacity charges, CP-level plan binding |
| 14/15 actor/org | Issuers, enrolled customers (via supply contracts 23) |
| 19 energy_carrier | Single-carrier denomination; unit bases |
| 20 market | Dynamic/RTP index reference prices; community tariffs respect market boundaries |
| 23 contract | Supply contracts bind parties to plans; switch events contractual |
| 24 schedule | ToU band calendars are schedule structures; CPP events schedule-typed |
| 27 settlement | Settlement executes resolution functions; V11 single-truth |
| 38/37 data | Price feeds registered sources; resolved price calendars are datasets |
| 41 feature / 44 model | Price features (current/forecast band, spread, event flags) standard model inputs |
| 57 kpi / 58 objective | Cost KPIs and optimisation objectives priced via this catalogue |
| 52 RD | Price-response RDs conditioned on tariff structures (ΔkW per Δ€/kWh by band) |

## 10. Benchmarking Requirements

Cost benchmarking (06) normalises by tariff context: € intensity comparisons disclose plan mix; "tariff-neutral" energy benchmarks preferred for fabric/operation comparisons. Plan-level statistics (average effective rate by class) computed over min n = 10 enrolments.

## 11. Dataset Requirements

Resolved price calendars per plan (interval-level €) are first-class datasets with plan version lineage; event histories (CPP) logged; value-version corpus preserves full repricing history for any past bill reproduction (audit requirement, 27/93).

## 12. Feature Requirements

Standard price features (41): current/next-interval price, band code, daily price shape stats, DAM-indexed forecast price, event flags + notice countdown, export spread, capacity-charge proximity (MIC headroom). Features cite tariffID + version; sandbox prices excluded (20 A2).

## 13. Model Requirements

Price-response models estimate elasticity per band/event type; optimisation models embed full resolution functions (V11 library); tariff-recommendation models compare plans on metered history (counterfactual billing engine = settlement engine in simulation mode). RTP forecast models chain market price models (20 §13).

## 14. KPI Requirements

KPIs: effective €/kWh by carrier, bill components breakdown, ToU load-shift share, peak-band avoidance, export revenue, CPP event response, capacity-charge utilisation. All cite tariffID/version; community KPIs include allocation fairness measures.

## 15. Response Derivative Requirements

Price-conditioned RDs are core: ∂kW/∂price by band, event-response derivatives (CPP), export-price response. Tariff structure defines the conditioning variables; RD records cite tariffID + component + band. Tariff changes invalidate price-conditioned RDs for re-estimation (52 lifecycle trigger).

## 16. Decision Requirements

Cost-optimal scheduling decisions (60) price via the resolution function; tariff-switch recommendations document counterfactual bills both plans, switching costs, and risk notes (dynamic exposure); CPP event responses validate comfort/authority constraints before curtailment (14/16 ceilings).

## 17. Ontology Mapping

Classes: TariffPlan ⊐ by type; TariffComponent ⊐ by kind; RateStructure ⊐ {FlatRate, TimeBandStructure, BlockStructure, IndexedFormula, PriceFeed, EventSchedule}; TimeBand; RateValueVersion.
Object properties: hasComponent, issuedBy, appliesTo (→ CustomerClass/ConnectionPoint), denominatedIn (→ Carrier), indexes (→ Market PriceOutput), hasBand, hasValueVersion, requiresMeterClass.
Datatype properties: tariffID, tariffType, direction, chargeBasis, validFrom/To, rate, resolutionGranularityMin.
Alignment: SAREF4ENER price profiles; CIM tariff classes (IEC 61968-9 patterns); schema.org PriceSpecification for public plan publication.

## 18. Knowledge Graph Mapping

Nodes: TariffPlan, TariffComponent, TimeBand, RateValueVersion. Relationships: HAS_COMPONENT, ISSUED_BY, INDEXES (→ Market price), APPLIES_TO (→ ConnectionPoint enrolments, temporal), DENOMINATED_IN, SUPERSEDES (plan versions). Keys: tariffID, componentID. Enrolment edges temporal for point-in-time billing reconstruction.

## 19. Digital Twin Mapping

Twins price simulated flows with the SAME resolution library (V11) — twin cost outputs are settlement-grade comparable; what-if tariff scenarios swap plan references; CPP event simulations test response strategies pre-commitment.

## 20. Governance

Owner: EIG Catalogue Owner; commercial lead co-owns plan registrations; issuer publications monitored (DUoS annual updates each October cycle, PSO/tax changes) with value versions appended within 5 working days. Pricing library change control: dual sign-off (settlement owner + optimisation owner), hash-pinned releases.

## 21. Versioning

Plans semver; value versions effective-dated append-only (never rewrite); structure changes = new plan version with migration notes; withdrawn plans immutable. Bills/settlements pin (planVersion, valueVersionSet, libraryHash).

## 22. Example Records

```text
tariffID: EIG-TRF-0007 | "Smart ToU Standard" | TRF-TOU | import | carrier: EIG-ECR-EL | issuer: EIG-ACT-00041 (supplier)
customerClasses: [residential] | meterRequirements: [interval_30min, smart_capable] | status: published | validFrom: 2025-10-01
components: [CMP-0071 energy time_bands {DAY 08–23 0.342, NIGHT 23–08 0.168, PEAK 17–19 0.456 €/kWh, local_clock},
             CMP-0030 network_duos by-ref (issuer ESBN, DG1), CMP-0010 levy_pso, CMP-0011 standing 0.65 €/day]
resolutionGranularityMin: 30 | dstHandling: local_clock_bands | rounding: half_even 4dp/2dp
```

```text
tariffID: EIG-TRF-0021 | "DAM-Indexed Dynamic" | TRF-DYN | import | issuer: EIG-ACT-00041
meterRequirements: [interval_30min, smart_capable] | customerClasses: [residential, sme]
energy component: formula_indexed {rate = max(0, 1.18·DAM_MCP_hourly/1000 + 0.045) €/kWh, ref: EIG-MKT-DAM-01 DAM_MCP_hourly,
resolution 60→30 min repeat, cap 1.20 €/kWh} | priceCalendarPublication: D-1 14:00 via portal feed (37)
```

```text
tariffID: EIG-TRF-0033 | "Aran Community Allocation" | TRF-CMU | bidirectional | issuer: EIG-ACT-00031 (CEC)
scope: community EIG-CMU-0007 members | energy component: community_allocation {internal price 0.19 €/kWh for matched
self-consumption per netting window, residual at member's supplier plan} | reconciliation: supplier boundary per 20 A4
```

## 23. Completion Criteria

Complete when: every managed supply point resolves active plans (A1); all structures machine-resolve deterministically with DST/rounding tests passing; network/levy single-sourcing verified; pricing-library identity (V11) attested; price-conditioned RD hooks and optimisation objectives bind; historical bill reproduction sampled clean; Phase 14 confirms no pricing logic exists outside this catalogue + pinned library.
