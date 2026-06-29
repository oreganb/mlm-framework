---
type: catalogue
ontology: EIG-MLM
catalogue_no: 56
catalogue_id: EIG-CAT-056
entity: response_event
band: response_derivative
status: active
tags: [eig-mlm, catalogue, band:response_derivative, entity:response_event]
aliases: ["EIG-CAT-056", "Catalogue 56", "response_event"]
---

# CAT-56 — Response Event

> **EIG-CAT-056** · band: **Response Derivative Family** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-23 contract]] · [[CAT-24 schedule]] · [[CAT-26 trade_window]] · [[CAT-29 flexibility_service]] · [[CAT-30 flexibility]] · [[CAT-34 communication]] · [[CAT-35 edge_infrastructure]] · [[CAT-36 device]]
**Used by (downstream):** [[CAT-27 settlement]] · [[CAT-52 response_derivative]] · [[CAT-53 response_behaviour]] · [[CAT-55 response_profile]] · [[CAT-66 reinforcement_learning]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-056`
**Entity prefixes:** `EIG-REV-CMP-` (event campaigns), `EIG-REV-` (response events)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead (learning design) with Operations (execution)
**Depends on (upstream):** 23 contract, 24 schedule, 26 trade_window, 29 flexibility_service, 30 flexibility, 34 communication, 35 edge_infrastructure, 36 device
**Used by (downstream):** 27 settlement (delivered events), 52 response_derivative (evidence), 53 response_behaviour (observations), 55 response_profile (indirectly), 66 reinforcement_learning (experience)

---

## 1. Definition

A **response event** (`EIG-REV-*`) is a bounded episode in which a stimulus was applied (or deliberately withheld, for controls) to one or more subjects and the outcome was captured: the unit that *creates or reveals responses*. Events are where the RD layer touches reality — every RD record's evidence (52 A1), every behaviour observation (53), and every flexibility settlement line (27) traces to events registered here. **Event campaigns** (`EIG-REV-CMP-*`) are designed sequences of events — estimation campaigns to build RD evidence systematically (context-cell coverage), and the registry view of market-driven dispatch series.

Events come from three origins: **market dispatch** (real product activations, 25/29), **designed estimation** (deliberate stimulation to fill evidence gaps, within contract terms), and **natural experiments** (registered exogenous episodes — price spikes, outages — exploited analytically with weaker claims). Purpose: **learning loops** — the framework learns responsiveness only through events, and learns efficiently only through designed ones (A6).

## 2. Scope

**In scope:** event lifecycle (scheduled → armed → triggered → active → released → captured → closed); trigger taxonomy; participant rosters and control groups; consent/contract gating; execution mechanics refs (dispatch messages 34, edge enforcement 35, actuation/readback 36); outcome capture handoff (53); campaign design (context-cell targeting, event budgets, ethics of stimulation); linkage registers (RDs refreshed, settlements generated, experience tuples emitted).

**Out of scope:** the measured behaviour itself (53), RD estimation (52), settlement computation (27), generic calendar machinery (24 — events use it), generic platform events (63 — system event taxonomy; response events are the flexibility-specific kind, cross-referenced).

## 3. Classification Structure

### 3.1 Event origins (`origin`)

| Code | Origin | Stimulus authority | Evidence strength |
|---|---|---|---|
| EO-MKT | Market dispatch | accepted bid/product activation (25/29) | full (designed stimulus, contracted) |
| EO-EST | Designed estimation | estimation campaign within contract estimation allowance (23) | full (controlled design) |
| EO-NAT | Natural experiment | exogenous episode (price spike, network event 13) registered post-hoc | reduced (no controlled stimulus; confounding caveats; RD priors and 54 analysis, not primary RD evidence unless spec explicitly admits) |
| EO-CTL | Control episode | deliberate non-dispatch of matched subjects/windows | baseline machinery (53 OB-NULL) |

### 3.2 Trigger types (`trigger`)

| Code | Trigger | Mechanics |
|---|---|---|
| TG-EXPL | Explicit dispatch | FLEX-DISPATCH message (34 EIG-COM-SCH-FLEX-DISPATCH-v2), QoS2, readback (36 A6) |
| TG-PRICE | Price signal | tariff/price broadcast; response voluntary per 29 price-class latency (≤900 s) |
| TG-FREQ | Frequency/autonomous | edge-local trigger within localAuthorityScope (35 A3) — event reconstructed from edge logs |
| TG-NOTIF | Notification | occupant message (RDD-OCC, 17/76) |
| TG-NONE | Withheld | EO-CTL marker |

### 3.3 Event lifecycle (`status`)

SCHEDULED → ARMED (participants locked, baselines snapshotted) → TRIGGERED → ACTIVE → RELEASED → CAPTURING (windows closing, 53 observations generating) → CLOSED (all observations + linkages complete) → VOID (failed execution — retained with cause).

## 4. Hierarchy

```
Campaign (EIG-REV-CMP-*)              ← designed sequence: targets, budget, coverage plan
  └── Event (EIG-REV-*)               ← one stimulation episode
        ├── Participants (+ controls) ← subjects with consent/contract gating
        ├── Execution trail           ← 34 messages / 35 edge logs / 36 actuation+readback
        └── Outcome linkages          ← 53 observations, 52 records refreshed, 27 settlement lines, 66 experience
```

## 5. Field Groups & Fields

### 5.1 Campaign (`EIG-REV-CMP-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | campaignID | string | — | `EIG-REV-CMP-\d{4}` | M | ID |
| 2 | purpose | enum | — | rd_estimation, market_series, trial (NEST), revalidation | M | Why |
| 3 | targetSpecs | array<ref> | — | →52 EIG-RD-SPEC-* | M | RDs being built/refreshed |
| 4 | coveragePlan | json | — | context cells (52 §3.2) × subjects × events-per-cell (≥ minEvidence) | M | Designed coverage (A6) |
| 5 | eventBudget | json | — | per-subject caps honouring contract terms (23, e.g. ≤2/week EIG-CON-001207) + comfort guardrails (29 A7) | M | Stimulation ethics (A2) |
| 6 | controlDesign | json | — | matched-control strategy (EO-CTL allocation) | M | Baseline rigour |
| 7 | schedule | ref | — | →24 calendar + randomisation rules | M | When (randomised within windows to avoid time-confounding) |
| 8 | approvals | array<ref> | — | owner + DPO where occupant-mediated (03/17) | M | Sign-off |
| 9 | environment | enum | — | production, sandbox | M | Firewall tag |
| 10 | version / status | semver/enum | — | DRAFT, ACTIVE, COMPLETE, ABORTED | M | Lifecycle |

### 5.2 Event (`EIG-REV-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | eventID | string | — | `EIG-REV-\d{8}` | M | ID |
| 2 | origin | enum | — | §3.1 | M | Origin |
| 3 | campaignRef | ref | — | →EIG-REV-CMP-* | C | For EO-EST/EO-CTL; market series registry link for EO-MKT |
| 4 | trigger | json | — | {type §3.2, signal schema + magnitude, channel (34), messageRefs} | M | The stimulus (TG-NONE for controls) |
| 5 | tradeRef | ref | — | →25 EIG-TRD-* / 26 window | C | Market linkage (EO-MKT) |
| 6 | participants | array<json> | — | [{subjectRef, contractRef (23), consentCheck ✓, expectedRD ref (52) where exists}] | M | Roster, entitlement-gated (A1) |
| 7 | controls | array<ref> | — | matched non-dispatched subjects | C | Per controlDesign |
| 8 | timing | json | — | {scheduledFor, triggeredAt, releasedAt} UTC + source discipline (36 V11) | M | Event clock |
| 9 | contextRegistry | json | — | event-level context (season, TOD, network state 13, price state 21) | M | Shared context for all participant observations |
| 10 | executionTrail | json | — | {dispatchMessages (34), edgeLogs (35 — incl. degraded-mode replay), actuationReadbacks (36)} | M | Proof of stimulus delivery (V3) |
| 11 | observedOutcome | json | — | roster-level summary: delivered vs expected aggregate, outcome mix (53 §3.1 counts) | M | Headline (detail lives in 53) |
| 12 | linkedObservations | array<ref> | — | →53 EIG-RB-* (one per participant + controls, V4) | M | The capture |
| 13 | linkedRDs | array<ref> | — | →52 records using this event as evidence / refreshed by it | M | Learning linkage |
| 14 | linkedSettlement | array<ref> | — | →27 EIG-SET-* lines | C | EO-MKT |
| 15 | linkedExperience | array<ref> | — | →66 experience tuples | C | RL learning |
| 16 | status | enum | — | §3.3 | M | Lifecycle |
| 17 | voidCause | string | — | — | C | For VOID (comms failure 34, quarantine 35 A4, interlock 36) |
| 18 | environment | enum | — | production, sandbox | M | Firewall tag |

## 6. Controlled Vocabularies
origin (§3.1), trigger types (§3.2), lifecycle (§3.3), campaign purposes, void causes. Event windows and baselines follow the cited 52 spec's definitions — no per-event improvisation.

## 7. Applicability Rules

- **A1 — Entitlement gating.** Every participant passes the contract entitlement chain (23 A1) at ARMED: valid contract, within event-frequency caps, comfort floors configured (29 A7), consent current for occupant-mediated triggers (17). Failing subjects drop from the roster before TRIGGERED — never stimulated-then-excused.
- **A2 — Stimulation ethics.** EO-EST events stay within contract estimation allowances and campaign budgets; estimation never degrades service or comfort beyond contracted terms; occupant-facing campaigns carry DPO approval; subjects can opt out of estimation without product penalty (distinct from market events they've contracted for).
- **A3 — Proof of stimulus.** An event is evidence-grade only with a complete execution trail: dispatch delivery (34 QoS2 acks), actuation readback (36 A6), edge replay where autonomous (35 A3). No trail ⇒ event VOID for evidence (operational record retained).
- **A4 — Capture completeness.** CLOSED requires one 53 observation per participant and control (53 A2/V1 join); incomplete capture holds the event in CAPTURING with escalation.
- **A5 — Natural-experiment honesty.** EO-NAT events register the exogenous episode with confounding notes; they feed 54 analysis and 52 priors; primary RD evidence only where the spec explicitly admits EO-NAT with declared criteria (rare).
- **A6 — Designed coverage.** RD evidence gaps (52 coverage vs minEvidence per cell) drive campaign design — the framework schedules its own learning; gap reports generate campaign proposals quarterly.
- **A7 — Randomisation discipline.** EO-EST scheduling randomises within permissible windows (controls for time-of-day/weather confounding); systematic scheduling biases are a design-review finding.
- **A8 — Sandbox parity.** NEST trial events run the full lifecycle against synthetic feeds inside the firewall — the learning loop logic is identical; data never crosses (20 A2 chain).

## 8. Validation Rules

- **V1:** participant entitlement checks logged at ARMED; roster frozen thereafter.
- **V2:** timing monotone; trigger timestamps source-disciplined (36 V11).
- **V3:** execution trail completeness for evidence-grade status (A3 mechanical).
- **V4:** linkedObservations bijective with participants ∪ controls at CLOSED.
- **V5:** event-frequency caps per subject verified against contract terms (23) across the rolling window.
- **V6:** EO-MKT events reconcile with trade/window refs (25/26) and settlement lines (27).
- **V7:** campaign coverage accounting: events-per-cell tallies update on CLOSED; budget overruns block further scheduling.
- **V8:** VOID events carry cause + diagnostics linkage (34/35/36).
- **V9:** events immutable post-CLOSED; corrections supersede (status-append discipline).
- **V10:** randomisation audit: EO-EST schedule distributions tested against design quarterly.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 52 RD | evidence source; coverage-gap-driven campaigns; minEvidence accounting |
| 53 behaviour | one observation per participant; outcome detail |
| 27 settlement | EO-MKT delivered events settle; four-quantity model per event |
| 25/26/29 | market dispatch origins; gate/window alignment; latency classes |
| 23 contract | entitlement gating; event caps; estimation allowances |
| 34/35/36 | execution mechanics + proof trail; degraded-mode replay; interlock voids |
| 24 schedule | calendars + randomisation |
| 66 RL | experience tuples per event (state/action/outcome) |
| 13/21 | event-level network/price context registry |
| 63 event (platform) | response events cross-registered in the system event taxonomy |

## 10. Benchmarking Requirements
Learning-efficiency KPIs: evidence gained per event (cells advanced toward minEvidence), VOID rate by cause, capture latency (RELEASED→CLOSED), campaign coverage attainment vs plan.

## 11. Dataset Requirements
Event registries + execution trails package into RD-grade corpora (43; the frozen event sets behind EIG-MBM-SUITE-0011 TT-RD); retention ≥7 y where settlement/RD-cited.

## 12. Feature Requirements
Event-context features (41): events_last_30d per subject (fatigue input), time-since-last-event — registered with lineage to eventIDs.

## 13. Model Requirements
SER-07 estimators consume events via 53 observations; event-context registries supply conditioning data; campaign design itself may use optimisation (SER-12) for coverage-per-budget.

## 14. KPI Requirements
MET-015 delivery KPIs aggregate event outcomes; campaign ROI (evidence value per € of estimation incentive paid under 23 terms) tracked.

## 15. Response Derivative Requirements
Events are the only admissible primary RD evidence source (52 A1): the loop is gap (52 coverage) → campaign (A6) → event → observation (53) → record (52) → profile (55) → product (22) → market dispatch (more EO-MKT events) → richer evidence. Designed estimation bootstraps the loop; market operation sustains it.

## 16. Decision Requirements
60 schedules EO-EST events as low-priority dispatchable work (estimation yields when market/comfort needs conflict); event execution itself runs the standard automated-action chain (envelope, explanation 49, audit).

## 17. Ontology Mapping
Event ↔ EIG `eig:ResponseEvent` (PROV `prov:Activity` specialisation) with stimulus/participant/outcome structure; campaigns ↔ `prov:Plan`; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `EventCampaign`, `ResponseEvent`; edges `STIMULATED`→subjects {trigger}, `CONTROLLED`→controls, `OBSERVED_AS`→53, `EVIDENCES`→52, `SETTLED_AS`→27, `EXPERIENCED_AS`→66, `IN_CAMPAIGN`. Coverage dashboards (cells × evidence counts) are KG views.

## 19. Digital Twin Mapping
Twins pre-simulate planned events (expected response per current RDs) for go/no-go review; post-event, measured-vs-simulated divergence feeds twin diagnostics (53 §19); sandbox twins host NEST trial events end-to-end.

## 20. Governance
Owners: AI/ML Lead (campaign design) + Operations (execution); DPO sign-off for occupant-mediated campaigns (A2); quarterly gap-and-coverage review generates the next campaign slate; VOID-cause trends reviewed with platform/edge owners.

## 21. Versioning
Campaigns semver; events immutable with status-append + supersession. Catalogue 1.0.0.

## 22. Example Records

### EIG-REV-CMP-0007 — Aran winter thermal-shed estimation campaign
- purpose rd_estimation; targets [EIG-RD-SPEC-THERM-SHED@2.0]; coverage: {winter × occupied × {evening, midday} × {≤1 h, 1–3 h}} × 18 ASHP subjects × 5 events/cell
- budget: ≤1 estimation event/subject/week within EIG-CON-001207-class allowances; comfort floor 19 °C hard; controls: 6 matched non-dispatched dwellings rotating
- schedule: randomised within 16:30–19:30 / 11:00–14:00 windows, Nov 2025–Feb 2026; approvals: owner + DPO ✓; environment production

### EIG-REV-00337712 — the event behind EIG-RB-00219034 / EIG-RD-00184421
- origin EO-MKT; trigger TG-EXPL FLEX-DISPATCH (34), QoS2 acks ✓; trade EIG-TRD-008834-series (25), window EIG-TW-LFM-DA (26)
- participants: 12 subjects incl. EIG-AST-004211 (entitlement ✓, expectedRD EIG-RD-00184421); controls: 4 matched
- timing: triggered 2026-02-03T17:30:02Z (gateway-disciplined), released 18:15; context: winter evening peak, NZ-00017 constraint active, ToU peak band
- trail: 12/12 readbacks (36), edge logs EIG-EDGE-DEP-00231 ✓; outcome: 27.1 kW delivered vs 27.9 expected (97.1%), mix {10 OB-FULL, 1 OB-PART, 1 OB-OVR}
- linkages: 16 observations (53) ✓ bijective; 9 RD records refreshed (52); settlement EIG-SET-00194412-series (27); 12 experience tuples (66); status CLOSED

### EIG-REV-00341005 — VOID example
- EO-EST event; TG-EXPL sent; 3 readbacks missing (LoRaWAN uplink outage, 34 channel EIG-COM-CHN-00041 failover lag); A3 ⇒ VOID for evidence, cause logged, diagnostics → 35 channel review; rescheduled per campaign budget

## 23. Completion Criteria
☑ Origin/trigger/lifecycle taxonomy with proof-of-stimulus bar ☑ Campaign design machinery (coverage, budgets, randomisation, controls, ethics) ☑ Entitlement gating before stimulation ☑ Capture completeness joins to 53 ☑ Full linkage registers (RD/settlement/experience) ☑ Natural-experiment honesty ☑ Worked event closes the entire Phase 8 chain (campaign → event → observation → RD → profile).
