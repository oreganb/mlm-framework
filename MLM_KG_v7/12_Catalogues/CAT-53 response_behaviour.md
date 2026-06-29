---
type: catalogue
ontology: EIG-MLM
catalogue_no: 53
catalogue_id: EIG-CAT-053
entity: response_behaviour
band: response_derivative
status: active
tags: [eig-mlm, catalogue, band:response_derivative, entity:response_behaviour]
aliases: ["EIG-CAT-053", "Catalogue 53", "response_behaviour"]
---

# CAT-53 — Response Behaviour

> **EIG-CAT-053** · band: **Response Derivative Family** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-03 occupancy]] · [[CAT-36 device]] · [[CAT-38 data]] · [[CAT-40 data_quality]] · [[CAT-56 response_event]]
**Used by (downstream):** [[CAT-47 validation]] · [[CAT-52 response_derivative]] · [[CAT-55 response_profile]] · [[CAT-66 reinforcement_learning]] · [[CAT-70 model_selection]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-053`
**Entity prefixes:** `EIG-RB-` (behaviour observations), `EIG-RB-STAT-` (behaviour statistics)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 03 occupancy, 36 device, 38 data, 40 data_quality, 56 response_event
**Used by (downstream):** 47 validation (VM-RDV evidence), 52 response_derivative (stability/variability), 55 response_profile, 66 reinforcement_learning, 70 model_selection

---

## 1. Definition

The **Response Behaviour Catalogue** records what subjects *actually did* when stimulated: per-event **observations** (`EIG-RB-*`) — the measured response trace, timing, magnitude, and deviations for one subject in one response event (56); and rolling **statistics** (`EIG-RB-STAT-*`) — per subject × spec × context aggregations of those observations (means, dispersion, trends, reliability). This is the empirical layer between raw events (56) and estimated RDs (52): events happen, behaviour is observed here, RDs are estimated from it, and RD validation (47 VM-RDV) scores predictions against it.

Behaviour is descriptive, not predictive: an observation says what happened with measurement provenance; it makes no claims about next time. The catalogue's purpose is **learning and RD validation** — the closed loop that keeps RDs honest.

> **v1.1 (2026-06-13) — behaviour as the primary RD conditioning input.** With the realised-not-nameplate principle (52 v1.1, A9/A10), the observations and statistics recorded here are not merely RD *validation* evidence — they are the *basis from which realisable RD is estimated*. The occupant's realised self-consumption, when assets actually ran, and how reliably a response repeated are exactly the conditions (CX-OCC) that drag realisable RD below asset nameplate. In the synthetic generator this is sequenced explicitly: realised behaviour is produced first, and RD is derived from the residual (what was actually surplus and behaviourally permitted), never from the asset spec. Behaviour is therefore the input to RD, with the asset stack only setting the ceiling.

## 2. Scope

**In scope:** observation schema (trigger, timing decomposition, magnitude trace, override/abort capture, deviation analysis vs expectation); statistics schema (variability, reliability, trend, fatigue indicators feeding 55); occupant-mediated behaviour capture (override events, manual interventions) with privacy rules; behavioural drift detection.

**Out of scope:** event orchestration (56), RD estimation (52), profile composition (55), occupancy profile definitions (03 — behaviour components there are priors; observations here are evidence).

## 3. Classification Structure

### 3.1 Observation outcomes (`outcome`)

| Code | Outcome | Definition |
|---|---|---|
| OB-FULL | Full response | delivered ≥95% of expected over the window (22 product convention) |
| OB-PART | Partial response | 70–95% delivered |
| OB-FAIL | Failed response | <70% delivered |
| OB-OVR | Overridden | occupant/operator override curtailed the response (29 A4 — honoured, recorded, never penalised silently) |
| OB-ABRT | Aborted | system abort (interlock 36, comms loss 34, quarantine 35) |
| OB-NULL | No-response control | subject was in control group; baseline observation |

### 3.2 Timing decomposition (`timingPhases`)

| Phase | Definition |
|---|---|
| t_signal | dispatch signal receipt (34 message timestamp, source per 36 V11) |
| t_actuation | device actuation confirmed (readback, 36 A6) |
| t_response | measurable response onset (detection rule per spec) |
| t_peak | maximum response magnitude |
| t_decay_start | saturation onset |
| t_release | event end signal |
| t_recovered | return to baseline band (rebound complete) |

### 3.3 Deviation classes (`deviationClass`) — observation vs expectation (52 prediction where one existed)

| Code | Class | Meaning |
|---|---|---|
| DV-IN | Within band | inside RD CI80 |
| DV-UNDER / DV-OVER | Under/over-delivery | outside CI, direction noted |
| DV-LATE | Latency breach | response onset beyond RD latency p90 |
| DV-EARLYSAT | Early saturation | sustainable duration shortfall |
| DV-REBOUND+ | Excess rebound | recovery energy beyond model band |

## 4. Hierarchy

```
Response event (56 EIG-REV-*)
  └── Behaviour observation (EIG-RB-*)        ← one subject's measured behaviour in that event
        └── Behaviour statistics (EIG-RB-STAT-*) ← rolling per subject × spec × context
              → 52 stability/confidence inputs; 55 profile inputs
```

## 5. Field Groups & Fields

### 5.1 Observation (`EIG-RB-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | observationID | string | — | `EIG-RB-\d{8}` | M | ID |
| 2 | eventRef | ref | — | →56 EIG-REV-* | M | The stimulating event |
| 3 | subjectRef | ref | — | →02/04/11/30 | M | Who responded |
| 4 | rdSpecRef | ref | — | →52 EIG-RD-SPEC-* | M | Response kind observed |
| 5 | trigger | json | — | {signal schema (34), magnitude, channel} copy | M | What stimulated |
| 6 | contextSnapshot | json | — | 52 §3.2 axes valued at event time | M | Conditioning record |
| 7 | timing | json | s | §3.2 phase timestamps + sources (36 V11) | M | Full decomposition |
| 8 | responseTrace | ref | — | →38 EIG-DE-* slice (event window + baseline + recovery) | M | The measured series (rd fitness, 40) |
| 9 | baselineMethod | ref | — | declared method (spec-bound) + computed baseline | M | Counterfactual basis |
| 10 | magnitudeObserved | json | spec rdUnit basis | {mean over window, peak, energy} | M | Delivered quantities |
| 11 | expectedRef | ref | — | →52 EIG-RD-* prediction (where existed) | C | Prediction being tested |
| 12 | deviation | json | — | {class §3.3, normalised error} | C | vs expectation |
| 13 | outcome | enum | — | §3.1 | M | Verdict |
| 14 | overrideDetail | json | — | {who: occupant/operator, at, mechanism (36 HMI/29)} | C | For OB-OVR; privacy rules A4 |
| 15 | anomalies | array<string> | — | free + coded flags | C | Unusual phenomena (feeds 11/36 diagnostics) |
| 16 | dataQualityAttest | ref | — | →40 EIG-DQ-A-* covering windows | M | rd-fitness proof |
| 17 | environment | enum | — | production, sandbox | M | Firewall tag |

### 5.2 Statistics (`EIG-RB-STAT-*`) — rolling per subject × rdSpec × context cell

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | statID | string | — | `EIG-RB-STAT-\d{7}` | M | ID |
| 2 | subjectRef / rdSpecRef / contextCell | refs/json | — | as above | M | Aggregation key |
| 3 | window | json | — | {observationCount, span} | M | Evidence window |
| 4 | magnitudeStats | json | rdUnit | {mean, sd, cv, trend slope} | M | Central + dispersion |
| 5 | variability | decimal | — | normalised inter-event CV ∈ [0,1] | M | Feeds 52 stability (= 1 − variability normalised) |
| 6 | reliability | decimal | — | (OB-FULL + 0.5·OB-PART) / dispatched, overrides excluded per A3 | M | Delivery dependability |
| 7 | overrideRate | decimal | — | OB-OVR / dispatched | M | Occupant tolerance signal (03 feedback) |
| 8 | latencyStats | json | s | {p50, p90, trend} | M | Timing dependability |
| 9 | fatigueIndicator | json | — | {magnitude vs event-frequency regression, recovery-time trend} | M | Repeated-stimulation wear (55 fatigue input) |
| 10 | driftFlag | bool/json | — | CUSUM/changepoint on magnitude series | M | Behavioural drift ⇒ 52 re-estimation trigger |
| 11 | lastUpdated | datetime | — | UTC | M | Rolling currency |

## 6. Controlled Vocabularies
outcome (§3.1 — thresholds aligned to 22 product convention), timing phases (§3.2), deviation classes (§3.3), override mechanisms, anomaly flag codes. Reliability formula fixed (§5.2.6) framework-wide.

## 7. Applicability Rules

- **A1 — Measured-only.** Observations record measured behaviour at 40 rd fitness with 36 V11 timestamps; estimated/imputed values in event windows disqualify the observation from RD evidence (it remains a logged operational record, flagged non-evidentiary).
- **A2 — Every dispatched subject observed.** Each event participant (56) yields exactly one observation — including failures, overrides, aborts; selective recording corrupts reliability statistics (V3).
- **A3 — Overrides are respected data.** OB-OVR excluded from reliability denominators (the subject *could* respond; the human chose otherwise — that's 03 engagement data, not capability data) but counted in overrideRate and surfaced to contract analytics (23) per contract terms only.
- **A4 — Occupant privacy.** Override detail at person grain stays within the subject's own record; cohort behaviour publications respect n≥10 (38 A7); no individual behavioural profiling beyond the subject's own service relationship (GDPR minimisation, 37 A7).
- **A5 — Control observations.** OB-NULL controls (matched non-dispatched subjects/windows) recorded under the same schema — baseline-quality machinery for 52 estimators and 27 BL method audits.
- **A6 — Drift triggers learning.** driftFlag=TRUE ⇒ 52 re-estimation scheduled and 66 learning contexts updated; sustained DV-UNDER patterns escalate to asset diagnostics (11/36).
- **A7 — Sandbox parity.** Sandbox events yield observations under identical schema (NEST learning loops), firewall-tagged, never mixed into production statistics.

## 8. Validation Rules

- **V1:** observation per dispatched participant per event (completeness join vs 56 participant lists).
- **V2:** timing phases monotone (t_signal ≤ t_actuation ≤ t_response ≤ … ≤ t_recovered) with declared detection rules; violations flag anomalies, not silent fixes.
- **V3:** outcome consistent with magnitudeObserved vs expectation thresholds (§3.1 mechanical).
- **V4:** responseTrace windows cover baseline + event + recovery per spec; dataQualityAttest covers all three.
- **V5:** deviation computed only where expectedRef exists; class assignment mechanical.
- **V6:** statistics recomputable from member observations; reliability/override formulas fixed.
- **V7:** observations immutable; corrections supersede.
- **V8:** drift detection runs on every statistics update; flag transitions logged.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 56 response_event | parent events; participant completeness |
| 52 RD | stability/variability inputs; evidence observations; drift → re-estimation |
| 47 validation | VM-RDV scores RD predictions against observations (MT-RDERR source data) |
| 55 response_profile | reliability, fatigue, latency stats compose profiles |
| 03 occupancy | override behaviour ↔ engagement class/override propensity priors updated (DPO-governed) |
| 27 settlement | delivered quantities reconcile with settlement's four-quantity model (independent computation, reconciliation check) |
| 11/36 | anomalies feed asset/device diagnostics |
| 66 RL | behaviour streams as environment feedback (sandbox training; production exploitation observation) |

## 10. Benchmarking Requirements
Behaviour league per cohort (n≥10): reliability, override rates, latency distributions by classification node/season — the empirical counterpart to 01 flexibilityPotentialClass priors; persistent prior-vs-observed gaps trigger prior revision (01 governance).

## 11. Dataset Requirements
Observation archives package as RD-grade corpora (43, e.g. the event sets behind EIG-MBM-SUITE-0011); retention matches RD/settlement horizons (≥7 y where cited).

## 12. Feature Requirements
Behaviour-derived features (41 FC-FLX/FC-OCC): reliability_last_n, override_rate_rolling, latency_p90 — TX-MODEL-adjacent aggregates, registered with lineage to statIDs.

## 13. Model Requirements
SER-07 estimators consume observations as training/evidence data; SER-05 occupancy models update engagement parameters from override statistics under 03 §20 DPO governance.

## 14. KPI Requirements
MET-015 flex_delivery_success (12) computes from outcome statistics; comfort-impact KPIs join observations with comfort traces (04 zone series).

## 15. Response Derivative Requirements
The learning loop: 56 event → 53 observation → 52 record (estimation/refresh) → 47 VM-RDV (prediction vs observation) → 51 TT-RD (estimator competition) → better estimators → better RDs. Variability here is the direct source of 52 stability; drift here is the canonical re-estimation trigger.

## 16. Decision Requirements
60 consumes live reliability/override context: subjects with rising override rates get gentler dispatch strategies — moderation rules keyed to overrideRate bands; repeated OB-ABRT subjects route to diagnostics before further dispatch.

## 17. Ontology Mapping
Observation ↔ SOSA `sosa:Observation` pattern (stimulus/procedure/result) with EIG response extensions; statistics ↔ aggregation entities with PROV derivation; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `BehaviourObservation`, `BehaviourStatistics`; edges `IN_EVENT`→56, `OF_SUBJECT`, `TESTS`→52 record {deviation}, `FEEDS`→55 profile, `TRIGGERED_DRIFT`→52 re-estimation. Cohort behaviour queries respect privacy floors at the query layer.

## 19. Digital Twin Mapping
Twins replay observations for diagnosis (measured vs twin-simulated response — divergence localises model error); autonomous twins (M4) update behavioural parameters from statistics under versioned, governed updates (03 §19 pattern).

## 20. Governance
Owner: AI/ML Lead; occupant-behaviour analytics jointly with DPO (A4); reconciliation discrepancies vs settlement (§9) escalate to 27 adjustment review. Statistics methodology changes are MINOR+ with recompute.

## 21. Versioning
Observations immutable; statistics rolling with method version stamps. Catalogue 1.0.0.

## 22. Example Records

### EIG-RB-00219034 — observation (the kind behind EIG-RD-00184421's evidence)
- event EIG-REV-… (56, LFM dispatch 2026-02-03 17:30); subject EIG-AST-004211; spec THERM-SHED
- trigger: FLEX-DISPATCH shed, full magnitude, MQTT QoS2 (34); context snapshot matches RD cell (occupied, winter, evening, 1–3 h band)
- timing: t_signal 17:30:02 (gateway clock), t_actuation 17:30:18 (readback ✓), t_response 17:30:41, t_peak 17:42, t_release 18:15, t_recovered 19:22
- trace EIG-DE-ODEON-ASSET-TELEM-L2 slice; baseline matched-profile; magnitude {mean 2.25 kW, peak 2.6 kW, energy 1.69 kWh}
- expected EIG-RD-00184421 {2.3 kW ci80 [2.0,2.6]}; deviation DV-IN (−2.2%); outcome OB-FULL; quality EIG-DQ-R-021 attest ✓

### EIG-RB-STAT-0048812 — statistics for that subject × spec × cell
- 9 observations / 14 months; magnitude {mean 2.28 kW, cv 0.11}; variability 0.13 → 52 stability 0.87 ✓ (consistent)
- reliability 0.94 (1 OB-PART, overrides excluded); overrideRate 0.10 (1 OB-OVR — occupant guest evening); latency p90 74 s; fatigue: no magnitude-frequency slope; drift FALSE

### EIG-RB-00224471 — OB-OVR observation
- occupant override via app at t+12 min; overrideDetail within own record; reliability denominator excluded (A3); overrideRate counted; contract analytics (EIG-CON-001207: within 2/week terms, no penalty); 03 engagement prior update queued (DPO-approved aggregation)

## 23. Completion Criteria
☑ Observation schema with full timing decomposition, baseline, deviation, outcome ☑ Override/abort/control capture with privacy rules ☑ Rolling statistics (variability, reliability, fatigue, drift) with fixed formulas ☑ Completeness joins vs events ☑ Learning loop to 52/47/51 explicit ☑ Settlement reconciliation hook ☑ Examples chain to the worked RD record.
