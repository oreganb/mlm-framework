---
type: catalogue
ontology: EIG-MLM
catalogue_no: 52
catalogue_id: EIG-CAT-052
entity: response_derivative
band: response_derivative
status: active
tags: [eig-mlm, catalogue, band:response_derivative, entity:response_derivative]
aliases: ["EIG-CAT-052", "Catalogue 52", "response_derivative"]
---

# CAT-52 — Response Derivative

> **EIG-CAT-052** · band: **Response Derivative Family** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-01 building_classification]] · [[CAT-03 occupancy]] · [[CAT-05 building_fabric]] · [[CAT-07 building_archetype]] · [[CAT-11 asset]] · [[CAT-30 flexibility]] · [[CAT-36 device]] · [[CAT-38 data]] · [[CAT-40 data_quality]] · [[CAT-44 model]] · [[CAT-47 validation]] · [[CAT-51 model_benchmark]] · [[CAT-56 response_event]]
**Used by (downstream):** [[CAT-22 product]] · [[CAT-25 trade]] · [[CAT-27 settlement]] · [[CAT-29 flexibility_service]] · [[CAT-53 response_behaviour]] · [[CAT-55 response_profile]] · [[CAT-60 decision_intelligence]] · [[CAT-66 reinforcement_learning]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-052`
**Entity prefixes:** `EIG-RD-SPEC-` (RD specifications), `EIG-RD-` (RD records)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (RD is the framework's core theoretical contribution)
**Depends on (upstream):** 01 building_classification, 03 occupancy, 05 building_fabric, 11 asset, 30 flexibility, 36 device, 38 data, 40 data_quality, 44 model (SER-07), 47 validation, 51 model_benchmark, 56 response_event
**Used by (downstream):** 22 product, 25 trade, 27 settlement (BL methods), 29 flexibility_service, 53 response_behaviour, 55 response_profile, 60 decision_intelligence, 66 reinforcement_learning

---

## 1. Definition

The **Response Derivative (RD)** is the framework's central theoretical construct: a quantified, conditional, evidence-backed, **probabilistic** measure of responsiveness — *how much a response variable actually changes per unit change of an input variable, under the conditions prevailing right now*:

> **RD = ∂(response variable) / ∂(input variable) | context**, evaluated at realised conditions and carried with its uncertainty.

**The realised-not-nameplate principle (cardinal).** An asset stack states a *theoretical* potential — a PV array's nameplate, a battery's capacity, a heat pump's rating. That is the speedometer reading: *"how fast can the car go?" — 220 kph, but only in perfect conditions.* The RD answers the **useful** question instead: *"how much will this subject actually respond right now?"* — and that answer depends on the realised conditions: occupant behaviour and self-consumption, weather, asset state, comfort tolerance, network headroom, and how reliably the response repeats. The realisable RD is therefore almost always **below** what the asset spec alone suggests, and an RD that equals its nameplate availability is a modelling error, not a strong resource (V11). RD is **not an asset property**; it is a subject-level property that emerges from *assets × occupant behaviour × prevailing conditions*, estimated from **realised behaviour**, not from the asset specification.

An RD is not a generic sensitivity coefficient (54 — those serve analysis); an RD is **decision-grade conditional responsiveness**: it carries direction, magnitude with uncertainty, latency, saturation, rebound, stability, confidence, recency, and the full evidential context that makes it safe to act on. It replaces static assumptions with conditional, probabilistic reasoning, aligning physical feasibility with human behaviour and market logic — the missing link that lets the platform act autonomously without sacrificing explainability or trust. RD runs **in parallel with the MLM** model stack as the feasibility-and-trust check on what the models say is theoretically possible: the models say the car *can* do 220; the RD says how fast it will *actually* go on today's road. RDs are what flexibility products promise (22), markets buy (25), settlements verify against (27 BL methods), and decision intelligence dispatches on (60) — and the reason they "help avoid relying on unreliable flexibility resources" is precisely that they are estimated from what was realised, not from what the hardware claims.

The catalogue defines:
- **RD specifications** (`EIG-RD-SPEC-*`): the *kind* of RD — response/input variable pair, formula and estimation method (SER-07 estimator binding), context schema, and validity rules (e.g. EIG-RD-SPEC-THERM-SHED: thermal load shed per dispatch signal).
- **RD records** (`EIG-RD-*`): an estimated RD value for one subject in one context window, with evidence links to response events (56) and behaviour observations (53).

**Cardinal rule:** an RD without context is meaningless and without evidence is inadmissible. Every RD record binds subject + context + estimator + measured events (A1).

> **v1.1 (2026-06-13) — definition alignment with the IERC RD construct.** This revision makes three things explicit that the original treated implicitly: (1) the **realised-not-nameplate principle** — RD is estimated from realised behaviour and is structurally below asset-spec potential (new V11 gate; the "220 kph trap"); (2) **occupant behaviour is a primary conditioning axis** (CX-OCC strengthened), since the same assets yield different RDs under different occupants; (3) RD is **per-signal/per-service and time-varying** — evaluated for a specific input and conditioned on the prevailing interval's state ("how fast right now"), so it is carried as a time-resolved quantity, not a single static figure. The underlying formula ∂R/∂I|context is unchanged.

## 2. Scope

**In scope:** RD specification schema; record schema (direction, magnitude, confidence, latency, saturation, rebound, stability, recency); context dimensions and conditioning rules; estimation evidence bar; lifecycle (estimation → active → stale → invalidated → re-estimated); transfer rules (when an RD from one context may inform another); aggregation to portfolio RDs (30); product/settlement gate interfaces.

**Out of scope:** estimator internals (44 SER-07), generic sensitivities (54), raw observed responses (53 — evidence inputs here), event execution (56), profile composites (55 — built *from* RDs).

## 3. Classification Structure

### 3.1 RD domains (`rdDomain`)

| Code | Domain | Response variable class | Input variable class |
|---|---|---|---|
| RDD-THM | Thermal | heating/cooling power, zone temperature trajectory | setpoint Δ, dispatch signal, coast command |
| RDD-ELS | Electrical shed | active power (38 EIG-VAR power) | shed dispatch signal (34 FLEX-DISPATCH) |
| RDD-SHFT | Load shift | energy in window (TS-INT) | shift instruction / schedule Δ (24) |
| RDD-STO | Storage | charge/discharge power, SoC trajectory | dispatch signal |
| RDD-PRC | Price responsiveness | consumption Δ | tariff/price Δ (21) — behavioural RD |
| RDD-OCC | Occupant-mediated | consumption/comfort Δ | notification/engagement signal (17/76) |

### 3.2 Context dimensions (`contextSchema`) — mandatory conditioning axes

| Axis | Source | Examples |
|---|---|---|
| CX-CLASS | 01 classification node | EIG-BC-SUB level; flexibilityPotentialClass prior seed |
| CX-OCC | 03 occupancy state + realised behaviour | occupied/unoccupied; **realised self-consumption profile, historical load/generation pattern, override propensity, engagement class** — the "driver" of the response; the dominant reason realisable RD falls below nameplate (A9) |
| CX-SEASON | 09 climate | season band; ambient temperature band |
| CX-TOD | temporal | time-of-day band; day type |
| CX-ASSET | 11 asset state | asset class/tier; SoC band (storage); COP regime (HP); **nameplate availability recorded here as the theoretical ceiling — never the RD itself (A9, V11)** |
| CX-FAB | 05 fabric snapshot | HLC + mass class at estimation (retrofit ⇒ invalidation) |
| CX-NET | 13 network state | substation/feeder headroom at the subject's location and time — locational feasibility of the response (a realised condition, not a nameplate property) |
| CX-DUR | event duration band | e.g. ≤1 h, 1–3 h, >3 h (saturation regime) |

An RD record states its position on every axis (explicit `any` only where the spec proves invariance, V5). RDs MUST NOT transfer across CX-CLASS or CX-OCC boundaries without revalidation (01 §15, 03 §15 — A6).

### 3.3 Direction convention

Fixed framework-wide per 30 §6: **up = reduce net import** (shed/discharge/turn-down), **down = increase net import** (absorb/charge/turn-up). Signs in formulas follow this convention; UI renderings translate but never re-sign stored values.

### 3.4 Lifecycle status

ESTIMATED (fresh evidence, gates not yet checked) → ACTIVE (gates passed, citable) → STALE (recency expired — advisory only) → INVALIDATED (context broke: retrofit 05, asset change 11, estimator FAIL 47) → superseded by re-estimation. Records are immutable; status transitions append.

## 4. Hierarchy

```
RD specification (EIG-RD-SPEC-*)        ← variable pair × formula × estimator × context schema
  └── RD record (EIG-RD-*)              ← subject × context cell × evidence window
        ├── Evidence links → 56 events / 53 observations
        └── Aggregations → 30 portfolio RDs (simultaneity-adjusted)
```

## 5. Field Groups & Fields

### 5.1 RD specification (`EIG-RD-SPEC-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | rdSpecID | string | — | `EIG-RD-SPEC-[A-Z0-9-]{3,30}` | M | Spec ID |
| 2 | rdDomain | enum | — | §3.1 | M | Domain |
| 3 | responseVariable | ref | — | →38 EIG-VAR-* | M | What responds (registered variable) |
| 4 | inputVariable | ref | — | →38 EIG-VAR-* / signal schema (34) | M | What is varied |
| 5 | formula | string | — | machine-evaluable: ∂R/∂I estimator definition incl. baseline construction | M | e.g. `(P_baseline − P_event) / signal_magnitude` over event window, baseline per declared method |
| 6 | rdUnit | string | UCUM | dimensional: [response]/[input] (e.g. kW per K, kW per signal) | M | V2 dimensional check |
| 7 | estimatorBinding | ref | — | →44 SER-07 release (champion per 51 TT-RD) | M | Only validated SER-07 estimators (A2) |
| 8 | contextSchema | array<enum> | — | §3.2 axes + band definitions | M | Conditioning structure |
| 9 | minEvidence | json | — | {events ≥ N, span ≥ duration, fitness: rd (40)} | M | Evidence floor per record (default N=5) |
| 10 | latencyModel | json | — | {definition: signal→response start; estimator} | M | Latency is first-class (29 A8 classes) |
| 11 | saturationModel | json | — | {form: duration-decay/capacity-bound, parameters estimated per record} | M | Response is finite |
| 12 | reboundModel | json | — | {definition: post-event recovery energy / payback window} | M | Rebound is accounted (30) |
| 13 | recencyLimit | duration | — | default P90D (22 gate) | M | Staleness boundary |
| 14 | invalidationTriggers | array<enum> | — | retrofit (05), asset change (11), occupancy regime change (03), estimator supersession/FAIL (47/51), control system change (36) | M | Context-break events |
| 15 | transferPolicy | json | — | which axes permit prior-seeding vs hard revalidation (A6) | M | Cross-context rules |
| 16 | validationBinding | ref | — | →47 VM-RDV method + current record (+ conditions) | M | Estimator trust chain |
| 17 | version / status | semver/enum | — | — | M | Lifecycle |

### 5.2 RD record (`EIG-RD-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | rdID | string | — | `EIG-RD-\d{8}` | M | Record ID |
| 2 | rdSpecRef | ref | — | →EIG-RD-SPEC-* @version | M | Specification |
| 3 | subjectRef | ref | — | →02 building / 04 zone / 11 asset / 30 resource | M | Whose responsiveness |
| 4 | context | json | — | value per contextSchema axis | M | The conditioning cell (complete, V5) |
| 5 | direction | enum | — | up, down (§3.3) | M | Convention-fixed |
| 6 | magnitude | json | rdUnit | {point, ci80: [lo, hi], distribution ref} | M | The derivative with uncertainty |
| 7 | latency | json | s | {p50, p90} signal→response | M | Estimated per record |
| 8 | saturation | json | — | {sustainableDuration p50/p90, decayCurve params, capacityBound} | M | Finite-response account |
| 9 | rebound | json | — | {energyRatio, paybackWindow p50} | M | Post-event recovery |
| 10 | stability | decimal | — | [0,1]: 1 − normalised inter-event variance (53 variability feed) | M | Consistency across evidence events |
| 11 | confidence | decimal | — | [0,1] per §5.3 composition | M | The 22-gate quantity (≥0.8 for product citation) |
| 12 | evidenceEvents | array<ref> | — | →56 EIG-REV-* (≥ minEvidence) | M | Measured events only |
| 13 | evidenceQuality | json | — | per-event 40 rd-fitness attestation; syntheticLineage=FALSE (42 §15); timestampSource per 36 V11 | M | Machine-checkable bar (A1) |
| 14 | estimatedAt / estimatorRelease | datetime/string | — | UTC / SER-07 modelID@semver copy | M | Provenance |
| 15 | validUntil | datetime | — | estimatedAt + recencyLimit (or earlier trigger) | M | Staleness boundary |
| 16 | status | enum | — | §3.4 | M | Lifecycle (append-only transitions) |
| 17 | supersedes | ref | — | prior EIG-RD-* | C | Re-estimation chain |
| 18 | conditionsInherited | array<string> | — | from 47 PASS-CONDITIONAL (e.g. "events ≤3 h, occupied contexts only") | C | Binding limits (A3) |
| 19 | nameplateCeiling | json | rdUnit | {theoreticalMax, source: CX-ASSET nameplate} | M | **(v1.1)** The asset-spec theoretical maximum (the "220 kph"). magnitude MUST sit below this; the gap is the realised-conditions discount (A9, V11). Recorded, never the RD. |
| 20 | realisedDiscount | json | — | {fraction: magnitude.point / nameplateCeiling.theoreticalMax, dominantCause: enum[occupant, weather, network, comfort, asset_state, competing_claim]} | M | **(v1.1)** Explicit account of how far below nameplate the realisable RD sits and why — the auditable form of the realised-not-nameplate principle |
| 21 | intervalState | json | — | snapshot of time-varying CX axes at the conditioning interval (occupancy, weather band, SoC, CX-NET headroom, price band) | M | **(v1.1)** The "right now" conditions this RD value was evaluated against (A10); ties the record to its point in the ts_response_derivative stream |

### 5.3 Confidence composition (normative)

`confidence = f(evidenceCount vs minEvidence, stability, estimator validation strength (47 MT-RDERR), evidence recency profile, context-cell purity)` — declared monotone scoring function per spec version; recomputable (V7). Confidence is **evidential**, never forecast-derived (46 §15 conflation ban).

## 6. Controlled Vocabularies
rdDomain (§3.1), context axes + band definitions (§3.2 — bands versioned with specs), direction (§3.3 — frozen), lifecycle (§3.4), invalidation triggers. RD terminology in publications cites this catalogue (the RD concept register).

## 7. Applicability Rules

- **A1 — Evidence bar (the RD-grade rule).** Every record: ≥ minEvidence measured events (56) at 40 rd fitness; REAL/CALIBRATED-rooted, syntheticLineage=FALSE (42 §15); source timestamps (36 V11); zero estimated values inside event windows (40 A5); no forecasts as evidence (46 §15). Records failing any clause are inadmissible — not low-confidence, inadmissible.
- **A2 — Validated estimators only.** rdSpecRef binds the current champion SER-07 release (51 TT-RD) holding unexpired VM-RDV PASS/PASS-CONDITIONAL (47); estimator FAIL or expiry ⇒ all dependent ACTIVE records → STALE pending re-estimation.
- **A3 — Conditions bind.** 47 PASS-CONDITIONAL conditions copy into every record (conditionsInherited) and constrain citation: e.g. EIG-VAL-00102 ⇒ thermal-shed RDs citable only for events ≤3 h in occupied contexts; consumers (22/60) must check.
- **A4 — Gates for citation.** Product prequalification and bids (22/25): status=ACTIVE ∧ confidence ≥ product minConfidence (≥0.8 standard) ∧ within recency (≤90 d standard) ∧ context match ∧ conditions satisfied. Settlement BL methods using rd_counterfactual (27) cite specific rdIDs — auditable per trade.
- **A5 — Saturation and rebound are part of the promise.** Dispatch planning (60) and envelope arithmetic (30) consume magnitude × sustainableDuration − rebound; quoting magnitude alone overstates deliverable flexibility and is a 47-auditable misuse.
- **A6 — Context discipline.** No transfer across CX-CLASS or CX-OCC without revalidation; other axes per transferPolicy (priors may seed, never substitute, evidence). Classification-node priors (01 flexibilityPotentialClass) initialise expectations only.
- **A7 — Invalidation propagates.** Trigger events (retrofit 05 §15, asset replacement 11, calibration writeback 50 A5, occupancy regime change 03, control/device change 36) flip affected records to INVALIDATED within 24 h and notify consumers (22 prequal re-check, 30 envelope recompute, 60 fallback).
- **A8 — Aggregation honesty.** Portfolio RDs (30) aggregate record magnitudes with simultaneity factors and correlation accounting — never naive sums; aggregated confidence ≤ min member confidence adjusted for diversification, method declared in 30.
- **A9 — Realised, not nameplate (the 220 kph rule).** An RD is estimated from *realised* behaviour under prevailing conditions, never computed from the asset specification. The asset nameplate (CX-ASSET) is recorded as the theoretical ceiling and is the *upper bound* the realisable RD sits below — it is never itself reported as the RD. The dominant discount is occupant behaviour (CX-OCC): the load sitting under the PV, when the HP actually ran, how the battery actually cycled to serve self-consumption, the occupant's tolerance for being shifted. A subject's realisable export/shed is what remains after self-consumption and behavioural permission, not what the hardware could theoretically deliver. Two subjects with identical assets but different occupants have different RDs; this is expected, not an error. An RD whose magnitude approaches its nameplate availability is a generation/estimation defect (V11) — it means the conditions were ignored.
- **A10 — Per-signal and time-varying.** RD is the derivative *with respect to a specific input* (RDD-domain / inputVariable), so it is estimated **per signal/service**: a subject may have high RD for thermal shed and low RD for P2P export under the same conditions. And because the conditioning set includes time-varying state (occupancy, weather, SoC, network headroom, price), RD is **time-resolved** — "how much can this subject respond *right now*, for *this* service." Records are conditioned on the prevailing interval's state; the generator and platform carry a time-series of RD value+confidence per subject × spec (the `ts_response_derivative` stream), not a single static figure. A characteristic per-subject RD distribution may be summarised from the series, but the operational quantity is the conditioned, current value.

## 8. Validation Rules

- **V1:** IDs/patterns; spec references resolve; one ACTIVE record per subject × spec × context cell (re-estimation supersedes).
- **V2:** rdUnit dimensional consistency: [responseVariable]/[inputVariable] per 38 UCUM register.
- **V3:** evidenceEvents ≥ minEvidence; each resolves in 56 with measured-outcome status.
- **V4:** evidenceQuality machine-verified: 40 assessment refs at rd fitness per event window; 42 taint check; 36 timestampSource check.
- **V5:** context complete (every schema axis valued); `any` only where spec invariance proof recorded.
- **V6:** magnitude CI consistent with stability and event dispersion (recomputable from evidence).
- **V7:** confidence recomputable from declared composition; manual overrides impossible.
- **V8:** validUntil = min(estimatedAt + recencyLimit, earliest trigger horizon); STALE transition automatic at expiry.
- **V9:** direction consistent with sign of magnitude under §3.3 convention.
- **V10:** citation audit: every 22 prequal, 25 bid, 27 rd_counterfactual settlement line resolves to records satisfying A4 at citation time (retrospective audits sample-checked).
- **V11 — realised-below-nameplate (the 220 kph detector).** Every record's magnitude is checked against the subject's nameplate availability (CX-ASSET ceiling): magnitude must be strictly below the theoretical maximum, with the gap attributable to recorded CX-OCC/CX-SEASON/CX-NET conditions. A record whose magnitude ≈ nameplate (within a configured tolerance) FAILS as a suspected nameplate-leak — RD was derived from the asset spec rather than realised behaviour. (In the synthetic generator this is gate `G-RD-REALISED`.)
- **V12 — per-signal, time-resolved.** RD records key on subject × spec (per signal/service) × context cell; the time-varying stream is continuous over the subject's active window with each value tagged to the interval state that conditioned it. A subject claiming identical RD across distinct signals, or an RD invariant across materially different interval conditions, is flagged for review (likely a conditioning omission).

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 56 response_event | evidence events; learning loop (event → observation → re-estimation) |
| 53 response_behaviour | observed-response statistics feed stability/variability; behaviour validates RD predictions |
| 55 response_profile | profiles composite RDs + behaviour into subject flexibility fingerprints |
| 54 sensitivity | analytical sensitivities are NOT RDs; boundary explicit (54 §1) |
| 44/47/51 | SER-07 estimators; VM-RDV trust; TT-RD champions |
| 40/42/36/38 | the RD-grade evidence machinery |
| 22/25/27 | product gates (conf ≥0.8, ≤90 d); bids; settlement counterfactuals |
| 29/30 | service latency classes; envelopes, portfolio aggregation, direction convention |
| 60/66 | dispatch decisions; RL state/reward features (RD values as state, never as reward ground truth without settlement confirmation) |
| 01/03/05/11 | context axes + invalidation sources |

## 10. Benchmarking Requirements
RD ecosystem KPIs: coverage (% flex-enrolled subjects with ACTIVE records per required spec), median confidence, staleness rate, invalidation response time, predicted-vs-delivered error tracked via 53/27 (the ultimate RD truth test).

## 11. Dataset Requirements
RD evidence reproducibility: records recomputable from cited RELEASED datasets (43 §15, e.g. EIG-DSET-ODEON-ASSET-TELEM); evidence retained ≥7 y where settlement-cited.

## 12. Feature Requirements
RD values re-enter 41 as FC-FLX features (rd_conf_last, headroom composites) marked TX-MODEL — usable as model inputs, never as RD evidence (41 A5 circularity bar).

## 13. Model Requirements
SER-07 estimators per 44 A5; estimator improvements compete on 51 TT-RD frozen event suites; champion change ⇒ staged re-estimation of ACTIVE records (priority: settlement-cited first).

## 14. KPI Requirements
MET-015 flex_delivery_success (12) is the operational verification of RD promises; KPI dashboards badge RD confidence/recency alongside delivery stats.

## 15. Response Derivative Requirements
(Self) — this catalogue is the normative home of the RD concept: definition, conditionality, evidence bar, gates, lifecycle. All other catalogues' §15 sections resolve here. The canonical statement, in one line: **RD is how much a subject will actually respond to a given signal right now, estimated from realised behaviour under prevailing conditions, carried with its uncertainty — never the asset nameplate, which is only the ceiling it sits below.**

## 16. Decision Requirements
60 dispatch logic per A4/A5: gate-check → envelope arithmetic (magnitude × duration − rebound, latency-feasible per 29 A8) → conditions check → action with per-action explanation (49) citing the rdID. STALE/INVALIDATED ⇒ advisory or fallback.

## 17. Ontology Mapping
RD ↔ EIG core ontology class `eig:ResponseDerivative` (the framework's novel class; SHIFT/MLM ontology line) with `eig:conditionedOn` context individuals, QUDT-typed magnitude, PROV evidence chains to events; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `RDSpecification`, `RDRecord`; edges `OF_SUBJECT`, `IN_CONTEXT` {axis values}, `EVIDENCED_BY`→56, `ESTIMATED_BY`→44 release, `GATES`→22/25/27 citations, `INVALIDATED_BY` {trigger}, `SUPERSEDES`. "Show all settlement-cited RDs whose estimator validation expires this quarter" is one query.

## 19. Digital Twin Mapping
Twins (32 M3+) surface live RD state per subject (ACTIVE records + gate status); twin what-if dispatch simulations consume RD records with saturation/rebound models; twin-simulated responses are NOT evidence (measured-only rule).

## 20. Governance
Owner: PI (concept integrity); operational stewardship: AI/ML Lead (estimators) + Data Management (evidence machinery). Spec changes (bands, formulas, gates) are MAJOR with consumer impact analysis. The RD register is the canonical citation for RD publications.

## 21. Versioning
Specs semver; records immutable with append-only status + supersession chains (27 A6 discipline). Catalogue 1.1.0 (v1.1: realised-not-nameplate principle, occupant + network conditioning axes, per-signal/time-varying RD, V11/V12 gates, nameplateCeiling/realisedDiscount/intervalState fields).

## 22. Example Records

### EIG-RD-SPEC-THERM-SHED @2.0 — thermal load shed RD (resolves 33/47/51 refs)
- domain RDD-THM; response EIG-VAR-0240 hp_power_w (38); input FLEX-DISPATCH shed signal (34 EIG-COM-SCH-FLEX-DISPATCH-v2)
- formula: (P̂_baseline − P_event)/1 over event window; baseline: matched-profile method (declared, distinct from 27 BL registry but reconciled)
- unit kW per dispatch; estimator: champion per EIG-MBM-SUITE-0011 (SER-07), validation EIG-VAL-00102 PASS-CONDITIONAL (conditions: events ≤3 h, occupied contexts)
- context schema: all §3.2 axes; CX-DUR bands {≤1 h, 1–3 h}; minEvidence 5 events/cell; recency P90D
- latency: signal→compressor response; saturation: thermal-mass decay form; rebound: recovery-energy ratio model

### EIG-RD-00184421 — record for EIG-AST-004211 (NIBE ASHP @ IE_B0009)
- spec THERM-SHED@2.0; subject asset 11; context {CX-CLASS: SUB residential detached, CX-OCC: occupied/responsive_automated, CX-SEASON: winter, CX-TOD: evening peak, CX-ASSET: HP normal-COP regime, CX-FAB: HLC 142 W/K snapshot, CX-DUR: 1–3 h}
- direction up; magnitude {2.3 kW, ci80 [2.0, 2.6]}; latency {p50 38 s, p90 71 s}; saturation {sustainable p50 2.4 h, capacity-bound by comfort floor 19 °C (EIG-CON-001207)}; rebound {ratio 0.42, payback p50 1.1 h}
- stability 0.87; confidence 0.86 ✓ (≥0.8 gate); evidence: 7 events (56 refs), all EIG-DQ-R-021 rd-fitness ✓, taint FALSE ✓, gateway timestamps ✓
- estimator SER-07 champion@…; estimatedAt 2026-05-02; validUntil 2026-07-31; status ACTIVE; conditionsInherited ["events ≤3 h", "occupied contexts only"]
- citations: EIG-FS-DR-01 prequal (22), LFM bids (25), envelope EIG-MDL-FLEXENV-01 cross-check (30)

### EIG-RD-00190772 — INVALIDATED example
- prior record for IE_B0014 zone; trigger: RK-CAL calibration writeback (EIG-TRN-0004907) changed thermal parameters → INVALIDATED 2026-06-09, consumers notified (22 prequal suspended, 30 envelope recomputed); re-estimation scheduled on next 5 qualifying events

## 23. Completion Criteria
☑ RD defined as conditional, evidence-backed, decision-grade responsiveness ☑ Spec/record split with complete field registers (direction/magnitude/confidence/stability/saturation/latency/rebound/recency) ☑ Context schema with transfer discipline ☑ RD-grade evidence bar machine-checkable ☑ Product/settlement gates (≥0.8, ≤90 d) wired ☑ Invalidation propagation ☑ Confidence composition recomputable ☑ EIG-RD-SPEC-THERM-SHED + worked Aran record resolve all forward refs.
