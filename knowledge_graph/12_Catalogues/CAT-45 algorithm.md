---
type: catalogue
ontology: EIG-MLM
catalogue_no: 45
catalogue_id: EIG-CAT-045
entity: algorithm
band: models
status: active
tags: [eig-mlm, catalogue, band:models, entity:algorithm]
aliases: ["EIG-CAT-045", "Catalogue 45", "algorithm"]
---

# CAT-45 — Algorithm

> **EIG-CAT-045** · band: **Models & Learning** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-44 model]]
**Used by (downstream):** [[CAT-44 model]] · [[CAT-46 forecast]] · [[CAT-48 hyperparameter]] · [[CAT-49 explainability]] · [[CAT-50 training]] · [[CAT-66 reinforcement_learning]] · [[CAT-70 model_selection]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-045`
**Entity prefix:** `EIG-ALG-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 44 model (consumer context)
**Used by (downstream):** 44 model, 46 forecast, 48 hyperparameter, 49 explainability, 50 training, 66 reinforcement_learning, 70 model_selection

---

## 1. Definition

The **Algorithm Catalogue** is the reference register of algorithms and techniques available to MLM model definitions (44). An algorithm record (`EIG-ALG-*`) describes a *method* — its category, valid use cases, input/output characteristics, strengths, weaknesses, explainability class, hyperparameter surface (48), data requirements, and compute cost — independent of any trained instance. Its purpose is **algorithm selection**: given a modelling problem (series, output form, data volume, latency, explainability duty), the register supports a defensible, recorded choice. Every 44 definition cites ≥1 EIG-ALG record and records the selection rationale (A1).

## 2. Scope

**In scope:** algorithm taxonomy across all four MLM layers; per-algorithm profiles (assumptions, data needs, failure modes); selection guidance matrix; explainability and compute classifications; hyperparameter surface pointers; reference implementations.

**Out of scope:** trained models (44 releases), hyperparameter *values* (48), training mechanics (50), benchmark *results* of specific releases (51 — though algorithm-level expectations are noted here).

## 3. Classification Structure

### 3.1 Algorithm categories (`category`)

| Code | Category | Layer affinity | Members (register) |
|---|---|---|---|
| AC-PHY | Physics/engineering | L1 | RC thermal networks (R1C1/R2C2/R3C3), EN ISO 52016 hourly, EN ISO 6946/13370 calculators, AC/DC power flow, tariff/settlement arithmetic |
| AC-STA | Statistical regression | L2 | OLS/GLS, GLM, GAM, quantile regression, regularised (ridge/lasso/elastic net) |
| AC-TS | Time-series | L2 | ARIMA/SARIMAX, exponential smoothing (ETS/Holt-Winters), state-space/Kalman, Prophet-class decomposition |
| AC-PRB | Probabilistic/Bayesian | L2 | Bayesian regression, Gaussian processes, copulas, Monte Carlo, bootstrap |
| AC-TREE | Tree ensembles | L3 | CART, random forest, gradient boosting (XGBoost/LightGBM class — EIG-ALG-GBM-01) |
| AC-NN | Neural networks | L3 | MLP, LSTM/GRU, TCN, CNN-1D, transformer/attention, autoencoder |
| AC-CLU | Clustering/unsupervised | L3 | k-means, hierarchical, DBSCAN, GMM, SOM (archetype clustering 07) |
| AC-ANO | Anomaly/diagnostics | L2/L3 | Hampel/MAD, isolation forest, one-class SVM, residual-based detection, NILM (HMM/deep) |
| AC-OPT | Optimisation | L1/L3 | LP/MILP, QP, genetic/evolutionary, simulated annealing, model-predictive control formulations |
| AC-RL | Reinforcement learning | L4 | tabular Q-learning, DQN, double/duelling DQN, policy gradient (REINFORCE/PPO-class), actor-critic, contextual bandits |
| AC-CAL | Calibration/estimation | L1/L2 | least-squares parameter fitting, Bayesian calibration, Kalman/EnKF state estimation, grey-box system identification |
| AC-XAI | Explanation methods | meta | SHAP, LIME, permutation importance, PDP/ICE, counterfactuals (registered here, applied via 49) |

### 3.2 Explainability classes (`explainabilityClass`) — consumed by 44 §5.1.21 / 49

| Code | Class | Meaning |
|---|---|---|
| XC-INTR | Intrinsic | Self-explaining (physics, linear models, shallow trees) |
| XC-POST | Post-hoc required | Opaque; needs AC-XAI companion (deep NN, large ensembles) |
| XC-HYB | Hybrid | Partially interpretable (GAM, attention weights with caveats) |

### 3.3 Compute cost classes (`computeClass`)

| Code | Train | Infer | Edge-feasible (35 CLS-E1) |
|---|---|---|---|
| CC-1 | trivial | trivial | yes |
| CC-2 | minutes | ms | yes |
| CC-3 | hours | ms–s | inference only |
| CC-4 | hours–days, GPU | s | no (platform only) |

## 4. Hierarchy

```
Category (AC-*)
  └── Algorithm (EIG-ALG-*)              ← method record
        └── Variant (parameterised flavour, e.g. quantile objective)
```

Algorithms are referenced by 44 definitions; variants are declared inline on the record.

## 5. Field Groups & Fields

### 5.1 Algorithm record (`EIG-ALG-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | algorithmID | string | — | `EIG-ALG-[A-Z0-9-]{2,20}` | M | ID (e.g. EIG-ALG-GBM-01) |
| 2 | name | string | — | ≤120 chars | M | Canonical name |
| 3 | category | enum | — | §3.1 | M | Category |
| 4 | layerAffinity | array<enum> | — | MLM-L1..L4 (44 §3.1) | M | Where it belongs in the stack |
| 5 | description | string | — | ≤2000 chars | M | Method summary with canonical citation |
| 6 | useCases | array<string> | — | mapped to 44 series (SER-01..13) | M | Valid problem classes |
| 7 | inputCharacteristics | json | — | {dataVolumeMin, featureTypes, missingTolerance, stationarityAssumed} | M | What it needs |
| 8 | outputForms | array<enum> | — | 44 outputForm vocab | M | Shapes it can produce |
| 9 | strengths | array<string> | — | — | M | Enumerated, honest |
| 10 | weaknesses | array<string> | — | — | M | Enumerated, honest (incl. failure modes) |
| 11 | assumptions | array<string> | — | — | M | Statistical/physical assumptions |
| 12 | explainabilityClass | enum | — | §3.2 | M | Transparency class |
| 13 | computeClass | json | — | {train: CC-*, infer: CC-*} | M | Cost profile |
| 14 | hyperparameterSurface | ref | — | →48 surface definition | M | Tunable parameters + ranges (values live in 48) |
| 15 | uncertaintyNative | bool | — | — | M | Produces calibrated uncertainty without wrapping |
| 16 | referenceImplementations | array<json> | — | {library, version pin, licence} | M | Approved implementations |
| 17 | knownBaselines | array<ref> | — | →51 baseline expectations | C | Typical performance envelope per use case |
| 18 | status / version | enum/semver | — | ACTIVE, DEPRECATED, RETIRED | M | Lifecycle |

### 5.2 Selection guidance matrix (normative rows, machine-filterable)

| Dimension | Filter logic |
|---|---|
| series (44) | useCases ⊇ target series |
| outputForm | outputForms ∋ required form (quantiles ⇒ AC-TREE quantile variant, AC-PRB, AC-NN quantile heads) |
| explainability duty | 44 explainabilityClass=intrinsic ⇒ XC-INTR only; post_hoc_required permits XC-POST + AC-XAI companion |
| latency/deployment | EX-EDGE ⇒ infer ∈ {CC-1, CC-2} |
| data volume | inputCharacteristics.dataVolumeMin ≤ available rows (43 corpus) |
| uncertainty need | probabilistic obligations (46) ⇒ uncertaintyNative or declared wrapper (conformal/bootstrap) |
| RD evidence path | SER-07 ⇒ category ∈ {AC-STA, AC-PRB, AC-CAL, AC-TREE} with uncertainty; deep opaque methods need 47 extra scrutiny |

## 6. Controlled Vocabularies
category (§3.1 — closed; additions MINOR), explainabilityClass, computeClass, layerAffinity. Reference implementation register: pinned library versions are governance-controlled (supply-chain discipline, 35 A6 spirit).

## 7. Applicability Rules

- **A1 — Recorded selection.** Every 44 definition records: chosen algorithmID(s), ≥1 considered alternative, and rationale against the §5.2 dimensions. "Default to GBM" without rationale is a 47 audit finding.
- **A2 — Implementation pinning.** Production releases (44) use referenceImplementations at pinned versions only; unpinned library drift invalidates reproducibility (50 V-chain).
- **A3 — Explainability pairing.** XC-POST algorithms deployed with decisionCoupling≠none MUST have an AC-XAI companion configured (49) before promotion.
- **A4 — Edge fit.** EX-EDGE model definitions select algorithms with infer ∈ {CC-1, CC-2}; training may remain platform-side (train-platform/infer-edge split is the norm, 35).
- **A5 — Uncertainty honesty.** Where calibrated intervals are an obligation (46/47), non-native algorithms declare the wrapping method; intervals from unvalidated wrappers are not publishable.
- **A6 — L4 containment.** AC-RL algorithms train in sandbox or simulation (32/42) only; live exploration on production actuation is forbidden — policies promote via 66/47 gates with frozen exploitation.

## 8. Validation Rules

- **V1:** algorithmID unique; category/layerAffinity consistent with §3.1 affinity column.
- **V2:** useCases reference valid 44 series codes.
- **V3:** hyperparameterSurface resolves in 48; every tunable named with type/range.
- **V4:** referenceImplementations carry exact version pins + licence.
- **V5:** strengths AND weaknesses non-empty (no marketing records).
- **V6:** DEPRECATED algorithms: no new 44 definitions may cite them; existing definitions flagged on next release.
- **V7:** XC-POST + automated coupling without 49 companion ⇒ promotion blocked (A3 enforcement).

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 44 model | definitions cite algorithms + rationale; layer/series compatibility |
| 48 hyperparameter | surfaces defined per algorithm; values per release |
| 49 explainability | XAI methods registered here as AC-XAI; pairing duty A3 |
| 50 training | implementations + pins consumed by runs |
| 51 model_benchmark | baseline expectations per algorithm/use-case |
| 66 RL | AC-RL methods; sandbox-only exploration (A6) |
| 70 model_selection | §5.2 matrix is its filter logic |

## 10. Benchmarking Requirements
Algorithm-level expectation envelopes (typical skill vs naive baselines per use case) maintained from 51 evidence; selection guidance updated when evidence contradicts received wisdom.

## 11. Dataset Requirements
inputCharacteristics validated against candidate corpora (43) at selection time; insufficient volume ⇒ algorithm filtered out, not "made to work".

## 12. Feature Requirements
Feature-type compatibility (categorical handling, missing tolerance) checked against the 41 input contract before training (50 pre-flight).

## 13. Model Requirements
Per 44 A1/A2: registered algorithms only; promotion evidence (47) interpreted against the algorithm's known weaknesses (e.g. extrapolation failure for trees noted in review).

## 14. KPI Requirements
Selection-quality KPI: % definitions with recorded rationale; % deployments matching §5.2 filters without exception.

## 15. Response Derivative Requirements
RD estimators (SER-07) favour methods with native uncertainty and inspectable structure; the §5.2 RD row is binding — opaque estimators require 47-documented justification and enhanced validation before any RD record cites them.

## 16. Decision Requirements
Decision paths (60) inherit algorithm caveats: tree extrapolation limits, RL policy validity domains, MILP infeasibility handling — declared weaknesses become runtime guards.

## 17. Ontology Mapping
Algorithm ↔ MLS `mls:Algorithm`/`mls:Implementation`; categories ↔ MLM ontology technique classes; citations ↔ dct:references; alignment owned by 79.

## 18. Knowledge Graph Mapping
Node `Algorithm`; edges `USED_BY`→44 definitions, `HAS_SURFACE`→48, `EXPLAINED_BY`→49 methods, `IMPLEMENTED_IN` {library, version}. Selection queries ("edge-feasible quantile methods for SER-02") run on these properties.

## 19. Digital Twin Mapping
Twin model requirements (33) resolve to algorithm constraints transitively (e.g. F3 calibration requires AC-CAL availability); twin simulation engines are AC-PHY records with pinned implementations.

## 20. Governance
Owner: EIG AI/ML Lead; new algorithm admission requires a profile review (strengths/weaknesses honesty check) and reference-implementation security review. Annual register review retires unused methods.

## 21. Versioning
Records semver; implementation pin updates are PATCH with reproducibility note. Catalogue 1.0.0.

## 22. Example Records

### EIG-ALG-GBM-01 — Gradient-boosted trees (resolves 44 ref)
- category AC-TREE; layers [L3]; useCases [SER-02, SER-04, SER-05, SER-06, SER-11]; outputForms [point, quantiles (quantile objective variant), classification]
- inputs: ≥5k rows preferred; mixed feature types; missing-tolerant; non-stationary OK with retraining cadence
- strengths: strong tabular accuracy, fast CC-2 inference (edge ✓), monotonic constraints available, native feature importance
- weaknesses: no extrapolation beyond training range (runtime guard required), quantile crossing possible (post-sort fix declared), drift-sensitive
- explainability XC-POST (SHAP companion standard); compute {train CC-2/3, infer CC-2}; uncertaintyNative FALSE (quantile variant or conformal wrapper)
- implementations: LightGBM 4.x pin, licence MIT; surface →48 EIG-HYP-SUR-GBM-01

### EIG-ALG-R2C2-01 — Two-node RC thermal network
- category AC-PHY; layers [L1]; useCases [SER-01, SER-06, SER-07]; XC-INTR; compute {train n/a (calibrated via AC-CAL), infer CC-1, edge ✓}
- assumptions: lumped capacitance validity, linear heat transfer regime; weaknesses: multi-zone coupling crude, solar gain simplification
- consumers: EIG-MDL-SPEC-THERM-R2C2 (44)

### EIG-ALG-QL-01 — Tabular Q-learning
- category AC-RL; layers [L4]; useCases [SER-13]; sandbox-only exploration (A6); XC-HYB (Q-table inspectable); compute {train CC-2 sim, infer CC-1, edge ✓}
- weaknesses: state-space explosion, discretisation sensitivity; promotion via 66 policy gates

## 23. Completion Criteria
☑ Twelve categories spanning all four layers ☑ Honest strengths/weaknesses per record ☑ Machine-filterable selection matrix (70 logic) ☑ Explainability pairing + edge/uncertainty/RD rules ☑ Implementation pinning ☑ EIG-ALG-GBM-01 resolves 44 ref.
