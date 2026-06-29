---
type: catalogue
ontology: EIG-MLM
catalogue_no: 20
catalogue_id: EIG-CAT-020
entity: market
band: markets
status: active
tags: [eig-mlm, catalogue, band:markets, entity:market]
aliases: ["EIG-CAT-020", "Catalogue 20", "market"]
---

# CAT-20 — Market

> **EIG-CAT-020** · band: **Markets, Tariffs & Settlement** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Market Catalogue

## 1. Definition

The Market Catalogue is the authoritative register of all energy and flexibility markets in which EIG MLM actors can participate: wholesale day-ahead and intraday markets, balancing and capacity mechanisms, system/ancillary services, local flexibility markets, peer-to-peer markets, community markets and carbon markets. A market is a governed trading venue or mechanism with defined participants, products, trading windows, price formation, gate closures and settlement arrangements.

This catalogue defines market identity, typology, governance, participation requirements, traded products (22), temporal structure (windows 26), price formation, market rules and the bindings that make transactive-energy logic executable: which actors may trade which products in which windows under which constraints.

## 2. Scope

In scope:

- SEM wholesale markets (DAM, IDA1–3/IDC, balancing market BM) operated by SEMO; capacity market (CRM); DS3/system services (and its FlexTech/phased successor arrangements).
- Local flexibility markets (DSO-procured, e.g. National Network, Local Connections-style schemes; EIG sandbox LFMs).
- P2P and community trading arrangements (intra-CEC trading, virtual netting schemes).
- Carbon/attribute markets as traded venues (EU ETS as context; GO/REGO attribute markets) — carbon ACCOUNTING is 28.
- Sandbox/virtual markets inside NEST-class environments, flagged non-settling.

Out of scope:

- Tariff structures (21 — supplier-to-customer pricing is not a market here).
- Product technical definitions (22), window timing details (26), trade records (25), settlement mechanics (27) — this catalogue binds them.
- Bilateral contracts (23) except where a market mediates them.

## 3. Classification Structure

Market type (`mktType`) — closed vocabulary:

| Code | Type | Definition |
|---|---|---|
| MKT-DAM | day_ahead | Day-ahead auction energy market |
| MKT-IDM | intraday | Intraday auctions/continuous energy market |
| MKT-BAL | balancing | Real-time balancing energy/imbalance mechanism |
| MKT-CAP | capacity | Capacity remuneration mechanism |
| MKT-ASM | ancillary_system_services | Frequency, reserve, voltage, inertia services |
| MKT-LFM | local_flexibility | DSO/TSO-procured local flexibility venue |
| MKT-P2P | peer_to_peer | Direct participant-to-participant trading |
| MKT-CMU | community | Intra-community trading/sharing scheme |
| MKT-CRB | carbon_attribute | Carbon allowance / attribute certificate venue |
| MKT-SBX | sandbox_virtual | Non-settling virtual market (NEST-class) |

Market mechanism (`mechanism`): auction_uniform_price, auction_pay_as_bid, continuous_trading, procurement_tender, regulated_mechanism, netting_allocation, bilateral_matched.

Geographic scope: references EIG-GEO (08) — bidding_zone (SEM all-island), network_zone (LFM → EIG-NET-NZ refs), community (EIG-CMU refs).

## 4. Hierarchy

```text
MarketFamily (SEM | DSO_flex | community | carbon | sandbox)
└── Market (EIG-MKT-XXX-NN)
    ├── MarketSegment (optional: IDA1/IDA2/IDA3 within IDM)
    ├── tradedProducts → Product (22)
    ├── tradingWindows → TradeWindow (26)
    └── participationRegister → Actor registrations (14)
```

## 5. Field Groups & Fields

