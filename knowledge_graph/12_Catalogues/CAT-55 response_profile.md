---
type: catalogue
ontology: EIG-MLM
catalogue_no: 55
catalogue_id: EIG-CAT-055
entity: response_profile
band: response_derivative
status: active
tags: [eig-mlm, catalogue, band:response_derivative, entity:response_profile]
aliases: ["EIG-CAT-055", "Catalogue 55", "response_profile"]
---

# CAT-55 — Response Profile

> **EIG-CAT-055** · band: **Response Derivative Family** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-01 building_classification]] · [[CAT-30 flexibility]] · [[CAT-52 response_derivative]] · [[CAT-53 response_behaviour]]
**Used by (downstream):** [[CAT-22 product]] · [[CAT-25 trade]] · [[CAT-29 flexibility_service]] · [[CAT-60 decision_intelligence]] · [[CAT-66 reinforcement_learning]] · [[CAT-70 model_selection]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-055`
**Entity prefixes:** `EIG-RP-TPL-` (profile templates), `EIG-RP-` (subject profiles)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 01 building_classification, 30 flexibility, 52 response_derivative, 53 response_behaviour
**Used by (downstream):** 22 product (matching), 25 trade (portfolio construction), 29 flexibility_service (eligibility), 60 decision_intelligence (dispatch strategy), 66 reinforcement_learning (context), 70 model_selection

---

## 1. Definition

A **response profile** (`EIG-RP-*`) is a subject's flexibility fingerprint: the standing composite of its Response Derivatives (52) and observed behaviour statistics (53) across contexts, summarised along the operational axes that matter for **flexibility profiling** — speed, magnitude, stability, fatigue, and recovery. Where an RD record answers "how much will *this* respond to *that* in *this exact context*?", a profile answers the portfolio question: "what kind of responder is this subject overall, and how should it be used?".

**Profile templates** (`EIG-RP-TPL-*`) define archetype responder classes (e.g. "fast-twitch thermal", "deep-shift storage", "behavioural slow-responder") with axis-band definitions, letting subjects be classified, matched to products (22), composed into portfolios (25/30), and dispatched with type-appropriate strategies (60). Profiles are derived artefacts: they hold no evidence of their own — every figure traces to RD records and behaviour statistics, and a profile is exactly as current as its inputs (A1).

## 2. Scope

**In scope:** the five profile axes (speed, magnitude, stability, fatigue, recovery) with normative scoring; template/instance schema; per-context profile cells and the overall summary; derivation rules from 52/53; profile currency and degradation; product-matching and portfolio-composition interfaces; dispatch-strategy hints.

**Out of scope:** RD estimation (52), behaviour observation (53), portfolio simultaneity mathematics (30 — profiles feed it), occupancy behaviour priors (03 — observed engagement arrives via 53).

## 3. Classification Structure

### 3.1 Profile axes (normative definitions)

| Axis | Definition | Source quantities | Scale |
|---|---|---|---|
| AX-SPD | Speed | RD latency p90 across active cells (52 §5.2.7) | bands: SPD-1 ≤1 s, SPD-2 ≤60 s, SPD-3 ≤900 s, SPD-4 >900 s (aligned to 29 A8 latency classes) |
| AX-MAG | Magnitude | deliverable response: Σ-relevant RD magnitude × sustainable duration − rebound (52 A5 arithmetic), normalised per subject scale | absolute kW/kWh + normalised score [0,1] vs cohort |
| AX-STB | Stability | RD stability (52) ∧ behaviour variability (53), worst-of | [0,1] |
| AX-FAT | Fatigue | 53 fatigueIndicator: response degradation under repeated stimulation + override-rate trend | classes: FAT-NONE, FAT-LOW, FAT-MED, FAT-HIGH |
| AX-REC | Recovery | rebound payback window p50 + return-to-availability time (52 rebound, 53 t_recovered stats) | duration bands: REC-FAST ≤30 min, REC-STD ≤2 h, REC-SLOW >2 h |

### 3.2 Response types (`responseType`) — template families

| Code | Type | Signature | Typical subjects |
|---|---|---|---|
| RT-FTT | Fast-twitch thermal | SPD-2, MAG modest, REC-STD, FAT-LOW | heat pumps with thermal mass (EIG-AC-HVC-01-01 class) |
| RT-DST | Deep-shift thermal | SPD-3, MAG high (duration-rich), REC-SLOW | storage heating, well-insulated fabric (05 high-κ) |
| RT-STO | Storage | SPD-1/2, MAG symmetric up/down, REC-FAST, FAT-NONE (cycle-cost noted) | batteries (EIG-AC-STO-01-01) |
| RT-PROC | Process-schedulable | SPD-3/4, MAG high, FAT-MED (operational tolerance) | industrial schedulable loads (OPC-02) |
| RT-BEH | Behavioural | SPD-4, MAG variable, STB lower, FAT-HIGH (engagement wear) | notification-mediated response (RDD-OCC) |
| RT-MIX | Composite | declared mix | multi-asset buildings/sites |

### 3.3 Profile status

CURRENT (all inputs ACTIVE/fresh) → DEGRADED (some inputs STALE — axes flagged) → SUSPENDED (critical inputs INVALIDATED — not matchable) → rebuilt on input refresh. Profiles never outlive their evidence.

## 4. Hierarchy

```
Template (EIG-RP-TPL-*)                    ← responder archetype: axis-band definitions
  └── Subject profile (EIG-RP-*)           ← one subject's fingerprint
        ├── Context cells                  ← per 52 context cell: axis values + input refs
        └── Summary                        ← overall axis scores + template classification
```

## 5. Field Groups & Fields

### 5.1 Template (`EIG-RP-TPL-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | templateID | string | — | `EIG-RP-TPL-\d{3}` | M | ID |
| 2 | responseType | enum | — | §3.2 | M | Family |
| 3 | axisBands | json | — | required band per §3.1 axis (ranges, with `any` permitted) | M | Membership definition (machine-evaluable) |
| 4 | productAffinity | array<ref> | — | →22 EIG-PRD-* / 29 EIG-FS-* | M | Which products/services this type suits |
| 5 | dispatchHints | json | — | strategy parameters for 60 (duty-cycle caps, rest periods per FAT class, staging order) | M | Type-appropriate use |
| 6 | classificationAffinity | array<ref> | — | →01 nodes where typical | C | Prior linkage |
| 7 | version / status | semver/enum | — | — | M | Lifecycle |

### 5.2 Subject profile (`EIG-RP-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | profileID | string | — | `EIG-RP-\d{7}` | M | ID |
| 2 | subjectRef | ref | — | →02/04/11/30 resource | M | One subject (one CURRENT profile each, V1) |
| 3 | contextCells | array<json> | — | per 52 cell: {context, AX-SPD..AX-REC values, inputRefs: [→52 EIG-RD-*, →53 EIG-RB-STAT-*]} | M | The evidence-traced body |
| 4 | summary | json | — | overall axis scores (declared aggregation: availability-weighted across cells), dominant context coverage | M | The fingerprint headline |
| 5 | templateClassification | ref+score | — | →EIG-RP-TPL-* + match score [0,1] | M | Best-fit type (RT-MIX with components where no clean fit) |
| 6 | magnitudeAbsolute | json | kW/kWh | deliverable by direction (up/down per 30 §6) and duration band | M | Portfolio-construction quantities |
| 7 | confidenceFloor | decimal | — | min input confidence across cited RDs | M | Weakest-link honesty |
| 8 | currency | json | — | {status §3.3, freshestInput, stalestInput, degradedAxes[]} | M | Liveness |
| 9 | derivedAt / derivationVersion | datetime/semver | — | UTC / derivation-rule version | M | Recompute provenance |
| 10 | environment | enum | — | production, sandbox | M | Firewall tag |

## 6. Controlled Vocabularies
Axes + bands (§3.1 — bands frozen with catalogue version; SPD bands locked to 29 A8 classes), responseType (§3.2), status (§3.3), aggregation methods for summaries (declared register). Direction vocabulary inherited from 30 §6.

## 7. Applicability Rules

- **A1 — Derived, never asserted.** Every axis value in every cell traces to cited 52/53 inputs; profiles carry no free-standing claims. A subject with no ACTIVE RDs has no profile (priors from 01 templates inform *expectations*, not profiles).
- **A2 — Currency follows inputs.** Input transitions (52 STALE/INVALIDATED, 53 drift) propagate within 24 h: affected axes flagged, status recomputed; SUSPENDED profiles drop out of product matching and portfolio rosters automatically (22/25/30 hooks).
- **A3 — Weakest-link confidence.** confidenceFloor = min over cited RD confidences; product matching uses the floor, not the average (no confidence laundering through aggregation).
- **A4 — Fatigue is binding.** Dispatch strategies (60) respect FAT-class rest periods and duty-cycle caps from dispatchHints; profile fatigue degradation (53 trend) tightens caps before failures occur — protect the resource, protect the occupant relationship (03).
- **A5 — Direction completeness.** magnitudeAbsolute states both directions explicitly (zero where unevidenced); portfolio construction (30) never infers down-capability from up-evidence.
- **A6 — Template honesty.** templateClassification below declared match-score threshold ⇒ RT-MIX with explicit components; forcing clean labels onto messy responders corrupts portfolio assumptions.
- **A7 — Sandbox parity.** NEST sandbox profiles derive identically from sandbox RDs/behaviour, firewall-tagged; never matched to production products.

## 8. Validation Rules

- **V1:** one CURRENT profile per subject; rebuilds supersede.
- **V2:** every cell input ref resolves (52 ACTIVE/STALE-flagged, 53 current stats); dangling refs ⇒ SUSPENDED.
- **V3:** axis values recomputable from inputs under the stamped derivationVersion.
- **V4:** SPD band consistent with cited latency p90s; MAG arithmetic consistent with 52 A5 (magnitude × duration − rebound).
- **V5:** summary aggregation matches declared method; coverage gaps (uncovered context cells) listed, not hidden.
- **V6:** templateClassification score recomputable against axisBands.
- **V7:** confidenceFloor = true minimum (V-audited).
- **V8:** currency propagation latency ≤24 h (monitored).
- **V9:** profiles immutable per derivation; recomputes supersede with derivedAt chain.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 52 RD | magnitude/latency/saturation/rebound inputs; lifecycle propagation |
| 53 behaviour | stability/fatigue/recovery/override inputs; drift propagation |
| 22 product | matching: product rdRequirements ∧ template affinity ∧ confidenceFloor |
| 25/30 | portfolio rosters built from CURRENT profiles; magnitudeAbsolute + simultaneity (30 maths) |
| 29 service | SPD bands lock to latency classes; eligibility screening |
| 60 decision | dispatchHints (staging, rest, duty cycles); SUSPENDED exclusion |
| 66 RL | profile axes as agent context features; template as state component |
| 01 | classification affinities as priors/expectations (never profile substitutes) |
| 70 | estimator/strategy selection per responder type |

## 10. Benchmarking Requirements
Cohort profile distributions per classification node (n≥10): axis distributions, template mix — the empirical flexibility census that planning (89) and product design (22) consume; prior-vs-profile gap reporting to 01 governance.

## 11. Dataset Requirements
Profile snapshots packaged for planning studies (43) with input-ref manifests; reproducibility = inputs + derivationVersion.

## 12. Feature Requirements
Profile-derived features (41 FC-FLX): template one-hots, axis scores, confidenceFloor — registered with lineage to profileIDs; TX-MODEL class (not RD evidence).

## 13. Model Requirements
Per-type model selection (70): RT-DST subjects route to duration-rich envelope models; RT-BEH to engagement-aware estimators — template drives the model applicability filter alongside 01.

## 14. KPI Requirements
Profile-quality KPIs (57): % flex-enrolled subjects with CURRENT profiles, mean confidenceFloor, degradation dwell time, template-mix trends.

## 15. Response Derivative Requirements
Profiles are the RD layer's portfolio interface: they composite RD records without diluting their gates — citation gates (52 A4) still check the underlying records at dispatch/bid time; the profile accelerates matching, it never bypasses evidence.

## 16. Decision Requirements
60 dispatch strategy per type: staging order (STO before DST for fast needs), rest-period enforcement (A4), recovery-aware re-dispatch (REC bands gate back-to-back events), behavioural-subject gentleness (RT-BEH duty caps tied to override trends).

## 17. Ontology Mapping
Profile ↔ EIG ontology `eig:ResponseProfile` aggregating `eig:ResponseDerivative` individuals via `eig:summarises`; axes as datatype properties with band individuals; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `ProfileTemplate`, `ResponseProfile`; edges `OF_SUBJECT`, `SUMMARISES`→52/53 inputs, `CLASSIFIED_AS`→template {score}, `MATCHED_TO`→22 products, `IN_ROSTER`→30 portfolios. Portfolio-composition queries ("CURRENT RT-FTT subjects, up ≥2 kW, conf ≥0.8, Inis Mór") are KG-native.

## 19. Digital Twin Mapping
Twins surface the live profile in the subject view (32); what-if dispatch simulation uses profile dispatchHints; community twins (86 scope) render aggregate profile mixes.

## 20. Governance
Owner: AI/ML Lead; template definitions reviewed with product owners (22) and operations (60 consumers); derivation-rule changes are MINOR+ with full recompute; axis-band changes are MAJOR (portfolio impact analysis).

## 21. Versioning
Templates semver; profiles supersede-on-recompute with derivationVersion stamps. Catalogue 1.0.0.

## 22. Example Records

### EIG-RP-TPL-001 — Fast-twitch thermal (RT-FTT)
- bands: AX-SPD ∈ SPD-2; AX-MAG normalised ≥0.4; AX-STB ≥0.7; AX-FAT ≤ FAT-LOW; AX-REC ≤ REC-STD
- productAffinity: EIG-FS-DR-01/02, LFM products (22); dispatchHints: max 2 events/day, ≥3 h spacing (REC-aware), stage after RT-STO
- classificationAffinity: SEC-01 detached with ASHP (01)

### EIG-RP-0004211 — profile for EIG-AST-004211 (the worked Aran ASHP)
- cells: winter/occupied/evening cell ← EIG-RD-00184421 + EIG-RB-STAT-0048812 {SPD-2 (74 s p90), MAG 2.3 kW × 2.4 h − rebound ⇒ 3.2 kWh deliverable up, STB 0.87, FAT-NONE (no slope), REC-STD (1.1 h payback)}; shoulder-season cell (separate RD) {MAG 1.4 kW, …}; summer cell: uncovered (gap listed)
- summary: SPD-2, MAG-norm 0.62, STB 0.85, FAT-NONE, REC-STD; template RT-FTT score 0.91 ✓
- magnitudeAbsolute: up {2.3 kW / 1–3 h winter; 1.4 kW shoulder}, down 0 (unevidenced, A5); confidenceFloor 0.83; currency CURRENT
- consumers: EIG-FS-DR-01 match ✓, Aran portfolio roster (EIG-FLX-PF-0007, 30), 60 staging order

### EIG-RP-0005102 — DEGRADED example
- IE_B0014 zone profile after calibration writeback invalidated its thermal RD (52 example): AX-MAG/AX-REC flagged, status DEGRADED → SUSPENDED for matching; auto-restore on re-estimation

## 23. Completion Criteria
☑ Five axes normatively defined (speed/magnitude/stability/fatigue/recovery) with bands locked to service latency classes ☑ Template/instance split with machine-evaluable membership ☑ Derived-never-asserted discipline with input tracing ☑ Currency propagation + weakest-link confidence ☑ Direction completeness + fatigue-binding dispatch hints ☑ Portfolio/product/decision interfaces ☑ Worked profile chains to the 52/53 Aran records.
