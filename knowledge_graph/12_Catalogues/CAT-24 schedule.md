---
type: catalogue
ontology: EIG-MLM
catalogue_no: 24
catalogue_id: EIG-CAT-024
entity: schedule
band: markets
status: active
tags: [eig-mlm, catalogue, band:markets, entity:schedule]
aliases: ["EIG-CAT-024", "Catalogue 24", "schedule"]
---

# CAT-24 — Schedule

> **EIG-CAT-024** · band: **Markets, Tariffs & Settlement** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Schedule Catalogue

## 1. Definition

The Schedule Catalogue is the authoritative register of schedule structures used across the EIG MLM framework: asset operating schedules, occupancy schedules, trading schedules, dispatch schedules, flexibility availability schedules, maintenance schedules and forecast schedules. A schedule is a time-structured plan or pattern that assigns values, states or actions to time intervals — either recurring (calendar patterns) or dated (specific horizons).

This catalogue defines schedule identity, typology, the canonical temporal representation (calendar patterns, interval series, recurrence rules), schedule semantics (setpoint vs state vs quantity vs action), versioning/supersession rules and the binding model that attaches schedules to assets, spaces, markets, services and models. One temporal grammar serves all schedule kinds, so optimisation, forecasting and control consume schedules uniformly.

## 2. Scope

In scope:

- **Asset schedules**: operating modes, setpoints, charge/discharge plans, on/off programmes per asset (11).
- **Occupancy schedules**: expected presence/usage patterns per space/building (03/04) — planned patterns; observed occupancy is data (38).
- **Trading schedules**: planned market positions per window (binds 20/26): nominated volumes per settlement period.
- **Dispatch schedules**: accepted/instructed delivery plans (post-trade or SO-instructed) at asset/pool level.
- **Flexibility schedules**: availability declarations per product (22 availabilityWindow targets) and committed flexibility blocks.
- **Maintenance schedules**: planned outages/service windows per asset with availability impact.
- **Forecast schedules**: the publication/refresh calendar of forecasts (44/46): which forecast runs when, covering which horizon.
- ToU band calendars (21) and trade-window recurrences (26) reuse this catalogue's temporal grammar.

Out of scope:

- Time-series observations (38), forecast VALUES (46), trade records (25), workflow orchestration logic (64 — workflows may EMIT schedules), event instances (63).

## 3. Classification Structure

Schedule kind (`schKind`) — closed vocabulary:

| Code | Kind | Payload semantics |
|---|---|---|
| SCH-AST | asset_operating | mode/setpoint/power targets per interval |
| SCH-OCC | occupancy | expected occupancy fraction/state per interval |
| SCH-TRD | trading | nominated MWh/kW per settlement period per market |
| SCH-DSP | dispatch | instructed/accepted kW delivery per interval |
| SCH-FLX | flexibility_availability | offerable kW up/down per interval per product |
| SCH-MNT | maintenance | availability state (available/derated/outage) per window |
| SCH-FCT | forecast_calendar | run times, horizons, refresh cadence per forecast series |

Temporal form (`temporalForm`): recurring_pattern (calendar rule, e.g. weekly profile), dated_series (explicit intervals over a horizon), event_window_set (list of dated windows), hybrid (pattern + dated exceptions).

Authority status (`authorityStatus`): proposed, planned, committed, instructed, superseded, cancelled — committed/instructed schedules are operative and feed control/settlement.

## 4. Hierarchy

```text
Schedule (EIG-SCH-NNNNNN)
├── boundTo → Asset | Space/Building | Pool | Market position | Product | ForecastSeries
├── SchedulePattern (recurring: dayType profiles + season/months + timezone rules)
│   └── Exception register (dated overrides: holidays, events)
└── ScheduleIntervals (dated: [{start, end, payload}] at declared resolution)
Supersession chain: Schedule vN ─supersededBy→ vN+1 (same binding, overlapping horizon)
```

Payload typing per kind (closed): SCH-AST {mode?, setpoint{var, value, unit}?, powerTarget_kW?}; SCH-OCC {occupiedFraction 0–1 | state}; SCH-TRD {quantity, unit, marketID, windowRef}; SCH-DSP {power_kW, productID, dispatchRef}; SCH-FLX {availUp_kW, availDown_kW, productID}; SCH-MNT {availabilityState, deratingPct?}; SCH-FCT {runTime, horizon, resolution, seriesRef}.

