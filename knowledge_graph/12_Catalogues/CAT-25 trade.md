---
type: catalogue
ontology: EIG-MLM
catalogue_no: 25
catalogue_id: EIG-CAT-025
entity: trade
band: markets
status: active
tags: [eig-mlm, catalogue, band:markets, entity:trade]
aliases: ["EIG-CAT-025", "Catalogue 25", "trade"]
---

# CAT-25 — Trade

> **EIG-CAT-025** · band: **Markets, Tariffs & Settlement** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Trade Catalogue

## 1. Definition

The Trade Catalogue is the authoritative register of trade events and transactions in the EIG MLM framework. A trade is an executed transaction: a matched, accepted or awarded commitment between a buyer and a seller for a defined product (22), quantity, price and delivery time window, formed inside a market (20) within a trading window (26), governed by contracts (23), and progressing through delivery to settlement (27).

This catalogue defines trade identity, parties, the bid/offer lineage that produced the trade, the commercial terms (product, quantity, price, window), delivery tracking status, settlement status, amendment/cancellation rules and the lifecycle state machine. It covers wholesale market trades, local flexibility awards and dispatches, P2P matches, community allocations and sandbox (synthetic) trades.

## 2. Scope

In scope:

- Market trades: DAM/IDM cleared positions, balancing actions, capacity awards.
- Flexibility trades: LFM tender awards, call-off dispatch acceptances, ASM availability/utilisation transactions.
- P2P trades: matched peer transactions under master agreements.
- Community trades: netting-window allocations executed as trade records (virtual trades, 20 A4).
- Sandbox trades: NEST-class synthetic trades — fully represented, permanently flagged, never settled.
- Bid/offer records as trade lineage (submitted→matched portions).

Out of scope:

- Market rules (20), window timing (26), product specs (22), settlement computation (27), dispatch schedule details (24 — trades SPAWN dispatch schedules), price formation logic.

## 3. Classification Structure

Trade kind (`trdKind`) — closed vocabulary:

| Code | Kind | Formation |
|---|---|---|
| TRD-MKT | market_cleared | Auction clearing / continuous match |
| TRD-BAL | balancing_action | SO-accepted balancing bid/offer |
| TRD-CAP | capacity_award | Capacity auction award |
| TRD-FLX-AW | flexibility_award | LFM tender award (availability) |
| TRD-FLX-DSP | flexibility_dispatch | Accepted call-off/utilisation dispatch |
| TRD-P2P | p2p_match | Peer match under master agreement |
| TRD-CMU | community_allocation | Netting-window allocation |
| TRD-SBX | sandbox_synthetic | Virtual trade in MKT-SBX |

Side semantics: every trade has exactly one buyer and one seller party record; central-counterparty markets record the CCP/operator as formal counterparty with the participant on the economic side (`counterpartyModel`: bilateral, central_counterparty, allocation_engine).

## 4. Hierarchy

```text
BidOffer (EIG-TRD-BO-NNNNNNN)          ← submission lineage (may match 0..n times)
└── Trade (EIG-TRD-NNNNNN)             ← executed transaction
    ├── deliveryWindow → TradeWindow ref (26) + interval breakdown
    ├── spawns → DispatchSchedule (24, for physical-delivery kinds)
    ├── deliveryTracking (status + evidence refs)
    └── settlementLink → Settlement line items (27)
Amendment/Cancellation events (append-only)
```

## 5. Field Groups & Fields

### 5.1 Identity & formation

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | tradeID | string | — | `EIG-TRD-NNNNNN` | M | Unique trade ID |
| 2 | trdKind | enum | — | §3 TRD-* | M | Trade kind |
| 3 | marketID | string | — | EIG-MKT-* (20) | M | Venue |
| 4 | tradeWindowID | string | — | EIG-TW-* (26) | M | Window of formation |
| 5 | productID | object | — | {EIG-PRD-*, productVersion} | M | Product + version pinned (22 §21) |
| 6 | bidOfferRefs | array | — | EIG-TRD-BO refs both sides where applicable | C | Submission lineage |
| 7 | formationMethod | enum | — | auction_clearing, continuous_match, tender_award, dispatch_acceptance, allocation_run, manual_confirmed | M | How formed |
| 8 | formationTimestamp | datetime | — | ISO 8601 UTC | M | Execution instant |
| 9 | counterpartyModel | enum | — | §3 models | M | Counterparty structure |
| 10 | synthetic | boolean | — | true ⟺ trdKind=TRD-SBX or market.settling=false | M | Sandbox flag (V3) |
| 11 | externalRef | string | — | operator trade/settlement ref | C | SEMO/DSO platform reference |