### 5.1 Identity & governance

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | marketID | string | — | `EIG-MKT-<TYPE>-NN` | M | Unique market ID |
| 2 | marketName | string | — | ≤ 120 chars | M | Official name |
| 3 | mktType | enum | — | §3 MKT-* | M | Market type |
| 4 | mechanism | enum | — | §3 mechanisms | M | Clearing/matching mechanism |
| 5 | operatorActorID | string | — | EIG-ACT (ACT-MKO or ACT-DSO) | M | Operating party |
| 6 | operatorOrgID | string | — | EIG-ORG | M | Operator legal entity |
| 7 | regulatorOrgID | string | — | EIG-ORG (ORG-REG) | C | Oversight authority (null for SBX) |
| 8 | rulebookRef | string | — | document ref + version | M | Trading & settlement code reference |
| 9 | geographicScope | object | — | {scopeKind, refs[] (EIG-GEO/NET/CMU)} | M | Where it applies |
| 10 | settling | boolean | — | true/false | M | FALSE for MKT-SBX (hard rule V3) |
| 11 | status | enum | — | design, pilot, live, suspended, closed | M | Lifecycle |
| 12 | currency | string | — | ISO 4217 (EUR) | M | Settlement currency |

### 5.2 Participation requirements

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | eligibleActorTypes | array | — | ACT-* (14) | M | Who may register |
| 2 | registrationRequirements | array | — | [{req: licence/LEI/brp_nomination/credit_cover/metering_class/prequalification, detail}] | M | Entry conditions |
| 3 | brpRequired | boolean | — | true/false | M | Balance responsibility needed (links 14 brpActorID) |
| 4 | minParticipantSize | object | — | {value, unit} or null | C | E.g. LFM ≥ 50 kW aggregated; community markets null |
| 5 | aggregationAllowed | boolean | — | true/false | M | Pools permitted (ACT-AGG) |
| 6 | prequalificationRef | string | — | test procedure ref | C | For ASM/LFM technical prequal (links product 22 reqs) |
| 7 | creditCoverModel | enum | — | collateral, bank_guarantee, prepayment, none | C | Financial security regime |
| 8 | participationRegister | array | — | [{actorID, registrationRef, products[], status, from, to}] | D | Mirror of 14 marketRegistrations (reconciled V7) |

### 5.3 Products & temporal structure (bindings)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | tradedProductIDs | array | — | EIG-PRD-* (22) | M | Products traded here |
| 2 | tradeWindowIDs | array | — | EIG-TW-* (26) | M | Window definitions in force |
| 3 | carrierIDs | array | — | EIG-ECR-* (19) | M | Carriers (derived from products; consistency V8) |
| 4 | settlementPeriodMin | integer | min | 5, 15, 30, 60 | M | Imbalance/metering granularity (SEM 30→15 transition flagged per rulebook version) |
| 5 | gateClosureModel | string | — | summary + 26 refs | M | Gate structure description |
| 6 | locationalGranularity | enum | — | zonal, nodal_zone, feeder, connection_point, none | M | Locational price/need resolution |

### 5.4 Price formation & limits

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | priceFormation | enum | — | uniform_clearing, pay_as_bid, regulated_tariff, negotiated, formula_indexed | M | How prices form |
| 2 | priceFloor / priceCap | decimal | EUR/MWh (or product unit) | rulebook values | M | Bid/offer limits (e.g. DAM −500/+4000 EUR/MWh per SDAC) |
| 3 | referencePriceOutputs | array | — | [{priceCode, description, publication}] | M | Published prices (DAM hourly MCP; BM imbalance price; LFM clearing per window/zone) |
| 4 | imbalancePricingRef | string | — | mechanism ref | C | For markets creating imbalance exposure |
| 5 | marketPowerMitigations | array | — | bidding controls, monitoring refs | O | Where applicable |

