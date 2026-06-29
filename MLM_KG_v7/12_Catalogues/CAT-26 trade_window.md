---
type: catalogue
ontology: EIG-MLM
catalogue_no: 26
catalogue_id: EIG-CAT-026
entity: trade_window
band: markets
status: active
tags: [eig-mlm, catalogue, band:markets, entity:trade_window]
aliases: ["EIG-CAT-026", "Catalogue 26", "trade_window"]
---

# CAT-26 — Trade Window

> **EIG-CAT-026** · band: **Markets, Tariffs & Settlement** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID prefix:** `EIG-TW`
**Catalogue number:** 26 of 94
**Layer:** Market & Commercial
**Version:** 1.0.0
**Status:** ACTIVE

---

## 1. Definition

The Trade Window Catalogue defines every **valid trading window**: a bounded interval of time, attached to exactly one market (20), during which trades (25) for specified products (22) and delivery periods may be formed, amended, or cancelled. A window is the temporal gatekeeper of the market layer — no trade exists outside a window, and the window's gate state at formation time is the authoritative test of trade validity (25 V2).

A trade window record is a **rule object, not an event log**: it defines opening, gate closure, delivery coupling, eligibility, and recurrence. Concrete window instances (e.g. the day-ahead window for delivery day 2026-06-13) are generated deterministically from the window definition's recurrence rule and are identified by suffixing the definition ID with the instance date/time.

## 2. Scope

**In scope:** day-ahead auction windows; intraday continuous and auction windows; balancing/dispatch windows; local flexibility market tender and dispatch windows; P2P and community trading windows; sandbox windows (NEST replicas); gate-closure logic; window↔settlement-period alignment; forecast-horizon declarations; recurrence and calendar rules (timezone, DST, market holidays).

**Out of scope:** the trades formed within windows (25); market governance and rulebooks (20); product physical definitions (22); settlement runs (27); schedules derived from accepted trades (24); forecast model definitions (46).

## 3. Classification Structure

Primary axis `windowType`:

| Code | Type | Description |
|---|---|---|
| TW-DA | day_ahead_auction | Single uniform-price auction for next-day delivery |
| TW-ID-A | intraday_auction | Discrete intraday auctions (e.g. IDA1–IDA3 in SEM) |
| TW-ID-C | intraday_continuous | Continuous order-book trading until per-period gate |
| TW-BAL | balancing_dispatch | TSO/DSO dispatch acceptance window, near-real-time |
| TW-LFM-T | local_flex_tender | Week/season-ahead tender for local flexibility (20 MKT-LFM) |
| TW-LFM-D | local_flex_dispatch | Day-ahead/intraday activation window for procured flexibility |
| TW-P2P | p2p_session | Peer-to-peer matching session window |
| TW-CMU | community_session | Community allocation/sharing computation window |
| TW-SBX | sandbox | NEST sandbox replica of any above type (settling=FALSE inherited from 20 MKT-SBX) |

Secondary axes: `recurrenceClass` (daily, intraday_repeating, weekly, seasonal, continuous, ad_hoc); `gateModel` (single_gate, per_period_gate, rolling_gate).

## 4. Hierarchy

```
Market (20, EIG-MKT-*)
└── WindowDefinition (EIG-TW-<MKT>-<NAME>)        ← this catalogue's primary record
    ├── RecurrenceRule (embedded)
    ├── GateRule(s) (embedded, ≥1)
    └── WindowInstance (EIG-TW-<MKT>-<NAME>-<YYYYMMDD[-HHMM]>)   ← generated, immutable
        └── referenced by Trades (25, tradeWindowID)
```

Window instances are never hand-authored; they are materialised by the platform calendar service from the definition. An instance, once opened, is immutable except for operator suspension events.

## 5. Field Groups & Fields