### 5.2 Parties

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | buyer | object | — | {actorID, orgID?, brpActorID?, registrationRef} | M | Buying party (economic) |
| 2 | seller | object | — | {actorID, orgID?, brpActorID?, registrationRef} | M | Selling party (economic) |
| 3 | formalCounterparty | object | — | {actorID} | C | CCP/operator where model demands |
| 4 | contractBasis | array | — | EIG-CON refs + clause IDs | M | Entitlement chain (23 A1): master/participation/framework contracts authorising the trade |
| 5 | poolComposition | object | — | {poolRef, memberAllocationRule} | C | Aggregated trades: allocation rule to members (settlement 27 applies it) |

### 5.3 Commercial terms

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | quantity | decimal | product unit (22) | > 0, resolution-compliant | M | Traded quantity |
| 2 | quantityProfile | array | — | [{intervalStart, intervalEnd, qty}] | C | Per-interval breakdown where product spans periods (sum = quantity, V6) |
| 3 | price | decimal | currency/unit | within market floor/cap | M | Execution price |
| 4 | priceBasis | enum | — | clearing_price, pay_as_bid, tender_price, regulated_rate, allocation_price | M | Price provenance |
| 5 | currency | string | — | EUR | M | Currency |
| 6 | deliveryStart / deliveryEnd | datetime | — | ISO 8601; grid-aligned (V7) | M | Delivery window |
| 7 | deliveryPointRef | string | — | per product deliveryPointKind (22): CP/feeder/zone/registry | M | Where delivery measured |
| 8 | direction | enum | — | from product; buy/sell perspective resolved | M | Flow direction |
| 9 | totalValue | decimal | EUR | = Σ qty×price (rounding per 21 rules) | D | Computed value |

### 5.4 Delivery & settlement status

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | tradeStatus | enum | — | executed, amended, cancelled, void | M | Commercial status |
| 2 | deliveryStatus | enum | — | pending, in_delivery, delivered_full, delivered_partial, failed, not_applicable | M | Physical delivery state (graded per 22 thresholds at verification) |
| 3 | deliveryEvidenceRefs | array | — | dispatch schedule (24), telemetry datasets (38), baseline calc refs (27) | C | Evidence chain |
| 4 | dispatchScheduleRef | string | — | EIG-SCH-* (SCH-DSP) | C | Spawned schedule (physical kinds, V9) |
| 5 | settlementStatus | enum | — | unsettled, provisionally_settled, settled_final, disputed, written_off, exempt_synthetic | M | Financial state |
| 6 | settlementRefs | array | — | EIG-SET-* line items (27) | C | Settlement linkage |
| 7 | amendments | array | — | [{at, byRef, change, reason, authorityRef}] | D | Append-only event log |
| 8 | cancellationTerms | object | — | {allowedUntil: gate rule (26), feeModel} | C | Cancellation rules in force |

### 5.5 Record metadata

recordVersion, createdDate, modifiedDate, sourceSystem (operator feed / platform engine), ingestionLineage (37/38), governanceStatus — programme standard.

## 6. Controlled Vocabularies

Closed: trdKind, formationMethod, counterpartyModel, priceBasis, tradeStatus, deliveryStatus, settlementStatus, direction. External: SEMO trade/settlement reference formats, ENTSO-E transaction conventions where applicable.

## 7. Applicability Rules

| Rule | Statement |
|---|---|
| A1 | A trade exists only inside an active market window with both parties holding valid registrations and contract basis (joins 20 A1, 23 A1) — validated at formation |
| A2 | Synthetic discipline: synthetic=true trades carry settlementStatus=exempt_synthetic permanently; they never link settlement records, never enter production features (20 A2), and render with synthetic provenance class everywhere |
| A3 | Physical-delivery trades (TRD-FLX-DSP, TRD-BAL energy actions) spawn an instructed dispatch schedule (24 A2) at acceptance; trade and schedule cross-reference |
| A4 | Pool trades: member allocation rule fixed at formation; member-level economic effects materialise only in settlement (27), not as child trades |
| A5 | Community allocation trades form per netting window from the allocation engine; physical supplier settlement unchanged (20 A4); allocation prices from TRF-CMU (21 A5) |
| A6 | Amendments permitted only per market rulebook within window rules (26 gate states); post-gate changes occur only via operator correction events with externalRef |
| A7 | Capacity/availability trades (TRD-CAP, TRD-FLX-AW) deliver an OBLIGATION over a period: deliveryStatus tracks availability attainment, with utilisation dispatches as separate TRD-FLX-DSP children referencing the award |