### 5.5 Market rules (executable constraints)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | bidConstraints | array | — | [{constraint, value}] | M | Min/max bid size, tick size, divisibility, block rules |
| 2 | positionLimits | array | — | per-actor/product limits | C | Where rulebook imposes |
| 3 | conflictRules | array | — | cross-market exclusivity rules | C | E.g. capacity committed to ASM not double-sold to LFM same window (baseline/exclusivity discipline; links 27 verification) |
| 4 | baselineMethodology | string | — | method ref | C | For flex markets: baseline computation (links 27 delivered-quantity rules and 52 RD evidence) |
| 5 | penaltyRegime | string | — | summary + 23/27 refs | M | Non-delivery consequences |
| 6 | dataPublicationObligations | array | — | what operator publishes, latency | M | Transparency feed (data sources 37) |

### 5.6 Record metadata

recordVersion, createdDate, modifiedDate, owner, sourceOfTruth (rulebook version), governanceStatus — programme standard.

## 6. Controlled Vocabularies

Closed: mktType, mechanism, status, priceFormation, creditCoverModel, locationalGranularity, registrationRequirement kinds. External anchors: SEM Trading & Settlement Code, SDAC/SIDC rules, EBGL/ENTSO-E balancing terminology, DS3/System Services definitions, EU Electricity Market Design provisions on aggregation/flexibility.

## 7. Applicability Rules

| Rule | Statement |
|---|---|
| A1 | An actor may submit bids only to markets where its participationRegister entry is active for the relevant product (joins 14 V-checks) |
| A2 | MKT-SBX: settling = false; trades flagged synthetic end-to-end (25); no settlement records (27) ever created; sandbox prices never feed production decision models unless explicitly scenario-labelled (89) |
| A3 | LFM markets bind geographicScope to network zones (13); offers must locate at connection points within scope; island_zone constraints (EIG-NET-NZ island flags) propagate as market context (EIG-CTX) |
| A4 | Community markets (MKT-CMU) operate within a community boundary (86); netting allocations respect supplier/BRP arrangements — virtual trades, physical settlement unchanged unless rulebook states otherwise |
| A5 | ASM/LFM participation requires product prequalification evidence (22) including RD-derived capability proof where products demand verified flexibility (52 confidence ≥ product threshold) |
| A6 | Cross-market conflict rules enforce single-commitment of the same capacity per window (5.5 conflictRules; checked at bid validation, decision layer 60) |
| A7 | Carbon/attribute markets: registry-account requirements in registrationRequirements; certificates link to 28 instruments |

## 8. Validation Rules

| ID | Rule | Severity |
|---|---|---|
| V1 | marketID unique; operator actor type ∈ {ACT-MKO, ACT-DSO, ACT-TSO, ACT-PLT(community/sandbox)} | error |
| V2 | rulebookRef + version present; rule fields consistent with cited version | error |
| V3 | settling = false ⟺ mktType = MKT-SBX or status ∈ {design, pilot non-settling} | error |
| V4 | tradedProductIDs resolve (22) and each product's allowedMarketTypes includes this mktType | error |
| V5 | tradeWindowIDs resolve (26) and window.marketID = this market | error |
| V6 | priceFloor < priceCap; reference prices defined for every traded product | error |
| V7 | participationRegister ⟷ actor.marketRegistrations bidirectional zero-diff | error |
| V8 | carrierIDs = union of product carriers (derived consistency) | error |
| V9 | brpRequired ⇒ every registered participant has brpActorID resolving to active ACT-BRP | error |
| V10 | LFM scope refs resolve to network zones; every offer location within scope (checked at bid time) | error |
| V11 | Baseline methodology present for any market settling on delivered-vs-baseline quantities | error |
| V12 | Status transitions design→pilot→live→{suspended↔live, closed}; closed markets immutable | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 14/15 actor/org | Operators, regulators, participants, BRP chains; registration mirror V7 |
| 19 energy_carrier | Market carrier denomination (via products) |
| 21 tariff | Dynamic tariffs index market reference prices (DAM hourly) |
| 22 product | Traded products; eligibility/prequal binding A5 |
| 23 contract | Market participation agreements; framework contracts for LFM tenders |
| 25 trade / 26 trade_window | Trades occur in market windows; window definitions owned by 26 |
| 27 settlement | Settlement rules per market; baseline verification V11 |
| 28 carbon | Attribute/carbon market instruments |
| 29/30 flexibility | Flex services monetise through ASM/LFM/CMU markets |
| 52 RD | RD evidence for prequalification and baseline credibility |
| 60/61 decision/action | Bid decisions validate A1/A6 pre-submission |
| 89 scenario | Market-design scenarios; sandbox markets host them |