### 5.1 Identity & Classification

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | windowDefID | string | — | `EIG-TW-[A-Z0-9-]+`, unique | M | Window definition identifier |
| 2 | windowName | string | — | ≤120 chars | M | Human-readable name |
| 3 | windowType | string | — | §3 codes | M | Window class |
| 4 | marketID | string | — | EIG-MKT-* (20), exactly one | M | Owning market; must list this window in its tradeWindowIDs (20 §5) |
| 5 | status | string | — | draft, active, suspended, retired | M | Lifecycle state |
| 6 | rulebookVersion | string | — | semver or operator version string | M | Market rulebook version this window definition implements |

### 5.2 Temporal Structure

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | timezone | string | — | IANA TZ, default `Europe/Dublin` | M | All wall-clock fields interpreted in this zone (DST-aware, consistent with 21 V6) |
| 2 | windowOpen | string | — | local time `HH:MM` or offset rule `D-1T11:00` | M | Opening instant relative to delivery day (D) |
| 3 | windowClose | string | — | same format | M | Hard close; no formation after this instant |
| 4 | gateClosure | string/object | — | single instant, or per-period rule `{offsetMinBeforeDelivery: int}` | M | Last instant a trade for a given delivery period may be formed/amended; ≤ windowClose |
| 5 | amendmentDeadline | string | — | instant/offset; ≤ gateClosure | C | Last amendment instant if it differs from gate (25 A6) |
| 6 | cancellationDeadline | string | — | instant/offset; ≤ gateClosure | C | Last cancellation instant (25 §5 cancellationTerms) |
| 7 | recurrenceRule | object | — | {class: §3 recurrenceClass, rrule: RFC 5545 string, calendarID} | M | Instance generation rule; calendarID references market holiday calendar |
| 8 | suspensionPolicy | string | — | free text + operator event codes | O | Conditions under which operator may suspend instances |

### 5.3 Delivery Coupling

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | deliveryHorizonStart | string | — | offset from window close, ISO 8601 duration | M | Earliest delivery period tradable in this window |
| 2 | deliveryHorizonEnd | string | — | ISO 8601 duration | M | Latest delivery period tradable |
| 3 | deliveryPeriodMin | integer | min | 5, 15, 30, 60 | M | Delivery period granularity; MUST equal or evenly divide market settlementPeriodMin (20 §5) |
| 4 | settlementPeriodMin | integer | min | 5, 15, 30, 60 | M | Settlement granularity inherited from 20; flagged for SEM 30→15 transition |
| 5 | periodAlignment | string | — | top_of_hour, half_hour_grid, quarter_hour_grid | M | Delivery grid alignment (25 delivery grid validation) |

### 5.4 Forecast Horizon Declaration

Windows declare the forecast lead time participants face, which downstream forecasting (46) and RD recency gating (52, 22 §15) consume.

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | forecastHorizonMin | integer | min | >0 | M | Minimum lead time between gate closure and first delivery period |
| 2 | forecastHorizonMax | integer | min | ≥ forecastHorizonMin | M | Maximum lead time (gate to last delivery period) |
| 3 | recommendedForecastModels | array | — | EIG-FC-* (46) | O | Forecast definitions tuned to this horizon |
| 4 | rdRecencyAtGate | boolean | — | true/false | M | Whether product rdRequirements recency (22 §15) is evaluated at gate closure (true) or at formation (false); default true |

### 5.5 Eligibility

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | eligibleProductIDs | array | — | EIG-PRD-* (22), ⊆ market's product list | M | Products tradable in this window |
| 2 | eligibleParticipantClasses | array | — | actor types (14 §3) | M | Actor classes permitted to submit |
| 3 | prequalificationRequired | boolean | — | true/false | M | Whether market prequalification record (20) is required |
| 4 | zoneRestriction | array | — | EIG-NET-NZ-* (13), EIG-GEO-* (08) | C | Mandatory for TW-LFM-* and TW-CMU; spatial limits |
| 5 | minOrderQty | decimal | kWh or kW | >0, unit per product | O | Minimum order size |
| 6 | maxOrderQty | decimal | kWh or kW | >0 | O | Maximum order size |
| 7 | tickSize | decimal | EUR | >0 | O | Price increment |