## 8. Validation Rules

| ID | Rule | Severity |
|---|---|---|
| V1 | tradeID unique; market/window/product refs resolve; product admitted to market (20 V4) | error |
| V2 | Window state at formationTimestamp permitted formation (26 gate logic) | error |
| V3 | synthetic ⟺ market.settling=false; synthetic trades have no settlementRefs | error |
| V4 | Parties: buyer ≠ seller (economic); registrations active for product at formation; contractBasis clauses active (23 V13 chain) | error |
| V5 | Price within market floor/cap (20 §5.4); quantity within product lot bounds and divisibility (22 V3) | error |
| V6 | quantityProfile sums to quantity; intervals tile delivery window at market settlement-period resolution | error |
| V7 | deliveryStart/End align to window's delivery grid (26) | error |
| V8 | brp linkage present where market.brpRequired (20 V9) | error |
| V9 | Physical kinds carry dispatchScheduleRef once accepted (A3) | error |
| V10 | Status machines: tradeStatus executed→{amended*, cancelled, void}; deliveryStatus pending→in_delivery→{delivered_full/partial, failed}; settlementStatus unsettled→provisionally_settled→settled_final (disputes branch and must resolve) | error |
| V11 | Amendment events carry authority (operator ref or rule-permitted party action); append-only | error |
| V12 | totalValue recomputation matches stored value under 21 rounding rules | error |
| V13 | TRD-FLX-DSP must reference an active parent TRD-FLX-AW or framework call-off contract | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 14/15 actor/org | Parties, registrations, BRP chains, pool membership |
| 20 market | Venue, price limits, conflict rules verified at bid stage |
| 21 tariff | Rounding/currency conventions; community allocation prices |
| 22 product | Terms pinned (version), delivery grading thresholds |
| 23 contract | Entitlement chain; penalty exposure on failure |
| 24 schedule | Spawned dispatch schedules; nomination schedules pre-trade |
| 26 trade_window | Formation/gate validity; delivery grid |
| 27 settlement | Settlement lines per trade; baseline verification of delivery |
| 28 carbon | Attribute trades transfer instruments; avoided-emission claims cite delivered flex trades |
| 52 RD | Delivery outcomes feed RD validation (observed response vs committed) |
| 37/38 data | Operator trade feeds as registered sources; evidence datasets |
| 60/61 decision/action | Bid decisions upstream; acceptance confirmations as actions |

## 10. Benchmarking Requirements

Trading performance cohorts (min n = 10): acceptance rate, realised price vs reference, delivery success by product (joins 12 MET-015), imbalance exposure. Synthetic trades excluded from all production benchmarks (A2).

## 11. Dataset Requirements

Trade ledger append-only with full lineage (bidOffer→trade→amendments→settlement links); operator feed reconciliation datasets (externalRef matching, zero-diff target); per-trade evidence bundles retained per 93 settlement-grade schedule; synthetic trade sets partitioned physically from production.

## 12. Feature Requirements

Features: realised price series, position features (net length per window), delivery-success history, acceptance-probability features (own historical bid outcomes vs clearing). Synthetic-trade features only inside sandbox/scenario model scopes (89), never production (A2).

## 13. Model Requirements

Bid-optimisation models consume trade history for acceptance/price models; delivery-risk models score open physical trades; allocation engines (TRD-CMU) are deterministic registered algorithms (45) with versioned rules; reconciliation anomaly models flag operator-feed mismatches.

## 14. KPI Requirements

KPIs: traded volume/value by kind/market/product, acceptance rate, realised vs reference price spread, delivery success %, dispute rate, time-to-final-settlement. All carry tradeID-resolvable dimensions; synthetic KPIs reported only in sandbox dashboards.

## 15. Response Derivative Requirements

Each delivered flexibility trade is an RD validation event: committed ΔkW (trade) vs observed Δ (27 verification) updates RD confidence (52 lifecycle). Failed deliveries trigger RD review and may suspend product eligibility (22 A3 / 11 declared-vs-observed rule).

## 16. Decision Requirements

Trade acceptance decisions (dispatch confirmations) re-validate feasibility at decision time (asset availability via 24 maintenance, envelopes, conflict rules 20 A6); cancellation decisions check window gate rules and fee exposure; dispute-raising decisions require evidence bundle completeness.