## 10. Benchmarking Requirements

Market performance metrics (operator-published): clearing prices, volumes, liquidity, dispatch rates. Participant benchmarking (12/06 style cohorts): bid acceptance rate, delivery success, revenue per kW enrolled — internal cohorts min n = 10, no individual disclosure.

## 11. Dataset Requirements

Per market: published price/volume series (37 source registrations: SEMO reports, DSO flex platforms), rulebook version corpus, participation snapshots. All price series carry marketID, product, window, publication timestamp; revisions versioned (preliminary→final imbalance prices).

## 12. Feature Requirements

Market features for models: reference prices (lags/leads), spreads (DAM-IDM, DAM-BM), volatility measures, LFM activation frequency by zone, scarcity flags, gate-closure proximity features. Sandbox-market features quarantined from production feature sets (A2).

## 13. Model Requirements

Price-forecast models per market/product with window-aligned horizons (26); bid-optimisation models embed bidConstraints and conflict rules as hard constraints; market-simulation models (NEST-class) register as MKT-SBX consumers. Models executing bids run under service identities with market-scoped roles (16/17).

## 14. KPI Requirements

Participant KPIs: market revenue by market/product, acceptance rate, imbalance cost, delivery success (joins 12 MET-015), penalty incidence. Market-context KPIs: average clearing price, price volatility, local activation rates. All KPI records carry marketID dimension.

## 15. Response Derivative Requirements

Markets define the monetisation context for RDs: product thresholds set minimum RD confidence (A5); baseline methodologies determine which RD-derived counterfactuals are admissible in delivery verification (27); market window structures (26) define the temporal frames over which RDs must be expressed (e.g. 30-min settlement-period granularity).

## 16. Decision Requirements

Bid/offer decisions (60/61): validate registration (A1), product eligibility, window gate status (26), bid constraints, conflict rules (A6), credit headroom, role approval limits (16). Every submitted bid stores the full validation evidence chain. Suspended markets reject all submissions automatically.

## 17. Ontology Mapping

Classes: Market ⊐ {DayAheadMarket, IntradayMarket, BalancingMarket, CapacityMarket, AncillaryServicesMarket, LocalFlexibilityMarket, P2PMarket, CommunityMarket, CarbonAttributeMarket, SandboxMarket}, MarketSegment, MarketRule, PriceOutput, ParticipationRegistration (reified).
Object properties: operatedBy, regulatedBy, trades (→ Product), hasWindow (→ TradeWindow), scopedTo (→ Geo/NetworkZone/Community), hasRegistration (→ Actor), publishes (→ PriceOutput), governs (→ Trade).
Datatype properties: marketID, mktType, mechanism, settling, settlementPeriodMin, priceFloor, priceCap, status.
Alignment: USEF market roles/interaction model; HEMRM (MarketOperator, BalanceResponsibleParty); CIM market extensions (IEC 62325); OpenADR for DR event semantics at LFM dispatch boundary.

## 18. Knowledge Graph Mapping

Nodes: Market, MarketSegment, PriceOutput. Relationships: OPERATED_BY, REGULATED_BY, TRADES (→ Product), HAS_WINDOW, SCOPED_TO, REGISTERED (→ Actor, temporal), PUBLISHES, SETTLED_UNDER (→ Settlement rules). Keys: marketID. Sandbox subgraphs tagged synthetic.