## 5. Field Groups & Fields

### 5.1 Identity & binding

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | scheduleID | string | — | `EIG-SCH-NNNNNN` | M | Unique schedule ID |
| 2 | schKind | enum | — | §3 SCH-* | M | Schedule kind |
| 3 | temporalForm | enum | — | §3 forms | M | Representation form |
| 4 | boundEntityKind | enum | — | asset, space, building, pool, market_position, product_availability, forecast_series | M | Target kind |
| 5 | boundEntityRef | string | — | EIG-AST/SPC/BLD/ACT-pool/MKT+window/PRD/forecast refs | M | Target; kind-consistent (V2) |
| 6 | authorityStatus | enum | — | §3 statuses | M | Operative standing |
| 7 | issuerRef | object | — | {userID or serviceID (17), roleID (16), or workflowID (64)} | M | Who/what issued it |
| 8 | sourceKind | enum | — | manual, optimiser, market_result, so_instruction, contract_default, template | M | Provenance |
| 9 | sourceRef | string | — | trade/decision/workflow/contract ref | C | Provenance link (mandatory for committed+, V5) |
| 10 | status | enum | — | active, superseded, cancelled, expired | M | Record lifecycle |

### 5.2 Temporal structure

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | timezone | string | — | IANA (Europe/Dublin) | M | Clock context |
| 2 | dstRule | enum | — | local_clock, utc_fixed | M | DST behaviour (aligns 21 §5.4) |
| 3 | resolutionMin | integer | min | 1, 5, 15, 30, 60, 1440 | M | Interval resolution |
| 4 | horizonStart / horizonEnd | datetime | — | ISO 8601; end open for recurring | M | Coverage window |
| 5 | pattern | object | — | {dayTypes: [{dayType: weekday/sat/sun/holiday, profile: [{from, to, payload}]}], months?/seasons?, holidayCalendarRef} | C | Recurring form; profiles tile 24 h (V6) |
| 6 | exceptions | array | — | [{date or range, profile or interval set, reason}] | C | Dated overrides; exceptions beat pattern |
| 7 | intervals | array | — | [{start, end, payload}] contiguous, non-overlapping | C | Dated form |
| 8 | gapPolicy | enum | — | forbid_gaps, default_payload, inherit_previous | M | Gap semantics |
| 9 | holidayCalendarRef | string | — | IE public holiday calendar ID (37 source) | C | For dayType=holiday |

### 5.3 Payload & constraints

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | payloadSchema | string | — | §4 typed schema per schKind | M | Validated payload type |
| 2 | unitRefs | array | — | units per 19/39 registry | M | Units declared |
| 3 | constraintRefs | array | — | comfort bounds (03/04), asset envelopes (11 §5.6), contractual envelopes (23 §15), network limits (13) | C | Constraints the schedule must satisfy (V8) |
| 4 | priorityClass | integer | — | 1 (life-safety lockouts) … 9 (advisory) | M | Conflict-resolution precedence |
| 5 | overridePolicy | enum | — | occupant_override_allowed, operator_only, locked | M | Who may deviate (joins 14 overrides, 16 authority) |

### 5.4 Record metadata

recordVersion, createdDate, modifiedDate, supersedesRef, governanceStatus — programme standard.

## 6. Controlled Vocabularies

Closed: schKind, temporalForm, authorityStatus, boundEntityKind, sourceKind, dstRule, gapPolicy, overridePolicy, dayType set, payload schemas per kind. External: IANA timezones, iCalendar RRULE semantics (recurrence expressiveness bounded to the pattern model above), IE public holiday calendar.

## 7. Applicability Rules

| Rule | Statement |
|---|---|
| A1 | At most ONE committed/instructed schedule per (boundEntity, schKind, instant): supersession resolves overlaps before commitment (V7) |
| A2 | Dispatch schedules (SCH-DSP) derive only from accepted trades (25) or SO instructions; sourceRef mandatory; quantities ≤ committed product capacity (22) |
| A3 | Trading schedules per market respect window structure (26): interval boundaries align to settlement periods; nominations within gate rules |
| A4 | Flexibility availability schedules must lie within asset/pool technical envelopes (11 §5.6) AND contractual envelopes (23 §15) — min of both (V8) |
| A5 | Maintenance schedules propagate availability: overlapping SCH-FLX/SCH-DSP commitments re-validate on maintenance commit; conflicts block commitment until resolved |
| A6 | Occupancy schedules drive comfort constraint windows: control optimisation may not relax comfort during scheduled-occupied intervals beyond contracted bounds |
| A7 | Priority resolution at runtime: lower priorityClass number wins; life-safety (1) never overridden; occupant overrides (where allowed) log to 14/17 attribution |
| A8 | Forecast calendars (SCH-FCT) gate model runs (44): runs outside their calendar flag as ad-hoc with reason |