## 17. Ontology Mapping

Classes: Trade ⊐ by kind; BidOffer; TradeParty (reified side); DeliveryTracking; Amendment.
Object properties: formedIn (→ Market), withinWindow (→ TradeWindow), forProduct (→ Product version), hasBuyer/hasSeller (→ Actor), authorisedBy (→ Contract clause), spawnsSchedule (→ Schedule), evidencedBy (→ Dataset/Baseline), settledBy (→ Settlement line), amendedBy, childOf (dispatch→award).
Datatype properties: tradeID, trdKind, quantity, price, formationTimestamp, deliveryStart/End, synthetic, statuses.
Alignment: CIM market transaction classes (IEC 62325 EnergyTransaction patterns), FIBO trade concepts for financial structure, USEF flexibility transaction lifecycle.

## 18. Knowledge Graph Mapping

Nodes: Trade, BidOffer. Relationships: FORMED_IN, WITHIN_WINDOW, FOR_PRODUCT, BUYER/SELLER (→ Actor), AUTHORISED_BY (→ Contract), SPAWNED (→ Schedule), SETTLED_BY (→ Settlement), VALIDATES (→ RD record), CHILD_OF. Keys: tradeID. Synthetic subgraph tagged and query-partitioned.

## 19. Digital Twin Mapping

Twin trading (NEST) generates TRD-SBX records exercising the full lifecycle minus settlement; live twins display open positions and delivery progress against telemetry; replay twins reconstruct delivery windows for dispute support using archived schedules and evidence bundles.

## 20. Governance

Owner: EIG Catalogue Owner; trading operations co-own reconciliation (daily operator-feed zero-diff check); amendment authority controlled (16 permissions); synthetic partition audited quarterly (A2/20 A2 joint audit); ledger immutability attested.

## 21. Versioning

Trades are events: immutable post-formation except append-only amendment/status events; record schema semver; all references version-pinned (product, contract clauses, market record). Ledger never rewritten; corrections are new events with lineage.

## 22. Example Records

```text
tradeID: EIG-TRD-008834 | TRD-FLX-DSP | market: EIG-MKT-LFM-01 | window: EIG-TW-LFM-DA (2026-06-12)
product: {EIG-PRD-FLX-SHED-01, v1.3} | formation: dispatch_acceptance @ 2026-06-12T10:42Z | counterparty: central via DSO desk
buyer: {EIG-ACT-00052 DSO} | seller: {EIG-ACT-00031 aggregator, pool POOL-01, allocation: pro-rata enrolled kW}
contractBasis: [EIG-CON-000884 §call-off, EIG-CON-001207 §dispatch-consent] | parent award: EIG-TRD-008101 (TRD-FLX-AW)
quantity: 180 kW | profile: 17:00–18:00 @180, 18:00–18:30 @120 | price: 0.42 EUR/kWh-reduced | basis: tender_price
deliveryPoint: feeder F-NZ17-03 (EIG-NET-NZ-00017) | dispatchSchedule: EIG-SCH-040912
tradeStatus: executed | deliveryStatus: delivered_full (96.4% vs baseline BL-LFM-01 ⇒ full ≥ 95%) | settlement: provisionally_settled → EIG-SET-…
```

```text
tradeID: EIG-TRD-007210 | TRD-MKT | market: EIG-MKT-DAM-01 | window: EIG-TW-DAM-D1-1100 (delivery 2026-06-13)
product: {EIG-PRD-EN-HH-01, v1.0} | formation: auction_clearing | buyer: {EIG-ACT-00041 supplier, BRP-…} | seller: CCP model
quantity: 12.5 MWh profile across 6 hourly intervals | price: clearing 84.20 EUR/MWh | delivery: zonal, BRP perimeter
deliveryStatus: not_applicable (energy position) | settlementStatus: settled_final (imbalance handled per 27)
```

```text
tradeID: EIG-TRD-SBX-114207 | TRD-SBX | market: EIG-MKT-SBX-01 | synthetic: TRUE | settlementStatus: exempt_synthetic
mirrors DAM structure for NEST scenario SCN-2026-014; provenance class SYNTHETIC end-to-end; excluded from production features
```

## 23. Completion Criteria

Complete when: every venue's trades ingest with zero-diff operator reconciliation; lifecycle state machines enforced; entitlement-chain validation live at formation; physical trades spawn schedules automatically; synthetic partition airtight; settlement linkage bidirectional; Phase 14 confirms 27 settles only trades from this ledger and no trade-like records exist elsewhere.
