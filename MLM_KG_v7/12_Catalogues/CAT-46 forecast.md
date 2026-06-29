---
type: catalogue
ontology: EIG-MLM
catalogue_no: 46
catalogue_id: EIG-CAT-046
entity: forecast
band: models
status: active
tags: [eig-mlm, catalogue, band:models, entity:forecast]
aliases: ["EIG-CAT-046", "Catalogue 46", "forecast"]
---

# CAT-46 — Forecast

> **EIG-CAT-046** · band: **Models & Learning** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-38 data]] · [[CAT-41 feature]] · [[CAT-44 model]] · [[CAT-45 algorithm]]
**Used by (downstream):** [[CAT-22 product]] · [[CAT-25 trade]] · [[CAT-27 settlement]] · [[CAT-52 response_derivative]] · [[CAT-57 kpi]] · [[CAT-60 decision_intelligence]] · [[CAT-75 dashboard]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-046`
**Entity prefixes:** `EIG-FC-DEF-` (forecast definitions), `EIG-FC-` (forecast runs)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 38 data, 41 feature, 44 model, 45 algorithm
**Used by (downstream):** 22 product, 25 trade, 27 settlement (baselines), 52 response_derivative, 57 kpi, 60 decision_intelligence, 75 dashboard

---

## 1. Definition

The **Forecast Catalogue** governs operational forecasting: the **forecast definitions** (`EIG-FC-DEF-*`) that bind a forecasting model (44, horizon>0) to a subject, horizon structure, cadence, and uncertainty obligation; and the **forecast runs** (`EIG-FC-*`) — the timestamped, immutable output records each execution produces. A forecast run is the unit consumed operationally: it carries the issued values (point/quantiles/intervals), the issuing model release, input snapshot reference, confidence, validity period, and supersession links. Forecasts are perishable evidence: every run has an explicit validity window, and consumers (60 decisions, 25 bids, 75 dashboards) MUST check freshness before use (A2).

## 2. Scope

**In scope:** definition and run records; horizon/lead-time/cadence semantics; uncertainty representation (quantiles, intervals, distributions); confidence scoring; validity and supersession; issue-time discipline (no hindsight contamination); skill tracking hooks (47/51); operational staleness/fallback rules.

**Out of scope:** model internals (44), training (50), validation methodology (47 — forecast skill metrics defined there), scenario projections for planning (89 — multi-year what-ifs are scenarios, not operational forecasts).

## 3. Classification Structure

### 3.1 Forecast domains (`domain`)

| Code | Domain | Subjects | Typical models (44) |
|---|---|---|---|
| FD-LOAD | Demand/load | building, zone, asset, portfolio, network node | SER-02 |
| FD-GEN | Generation | PV/wind asset, site | SER-03 |
| FD-WX | Weather | station, zone (09) | external (37 EIG-DS-00890 class) + downscaling |
| FD-PRC | Price | market (20/21) | SER-08 |
| FD-CI | Carbon intensity | grid, zone (28) | SER-09 |
| FD-FLX | Flexibility availability | asset, building, portfolio (30) | SER-06 |
| FD-OCC | Occupancy | building, zone (03) | SER-05 |
| FD-NET | Network state | feeder, transformer (13) | SER-10 |

### 3.2 Horizon classes (`horizonClass`)

| Code | Class | Lead time | Cadence norm |
|---|---|---|---|
| HZ-NOW | Nowcast | 0–1 h | PT1M–PT15M |
| HZ-ST | Short-term | 1–48 h | PT30M–PT1H (DAM gate alignment, 26) |
| HZ-MT | Medium-term | 2–14 d | P1D |
| HZ-LT | Long-term operational | 2 w–13 m | P1W/P1M (scenario boundary beyond) |

### 3.3 Uncertainty forms (`uncertaintyForm`)

| Code | Form | Representation |
|---|---|---|
| UF-PT | Point | single value (only where 44 obligation permits) |
| UF-QT | Quantiles | declared set, default {p10,p50,p90}; crossing-corrected |
| UF-INT | Interval | central interval at declared coverage (e.g. 80%) |
| UF-DIST | Distribution | parametric/ensemble members |

## 4. Hierarchy

```
Forecast definition (EIG-FC-DEF-*)        ← model@release × subject × horizon × cadence
  └── Forecast run (EIG-FC-*)             ← one issuance, immutable
        └── Horizon steps (lead-time-indexed values + uncertainty)
```

## 5. Field Groups & Fields

### 5.1 Forecast definition (`EIG-FC-DEF-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | fcDefID | string | — | `EIG-FC-DEF-\d{4}` | M | Definition ID |
| 2 | domain | enum | — | §3.1 | M | Domain |
| 3 | subjectRef | ref | — | →02/04/11/13/20 registry ID | M | Forecast subject |
| 4 | targetVariable | ref | — | →38 EIG-VAR-* | M | What is forecast |
| 5 | modelRef | ref | — | →44 EIG-MDL-*@release | M | Issuing release (pinned; release change = definition MINOR) |
| 6 | horizonClass / horizon | enum/duration | — | §3.2 / ISO 8601 | M | Lead structure |
| 7 | stepResolution | duration | — | ISO 8601 (e.g. PT30M) | M | Step grain |
| 8 | cadence | duration/cron | — | issuance schedule | M | When runs are produced |
| 9 | uncertaintyForm | enum | — | §3.3 | M | Obligation (UF-PT requires 44-documented waiver) |
| 10 | quantileSet / coverage | json | — | per form | C | e.g. {p10,p50,p90} |
| 11 | validityRule | json | — | {validUntil: next_issuance \| fixed_duration} | M | Freshness contract (A2) |
| 12 | inputCutoffRule | duration | — | data availability lag honoured (41 A3) | M | Issue-time discipline (A3) |
| 13 | fallbackPolicy | json | — | {onMissing: last_valid≤bound \| persistence \| naive_seasonal, staleBound} | M | Operational degradation (A4) |
| 14 | skillObligation | ref | — | →47 metric+threshold, →51 baselines | M | Ongoing skill bar |
| 15 | consumers | array<ref> | — | →25/27/60/75 endpoints | M | Registered consumers (notification on change) |
| 16 | environment | enum | — | production, sandbox | M | Firewall tag |
| 17 | status / version | enum/semver | — | lifecycle (44-style) | M | Lifecycle |

### 5.2 Forecast run (`EIG-FC-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | forecastID | string | — | `EIG-FC-\d{10}` | M | Run ID |
| 2 | fcDefRef | ref | — | →EIG-FC-DEF-* @version | M | Definition |
| 3 | issuedAt | datetime | — | ISO 8601 UTC | M | Issuance timestamp |
| 4 | modelRelease | string | — | modelID@semver (copied, audit) | M | Exact issuer |
| 5 | inputSnapshotRef | ref | — | as-of feature assembly hash (41 point-in-time) | M | Reproducibility anchor (A3) |
| 6 | steps | array<json> | per target unit | [{targetTime, value \| quantiles \| interval, flags}] | M | The forecast; units per 38 register |
| 7 | confidence | decimal | — | [0,1] | M | Run-level confidence (model + input quality composite, §5.3) |
| 8 | inputQualityNote | json | — | {fitness per 40, missing inputs, fallbacks used} | M | Degradation transparency |
| 9 | validUntil | datetime | — | per validityRule | M | Freshness boundary |
| 10 | supersededBy | ref | — | next run on same definition | C | Set on next issuance |
| 11 | dataClass | enum | — | COMPUTED (fixed) | M | Provenance class |
| 12 | environment | enum | — | inherited from definition | M | Firewall tag |

### 5.3 Confidence composition (normative)

`confidence = w_m · modelSkillRecent (47 rolling) × w_q · inputFitnessScore (40) × w_c · contextValidity (assumption checks, 44 A8)`, weights declared per definition; any factor = 0 ⇒ confidence 0 and run flagged advisory-only.

## 6. Controlled Vocabularies
domain (§3.1), horizonClass (§3.2), uncertaintyForm (§3.3), fallback methods, validity rules. Step flags: {normal, fallback_input, degraded_model, assumption_violated}.

## 7. Applicability Rules

- **A1 — Registered definitions only.** Operational consumers accept runs only from ACTIVE definitions; ad-hoc forecast values in decision/bid paths are an audit failure.
- **A2 — Freshness check is the consumer's duty.** A run past validUntil is stale: 60 decision paths must fall back per fallbackPolicy; 75 dashboards must badge staleness; 25 bids must not cite stale runs.
- **A3 — Issue-time discipline.** A run may use only data available at issuedAt minus inputCutoffRule (no hindsight): inputSnapshotRef proves it (41 point-in-time rule). Backfilled "forecasts" are analyses, not runs, and are never written to this register.
- **A4 — Graceful degradation.** Missing inputs trigger declared fallbacks with flags; silent substitution forbidden (40 A3 spirit). Confidence reflects degradation (§5.3).
- **A5 — Uncertainty obligation.** Definitions feeding bids (25), settlement baselines (27), or automated decisions (60) use UF-QT/UF-INT/UF-DIST; UF-PT requires recorded waiver.
- **A6 — Sandbox firewall.** sandbox runs never feed production consumers (chain 20 A2 …); NEST trials consume sandbox definitions only.
- **A7 — Supersession, never edit.** Runs are immutable; corrections issue a new run with supersededBy links (27 A6 discipline). Skill evaluation (47) always scores the run that consumers actually saw.

## 8. Validation Rules

- **V1:** steps targetTimes strictly increasing at stepResolution; horizon coverage complete or gaps flagged.
- **V2:** quantile monotonicity (p10 ≤ p50 ≤ p90); crossing-corrected with flag.
- **V3:** issuedAt + inputCutoffRule ≥ max input timestamp (A3 machine check on inputSnapshotRef).
- **V4:** validUntil consistent with validityRule; supersededBy set on next issuance.
- **V5:** confidence ∈ [0,1] and recomputable from §5.3 factors.
- **V6:** units of step values match the 38 register for targetVariable.
- **V7:** definition modelRef points to ACTIVE release with horizon ≥ definition horizon.
- **V8:** consumer registry non-empty for ACTIVE definitions (orphan forecasts reviewed).
- **V9:** rolling skill (47) vs skillObligation: breach for >N consecutive evaluations ⇒ definition flagged, 70 model_selection review triggered.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 44 model | issuing releases; horizon/assumption inheritance |
| 41 feature | input snapshots; leakage/point-in-time discipline |
| 40 data_quality | input fitness in confidence; degradation flags |
| 47 validation | skill metrics + rolling evaluation; obligation thresholds |
| 51 model_benchmark | baselines (persistence, seasonal-naive) for skill context |
| 25/26 trade | bid construction consumes fresh runs aligned to gate times |
| 27 settlement | forecast-based baselines (where BL-* methods specify) cite run IDs |
| 52 RD | RD event scheduling uses FD-FLX/FD-LOAD runs; RD evidence itself never rests on forecasts (40 A5 — measured only) |
| 60 decision | freshness/fallback enforcement; confidence gates |
| 09/28 | FD-WX/FD-CI source coupling |

## 10. Benchmarking Requirements
Per definition: rolling skill vs 51 baselines (pinball/CRPS for probabilistic, MAE/MAPE for point), coverage calibration (PIT/coverage tests), published monthly. Definitions persistently beaten by naive baselines are deactivated.

## 11. Dataset Requirements
Run archives are L3 entities (38) packaged into datasets (43) for skill research; issue-time snapshots retained for the audit horizon of their consumers (bids/settlement ⇒ 7 y).

## 12. Feature Requirements
Forecast-aligned features (41 FC-WX/FC-MKT h+k) consume runs from this register, not raw external feeds — single forecasting truth per subject/horizon (one ACTIVE definition per subject×target×horizonClass, V-uniqueness).

## 13. Model Requirements
Forecasting definitions (44, horizon>0) become operational only through an EIG-FC-DEF; model promotion does not itself activate forecasting — definition activation is a separate, consumer-aware step.

## 14. KPI Requirements
Forecast KPIs (57): skill scores, coverage calibration, availability (% issuances on schedule), staleness incidents at consumers.

## 15. Response Derivative Requirements
Forecasts schedule and contextualise RD events (expected load, price, CI windows) but are never RD evidence — delivered-response quantification uses measured data exclusively (40 A5, 52). RD confidence and forecast confidence are distinct quantities and must not be conflated in product gates (22).

## 16. Decision Requirements
60 consumes runs with: freshness check (A2), confidence threshold per decision class, fallback policy execution, and flag awareness (degraded runs ⇒ advisory mode). Automated dispatch requires confidence ≥ declared floor and assumption flags clear.

## 17. Ontology Mapping
Run ↔ SOSA `sosa:Observation`-style with `eig:forecastOf` + lead-time; definition ↔ PROV plan; uncertainty ↔ QUDT quantity + declared coverage; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `ForecastDefinition`, `ForecastRun`; edges `ISSUED_BY`→44 release, `FORECASTS`→subject, `SNAPSHOT`→41 assembly, `CONSUMED_BY`→25/27/60/75, `SUPERSEDES`. Skill time-series attached per definition.

## 19. Digital Twin Mapping
Predictive twins (32 M3+) consume runs as boundary conditions (weather, price, occupancy); twin-generated projections are twin simulation outputs, registered as runs only when issued through an ACTIVE definition under issue-time discipline.

## 20. Governance
Owner: EIG AI/ML Lead; definition activation requires consumer sign-off (the people whose bids/decisions depend on it); skill breaches reviewed at the monthly model board (70).

## 21. Versioning
Definitions semver; runs immutable (A7). Catalogue 1.0.0.

## 22. Example Records

### EIG-FC-DEF-0021 — Aran portfolio HH load, day-ahead
- domain FD-LOAD; subject 30 portfolio EIG-FLX-PF-0007; target EIG-VAR-0102 aggregate; model EIG-MDL-LOADFC-RES-01@2.0.1
- horizon HZ-ST PT48H; step PT30M; cadence daily 09:00 UTC (ahead of DAM gate EIG-TW-DAM-D1-1100, 26); uncertainty UF-QT {p10,p50,p90}
- inputCutoff PT2H; validity until next issuance; fallback persistence ≤ PT26H stale bound; skill obligation pinball ≤ 0.85 × persistence (47/51)
- consumers: bid construction (25), NEST shadow (sandbox mirror definition, separate)

### EIG-FC-0001948821 — run instance
- def EIG-FC-DEF-0021@1.2; issued 2026-06-11T09:00:04Z; model copy LOADFC-RES-01@2.0.1; snapshot sha256:…
- steps: 96 × {targetTime, p10/p50/p90 kWh}; confidence 0.87 (skill 0.92 × fitness 0.98 × context 0.97); flags normal
- validUntil 2026-06-12T09:00Z; supersededBy EIG-FC-0001950112; consumed by trade EIG-TRD-… bid set

### EIG-FC-DEF-0034 — Grid CI 24 h forecast (FD-CI)
- subject EIG-CO2-CI grid series (28); model SER-09; UF-INT 80%; consumers: low-CI window features (41 FC-CB), 60 scheduling; disclosure note: forecast CI never enters compliance reporting (28 — measured factors only)

## 23. Completion Criteria
☑ Definition/run split with immutability + supersession ☑ Issue-time discipline machine-checkable ☑ Uncertainty obligations + confidence composition ☑ Freshness/fallback contract for consumers ☑ Skill obligations wired to 47/51 ☑ RD/settlement boundaries explicit ☑ Examples align to DAM gates and Aran portfolio refs.
