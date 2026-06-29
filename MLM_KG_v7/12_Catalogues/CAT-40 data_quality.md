---
type: catalogue
ontology: EIG-MLM
catalogue_no: 40
catalogue_id: EIG-CAT-040
entity: data_quality
band: data
status: active
tags: [eig-mlm, catalogue, band:data, entity:data_quality]
aliases: ["EIG-CAT-040", "Catalogue 40", "data_quality"]
---

# CAT-40 — Data Quality

> **EIG-CAT-040** · band: **Data, Features & Datasets** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-09 weather_climate]] · [[CAT-11 asset]] · [[CAT-36 device]] · [[CAT-37 data_source]] · [[CAT-38 data]] · [[CAT-39 metadata]]
**Used by (downstream):** [[CAT-41 feature]] · [[CAT-43 dataset]] · [[CAT-44 model]] · [[CAT-47 validation]] · [[CAT-50 training]] · [[CAT-52 response_derivative]] · [[CAT-57 kpi]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-040`
**Entity prefixes:** `EIG-DQ-R-` (quality regimes/profiles), `EIG-DQ-RULE-` (individual rules), `EIG-DQ-A-` (assessments)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Data Management Lead
**Depends on (upstream):** 09 weather_climate, 11 asset, 36 device, 37 data_source, 38 data, 39 metadata
**Used by (downstream):** 41 feature, 43 dataset, 44 model, 47 validation, 50 training, 52 response_derivative, 57 kpi

---

## 1. Definition

The **Data Quality Catalogue** defines the framework's quality machinery: the **dimensions** by which data is judged, the **rules** that test it, the **flags** that mark individual values, the **regimes** (`EIG-DQ-R-*`) that bundle rules + thresholds per data context, and the **assessments** (`EIG-DQ-A-*`) that record outcomes. It is the single authority for the quality-flag vocabulary used throughout 38 (per-value flags) and 39 (MC-QLY metadata), and it sets the fitness thresholds that gate settlement (27), RD computation (52), model training (50), and reporting.

Quality is assessed, never assumed: every L1+ data entity (38) carries flags applied under a named regime, and every flagged intervention (imputation, correction) is visible — no silent repair (38 A5).

## 2. Scope

**In scope:** quality dimensions and their measurement formulas; rule taxonomy (completeness, accuracy, timeliness, consistency, plausibility, outlier, missing-data, validation); flag vocabulary; regime definitions incl. EIG-DQ-R-014; fitness-for-purpose thresholds; imputation policy; outlier methods; quarantine interaction with 37/METIS-128; assessment records.

**Out of scope:** metadata structure carrying scores (39 MC-QLY); model validation (47 — model outputs, not input data); device calibration (36 §metrology); ingestion contracts (37 — they *invoke* regimes defined here).

## 3. Classification Structure

### 3.1 Quality dimensions (`dimension`)

| Code | Dimension | Definition | Measurement (per entity, per window) |
|---|---|---|---|
| DQ-COM | Completeness | Expected values present | present / expected (grain × coverage), explicit NULLs count as missing |
| DQ-ACC | Accuracy | Agreement with truth/reference | 1 − normalised error vs reference (meter cross-check, calibration cert 36) |
| DQ-TIM | Timeliness | Arrival within latency contract | on-time records / total, vs 37 stream latency class |
| DQ-CON | Consistency | Internal & cross-entity coherence | rules passed / rules evaluated (balance checks, unit coherence) |
| DQ-PLS | Plausibility | Physically/behaviourally credible | values within plausibility envelope / total |

All dimension scores ∈ [0,1]; reported per assessment window in 39 MC-QLY.dimensionScores.

### 3.2 Rule classes (`ruleClass`)

| Code | Class | Tests | Example |
|---|---|---|---|
| RC-COM | Completeness | Gaps vs expected calendar grain | ≥ 1 reading per half-hour per MPRN |
| RC-RNG | Range/accuracy | Value within variable validRange (38 §5 variable register) | active_power_w ∈ [0, ratedPower×1.1] (11) |
| RC-TIM | Timeliness | Timestamp lag vs latency class | D+1 06:30 SFTP arrival (EIG-DS-00211) |
| RC-CONS | Consistency | Cross-field/cross-entity algebra | Σ sub-meters ≤ main meter +3%; cumulative monotone (TS-CUM, 38 §temporal) |
| RC-PLAUS | Plausibility | Physics/behaviour envelopes | COP ∈ [1.0, 6.5] for ASHP class EIG-AC-HVC-01-01; ramp ≤ asset maxRamp |
| RC-OUT | Outlier | Statistical deviation | Hampel filter (MAD, k=3) on rolling 48-period window; seasonal-decompose residual z>4 |
| RC-MISS | Missing-data handling | Gap classification + imputation policy | gap ≤2 intervals ⇒ linear-interp permitted, flagged estimated |
| RC-VAL | Structural validation | Schema, keys, duplicates, units | UCUM unit match (38 A2); PK uniqueness; timezone=UTC |

### 3.3 Quality flags (`qualityFlag`) — **single framework vocabulary**

| Flag | Meaning | Set by | Downstream effect |
|---|---|---|---|
| valid | Passed all applicable rules | Assessment | Usable everywhere fitness permits |
| suspect | Failed soft rule (outlier/plausibility) but not disproven | Assessment | Excluded from settlement & RD; usable for exploratory with caveat |
| estimated | Value imputed/corrected; method recorded | Imputation step | dataClass→CALIBRATED for that value (38 A5); never settlement-grade |
| missing | Expected but absent; explicit NULL+flag (no silent fill) | Assessment | Counts against DQ-COM; gaps visible to models |
| quarantined | Stream/segment under 37 quarantine or METIS-128 isolation (35 A4) | Ingestion/security | Blocked from ALL use until released by steward |

Flags are per-value (38), aggregated to statistics in 39 MC-QLY.flagStatistics. No other flag values are permitted framework-wide (V1).

### 3.4 Fitness purposes (`fitnessPurpose`) and default thresholds

| Purpose | DQ-COM | DQ-ACC | DQ-TIM | DQ-CON | DQ-PLS | Extra conditions |
|---|---|---|---|---|---|---|
| settlement | ≥0.995 | ≥0.99 | ≥0.99 | ≥0.99 | ≥0.99 | trust TR-A only (37 A3); zero estimated values in settled quantities; metrology bar (36 A7) |
| rd | ≥0.98 | ≥0.97 | ≥0.95 | ≥0.98 | ≥0.98 | REAL/CALIBRATED only; source timestamps (36 V11); no estimated within event windows |
| training | ≥0.95 | ≥0.95 | — | ≥0.95 | ≥0.95 | estimated permitted if flag exposed as model feature |
| reporting | ≥0.97 | ≥0.95 | ≥0.90 | ≥0.97 | ≥0.97 | SYNTHETIC excluded from disclosure (28 V9) |
| exploratory | ≥0.80 | — | — | — | — | any class, caveated |

Regimes may tighten but never loosen these defaults (A2).

## 4. Hierarchy

```
Regime (EIG-DQ-R-*)                 ← bundle: rules + thresholds + imputation policy
  ├── Rule (EIG-DQ-RULE-*)          ← one test, one ruleClass, machine-evaluable
  └── Assessment (EIG-DQ-A-*)       ← regime applied to one data entity over one window
        └── per-value flags written into 38 entity
```

## 5. Field Groups & Fields

### 5.1 Rule (`EIG-DQ-RULE-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | ruleID | string | — | `EIG-DQ-RULE-\d{4}` | M | Rule ID |
| 2 | ruleClass | enum | — | §3.2 | M | Class |
| 3 | dimension | enum | — | §3.1 | M | Dimension the rule scores |
| 4 | appliesTo | ref/expr | — | variable refs (38 EIG-VAR-*) and/or entity selector | M | Targets |
| 5 | expression | string | — | machine-evaluable predicate (SQL/CEL) | M | The test; no prose-only rules |
| 6 | severity | enum | — | hard, soft | M | hard ⇒ fail flag missing/suspect per class; soft ⇒ suspect |
| 7 | parameters | json | — | — | C | e.g. {k:3, window:48} |
| 8 | referenceSource | ref | — | →36 calibration / →11 ratings / →09 climatology | C | Truth source for DQ-ACC/DQ-PLS |
| 9 | onFailFlag | enum | — | §3.3 (not valid) | M | Flag written on failure |
| 10 | status | enum | — | DRAFT, ACTIVE, RETIRED | M | Lifecycle |

### 5.2 Regime (`EIG-DQ-R-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | regimeID | string | — | `EIG-DQ-R-\d{3}` | M | Regime ID |
| 2 | name | string | — | ≤120 chars | M | Human name |
| 3 | targetContext | expr | — | entity selector (38) / stream selector (37) | M | Where regime applies |
| 4 | ruleSet | array<ref> | — | →EIG-DQ-RULE-* | M | Ordered rules |
| 5 | thresholds | json | — | per-dimension overrides, ≥ §3.4 defaults | M | Fitness thresholds (tighten-only, A2) |
| 6 | imputationPolicy | json | — | {maxGapIntervals, methods[], flag:estimated} | M | Permitted repair; silent repair forbidden |
| 7 | assessmentCadence | enum | — | on_ingest, hourly, daily, monthly | M | When assessed |
| 8 | escalation | json | — | {suspectRate>x ⇒ alert →76 EIG-ALERT-CHN-STD; quarantineTrigger} | M | Operational response |
| 9 | version / status | semver/enum | — | — | M | Lifecycle |

### 5.3 Assessment (`EIG-DQ-A-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | assessmentID | string | — | `EIG-DQ-A-\d{8}` | M | ID |
| 2 | regimeRef | ref | — | →EIG-DQ-R-* @version | M | Regime applied |
| 3 | entityRef | ref | — | →38 EIG-DE-* | M | Assessed entity |
| 4 | window | interval | — | ISO 8601 | M | Assessed period |
| 5 | dimensionScores | json | — | §3.1, ∈[0,1] | M | Outcomes |
| 6 | flagCounts | json | — | per §3.3 flag | M | Statistics |
| 7 | fitnessResult | array<enum> | — | purposes passed (§3.4) | M | Written to 39 MC-QLY.fitForPurpose |
| 8 | ruleFailures | array<json> | — | {ruleID, count, sample} | C | Diagnostics |
| 9 | executedAt / executedBy | datetime/ref | — | UTC / →17 USR-S-* | M | Provenance (39 MC-LIN) |

## 6. Controlled Vocabularies
Binding framework-wide: qualityFlag (§3.3 — exhaustive), dimension (§3.1), ruleClass (§3.2), fitnessPurpose (§3.4), severity, imputation methods {linear_interp, persistence, seasonal_profile, regression_donor} (each writes estimated).

## 7. Applicability Rules

- **A1 — Every L1+ entity under exactly one ACTIVE regime.** Promotion L0→L1 (38) is the act of applying a regime; unassessed data cannot leave L0.
- **A2 — Tighten-only thresholds.** Regimes may exceed §3.4 defaults, never relax them.
- **A3 — No silent repair.** Any value alteration writes flag=estimated and dataClass=CALIBRATED for that value (38 A5); raw L0 untouched (38 A3).
- **A4 — Settlement quantities exclude estimated/suspect.** The four-quantity model (27) computes on valid TR-A values only; shortfall handled by 27 adjustment runs, not by imputation here.
- **A5 — RD evidence bar.** RD inputs (52): flags=valid, dataClass∈{REAL,CALIBRATED-by-calibration not by imputation}, source timestamps; any estimated value inside an event window disqualifies the interval from RD evidence.
- **A6 — Quarantine is absolute.** quarantined values invisible to every consumer incl. exploratory; release only by steward action with audit trail (37 A4; METIS-128 lifecycle 35 A4).
- **A7 — Plausibility envelopes are asset-aware.** RC-PLAUS rules MUST parameterise from asset/device registries (11 ratings, 36 type specs), not global constants.
- **A8 — Sandbox parity.** Sandbox streams (20 A2 chain) are assessed under the same regimes; quality is not a firewall bypass.

## 8. Validation Rules

- **V1:** flag values ∉ §3.3 vocabulary rejected at write time (framework invariant).
- **V2:** regime thresholds ≥ defaults (A2) checked at regime activation.
- **V3:** rule expressions must parse and be side-effect-free; prose-only rules rejected.
- **V4:** assessment dimensionScores recomputable from flagCounts + rule logs (auditable).
- **V5:** imputed share per entity-window ≤ imputationPolicy cap; exceed ⇒ window flagged suspect wholesale.
- **V6:** cumulative variables (TS-CUM) monotone non-decreasing except documented meter resets (36) — violations flag suspect.
- **V7:** every 37 ingestionContract references an ACTIVE regime; dangling refs block stream activation.
- **V8:** fitnessResult derivation matches thresholds (no manual override; exceptions via regime version change only).
- **V9:** Σ flagCounts = expected record count of window (no unaccounted values).
- **V10:** quarantine release events carry steward identity + reason (A6 audit).

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 37 data_source | Ingestion contracts invoke regimes; quarantine machinery shared; trust classes gate settlement fitness |
| 38 data | Flags written per-value; promotion L0→L1 = regime application; temporal semantics drive RC-CONS |
| 39 metadata | MC-QLY carries regime ref + scores + flag stats + fitForPurpose |
| 27 settlement | settlement fitness gate (A4); estimated exclusion; adjustment-run interplay |
| 52 response_derivative | rd fitness gate (A5) — the formal definition of "RD-grade" promised by 38 §15 |
| 50 training / 44 model | training fitness gate; flags exposable as features |
| 36 device / 11 asset | calibration certs and ratings parameterise accuracy/plausibility rules |
| 76 alert | escalation channels (EIG-ALERT-CHN-STD) |
| 09 weather_climate | climatology envelopes for WX variable plausibility |

## 10. Benchmarking Requirements
Quality league per stream/provider (37): monthly dimension scores, suspect rates, imputation rates. Provider TR-class reviews (37 §trust) cite these assessments. Cohort floor n≥10 applies only where building-identifiable.

## 11. Dataset Requirements
A dataset (43) inherits the **minimum** fitnessResult across its member entity-windows; release notes MUST disclose imputation rates and flag statistics per 39 MC-QLY.

## 12. Feature Requirements
Features (41) computed only on flags=valid∪estimated (estimated propagates a feature-level estimated flag); features over suspect/missing values are NULL, never guessed.

## 13. Model Requirements
Training pipelines (50) MUST filter by declared fitness purpose; model cards (44/39 MC-QLY) record the quality envelope of training data; drift monitors compare live assessment scores to training-time scores.

## 14. KPI Requirements
KPIs (57) computed on windows failing reporting fitness are published with a quality caveat code; settlement-derived KPIs (e.g. MET-015 flex_delivery_success, 12) require settlement fitness.

## 15. Response Derivative Requirements
This catalogue owns the **RD-grade definition** (A5 + §3.4 rd row): completeness ≥0.98 over event+baseline windows, REAL/CALIBRATED only, source timestamps, zero estimated in event windows, no quarantined segments. 52 cites EIG-DQ fitness=rd as a hard precondition; RD confidence (≥0.8 product gate, 22) is computed only over RD-grade intervals.

## 16. Decision Requirements
Automated dispatch (60/61) requires live telemetry windows at rd fitness or better; degradation to suspect triggers fallback to advisory/last-known-good per edge degraded-mode rules (35 A3).

## 17. Ontology Mapping
Dimensions ↔ DQV (W3C Data Quality Vocabulary) `dqv:Dimension/Metric/QualityMeasurement`; assessments ↔ `dqv:QualityAnnotation`; flags ↔ SSN/SOSA observation quality pattern; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `QualityRegime`, `QualityRule`, `QualityAssessment`; edges `ASSESSES`→38 entity, `UNDER_REGIME`, `GATES`→{27,50,52} use-purposes. Assessment time-series enables quality-trend queries per provider.

## 19. Digital Twin Mapping
Twin fidelity gates (32: F3 CVRMSE ≤15%/NMBE ±5%) consume DQ-ACC assessments of twin input streams; a twin whose inputs fall below rd fitness auto-degrades one fidelity class until restored.

## 20. Governance
Owner: EIG Data Management Lead; rule changes reviewed by domain owner of affected catalogue (e.g. RC-PLAUS asset envelopes with 11 owner). Regime version bumps notify all dependent streams (37) and entities (38) within 24 h. QRC-style operational cheat sheets for field technicians reference flag meanings verbatim from §3.3.

## 21. Versioning
Regimes/rules semver; assessments immutable (append-only; corrections by new assessment superseding — 27 A6 discipline). Catalogue 1.0.0.

## 22. Example Records

### EIG-DQ-R-014 — Residential half-hourly electricity regime (the regime cited by 37/38/39)
- targetContext: entities matching EIG-DE-*-ELEC-HH-* (grain building×PT30M, VD-EL)
- ruleSet: RULE-0008 RC-COM (≥48 values/day/MPRN), RULE-0011 RC-RNG (kWh ∈ [0, 30] per HH — domestic envelope), RULE-0015 RC-CONS (TS-CUM register monotone; interval = register diff ±0.5%), RULE-0019 RC-TIM (D+1 06:30 SLA), RULE-0023 RC-OUT (Hampel k=3, window 48), RULE-0027 RC-PLAUS (night-baseload > 0 unless vacancy flag, 03), RULE-0031 RC-VAL (UTC, PK [mprn_pseudonym, ts], UCUM kWh)
- thresholds: settlement defaults (§3.4) — this regime is settlement-grade
- imputationPolicy: maxGapIntervals 2; method linear_interp; flag estimated; imputed share cap 1.5%/month
- escalation: suspectRate>2% daily ⇒ EIG-ALERT-CHN-STD; provider repeat-breach ⇒ 37 trust review
- assessmentCadence: on_ingest + daily roll-up

### EIG-DQ-R-021 — Asset telemetry (heat pump) regime
- targetContext: EIG-DE-ODEON-ASSET-TELEM-* (VD-TH/VD-EL, PT1M)
- key rules: COP plausibility [1.0,6.5] vs EIG-AC-HVC-01-01; power ≤ ratedPower×1.1 (EIG-AST-004211); gateway timestampSource required (36 V11); ramp ≤ maxRamp
- thresholds: rd row §3.4; estimated banned inside dispatch event windows (A5)
- escalation: quarantine on METIS-128 signal (35 A4)

### EIG-DQ-A-00045112 — assessment instance
- regime EIG-DQ-R-014@1.0.0; entity EIG-DE-ARAN-ELEC-HH-L1; window 2026-05
- scores {COM .991, ACC .97, TIM .99, CON .98, PLS .99}; flags {valid 1 312 044, suspect 2 113, estimated 9 871, missing 1 904, quarantined 0}
- fitnessResult [settlement, rd, training, reporting] → written to EIG-MET-000412 MC-QLY

## 23. Completion Criteria
☑ Five dimensions with formulas ☑ Eight rule classes machine-evaluable ☑ Flag vocabulary fixed framework-wide ☑ Fitness thresholds incl. settlement/RD gates ☑ EIG-DQ-R-014 fully defined (resolves 37/38/39 refs) ☑ Imputation/no-silent-repair policy ☑ DQV mapping ☑ Quarantine integration with 37/METIS-128.