### 5.6 Sandbox Marking

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | sandbox | boolean | — | true iff marketID is MKT-SBX class | M | Inherited; sandbox firewall (20 A2) applies — sandbox window outcomes never feed production features |
| 2 | replicaOfWindowDefID | string | — | EIG-TW-* | C | For sandbox replicas: the production window mirrored |

## 6. Controlled Vocabularies

- `windowType`: §3 table.
- `recurrenceClass`: daily, intraday_repeating, weekly, seasonal, continuous, ad_hoc.
- `gateModel`: single_gate (one gate for all delivery periods, e.g. DAM), per_period_gate (gate per delivery period, e.g. intraday continuous), rolling_gate (continuously advancing, e.g. balancing).
- Instance state machine: `scheduled → open → gate_closed → closed → results_published` with operator branch `suspended` (from open) and `cancelled_by_operator` (terminal). Trades may form only in `open`; amendments per §5.2 deadlines; state `gate_closed` applies per delivery period under per_period_gate.
- `periodAlignment`: top_of_hour, half_hour_grid, quarter_hour_grid.

## 7. Applicability Rules

| # | Rule |
|---|---|
| A1 | Every trade (25) MUST reference a window instance whose state permitted formation at formationTimestamp; this catalogue is the single source of gate truth. |
| A2 | A window definition belongs to exactly one market; multi-market windows are forbidden — define one per market even if temporally identical. |
| A3 | Sandbox windows (TW-SBX) exist only under MKT-SBX markets; production windows never under sandbox markets. Sandbox firewall 20 A2 applies to all instance outcomes. |
| A4 | TW-LFM-* and TW-CMU windows MUST carry zoneRestriction; zone references must exist in 13/08. |
| A5 | deliveryPeriodMin must evenly divide settlementPeriodMin or equal it; misaligned grids are rejected at definition time. |
| A6 | Window instances are generated only from recurrenceRule by the calendar service; manual instance creation is forbidden except operator ad_hoc windows, which require externalRef to the operator notice. |
| A7 | All wall-clock logic is DST-aware local time (Europe/Dublin default), consistent with tariff time-band rules (21 V6); a delivery day has 23/25 hours on transition days and instance generation must honour this. |

## 8. Validation Rules

| # | Rule | Severity |
|---|---|---|
| V1 | windowDefID unique; matches `EIG-TW-` pattern | error |
| V2 | marketID exists in 20 and lists this window in tradeWindowIDs (bidirectional, Phase 14 check) | error |
| V3 | gateClosure ≤ windowClose; amendmentDeadline ≤ gateClosure; cancellationDeadline ≤ gateClosure | error |
| V4 | eligibleProductIDs ⊆ products registered for marketID (20/22 symmetric binding) | error |
| V5 | deliveryPeriodMin/settlementPeriodMin alignment (A5) | error |
| V6 | recurrenceRule parses as valid RFC 5545; instance generation over next 400 days produces no overlapping instances of the same definition unless gateModel=rolling_gate | error |
| V7 | forecastHorizonMin = (first delivery period start − gate closure) computed from temporal fields; declared value must match computed value | error |
| V8 | sandbox flag consistent with marketID class (A3) | error |
| V9 | zoneRestriction present for TW-LFM-*/TW-CMU (A4) | error |
| V10 | DST transition days: generated instances cover 23/25-hour delivery days without gaps/overlaps | error |
| V11 | status=retired requires no instance in state scheduled/open | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 20 market | Parent; rulebook, settlementPeriodMin, product registry, sandbox class |
| 21 tariff | Shares DST-aware local-clock convention (V6/A7); DAM-indexed tariffs read prices published per window instance |
| 22 product | eligibleProductIDs; rdRecencyAtGate evaluation point for §15 gates |
| 23 contract | Entitlement chain: participation in a window requires active enrolment/registration clause (23 A1) |
| 24 schedule | Accepted trades in a closed window spawn dispatch/trading schedules |
| 25 trade | Every trade carries tradeWindowID; gate state validates formation |
| 27 settlement | Settlement runs key on (market, window instance, settlement period) |
| 46 forecast | Forecast horizon declarations; recommended models |
| 52 RD | Recency/confidence gates evaluated at gate closure when rdRecencyAtGate=true |
| 13/08 network/geographic | zoneRestriction targets |

