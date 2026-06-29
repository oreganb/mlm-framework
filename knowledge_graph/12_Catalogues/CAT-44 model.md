---
type: catalogue
ontology: EIG-MLM
catalogue_no: 44
catalogue_id: EIG-CAT-044
entity: model
band: models
status: active
tags: [eig-mlm, catalogue, band:models, entity:model]
aliases: ["EIG-CAT-044", "Catalogue 44", "model"]
---

# CAT-44 — Model

> **EIG-CAT-044** · band: **Models & Learning** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-01 building_classification]] · [[CAT-38 data]] · [[CAT-39 metadata]] · [[CAT-40 data_quality]] · [[CAT-41 feature]] · [[CAT-42 synthetic_data]] · [[CAT-43 dataset]]
**Used by (downstream):** [[CAT-45 algorithm]] · [[CAT-46 forecast]] · [[CAT-47 validation]] · [[CAT-48 hyperparameter]] · [[CAT-49 explainability]] · [[CAT-50 training]] · [[CAT-51 model_benchmark]] · [[CAT-52 response_derivative]] · [[CAT-60 decision_intelligence]] · [[CAT-66 reinforcement_learning]] · [[CAT-70 model_selection]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-044`
**Entity prefixes:** `EIG-MDL-` (model definitions/releases), `EIG-MDL-SER-` (series), layer codes `MLM-L1..L4`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 01 building_classification, 38 data, 39 metadata, 40 data_quality, 41 feature, 42 synthetic_data, 43 dataset
**Used by (downstream):** 45 algorithm, 46 forecast, 47 validation, 48 hyperparameter, 49 explainability, 50 training, 51 model_benchmark, 52 response_derivative, 60 decision_intelligence, 66 reinforcement_learning, 70 model_selection

---

## 1. Definition

The **Model Catalogue** is the single register of all MLM models: every analytic, simulation, forecasting, learning, and control model in the framework — the full MLM/FLEXGPT cognitive stack. A **model definition** (`EIG-MDL-*`) declares identity, layer, series, inputs (feature sets, 41), outputs (variables, 38), execution frequency, assumptions, dependencies, validation obligations (47), and lifecycle status. A **model release** (`EIG-MDL-*@semver`) is a trained/parameterised, immutable, deployable artefact of a definition with full lineage metadata (39 EIG-MET-STD-MDL).

The register is organised by the **four-layer MLM cognitive stack** and **thirteen series**; the 139 sub-models of the MLM/FLEXGPT framework map onto this register exhaustively (V10). No model executes in any pipeline, dashboard, twin, or decision path unless registered here (A1).

## 2. Scope

**In scope:** layer/series taxonomy; model definition and release records; input/output contracts; execution profiles (batch/online/edge); assumption and dependency registers; applicability binding (01 matrix); promotion gates; dependency-graph change notification; model card obligations.

**Out of scope:** algorithm theory (45 — a model *uses* an algorithm); training mechanics (50); hyperparameter values (48); validation methods (47); RD semantics (52 — RD models register here, RD records live there); RL machinery (66 — L4 models register here, learning policies there).

## 3. Classification Structure

### 3.1 MLM layers (`layer`) — the four-layer cognitive stack

| Code | Layer | Nature | Determinism | Typical algorithms (45) |
|---|---|---|---|---|
| MLM-L1 | Deterministic | Physics/engineering/accounting models | Fully deterministic given inputs | RC networks, EN ISO engines, power flow, tariff arithmetic |
| MLM-L2 | Stochastic | Statistical/probabilistic models | Distributional outputs | regression, ARIMA/SARIMAX, GAM, copulas, Bayesian |
| MLM-L3 | AI/ML | Learned pattern models | Trained, data-driven | GBM, random forest, NN/LSTM/TCN, clustering, transformers |
| MLM-L4 | RL/Q-Learning | Sequential decision/learning agents | Policy-driven, environment-coupled | Q-learning, DQN, policy gradient, contextual bandits |

Layer discipline: L1 outputs may feed any layer; L4 consumes L1–L3 outputs as state; cross-layer hybrid models declare a primary layer plus `hybridLayers`.

### 3.2 Model series (`EIG-MDL-SER-*`) — thirteen series, 139 sub-models

| Series | Name | Layers | Sub-models | Representative sub-models |
|---|---|---|---|---|
| SER-01 | Building thermal & physics | L1/L2 | 14 | R1C1, R2C2 (EIG-MDL-SPEC-THERM-R2C2), R3C3, DEAP/BER engine, EN ISO 52016 hourly, coast-down, pre-heat, condensation screen |
| SER-02 | Load forecasting | L2/L3 | 16 | HH building load (EIG-MDL-LOADFC-RES-01), zone load, portfolio load, peak, baseload, holiday-adjusted, probabilistic quantile set |
| SER-03 | Generation & renewables | L1/L2/L3 | 10 | PV yield (deterministic + ML-corrected), wind, solar-thermal, curtailment estimator |
| SER-04 | Asset & equipment performance | L1/L3 | 13 | HP COP map, COP degradation, boiler efficiency, storage state/SoH, EV charge models, calibration models (EIG-MDL-CAL-RES-01) |
| SER-05 | Occupancy & behaviour | L2/L3 | 9 | presence probability, arrival/departure, override propensity, engagement scoring |
| SER-06 | Flexibility & envelope | L1/L2/L3 | 12 | flex envelope (EIG-MDL-FLEXENV-01), headroom nowcast, availability forecast, rebound, simultaneity |
| SER-07 | Response Derivative estimation | L2/L3 | 11 | thermal-shed RD (feeds EIG-RD-SPEC-THERM-SHED, 52), setpoint RD, price RD, latency/saturation estimators, RD confidence model |
| SER-08 | Market price & tariff | L2/L3 | 10 | DAM price forecast, imbalance forecast, tariff cost engine (L1 arithmetic + pricingLibraryHash, 21 V11), spread models |
| SER-09 | Carbon & emissions | L1/L2 | 8 | CI nowcast/forecast (28 series), avoided-emissions engines per AVM, embodied calculators |
| SER-10 | Network & grid | L1/L3 | 9 | power flow, transformer loading, voltage estimation, hosting capacity, loss models (13 zones) |
| SER-11 | Anomaly, diagnostics & data | L2/L3 | 10 | consumption anomaly, sensor fault, drift detectors (40/41 hooks), imputation models, NILM disaggregation |
| SER-12 | Optimisation & scheduling | L1/L3 | 9 | MILP dispatch, schedule optimiser, retrofit option ranking, portfolio bid construction |
| SER-13 | RL control & trading agents | L4 | 8 | zone-control Q-learning, HP dispatch DQN, bidding agent, contextual-bandit model selector (70 hook) |

Σ = 139. The sub-model register (machine table, one row per sub-model with definitionID) is normative; series totals change only by catalogue version bump (V10).

### 3.3 Execution profiles (`executionProfile`)

| Code | Profile | Latency target | Where |
|---|---|---|---|
| EX-BATCH | Batch | hours | platform (31 PLT-AI/DATA) |
| EX-ON | Online service | ≤ LAT-FAST (34) | platform inference service |
| EX-EDGE | Edge-deployable | ≤ LAT-RT/FAST | 35 nodes (CLS-E1 baseline resources, degraded-mode capable) |
| EX-SBX | Sandbox-only | — | NEST/sandbox firewall (A6) |

### 3.4 Lifecycle status

DRAFT → CANDIDATE (validation 47 running) → ACTIVE (promoted) → DEPRECATED (migration window ≥90 d) → RETIRED (closed to execution; releases retained for audit/replay).

## 4. Hierarchy

```
Layer (MLM-L1..L4)
  └── Series (EIG-MDL-SER-01..13)
        └── Model definition (EIG-MDL-*)          ← sub-model of the 139 register
              └── Release (@MAJOR.MINOR.PATCH)    ← immutable trained artefact
                    └── Deployment binding        ← →31 platform / →35 edge (EIG-EDGE-DEP-*)
```

## 5. Field Groups & Fields

### 5.1 Model definition (`EIG-MDL-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | modelID | string | — | `EIG-MDL-[A-Z0-9-]{3,30}` | M | Definition ID (name-stable) |
| 2 | name | string | — | ≤120 chars | M | Human name |
| 3 | layer | enum | — | MLM-L1..L4 | M | Primary layer |
| 4 | hybridLayers | array<enum> | — | additional layers | C | For hybrids |
| 5 | seriesRef | ref | — | →EIG-MDL-SER-01..13 | M | Series (exactly one) |
| 6 | purpose | string | — | ≤1000 chars | M | What it predicts/computes/controls |
| 7 | algorithmRefs | array<ref> | — | →45 EIG-ALG-* | M | Algorithm(s) used |
| 8 | inputContract | ref | — | →41 EIG-FT-SET-* @version | M | Pinned feature set (no inline features, 41 A1) |
| 9 | auxInputs | array<ref> | — | →38 EIG-VAR-* / registry refs (01/07/09/21) | C | Non-feature inputs (parameters, registries) |
| 10 | outputVariables | array<ref> | — | →38 EIG-VAR-* (registered, COMPUTED) | M | Outputs bind to the variable register (V2) |
| 11 | outputForm | enum | — | point, quantiles, distribution, schedule, policy_action, classification | M | Output shape |
| 12 | frequency | duration | — | ISO 8601 (e.g. PT30M, P1D, on_event) | M | Execution cadence |
| 13 | horizon | duration | — | ISO 8601; 0 for nowcast/state models | C | For forecasting models (46) |
| 14 | spatialGrain | string | — | building/zone/asset/network/portfolio per registry | M | Subject grain |
| 15 | applicabilityMatrix | array<ref> | — | →01 EIG-BC-MTX-* slice | M | Where the model may run (01 §13; 70 enforces) |
| 16 | assumptions | array<json> | — | {id, statement, basis, violatedIf} | M | Enumerated, machine-checkable where possible |
| 17 | dependencies | array<ref> | — | →EIG-MDL-* (upstream models), →43 datasets, →registries | M | Dependency graph edges (A4 notifications) |
| 18 | qualityFloor | enum | — | 40 fitnessPurpose | M | Min input fitness (rd for RD-feeding models, A5) |
| 19 | syntheticPolicy | json | — | {trainingShareMax, requiresAblation per 42 A8} | M | Synthetic-data stance |
| 20 | validationObligations | array<ref> | — | →47 method refs + thresholds | M | What 47 must pass for promotion (A2) |
| 21 | explainabilityClass | enum | — | 49 classes (intrinsic, post_hoc_required, exempt_L1) | M | Transparency duty |
| 22 | executionProfile | array<enum> | — | §3.3 | M | Deployment surfaces |
| 23 | edgeResourceEnvelope | json | — | {ramMB, storageMB, cpuClass} ≤ CLS-E1 for EX-EDGE | C | 35 A7 fit |
| 24 | decisionCoupling | enum | — | none, advisory, automated_within_envelope | M | 60 gating class |
| 25 | owner / steward / reviewer / approver | ref | — | →16 roles (SoD: author ≠ approver) | M | Duties |
| 26 | status / version | enum/semver | — | §3.4 | M | Lifecycle |

### 5.2 Model release (per trained artefact)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | releaseRef | string | modelID@MAJOR.MINOR.PATCH | M | Immutable release |
| 2 | trainingRunRef | ref | →50 EIG-TRN-* | M | Producing run (reproducibility chain) |
| 3 | trainingDatasets | array<ref> | →43 EIG-DSET-*@version + split hash | M | Exact corpora (43 A4) |
| 4 | hyperparameterSetRef | ref | →48 EIG-HYP-* | M | Frozen configuration |
| 5 | artefactHash | string | SHA-256 | M | Pinned binary/params (35 A6 discipline) |
| 6 | validationResults | array<ref> | →47 EIG-VAL-* | M | Promotion evidence (e.g. EIG-VAL-00077) |
| 7 | benchmarkResults | array<ref> | →51 EIG-MBM-* | C | Comparative standing |
| 8 | metadataRef | ref | →39 EIG-MET-* (EIG-MET-STD-MDL, CL-FULL) | M | Release gate |
| 9 | modelCard | string | path/URI | M | Human-readable card: purpose, data, performance, limits, ethics |
| 10 | environment | enum | production, sandbox | M | Firewall tag (A6) |
| 11 | deployments | array<ref> | →31/→35 EIG-EDGE-DEP-* | C | Where running |
| 12 | supersedes | ref | prior release | C | Chain |

## 6. Controlled Vocabularies
layer (§3.1), series register (§3.2 — closed; sub-model additions are MINOR catalogue changes with Σ updated), executionProfile (§3.3), status (§3.4), outputForm, decisionCoupling. Model naming: `EIG-MDL-<DOMAIN>-<SCOPE>-<NN>` style (e.g. EIG-MDL-LOADFC-RES-01).

## 7. Applicability Rules

- **A1 — Registered models only.** Pipelines (50), services (31), edge deployments (35), twins (32), dashboards (75), and decision paths (60) execute only ACTIVE releases of registered definitions; ad-hoc model code in production is a 47 audit failure.
- **A2 — Promotion gate.** CANDIDATE→ACTIVE requires: all validationObligations passed (47), metadata CL-FULL (39), benchmark entry where a baseline exists (51), approver sign-off (SoD). Synthetic-trained models additionally satisfy 42 A8 (real-data validation + ablation).
- **A3 — Applicability enforcement.** A model may not run on a subject whose classification verdict (01 matrix) is not_applicable; conditional verdicts evaluate their condition at invocation (70 model_selection is the enforcement point).
- **A4 — Dependency notifications.** Changes to any dependency (feature set version, upstream model release, dataset withdrawal 43 V10, variable semantics 38) flag all dependent definitions for review within 24 h; unreviewed flags block new release promotion.
- **A5 — RD evidence bar.** SER-07 models and any model whose outputs enter RD evidence (52): qualityFloor=rd, syntheticLineage=FALSE inputs (42 §15), source-timestamped telemetry (36 V11), and no TX-MODEL circularity on evidence paths (41 A5).
- **A6 — Sandbox firewall.** environment=sandbox releases never serve production consumers; promotion from sandbox re-validates on production data (chain 20 A2 … 43 A7). EX-SBX definitions cannot acquire production deployments without reclassification.
- **A7 — Edge discipline.** EX-EDGE releases fit CLS-E1 envelopes (35 A7), pin artefact hashes (35 A6), and declare degraded-mode behaviour within localAuthorityScope (35 A3) — automated decisionCoupling on edge stays inside contract caps (e.g. EIG-EDGE-DEP-00231 enforcing EIG-CON-001207).
- **A8 — Assumption monitoring.** Each assumption with a machine-checkable `violatedIf` is monitored in operation; violation ⇒ release flagged suspect, consumers notified, fallbacks engaged (60/46 staleness rules).

## 8. Validation Rules

- **V1:** modelID unique, name-stable; exactly one seriesRef; layer/series compatibility per §3.2 layer columns.
- **V2:** outputVariables resolve in 38 register (no unregistered outputs).
- **V3:** inputContract pinned to explicit feature-set version (no floating refs, 41 V8).
- **V4:** release immutability: artefactHash never changes under a releaseRef; retraining ⇒ new release.
- **V5:** dependency graph acyclic at definition level (TX-MODEL chains terminate in data, not loops).
- **V6:** EX-EDGE releases: edgeResourceEnvelope present and ≤ class limits.
- **V7:** decisionCoupling=automated_within_envelope requires: envelope refs resolvable (30/11), comfort floors (29 A7), override honoured (29 A4), and 47 closed-loop validation evidence.
- **V8:** model card present and current (regenerated per release).
- **V9:** DEPRECATED definitions cannot receive new deployments; RETIRED releases blocked from execution but retained ≥7 y where settlement/RD-cited.
- **V10:** sub-model census: Σ definitions per series equals the §3.2 register (139 total); drift ⇒ catalogue version bump with register update.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 45 algorithm | algorithmRefs; selection rationale recorded per definition |
| 41 feature | input contracts (pinned sets); drift notifications (41 V9 → A4) |
| 43 dataset | training corpora citation; withdrawal notifications |
| 47 validation | promotion gates; obligations declared here, evidence there |
| 48 hyperparameter | frozen sets per release |
| 50 training | producing runs; reproducibility chain |
| 51 model_benchmark | comparative standing; regression gates on re-release |
| 46 forecast | forecasting definitions (horizon>0) produce forecast runs there |
| 52 RD | SER-07 outputs feed RD records under A5 bar |
| 60/66/70 | decision coupling; L4 agents; selection enforcement |
| 32/33 | twins bind model requirements via templates; F-class gates consume 47 evidence |
| 01 | applicability matrix slices |

## 10. Benchmarking Requirements
Every ACTIVE forecasting/estimation definition holds a current 51 entry vs declared baselines (persistence, climatology, prior release); re-releases must not regress beyond tolerance (51 gates). Census KPI: register coverage 139/139, % ACTIVE with passing validation ≤90 d old.

## 11. Dataset Requirements
Releases cite RELEASED datasets only (43 §13); training-data quality envelopes recorded (40 §13); drift monitors compare live vs training distributions.

## 12. Feature Requirements
All inputs via EIG-FT-SET contracts; new feature needs route through 41 registration, never inline (41 A1).

## 13. Model Requirements
(Self) — the 139-sub-model register is the normative census; every MLM/FLEXGPT sub-model has exactly one definition row; hybrids declare layers honestly; paper-facing descriptions ("A Multi-Layered Machine Learning Architecture…") cite register IDs.

## 14. KPI Requirements
Model-health KPIs (57): accuracy vs obligation thresholds, drift incidents, assumption violations, availability of EX-ON services. KPI computation uses registered output variables only.

## 15. Response Derivative Requirements
SER-07 definitions are the only authorised RD estimators; their releases, evidence datasets, and validation results are the provenance chain behind every EIG-RD-* record (52), product gate check (22 conf ≥0.8/≤90 d), and settlement baseline using rd_counterfactual (27 BL-LFM-01).

## 16. Decision Requirements
decisionCoupling governs 60: none/advisory models inform; automated_within_envelope models act only inside flex envelopes (30), contract caps (23), comfort floors (29), with readback (36 A6) and degraded-mode bounds (35 A3).

## 17. Ontology Mapping
Model ↔ MLS (`mls:Model`, `mls:Implementation`, `mls:Run` via 50); layer/series ↔ MLM ontology classes (mlm_v5 line, 79); assumptions ↔ PROV qualified usage; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `ModelDefinition`, `ModelRelease`, `Series`, `Layer`; edges `IN_SERIES`, `USES_ALGORITHM`→45, `CONSUMES`→41 sets, `TRAINED_ON`→43, `VALIDATED_BY`→47, `DEPENDS_ON` (model graph), `DEPLOYED_AT`→31/35, `FEEDS_RD`→52. The dependency graph powers A4 impact queries — the MLM visualiser (mlm_v6 line) renders from this graph.

## 19. Digital Twin Mapping
Twin templates (33) declare required model sets per fidelity class (e.g. F3 residential: SER-01 R2C2 + SER-04 calibration + SER-06 envelope); twins (32) bind releases by releaseRef; fidelity gates (CVRMSE/NMBE) consume 47 evidence for the bound releases.

## 20. Governance
Owner: EIG AI/ML Lead. Series stewardship may be delegated per domain. Promotion approvals follow SoD; automated-coupling promotions additionally reviewed by the platform owner (31). Register census audited quarterly (V10).

## 21. Versioning
Definitions semver (MAJOR: input/output contract or purpose change; MINOR: assumption/applicability change; PATCH: documentation). Releases immutable. Catalogue 1.0.0 fixes the 13×139 register.

## 22. Example Records

### EIG-MDL-FLEXENV-01 — Asset flexibility envelope model (SER-06, resolves 39/41/43 refs)
- layer MLM-L1 (physics) + hybridLayers [L3 correction]; series SER-06; purpose: live shiftable-power envelope per asset respecting comfort/contract constraints
- inputs: thermal feature set (41) incl. EIG-FT-0012; aux: asset ratings (11 EIG-AST-004211), contract caps (23 EIG-CON-001207), comfort floors (29)
- outputs: EIG-VAR flex_envelope_kw_up/down (registered); form point; frequency PT1M; grain asset
- executionProfile [EX-ON, EX-EDGE]; edge envelope {ram 512 MB, CLS-E1 ✓}; decisionCoupling automated_within_envelope (V7 evidence: EIG-VAL-00077 + closed-loop trial)
- release @1.4.2: trainingRun EIG-TRN-…; datasets [EIG-DSET-ODEON-ASSET-TELEM@2.3]; hash sha256:…; metadata EIG-MET-000587; deployments [EIG-EDGE-DEP-00231]; environment production
- assumptions: A-01 telemetry latency ≤60 s (violatedIf lag>120 s → suspect); A-02 COP map valid 0–10 °C ambient

### EIG-MDL-LOADFC-RES-01 — Residential HH load forecast (SER-02)
- layer MLM-L3; algorithm EIG-ALG-GBM-01 (45); inputContract EIG-FT-SET-0009@…; outputs quantiles p10/p50/p90 of EIG-VAR-0102; horizon PT24H–PT48H; frequency PT30M
- qualityFloor training; validationObligations: pinball loss ≤ threshold vs persistence + seasonal-naive baselines (51); applicability: SEC-01 nodes (01)
- feeds 46 forecast runs; advisory decisionCoupling

### EIG-MDL-SPEC-THERM-R2C2 — Two-node RC thermal model (SER-01)
- layer MLM-L1; deterministic; parameters from 04 zone C/R or 50 calibration (EIG-MDL-CAL-RES-01 companion); outputs zone temp trajectory; explainability exempt_L1 (intrinsic physics)
- consumers: SER-06 envelope, SER-07 thermal-shed RD estimator, 32 F3 twin gates

## 23. Completion Criteria
☑ Four-layer stack + thirteen-series register with 139 sub-model census (V10) ☑ Definition/release split with immutability + lineage ☑ Input/output contracts bound to 41/38 registers ☑ Promotion gates (47/39/51, SoD) ☑ Dependency-graph notifications ☑ RD evidence bar ☑ Edge/sandbox discipline ☑ Examples resolve EIG-MDL-FLEXENV-01@1.4.2, EIG-MDL-LOADFC-RES-01, EIG-MDL-SPEC-THERM-R2C2, EIG-MDL-CAL-RES-01 refs from 32/33/39/41/43.