## 8. Validation Rules

| ID | Rule | Severity |
|---|---|---|
| V1 | scheduleID unique; schKind/payload schema match | error |
| V2 | boundEntityRef resolves and kind-consistent | error |
| V3 | Resolution divides all interval/profile boundaries; horizonStart < horizonEnd | error |
| V4 | authorityStatus transitions proposed→planned→committed→{instructed, superseded, cancelled}; instructed only for SCH-DSP | error |
| V5 | committed/instructed require sourceRef + issuer with authority (16 permission check) | error |
| V6 | Recurring profiles tile 24 h per dayType, no gap/overlap; DST days handled per dstRule (23/25-h days resolve deterministically) | error |
| V7 | No two operative schedules overlap per A1 key | error |
| V8 | Payloads within constraintRefs bounds at every interval (envelope check) | error |
| V9 | Dated intervals contiguous per gapPolicy; payloads non-null where forbid_gaps | error |
| V10 | SCH-TRD interval grid = market settlement period grid (20 §5.3) | error |
| V11 | Supersession chains acyclic; superseded schedules immutable | error |
| V12 | Holiday dayTypes require holidayCalendarRef | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 03/04 occupancy/space | Occupancy schedules per space; comfort windows |
| 11 asset | Operating/maintenance schedules; envelope constraints |
| 14/16/17 | Issuer attribution, override authority, occupant override logging |
| 20/26 market/window | Trading schedule grids; gate-time discipline |
| 22 product | Availability schedules per product; dispatch quantity caps |
| 23 contract | Contractual envelopes as constraints; contract-default schedules |
| 25 trade | Accepted trades spawn dispatch schedules (A2) |
| 27 settlement | Instructed dispatch schedules are settlement evidence inputs |
| 38/46 data/forecast | Observed vs scheduled comparison datasets; forecast calendars |
| 44 model | Optimisers emit proposed schedules; forecast runs gated by SCH-FCT |
| 60/61/64 decision/action/workflow | Commitment decisions; schedule execution as actions; workflows manage lifecycle |
| 21 tariff | ToU band calendars reuse the pattern grammar |

## 10. Benchmarking Requirements

Schedule-adherence metrics: scheduled-vs-actual deviation (per kind), override frequency, maintenance plan attainment — cohorts min n = 10. Dispatch adherence feeds 12 MET-015 delivery success.

## 11. Dataset Requirements

Operative schedule snapshots archived at every supersession (point-in-time reconstruction for settlement disputes); scheduled-vs-observed paired datasets per binding; schedule event log (commitments, overrides, cancellations) append-only.

## 12. Feature Requirements

Schedule-derived features (41): scheduled state/setpoint at t, time-to-next-transition, scheduled-occupied flag, committed-dispatch kW, maintenance flag, deviation features (actual − scheduled). Features cite scheduleID + version operative at t.

## 13. Model Requirements

Optimisers output proposed schedules in this grammar (payload-typed, constraint-checked pre-commitment); occupancy pattern models learn/refresh SCH-OCC patterns from observed data with human approval to commit; forecast models respect SCH-FCT calendars; control models treat operative schedules as references with bounded deviation authority.

## 14. KPI Requirements

KPIs: schedule adherence %, override rate, comfort-in-occupied-hours compliance, dispatch fulfilment, maintenance on-time %, optimiser schedule acceptance rate. All carry scheduleID/kind dimensions.

## 15. Response Derivative Requirements

Schedules are the actuation frame for RD exploitation: committed flexibility blocks reference the RD evidence justifying them (via 22 A3 chain); RD estimation requires knowing the counterfactual schedule (baseline schedule = what would have run); schedule archives provide that counterfactual evidence for 27 baseline methods.

## 16. Decision Requirements

