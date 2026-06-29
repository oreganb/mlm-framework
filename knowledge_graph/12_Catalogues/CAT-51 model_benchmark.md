---
type: catalogue
ontology: EIG-MLM
catalogue_no: 51
catalogue_id: EIG-CAT-051
entity: model_benchmark
band: models
status: active
tags: [eig-mlm, catalogue, band:models, entity:model_benchmark]
aliases: ["EIG-CAT-051", "Catalogue 51", "model_benchmark"]
---

# CAT-51 — Model Benchmark

> **EIG-CAT-051** · band: **Models & Learning** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-43 dataset]] · [[CAT-44 model]] · [[CAT-47 validation]] · [[CAT-48 hyperparameter]] · [[CAT-50 training]]
**Used by (downstream):** [[CAT-44 model]] · [[CAT-45 algorithm]] · [[CAT-46 forecast]] · [[CAT-70 model_selection]] · [[CAT-77 report]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-051`
**Entity prefixes:** `EIG-MBM-SUITE-` (benchmark suites), `EIG-MBM-BL-` (baselines), `EIG-MBM-` (benchmark results)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 43 dataset, 44 model, 47 validation, 48 hyperparameter, 50 training
**Used by (downstream):** 44 promotion/regression gates, 45 expectation envelopes, 46 skill context, 70 model_selection, 77 report

---

## 1. Definition

The **Model Benchmark Catalogue** governs comparative evaluation: the **suites** (`EIG-MBM-SUITE-*`) — frozen benchmark datasets + task definitions + metrics on which competing models are scored identically; the **baselines** (`EIG-MBM-BL-*`) — the naive and reference methods every contender must beat (persistence, seasonal-naive, climatology, era-default physics, prior release); and the **results** (`EIG-MBM-*`) — immutable comparison records with per-model scores, statistical significance, and rankings. Purpose: **model comparison** — fair, frozen, repeatable answers to "which model is best for this task?", and **regression protection** — no re-release silently performs worse than its predecessor (A4).

Validation (47) asks "is this model fit for its claim?"; benchmarking asks "is it the best available, and better than naive?". Both gates apply at promotion.

## 2. Scope

**In scope:** suite construction (frozen data, task spec, metric set); baseline taxonomy and definitions; fair-comparison rules (identical data, tuning-budget parity, no test contamination); result records with significance testing; leaderboards per task; regression gates; champion/challenger mechanics (70 interface).

**Out of scope:** validation verdicts (47 — a benchmark win does not waive validation obligations), public/external benchmark participation (governed case-by-case), hyperparameter search (48 — budgets referenced for parity).

## 3. Classification Structure

### 3.1 Baseline classes (`EIG-MBM-BL-*`)

| Code | Baseline | Definition | Applies to |
|---|---|---|---|
| BL-PERS | Persistence | ŷ(t+h) = y(t) (last observed) | nowcast/short-term forecasting |
| BL-SNAIVE | Seasonal naive | ŷ(t+h) = y(t+h − seasonal period) (e.g. same HH last week) | load/price forecasting |
| BL-CLIM | Climatology | long-run mean/quantiles for the calendar slot | weather-coupled targets |
| BL-LIN | Linear reference | regularised linear model on the same feature set | "is the complexity earning its keep?" |
| BL-PHYS | Physics/era-default | uncalibrated L1 model with registry defaults | calibration/ML value-add quantification |
| BL-PRIOR | Prior release | the currently ACTIVE release of the same definition | regression gate (A4) |

Note: settlement baselines (27 BL-*) are a different concept — counterfactual consumption baselines; namespace kept distinct (EIG-MBM-BL- vs 27's BL-).

### 3.2 Suite task types (`taskType`)

| Code | Task | Metric core (47 §3.2) |
|---|---|---|
| TT-FCPT | Point forecasting | MT-MAE, MT-RMSE, MT-SKILL vs BL-PERS/BL-SNAIVE |
| TT-FCPR | Probabilistic forecasting | MT-PINB, MT-CRPS, MT-COV |
| TT-EST | Estimation/nowcast | MT-MAE, MT-RMSE |
| TT-CLS | Classification/anomaly | MT-F1, MT-AUC |
| TT-CALIB | Calibration quality | parameter recovery error (on reference cases), posterior coverage |
| TT-RD | RD estimation | MT-RDERR distribution on frozen event sets |
| TT-CTRL | Control policy | episodic reward, MT-ENV, constraint violations (sandbox/sim, 66) |

### 3.3 Comparison verdict semantics

Ranking by primary metric with: paired significance test (Diebold-Mariano for forecasts, paired bootstrap otherwise) at declared α; ties broken by secondary metrics then compute cost (45 computeClass). "Best" claims without significance are reported as "indistinguishable leaders" (A5 honesty).

## 4. Hierarchy

```
Suite (EIG-MBM-SUITE-*)            ← frozen data + task + metrics + baselines
  ├── Baselines (EIG-MBM-BL-* bindings, parameterised per suite)
  └── Result record (EIG-MBM-*)    ← one comparison event, immutable
        └── Per-contender scores + significance + ranking
```

## 5. Field Groups & Fields

### 5.1 Suite (`EIG-MBM-SUITE-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | suiteID | string | — | `EIG-MBM-SUITE-\d{4}` | M | ID |
| 2 | taskType | enum | — | §3.2 | M | Task |
| 3 | targetSeries | array<ref> | — | →44 SER-* applicability | M | Which model families compete |
| 4 | benchmarkDatasets | array<ref> | — | →43 EIG-DSET-*@version (frozen evaluation slices) | M | Identical for all contenders (A1) |
| 5 | evaluationProtocol | json | — | windows, issue-time rules (46 A3 for forecasts), event sets (TT-RD) | M | Frozen procedure |
| 6 | metrics | array<json> | — | [{metric (47 §3.2), primary\|secondary}] | M | Scoring set |
| 7 | baselines | array<ref> | — | →EIG-MBM-BL-* parameterised | M | Mandatory floor (≥1 naive + BL-PRIOR where exists) |
| 8 | significanceTest | json | — | {test, α, pairing} | M | §3.3 |
| 9 | fairnessRules | json | — | {tuningBudgetCap (48), featureSetPolicy: identical\|own-contract-declared, dataAccessIdentical: TRUE} | M | Parity (A2) |
| 10 | refreshPolicy | json | — | dataset extension cadence; suite version bump on data change (V2) | M | Evolution discipline |
| 11 | version / status | semver/enum | — | — | M | Lifecycle |

### 5.2 Result record (`EIG-MBM-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | benchmarkID | string | `EIG-MBM-\d{6}` | M | Record ID |
| 2 | suiteRef | ref | →EIG-MBM-SUITE-* @version | M | Suite |
| 3 | contenders | array<json> | [{releaseRef (44), trainingRunRef (50), tuningBudgetUsed (48), featureSet (41)}] | M | Who competed, with parity evidence |
| 4 | baselineScores | array<json> | per baseline per metric | M | The floor |
| 5 | scores | array<json> | per contender per metric (+ CIs) | M | Full results, no omissions (A5) |
| 6 | significance | json | pairwise test results at declared α | M | §3.3 |
| 7 | ranking | array<json> | ordered, with "indistinguishable" groupings | M | Outcome |
| 8 | regressionCheck | json | challenger vs BL-PRIOR: Δ per metric, within-tolerance verdict | C | A4 gate evidence |
| 9 | executedAt / executionRef | datetime/ref | UTC / reproducible run log (50-style) | M | Provenance |
| 10 | environment | enum | production-data, sandbox | M | Evidence class (sandbox suites for NEST/RL only) |
| 11 | supersedes | ref | prior record on suite refresh | C | Chain |

## 6. Controlled Vocabularies
Baseline classes (§3.1), task types (§3.2), significance tests, fairness rule fields. Leaderboard publication states: internal, consortium, public (public requires 43 open-release floor on the suite data).

## 7. Applicability Rules

- **A1 — Identical evidence.** All contenders score on the same frozen dataset versions, same windows, same issue-time rules; any data advantage voids the comparison.
- **A2 — Tuning parity.** Contender tuning budgets ≤ suite cap (48 campaign evidence attached); a heavily-tuned challenger vs default-config champion is not a fair result.
- **A3 — Beat-naive floor.** A model that does not significantly beat the mandatory naive baseline(s) on the primary metric is not promotable for that task regardless of other virtues (46 §10 deactivation rule operationalised here).
- **A4 — Regression gate.** Re-releases must score within declared tolerance of BL-PRIOR on every primary metric of every suite the prior release passed; regression beyond tolerance blocks promotion absent an approved trade-off record (e.g. −1% accuracy for edge deployability, documented).
- **A5 — Full publication.** All contenders' all-metric scores published internally — no dropping embarrassing rows; "indistinguishable" honesty where significance fails.
- **A6 — Suites are frozen.** Data refresh ⇒ suite MINOR/MAJOR version; results compare within suite versions only; cross-version trend analysis is labelled as such.
- **A7 — Champion/challenger.** 70 model_selection consumes rankings: champions hold task slots; challengers earn shadow deployment on benchmark wins, promotion on validation (47) + sustained shadow performance.
- **A8 — TT-RD discipline.** RD benchmark event sets: measured events at rd fitness (40), syntheticLineage=FALSE — same evidence bar as 47 VM-RDV; TT-CTRL suites run in sandbox/simulation (50 A7).

## 8. Validation Rules

- **V1:** suite datasets RELEASED + frozen (43 immutability); evaluation slices disjoint from all contenders' training data (split-hash checks vs 50 records).
- **V2:** suite data change without version bump rejected.
- **V3:** result completeness: every contender × metric populated; missing ⇒ record invalid.
- **V4:** significance recomputable from logged per-window scores.
- **V5:** regressionCheck present whenever BL-PRIOR exists.
- **V6:** parity evidence: tuning budgets within cap; identical data access attested.
- **V7:** records immutable; supersession on suite refresh.
- **V8:** rankings consistent with scores + significance (mechanical recompute).
- **V9:** public leaderboards: suite data meets 43 A8 open floor; SYNTHETIC suite data badged (42).

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 44 model | promotion + regression gates; benchmarkResults on releases |
| 47 validation | metric register shared; benchmark ≠ validation (both required) |
| 43 dataset | frozen suite corpora; disjointness vs training |
| 48 hyperparameter | tuning-budget parity evidence |
| 50 training | contender provenance; execution reproducibility pattern |
| 45 algorithm | expectation envelopes updated from results |
| 46 forecast | skill-context baselines; deactivation rule evidence |
| 70 model_selection | champion/challenger consumption (A7) |
| 66 RL | TT-CTRL suites in simulation |
| 77 report | leaderboard publication with honesty rules |

## 10. Benchmarking Requirements
(Self) — meta-KPIs: suite coverage (every ACTIVE forecasting/estimation definition has ≥1 suite), result freshness, regression-gate catch rate, challenger turnover (a leaderboard nobody can climb is broken).

## 11. Dataset Requirements
Suite corpora are RELEASED, immutable, documented (39 CL-FULL); refresh extensions ship as new dataset versions feeding new suite versions (A6).

## 12. Feature Requirements
featureSetPolicy declared per suite: "identical" isolates algorithm value; "own-contract-declared" compares whole modelling approaches — the policy choice is part of the question being asked and is recorded.

## 13. Model Requirements
Per 44 §10: ACTIVE forecasting/estimation releases hold current results vs declared baselines; promotion packets include the suite result alongside the 47 record.

## 14. KPI Requirements
Leaderboard-derived KPIs (57): skill-vs-naive margins per task, champion stability, regression incidents caught pre-promotion.

## 15. Response Derivative Requirements
TT-RD suites (frozen measured event sets) rank RD estimators (SER-07); the champion estimator per context class is what 52 records cite; estimator changes propagate RD re-estimation per 52 rules.

## 16. Decision Requirements
70/60 consume rankings under A7; automated-coupling champions additionally require unexpired VM-CL (47) — benchmark wins never bypass safety validation.

## 17. Ontology Mapping
Suite ↔ MLS `mls:Task` + `mls:EvaluationSpecification`; result ↔ `mls:Study` with per-model `mls:ModelEvaluation`; baselines ↔ EIG extension; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `BenchmarkSuite`, `Baseline`, `BenchmarkResult`; edges `SCORED`→44 releases {metric values}, `ON_SUITE`, `BEAT`/`INDISTINGUISHABLE` significance edges, `CHAMPION_OF` {task} maintained by 70. Leaderboard queries are KG-native.

## 19. Digital Twin Mapping
Twin engine comparisons (alternative SER-01 implementations) run TT-EST/TT-CALIB suites on reference cases (47 VM-PHY data); twin template model requirements (33) cite champion releases per task.

## 20. Governance
Owner: AI/ML Lead; suite design reviewed for fairness (A1/A2) before activation; trade-off approvals (A4 exceptions) signed at the model board; public publication approvals include 43/42 floor checks.

## 21. Versioning
Suites semver (data refresh ⇒ MINOR minimum); baselines versioned with suites; results immutable. Catalogue 1.0.0.

## 22. Example Records

### EIG-MBM-SUITE-0003 — Residential HH load forecasting (SER-02)
- task TT-FCPR; data EIG-DSET-ARAN-ELEC-HH@4.1 evaluation slice (2026-01/05, frozen; disjoint from all training); protocol: rolling-origin daily issuance, issue-time discipline, 48 steps
- metrics: MT-PINB primary, MT-CRPS, MT-COV80 secondary; baselines BL-PERS, BL-SNAIVE (weekly), BL-LIN (same features), BL-PRIOR (LOADFC-RES-01@1.x)
- significance: Diebold-Mariano α=0.05; fairness: tuning cap 150 trials, featureSetPolicy own-contract-declared

### EIG-MBM-000412 — result record
- suite 0003@1.2; contenders: LOADFC-RES-01@2.0.1 (GBM), challenger LSTM variant, BL set
- scores (pinball, lower better): BL-PERS 0.0612 | BL-SNAIVE 0.0488 | BL-LIN 0.0431 | @1.4 prior 0.0398 | **@2.0.1 0.0371** | LSTM 0.0379
- significance: @2.0.1 vs LSTM not significant (p=0.21) — "indistinguishable leaders"; both significantly beat all baselines ✓ (A3)
- regressionCheck vs prior: −6.8% pinball (improvement) ✓; ranking published in full; champion retained @2.0.1 (compute tiebreak: CC-2 vs CC-3, edge-relevant)

### EIG-MBM-SUITE-0011 — Thermal-shed RD estimation (TT-RD)
- frozen event set: 41 Aran winter events (rd fitness, measured); metric MT-RDERR p50/p90; baselines BL-PHYS (uncalibrated R2C2 prediction); champion estimator feeds EIG-RD-SPEC-THERM-SHED citations (52)

## 23. Completion Criteria
☑ Suite/baseline/result model with frozen identical evidence ☑ Mandatory naive floor + regression gate vs prior release ☑ Significance honesty ("indistinguishable") ☑ Tuning parity rules ☑ Champion/challenger interface to 70 ☑ TT-RD/TT-CTRL evidence bars ☑ Namespace distinction vs 27 settlement baselines ☑ Worked SER-02 comparison closing the LOADFC chain.
