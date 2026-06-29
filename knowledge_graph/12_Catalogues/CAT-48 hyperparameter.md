---
type: catalogue
ontology: EIG-MLM
catalogue_no: 48
catalogue_id: EIG-CAT-048
entity: hyperparameter
band: models
status: active
tags: [eig-mlm, catalogue, band:models, entity:hyperparameter]
aliases: ["EIG-CAT-048", "Catalogue 48", "hyperparameter"]
---

# CAT-48 — Hyperparameter

> **EIG-CAT-048** · band: **Models & Learning** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-43 dataset]] · [[CAT-44 model]] · [[CAT-45 algorithm]]
**Used by (downstream):** [[CAT-44 model]] · [[CAT-47 validation]] · [[CAT-50 training]] · [[CAT-51 model_benchmark]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-048`
**Entity prefixes:** `EIG-HYP-SUR-` (hyperparameter surfaces), `EIG-HYP-` (frozen sets), `EIG-HYP-TUN-` (tuning campaigns)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 43 dataset, 44 model, 45 algorithm
**Used by (downstream):** 44 releases, 47 validation, 50 training, 51 model_benchmark

---

## 1. Definition

The **Hyperparameter Catalogue** governs model configuration: the **surfaces** (`EIG-HYP-SUR-*`) that define, per algorithm (45), every tunable parameter with type, range, default, and performance sensitivity; the **frozen sets** (`EIG-HYP-*`) — exact value assignments pinned to a model release (44); and the **tuning campaigns** (`EIG-HYP-TUN-*`) — recorded searches that produced them. Purpose: **reproducible model training**. Given a release, its frozen set plus training run (50) plus dataset versions (43) fully determines the artefact; given a chosen value, the campaign record explains why.

## 2. Scope

**In scope:** surface definitions (parameters, types, ranges, defaults, sensitivity class); frozen-set records; tuning method taxonomy and campaign logging; search-space discipline; leakage-safe tuning rules; performance-impact recording; deterministic-layer parameter handling (L1 physical parameters are *calibrated*, not tuned — boundary defined).

**Out of scope:** learned model parameters/weights (release artefact, 44), physical parameter calibration (AC-CAL methods, 45/50 — calibration outputs are model state with uncertainty, not hyperparameters), feature definitions (41).

## 3. Classification Structure

### 3.1 Parameter kinds (`paramKind`)

| Code | Kind | Examples | Tuned? |
|---|---|---|---|
| PK-CAP | Capacity/complexity | n_estimators, max_depth, hidden units/layers | yes |
| PK-REG | Regularisation | lambda/alpha, dropout, min_child_weight, early-stopping patience | yes |
| PK-OPT | Optimisation | learning rate, batch size, optimiser choice, epochs cap | yes |
| PK-OBJ | Objective/loss config | quantile alphas, loss function, class weights | declared per obligation (46/47), tuned within |
| PK-DATA | Data handling | window lengths, resampling, categorical encoding mode | yes (leakage rules apply) |
| PK-RL | RL-specific | gamma, epsilon schedule, replay size, target-update period | yes (sandbox, 45 A6) |
| PK-SEED | Determinism | random seeds | fixed + logged, never tuned |
| PK-PHYS | Physical constants | NOT hyperparameters — calibrated via AC-CAL with uncertainty (boundary rule A6) |

### 3.2 Tuning methods (`tuningMethod`)

| Code | Method | Notes |
|---|---|---|
| TM-DEF | Defaults adopted | surface defaults; permitted with rationale for low-stakes models |
| TM-GRID | Grid search | small discrete spaces |
| TM-RAND | Random search | budgeted sampling |
| TM-BAYES | Bayesian optimisation | TPE/GP-based; budget + prior declared |
| TM-HALV | Successive halving / Hyperband | resource-adaptive |
| TM-MAN | Manual/expert | each trial logged with rationale (no untracked fiddling) |

### 3.3 Sensitivity classes (`sensitivity`)

| Code | Class | Meaning |
|---|---|---|
| SN-H | High | material metric impact; must be tuned or justified |
| SN-M | Medium | tune within budget |
| SN-L | Low | defaults acceptable |

## 4. Hierarchy

```
Surface (EIG-HYP-SUR-*, per algorithm 45)
  └── Tuning campaign (EIG-HYP-TUN-*, per model definition × dataset)
        └── Frozen set (EIG-HYP-*, pinned to a 44 release)
```

## 5. Field Groups & Fields

### 5.1 Surface (`EIG-HYP-SUR-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | surfaceID | string | — | `EIG-HYP-SUR-[A-Z0-9-]{2,20}` | M | ID (e.g. EIG-HYP-SUR-GBM-01) |
| 2 | algorithmRef | ref | — | →45 EIG-ALG-* | M | Owning algorithm |
| 3 | parameters | array<json> | — | [{name, paramKind, type: int\|float\|enum\|bool, range/values, scale: linear\|log, default, sensitivity, interactions[]}] | M | Complete tunable register |
| 4 | conditionals | array<json> | — | [{ifParam, condition, thenActive[]}] | C | Conditional parameter activation |
| 5 | implementationMapping | json | — | param name ↔ library kwarg per pinned implementation (45 §5.1.16) | M | No naming ambiguity |
| 6 | version / status | semver/enum | — | — | M | Lifecycle |

### 5.2 Tuning campaign (`EIG-HYP-TUN-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | campaignID | string | `EIG-HYP-TUN-\d{5}` | M | ID |
| 2 | modelDefRef | ref | →44 EIG-MDL-* | M | Target definition |
| 3 | surfaceRef | ref | →EIG-HYP-SUR-* @version | M | Search space basis |
| 4 | searchSpace | json | subset/refinement of surface ranges | M | Actual space searched (⊆ surface, V2) |
| 5 | tuningMethod | enum | §3.2 | M | Method |
| 6 | budget | json | {trials, walltime, compute} | M | Declared before search (A3) |
| 7 | objectiveMetric | ref | →47 metric + direction | M | What was optimised |
| 8 | evaluationProtocol | json | CV scheme / validation split refs (43), leakage controls (47 §5.1.7) | M | Tuning evaluation — NEVER the frozen test split (A2) |
| 9 | trials | array<json>/ref | [{trialID, values, metricResult, seed}] or artefact ref | M | Full trial log (no cherry-picking) |
| 10 | selectedTrial | ref | trialID | M | Winner + selection rule (best mean, 1-SE rule, etc.) |
| 11 | performanceImpact | json | {metric at defaults vs selected, Δ, sensitivity findings per parameter} | M | Recorded impact (spec requirement) |
| 12 | executedBy / executedAt | ref/datetime | →17 / UTC | M | Provenance |

### 5.3 Frozen set (`EIG-HYP-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | setID | string | `EIG-HYP-\d{5}` | M | ID |
| 2 | campaignRef | ref | →EIG-HYP-TUN-* (or TM-DEF rationale) | M | Origin |
| 3 | values | json | complete assignment for every surface parameter (incl. defaults used) | M | Nothing implicit (V3) |
| 4 | seeds | json | all RNG seeds | M | Determinism record |
| 5 | boundRelease | ref | →44 modelID@semver | M | Exactly one release (1:1, V4) |
| 6 | setHash | string | SHA-256 over canonical values JSON | M | Pin (matches 50 run log) |
| 7 | status | enum | DRAFT, FROZEN, SUPERSEDED | M | FROZEN at release promotion; immutable after |

## 6. Controlled Vocabularies
paramKind (§3.1), tuningMethod (§3.2), sensitivity (§3.3), scale types, selection rules. Parameter naming: surface names are canonical; library kwargs via implementationMapping only.

## 7. Applicability Rules

- **A1 — Complete and explicit.** A frozen set assigns every surface parameter explicitly (defaults written out); "library default" as an implicit value is forbidden — library upgrades silently changing behaviour is the failure mode this kills.
- **A2 — Tuning never touches the test split.** Evaluation protocol uses training/validation folds only (47 A2); test-split contact during tuning voids subsequent validation records.
- **A3 — Budget declared before search.** Campaign budgets fixed up front; budget extensions are logged amendments — prevents p-hacking by indefinite search.
- **A4 — Full trial logging.** All trials recorded, including failures; selection rule applied mechanically to the log.
- **A5 — One frozen set per release.** Releases bind exactly one FROZEN set; retuning ⇒ new campaign ⇒ new set ⇒ new release (44 V4 immutability chain).
- **A6 — Physical parameters are not hyperparameters.** L1 physical constants (R, C, COP coefficients, U-values) are calibrated state estimated via AC-CAL with uncertainty, governed by 50 calibration runs and 05/11 registries — they never appear in frozen sets; PK-PHYS exists only to mark the boundary.
- **A7 — RL tuning in sandbox.** PK-RL tuning campaigns run in simulation/sandbox environments only (45 A6); production exploitation parameters frozen.
- **A8 — Sensitivity honesty.** SN-H parameters left at defaults require recorded justification (TM-DEF rationale field).

## 8. Validation Rules

- **V1:** surface parameters complete vs pinned implementation signature (implementationMapping audit per version pin).
- **V2:** campaign searchSpace ⊆ surface ranges; out-of-range trials rejected.
- **V3:** frozen set covers 100% of surface parameters; types/ranges valid.
- **V4:** 1:1 set↔release binding; setHash recorded in the 50 training run and 44 release record — all three must agree.
- **V5:** selectedTrial exists in trial log; selection rule recomputable.
- **V6:** evaluation protocol dataset refs disjoint from the release's frozen test split (machine check via 43 split hashes).
- **V7:** FROZEN sets immutable; value corrections impossible — supersede via new release.
- **V8:** performanceImpact present with defaults-vs-selected comparison (spec field).
- **V9:** seeds logged for every stochastic trial and the final training run.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 45 algorithm | surfaces owned per algorithm; implementation kwarg mapping |
| 44 model | frozen set per release; promotion checks setHash agreement |
| 50 training | runs execute frozen sets; hash three-way agreement (V4) |
| 47 validation | tuning/test disjointness; objective metrics from the metric register |
| 43 dataset | evaluation protocol corpora; split hashes |
| 51 model_benchmark | comparisons annotate tuning budget parity (fair-comparison rule, 51) |
| 66 RL | PK-RL parameters; sandbox tuning |

## 10. Benchmarking Requirements
Tuning ROI tracking: Δ-metric per compute spent, per method (§3.2) — informs default budgets. Cross-model comparisons (51) record tuning budgets to keep comparisons fair.

## 11. Dataset Requirements
Campaign evaluation data: RELEASED datasets (43) at the model's qualityFloor (40); tuning on stale/withdrawn corpora invalidates the campaign.

## 12. Feature Requirements
PK-DATA parameters that alter feature semantics (window lengths affecting 41 definitions) are constrained: feature formulas live in 41; only consumption parameters (e.g. how many lags fed) are tunable here.

## 13. Model Requirements
Per 44 §5.2.4: every release cites its frozen set; reproducibility = dataset versions (43) + feature set (41) + frozen set (48) + training run (50) + seeds.

## 14. KPI Requirements
Configuration-governance KPIs: % releases with FROZEN sets and three-way hash agreement; % SN-H defaults with justification.

## 15. Response Derivative Requirements
RD estimator (SER-07) configurations are frozen sets like any other; PASS-CONDITIONAL validation conditions (47) may pin specific configuration constraints (e.g. smoothing windows) that subsequent campaigns must respect.

## 16. Decision Requirements
Automated-coupling releases (44 V7): configuration changes of any kind (new frozen set) re-trigger VM-CL validation before deployment — no "minor retune" exemption.

## 17. Ontology Mapping
Surface/parameter ↔ MLS `mls:HyperParameter`; frozen set ↔ `mls:HyperParameterSetting` collection; campaign ↔ `mls:Run` series; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `HyperparameterSurface`, `TuningCampaign`, `FrozenSet`; edges `FOR_ALGORITHM`→45, `TUNED_FOR`→44 definition, `FROZEN_IN`→44 release, `EVALUATED_ON`→43. Sensitivity findings attached as properties for cross-model meta-analysis.

## 19. Digital Twin Mapping
Twin-bound model releases (33 requirement sets) carry frozen sets like all releases; twin simulation engine numerical settings (timesteps, solvers) are surface parameters of AC-PHY records — same discipline applies.

## 20. Governance
Owner: AI/ML Lead; surface changes reviewed against pinned implementations (V1); campaign audits sample trial logs for completeness (A4). SN-H justification reviews at the model board.

## 21. Versioning
Surfaces semver (implementation pin changes ⇒ PATCH + V1 re-audit); campaigns immutable; frozen sets immutable post-FROZEN. Catalogue 1.0.0.

## 22. Example Records

### EIG-HYP-SUR-GBM-01 — LightGBM surface (resolves 45 ref)
- algorithm EIG-ALG-GBM-01; parameters incl.: n_estimators int [100, 5000] log SN-M; learning_rate float [0.005, 0.3] log SN-H; num_leaves int [15, 255] SN-H (interaction: max_depth); min_child_samples int [5, 200] SN-M; lambda_l1/l2 float [0, 10] SN-M; quantile alphas PK-OBJ per 46 obligation; seed PK-SEED fixed
- implementationMapping: LightGBM 4.x kwargs; conditionals: boosting=dart ⇒ drop_rate active

### EIG-HYP-TUN-00231 — campaign for EIG-MDL-LOADFC-RES-01
- surface GBM-01@1.2; method TM-BAYES; budget 120 trials / 6 h; objective MT-PINB (minimise) on blocked time-series CV (43 ARAN train folds; test split untouched, V6 ✓)
- 120 trials logged; selected trial 087 (1-SE rule); performanceImpact: pinball 0.0412 → 0.0371 (−10.0% vs defaults); sensitivity confirmed: learning_rate, num_leaves SN-H

### EIG-HYP-00882 — frozen set for LOADFC-RES-01@2.0.1
- campaign 00231; values complete (31 parameters explicit incl. defaults); seeds {global 88421, lgbm 7}; setHash sha256:… (matches EIG-TRN run + 44 release record); status FROZEN

## 23. Completion Criteria
☑ Surface/campaign/frozen-set model with 1:1 release binding ☑ Complete-and-explicit value rule ☑ Tuning/test disjointness + declared budgets + full trial logs ☑ Performance-impact recording ☑ Physical-parameter boundary (calibration ≠ tuning) ☑ Three-way hash agreement with 44/50 ☑ EIG-HYP-SUR-GBM-01 resolves 45 ref.