## 10. Benchmarking Requirements

Window-level operational metrics (computed per instance, aggregated per definition): gate-rejection rate (trades rejected for late submission), amendment rate, cancellation rate, fill ratio (matched/offered), sandbox-vs-production divergence (for replicas). Min cohort n=10 instances before publishing definition-level statistics; immutable releases per benchmark conventions (06/12).

## 11. Dataset Requirements

Window instance history (state transitions with timestamps) is an auditable dataset: retention ≥ 7 years aligned to settlement audit (27); timezone-explicit timestamps; instance records immutable post-close; suspension/cancellation events carry operator externalRef.

## 12. Feature Requirements

Features derivable for models: time-to-gate (min), window fill ratio so far, historical clearing price by window instance position (e.g. DAM hour-of-day), gate-proximity pressure features for bidding agents (72). Sandbox window features are firewalled (20 A2) — never mixed into production feature stores (41).

## 13. Model Requirements

Bidding/dispatch models (44, 72) MUST read gate logic from this catalogue, not hard-code times; any model submitting to a window declares the windowDefIDs it serves; model inference deadlines derive from gateClosure minus declared compute budget. Forecast models (46) serving a window must cover [forecastHorizonMin, forecastHorizonMax].

## 14. KPI Requirements

KPIs (57): on-time submission rate ≥ 99.5% per agent per window definition; gate-rejection rate ≤ 0.5%; forecast-at-gate accuracy tracked per window (feeds 46 validation); LFM tender participation rate per zone.

## 15. Response Derivative Requirements

Where rdRecencyAtGate=true, the RD confidence/recency thresholds in product rdRequirements (22 §15) are tested against gate-closure time, not formation time — an RD valid at formation but stale by gate fails. RD-backed offers must declare sustain/response times compatible with deliveryPeriodMin (an RD sustain shorter than one delivery period cannot back a full-period product).

## 16. Decision Requirements

Decision intelligence (60) treats windows as hard temporal constraints: bid/no-bid decisions must complete before gateClosure with declared safety margin; cancellation decisions check cancellationDeadline and fee model (25); window suspension triggers re-planning workflows (64). Conflict rule 20 A6 (no double commitment) is evaluated across all open windows simultaneously.

## 17. Ontology Mapping

Class: `eig:TradeWindow` ⊑ `eig:MarketStructure`. Object properties: `eig:belongsToMarket` (→ eig:Market), `eig:permitsProduct` (→ eig:Product), `eig:restrictedToZone` (→ eig:NetworkZone ∪ eig:GeographicArea), `eig:generatesInstance` (→ eig:TradeWindowInstance). Datatype properties: windowDefID, windowType, gateClosure, deliveryPeriodMin, settlementPeriodMin, forecastHorizonMin, sandbox. Alignments: FIBO market-phase concepts (design-intent level); time structure via W3C Time Ontology (`time:Interval` for instances).

## 18. Knowledge Graph Mapping

Nodes: `(:TradeWindowDef {windowDefID, windowType, sandbox})`, `(:TradeWindowInstance {instanceID, state, open, gateClosed, closed})`. Edges: `(:TradeWindowDef)-[:BELONGS_TO]->(:Market)`, `(:TradeWindowDef)-[:PERMITS]->(:Product)`, `(:TradeWindowInstance)-[:INSTANCE_OF]->(:TradeWindowDef)`, `(:Trade)-[:FORMED_IN]->(:TradeWindowInstance)`, `(:TradeWindowDef)-[:RESTRICTED_TO]->(:NetworkZone)`. Instance state history as temporal edge properties.

## 19. Digital Twin Mapping