Commitment decisions validate V5–V10 + envelope checks and record full evidence; conflict resolutions (A5/A7) are logged decisions; cancellation of instructed schedules requires SO/issuer authority and triggers settlement notice where delivery was committed.

## 17. Ontology Mapping

Classes: Schedule ⊐ by kind; SchedulePattern; ScheduleInterval; ScheduleException; PayloadType.
Object properties: boundTo, hasPattern, hasInterval, hasException, supersededBy, derivedFrom (→ Trade/Decision/Workflow), constrainedBy (→ Envelope/Contract/ComfortSpec), issuedBy.
Datatype properties: scheduleID, schKind, temporalForm, authorityStatus, resolutionMin, timezone, priorityClass.
Alignment: iCalendar (RRULE/EXDATE) for recurrence; SAREF saref:Profile / s4ener PowerProfile for asset schedules; OpenADR event schedules at dispatch boundary; CIM scheduling classes for trading nominations.

## 18. Knowledge Graph Mapping

Nodes: Schedule (versioned), Pattern. Relationships: BOUND_TO, SUPERSEDED_BY, DERIVED_FROM (→ Trade/Decision), CONSTRAINED_BY, ISSUED_BY, EVIDENCES (→ Settlement/baseline). Keys: scheduleID + version. Temporal validity on all edges.

## 19. Digital Twin Mapping

Twins execute operative schedules as boundary conditions; proposed schedules simulate pre-commitment (comfort/envelope verification A4/A6); twin replays use archived schedule versions for historical fidelity; maintenance windows derate twin asset models.

## 20. Governance

Owner: EIG Catalogue Owner; operations lead co-owns commitment workflow design; priorityClass register controlled (life-safety classes locked); supersession audit quarterly; holiday calendar refreshed annually.

## 21. Versioning

Schedules immutable once committed: changes occur via supersession (new version, chain-linked); pattern templates semver; archives permanent for settlement-relevant kinds (SCH-TRD/DSP/FLX) per 93 retention.

## 22. Example Records

```text
scheduleID: EIG-SCH-031177 | SCH-AST | recurring_pattern | boundTo: asset EIG-AST-004211 (ASHP @ EIG-BLD-000877)
authorityStatus: committed | issuer: optimiser svc EIG-USR-09001 under EIG-ROL-SVC-FCAST | sourceKind: optimiser
timezone: Europe/Dublin | dstRule: local_clock | resolution: 30 min | horizon: 2026-06-01 → open
pattern: weekday [{06:30–08:30 setpoint flow 45 °C}, {08:30–16:00 setback 35 °C}, {16:00–22:00 45 °C}, {22:00–06:30 off}]
        sat/sun [{07:30–22:30 45 °C}, …] | exceptions: [2026-06-01 IE holiday → sunday profile]
constraints: [comfort ≥ 19 °C occupied (03), envelope EIG-AST-004211 §5.6, contract EIG-CON-001207 floor 19 °C]
priorityClass: 5 | overridePolicy: occupant_override_allowed | gapPolicy: forbid_gaps
```

```text
scheduleID: EIG-SCH-040912 | SCH-DSP | dated_series | boundTo: pool EIG-ACT-00031-POOL-01
authorityStatus: instructed | sourceKind: market_result | sourceRef: trade EIG-TRD-008834 (LFM call-off)
resolution: 30 min | intervals: [{2026-06-12 17:00–17:30, {power_kW: −180, productID: EIG-PRD-FLX-SHED-01}},
{17:30–18:00, −180}, {18:00–18:30, −120}] | constraints: [framework EIG-CON-000884 envelope]
priorityClass: 3 | settlement evidence: feeds 27 delivered-vs-instructed
```

```text
scheduleID: EIG-SCH-050233 | SCH-MNT | event_window_set | boundTo: asset EIG-AST-002990 (community battery)
windows: [{2026-07-08 09:00–17:00, outage, reason: inverter firmware}] | authorityStatus: committed
propagation: SCH-FLX availability zeroed for window; conflicting SCH-DSP none (validated A5)
```

## 23. Completion Criteria

Complete when: all seven kinds carry typed payload schemas with envelope-validated commitment paths; one-operative-schedule invariant enforced; trading/dispatch grids align to market periods; supersession archives reconstruct any historical instant; pattern grammar reused by 21/26 verified; Phase 14 confirms no parallel schedule representations exist elsewhere.