## 19. Digital Twin Mapping

Market twins (NEST-class) replicate market structures from this catalogue: same products, windows, constraints, price formation — with settling = false. Building/community twins consume market context (live reference prices, activation signals) as boundary conditions; twin-recommended bids surface through decision layer with A1–A6 checks before any real submission.

## 20. Governance

Owner: EIG Catalogue Owner; market-operations lead co-owns rulebook tracking. Rulebook change watch: SEMO/regulator publications monitored; rule-version bumps within 10 working days of effective date. Sandbox market designs approved through NEST governance; clearly firewalled from production (A2 audits quarterly).

## 21. Versioning

Market records semver; rulebook-driven changes minor (parameters) or major (mechanism/participation changes). Historical trades/settlements pin the market record version in force at trade time. Closed markets retained immutable.

## 22. Example Records

```text
marketID: EIG-MKT-DAM-01 | SEM Day-Ahead Market | mktType: MKT-DAM | mechanism: auction_uniform_price
operator: EIG-ACT-00007-MKO (SEMO) / EIG-ORG-0103 | regulator: EIG-ORG-0102 (CRU; SEM Committee context)
rulebookRef: SEM T&SC vYY.MM | geographicScope: bidding_zone all-island (EIG-GEO-IE+NI)
settling: true | currency: EUR | settlementPeriodMin: 30 (15 per rulebook transition flag)
eligibleActorTypes: [ACT-SUP, ACT-AGG, ACT-BRP, generators via ACT-PRO/legal] | brpRequired: true
products: [EIG-PRD-EN-HH-01 hourly energy] | windows: [EIG-TW-DAM-D1-1100]
priceFormation: uniform_clearing | floor/cap: −500 / +4000 EUR/MWh (SDAC) | refPrices: [DAM_MCP_hourly]
bidConstraints: [{tick, 0.01 EUR/MWh}, {min lot, 0.1 MWh}, {block bids per rulebook}]
```

```text
marketID: EIG-MKT-LFM-01 | EIG Sandbox-Western LFM pilot | mktType: MKT-LFM | mechanism: procurement_tender
operator: EIG-ACT-00052 (DSO flex desk) | regulator: EIG-ORG-0102 | status: pilot | settling: true
geographicScope: network_zone [EIG-NET-NZ-00017 (Inis Mór), EIG-NET-NZ-00021] | locationalGranularity: feeder
eligibleActorTypes: [ACT-AGG, ACT-FLX] | minParticipantSize: 50 kW aggregated | aggregationAllowed: true
prequalification: PRQ-LFM-01 (response ≤ 10 min, RD confidence ≥ 0.8 per EIG-PRD-FLX-SHED-01)
products: [EIG-PRD-FLX-SHED-01, EIG-PRD-FLX-SHIFT-01] | windows: [EIG-TW-LFM-W1 week-ahead tender, EIG-TW-LFM-DA dispatch]
baselineMethodology: BL-LFM-01 (RD-informed counterfactual, 27) | penaltyRegime: per EIG-CON-FLX framework (23)
conflictRules: [no same-window capacity double-commitment vs MKT-ASM]
```

```text
marketID: EIG-MKT-SBX-01 | NEST virtual all-island market replica | mktType: MKT-SBX | settling: FALSE
operator: EIG-ACT-PLT (platform) | regulator: — | status: live (virtual) | mirrors: EIG-MKT-DAM-01 structure
purpose: C7 catalogue item market-replica; all trades synthetic-flagged; no production feature export (A2)
```

## 23. Completion Criteria

Complete when: all live IE/SEM market venues + pilot LFM + community/sandbox venues registered with current rulebook versions; product/window/participation bindings resolve zero-diff; conflict and baseline rules executable in bid validation; sandbox firewall verified; Phase 14 confirms 21/22/25/26/27 reference markets only through this catalogue.