The market twin (32) materialises window instances as scheduled simulation events; the sandbox (NEST) replicates production window definitions under TW-SBX with identical temporal logic so agent behaviour transfers; twin time acceleration must preserve relative gate offsets.

## 20. Governance

Owner: Market Operations Lead. Steward: platform calendar service administrator. Reviewer: market operator liaison (per rulebookVersion change). Approver: EIG-ROL-AGGM or market-facing authority tier T3+ (16). Duties registered per 16 A6. Changes to gate logic require rulebookVersion bump and operator evidence (externalRef). Sandbox window definitions governed by NEST sandbox board.

## 21. Versioning

Semver on window definitions. MAJOR: gate/temporal logic change, eligibility narrowing. MINOR: new eligible products, widened horizons. PATCH: descriptive text. Instances are never versioned — a definition change applies to instances generated after the change only; in-flight instances complete under the old definition. ChangeLog mandatory; rulebookVersion recorded per definition version.

## 22. Example Records

```yaml
windowDefID: EIG-TW-DAM-D1-1100
windowName: SEM Day-Ahead Auction (D-1 11:00)
windowType: TW-DA | marketID: EIG-MKT-DAM-01 | status: active
timezone: Europe/Dublin
windowOpen: D-9T09:00 | windowClose: D-1T11:00 | gateClosure: D-1T11:00
gateModel: single_gate
recurrenceRule: {class: daily, rrule: "FREQ=DAILY", calendarID: SEM-CAL-01}
deliveryHorizonStart: PT13H | deliveryHorizonEnd: PT37H
deliveryPeriodMin: 60 | settlementPeriodMin: 30 | periodAlignment: top_of_hour
forecastHorizonMin: 780 | forecastHorizonMax: 2220 | rdRecencyAtGate: true
eligibleProductIDs: [EIG-PRD-EN-HH-01]
eligibleParticipantClasses: [ACT-SUP, ACT-AGG, ACT-BRP]
prequalificationRequired: true | sandbox: false
rulebookVersion: SEM-TSC-2026.1
```

```yaml
windowDefID: EIG-TW-LFM-DA
windowName: Western LFM Day-Ahead Dispatch Window
windowType: TW-LFM-D | marketID: EIG-MKT-LFM-01 | status: active
timezone: Europe/Dublin
windowOpen: D-1T08:00 | windowClose: D-1T16:00 | gateClosure: {offsetMinBeforeDelivery: 240}
gateModel: per_period_gate
recurrenceRule: {class: daily, rrule: "FREQ=DAILY;BYDAY=MO,TU,WE,TH,FR", calendarID: LFM-CAL-01}
deliveryHorizonStart: PT8H | deliveryHorizonEnd: PT32H
deliveryPeriodMin: 30 | settlementPeriodMin: 30 | periodAlignment: half_hour_grid
forecastHorizonMin: 240 | forecastHorizonMax: 1920 | rdRecencyAtGate: true
eligibleProductIDs: [EIG-PRD-FLX-SHED-01, EIG-PRD-FLX-SHIFT-01]
eligibleParticipantClasses: [ACT-AGG, ACT-FLX]
prequalificationRequired: true
zoneRestriction: [EIG-NET-NZ-00017]
minOrderQty: 10 (kW) | sandbox: false
rulebookVersion: LFM-PILOT-2026.2
```

```yaml
windowDefID: EIG-TW-SBX-DAM-01
windowName: NEST Sandbox DAM Replica
windowType: TW-SBX | marketID: EIG-MKT-SBX-01 | status: active
replicaOfWindowDefID: EIG-TW-DAM-D1-1100
sandbox: true   # all outcomes firewalled per 20 A2
(remaining temporal fields identical to replica source)
```

## 23. Completion Criteria

Complete when: every market in 20 has all its windows defined here with bidirectional references; calendar service generates instances for 400 days with zero V6/V10 violations; every trade in 25 validates against a window instance gate state; DST transition days verified for the current year; sandbox replicas mirror production definitions byte-for-byte except identity/sandbox fields; Phase 14 confirms no orphan windows and no trades referencing undefined windows.
