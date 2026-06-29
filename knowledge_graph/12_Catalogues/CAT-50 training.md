---
type: catalogue
ontology: EIG-MLM
catalogue_no: 50
catalogue_id: EIG-CAT-050
entity: training
band: models
status: active
tags: [eig-mlm, catalogue, band:models, entity:training]
aliases: ["EIG-CAT-050", "Catalogue 50", "training"]
---

# CAT-50 — Training

> **EIG-CAT-050** · band: **Models & Learning** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-31 platform]] · [[CAT-41 feature]] · [[CAT-42 synthetic_data]] · [[CAT-43 dataset]] · [[CAT-44 model]] · [[CAT-45 algorithm]] · [[CAT-48 hyperparameter]]
**Used by (downstream):** [[CAT-44 model]] · [[CAT-47 validation]] · [[CAT-51 model_benchmark]] · [[CAT-66 reinforcement_learning]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-050`
**Entity prefixes:** `EIG-TRN-PIPE-` (training pipelines), `EIG-TRN-` (training runs)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 31 platform, 41 feature, 42 synthetic_data, 43 dataset, 44 model, 45 algorithm, 48 hyperparameter
**Used by (downstream):** 44 releases, 47 validation, 51 model_benchmark, 66 reinforcement_learning

---

## 1. Definition

The **Training Catalogue** governs how model releases come into existence: the **pipelines** (`EIG-TRN-PIPE-*`) — versioned, code-pinned procedures that assemble data, execute features, fit a model, and emit artefacts; and the **runs** (`EIG-TRN-*`) — immutable execution records capturing exactly what happened: dataset versions and split hashes (43), feature set version (41), algorithm implementation pin (45), frozen hyperparameter set (48), seeds, environment fingerprint, duration, resource use, training-time results, and the emitted artefact hash (44 release). Purpose: **reproducibility** — any release can be regenerated bit-comparably (within declared numeric tolerance) from its run record, and any result can be audited back to its exact inputs (A1).

This catalogue also governs **calibration runs** (AC-CAL fitting of physical parameters for L1 models) and **RL training runs** (66 policy training in sandbox/simulation) under the same record discipline.

## 2. Scope

**In scope:** pipeline definitions (stages, code pinning, environment specification); run records; reproducibility contract and audits; data assembly discipline (point-in-time, splits, leakage controls); training-time metrics (distinct from validation, 47); resource/duration accounting; environment fingerprints (containers, library locks); retraining triggers and cadences; calibration and RL run specialisations.

**Out of scope:** validation verdicts (47), hyperparameter search (48 campaigns — a tuning campaign *contains* many training executions, logged there; the *final* fit is the run here), deployment (31/35), dataset construction (43).

## 3. Classification Structure

### 3.1 Run kinds (`runKind`)

| Code | Kind | Emits | Notes |
|---|---|---|---|
| RK-FIT | Supervised/unsupervised fit | 44 release candidate | the standard run |
| RK-CAL | Calibration run | physical parameter estimates + uncertainty | AC-CAL methods; outputs update model state (e.g. R/C per zone 04), never hyperparameters (48 A6) |
| RK-RL | RL policy training | policy artefact | sandbox/simulation only (45 A6); environment = 32 twin or 42 scenario |
| RK-RETRAIN | Scheduled/triggered retrain | new release candidate | same pipeline, fresh data window |
| RK-DISTIL | Distillation/compression | edge-deployable artefact | platform model → CLS-E1-fit artefact (35 A7), equivalence evidence required (47) |

### 3.2 Retraining triggers (`retrainTrigger`)

| Code | Trigger | Source |
|---|---|---|
| RT-CAL | Calendar cadence | declared per definition (e.g. monthly for SER-02) |
| RT-DRIFT | Feature/performance drift | 41 V9 PSI alerts, 46 V9 skill breach |
| RT-DATA | New dataset version | 43 release of extended corpus |
| RT-CTX | Context change | retrofit (05 §15), asset replacement (11), occupancy regime change (03) |
| RT-ASSUME | Assumption violation | 44 A8 monitors |

### 3.3 Environment classes

| Code | Where | Fingerprint requirement |
|---|---|---|
| EN-PLT | Platform (31 PLT-AI) | container image hash + lock file |
| EN-SBX | Sandbox (NEST) | as EN-PLT + sandbox tag (firewall) |
| EN-SIM | Simulation (32 twin / 42 generator) | engine version + scenario refs |

## 4. Hierarchy

```
Pipeline (EIG-TRN-PIPE-*)            ← code-pinned procedure, versioned
  └── Run (EIG-TRN-*)                ← one execution, immutable
        └── Emitted artefact         ← →44 release (artefactHash agreement)
```

## 5. Field Groups & Fields

### 5.1 Pipeline (`EIG-TRN-PIPE-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | pipelineID | string | — | `EIG-TRN-PIPE-\d{4}` | M | ID |
| 2 | modelDefRef | ref | — | →44 EIG-MDL-* | M | Target definition |
| 3 | stages | array<json> | — | [{stage: assemble\|feature_exec\|fit\|evaluate_trainside\|package, codeRef + hash}] | M | Ordered, each pinned |
| 4 | dataAssembly | json | — | {datasetQuery (43 refs), pointInTimeRule (41), splitPolicy ref, leakageControls (47 §5.1.7)} | M | Assembly contract |
| 5 | featureSetRef | ref | — | →41 EIG-FT-SET-* @version | M | Input contract (matches 44 definition) |
| 6 | algorithmImpl | ref | — | →45 implementation @pin | M | Exact library |
| 7 | environmentSpec | json | — | {containerImage hash, lockfile hash, hardwareClass} | M | Fingerprint basis |
| 8 | outputContract | json | — | artefact format + 44 release fields populated | M | What a run must emit |
| 9 | retrainPolicy | array<enum> | — | §3.2 triggers + cadence | M | When runs happen |
| 10 | version / status | semver/enum | — | — | M | Lifecycle |

### 5.2 Run (`EIG-TRN-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | runID | string | — | `EIG-TRN-\d{7}` | M | Run ID |
| 2 | pipelineRef | ref | — | →EIG-TRN-PIPE-* @version | M | Procedure |
| 3 | runKind | enum | — | §3.1 | M | Kind |
| 4 | trigger | enum | — | §3.2 or initial | M | Why it ran |
| 5 | datasets | array<ref> | — | →43 EIG-DSET-*@version + split hashes | M | Exact corpora (RELEASED only) |
| 6 | featureSetUsed | ref | — | copy of pipeline ref (audit) | M | Contract confirmation |
| 7 | hyperparameterSet | ref | — | →48 EIG-HYP-* (FROZEN) + setHash | M | Configuration (three-way hash agreement, 48 V4) |
| 8 | seeds | json | — | all RNG seeds | M | Determinism |
| 9 | environmentFingerprint | json | — | image hash, lockfile hash, hardware, env class §3.3 | M | Exact environment |
| 10 | syntheticShare | decimal | — | [0,1] + →42 instance refs | M | Augmentation disclosure (42 A8; 0 for most) |
| 11 | startedAt / duration | datetime/duration | — | UTC / ISO 8601 | M | Timing |
| 12 | resources | json | — | {cpuH, gpuH, peakRamGB, energyKWhEst} | M | Cost accounting (57 hook) |
| 13 | trainsideResults | json | — | learning curves, final train/val metrics (NOT promotion evidence — 47 is) | M | Diagnostics |
| 14 | emittedArtefact | json | — | {artefactHash, format, sizeMB} | M | Output (44 release agreement, V3) |
| 15 | releaseRef | ref | — | →44 modelID@semver (set at promotion) | C | Linkage |
| 16 | logsRef | string | — | artefact store path | M | Full logs retained |
| 17 | executedBy | ref | — | →17 USR-S-* / user | M | Provenance |
| 18 | status | enum | — | RUNNING, COMPLETED, FAILED, ABORTED | M | Outcome (failures retained, A6) |

### 5.3 Calibration-run extensions (RK-CAL)

| # | Field | Type | Description |
|---|---|---|---|
| 1 | calibrationTargets | array<ref> | physical parameters estimated (e.g. zone R/C →04, COP map →11) |
| 2 | priorValues / posteriorValues | json | before/after with uncertainty (CI or posterior) |
| 3 | identifiabilityCheck | json | parameter identifiability evidence (correlation matrix, profile likelihood) |
| 4 | registryWriteback | array<ref> | registry records updated (04/11) with provenance — calibrated values flagged CALIBRATED class |

## 6. Controlled Vocabularies
runKind (§3.1), retrainTrigger (§3.2), environment classes (§3.3), run status. Stage vocabulary fixed; custom stages require pipeline review.

## 7. Applicability Rules

- **A1 — Reproducibility contract.** COMPLETED RK-FIT runs are regenerable: same pipeline version + datasets + splits + feature set + frozen set + seeds + environment ⇒ artefact hash match (bitwise where deterministic; declared numeric tolerance for GPU nondeterminism, with tolerance recorded). Quarterly sampled audits (V6).
- **A2 — RELEASED data only.** Runs cite RELEASED dataset versions (43 §11); training on loose paths or DRAFT corpora is invalid; dataset withdrawal (43 V10) flags dependent runs/releases.
- **A3 — Assembly discipline.** Point-in-time joins (41), split policies honoured, leakage controls executed and logged; test splits untouched by any training-side process (47 A2 / 48 A2 — machine-checked via split hashes).
- **A4 — Trainside ≠ validation.** Trainside metrics are diagnostics; promotion evidence comes only from 47 on held-out data — no release promotes on learning curves.
- **A5 — Calibration writeback provenance.** RK-CAL posterior values written to registries (04/11) carry run provenance and CALIBRATED data class; prior values retained (supersede-never-edit); affected RD records invalidated per 05 §15 / 52 rules.
- **A6 — Failures are records.** FAILED/ABORTED runs retained with logs — negative operational evidence informs pipeline health and 48 budget planning.
- **A7 — RL containment.** RK-RL runs execute in EN-SBX/EN-SIM only; emitted policies promote through 66 gates + 47 VM-CL before any production actuation (45 A6 chain).
- **A8 — Distillation equivalence.** RK-DISTIL artefacts require 47 equivalence evidence (declared metric deltas vs parent release) before edge deployment (35).
- **A9 — Sandbox firewall.** EN-SBX runs and their artefacts never serve production without production-data revalidation (44 A6).

## 8. Validation Rules

- **V1:** run references resolve: pipeline version ACTIVE, datasets RELEASED, frozen set FROZEN.
- **V2:** three-way hash agreement: 48 setHash = run record = 44 release record.
- **V3:** emittedArtefact hash = 44 release artefactHash on promotion.
- **V4:** split-hash disjointness vs the definition's frozen test split (A3).
- **V5:** syntheticShare consistency with dataset composition (42 instance refs) — undeclared synthetic content is a severe violation (no-fabrication chain).
- **V6:** reproducibility audit: sampled re-runs match within declared tolerance; mismatch ⇒ incident + pipeline freeze.
- **V7:** environment fingerprint complete (image + lockfile hashes); "latest" tags forbidden.
- **V8:** RK-CAL: identifiability evidence present; posterior uncertainty reported; writeback provenance links resolve.
- **V9:** runs immutable post-COMPLETED; corrections = new runs.
- **V10:** retention: runs backing ACTIVE releases retained for release lifetime + audit horizon (settlement/RD-linked ⇒ ≥7 y).

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 44 model | runs emit release candidates; artefact hash agreement; retraining ⇒ new release |
| 43 dataset | corpora citation; withdrawal notifications; split hashes |
| 41 feature | feature execution stage; point-in-time assembly |
| 48 hyperparameter | frozen sets executed; three-way agreement |
| 45 algorithm | implementation pins (A2 of 45) |
| 47 validation | disjointness checks; promotion evidence boundary (A4); distillation equivalence |
| 42 synthetic_data | augmentation disclosure; share verification |
| 66 RL | RK-RL runs feed policy lifecycle |
| 31/35 | execution environments; edge distillation targets |
| 04/11 | calibration writebacks with provenance |

## 10. Benchmarking Requirements
Pipeline health metrics: run success rate, mean duration, resource cost per release, reproducibility audit pass rate, retrain-trigger response time (drift alert → fresh release).

## 11. Dataset Requirements
Per 43 §13: citation discipline absolute; training-window definitions (which slice of which version) recorded in dataAssembly resolution logs.

## 12. Feature Requirements
Feature execution uses 41 pinned formulas; runtime feature-value distributions logged for drift baselining (41 V9 reference distributions are set here).

## 13. Model Requirements
The reproducibility quintuple per 44: dataset versions + feature set + frozen set + pipeline/seeds/environment + this run record. Model cards cite the producing run.

## 14. KPI Requirements
Training-ops KPIs (57): releases per quarter, retrain latency, compute/energy per release (resources field — the framework eats its own dog food on energy accounting).

## 15. Response Derivative Requirements
RD estimator releases (SER-07) train under the same discipline with the additional bar: corpora at rd fitness (40), syntheticShare = 0 on evidence-path models (42 §15), and calibration writebacks (thermal parameters) propagating RD invalidation correctly (A5).

## 16. Decision Requirements
Automated-coupling releases: any new run reaching promotion re-triggers VM-CL (48 §16 rule); decision audits trace action → release → run → corpora in four hops.

## 17. Ontology Mapping
Pipeline ↔ MLS `mls:Run` plan / PROV `prov:Plan`; run ↔ `prov:Activity` with qualified usages of datasets/configs; emitted artefact ↔ `prov:Entity`; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `TrainingPipeline`, `TrainingRun`; edges `EXECUTES`→pipeline, `TRAINED_ON`→43, `CONFIGURED_BY`→48, `EMITTED`→44 release, `TRIGGERED_BY` {trigger}, `CALIBRATED`→04/11 parameters. Full reproducibility chain is a path query.

## 19. Digital Twin Mapping
Twin calibration (32 F3) is RK-CAL: measured data (40 rd fitness) → parameter posteriors → twin state update with provenance; twin fidelity revalidation (47 VM-TWIN) follows every calibration run.

## 20. Governance
Owner: AI/ML Lead; pipeline changes peer-reviewed (SoD); reproducibility incidents (V6) escalate to the model board; resource budgets reviewed quarterly.

## 21. Versioning
Pipelines semver (stage/code changes ⇒ MINOR+; assembly contract changes ⇒ MAJOR); runs immutable. Catalogue 1.0.0.

## 22. Example Records

### EIG-TRN-PIPE-0012 — FLEXENV training pipeline
- model EIG-MDL-FLEXENV-01; stages: assemble (43 query ODEON-ASSET-TELEM) → feature_exec (41 thermal set) → fit (L1 envelope param + L3 LightGBM correction) → trainside_eval → package
- assembly: point-in-time PT1M as-of; split policy per DK-TRAIN @2.3; environment EN-PLT image sha256:… lockfile sha256:…

### EIG-TRN-0004821 — run emitting FLEXENV-01@1.4.2
- pipeline 0012@2.1; kind RK-FIT; trigger RT-DATA (corpus @2.3 release)
- datasets [EIG-DSET-ODEON-ASSET-TELEM@2.3 train+val splits]; frozen set EIG-HYP-… (hash three-way ✓); seeds logged; syntheticShare 0.0
- duration PT42M; resources {cpuH 6.1, gpuH 0, peakRam 11 GB, energy ~0.8 kWh}; trainside MAE 0.19 kW (diagnostic)
- artefact sha256:… = release record ✓; promoted after EIG-VAL-00077; logs retained

### EIG-TRN-0004907 — RK-CAL calibration run (EIG-MDL-CAL-RES-01)
- targets: zone R/C for IE_B0014 thermal zones (04); data: winter 25/26 measured at rd fitness (EIG-DQ-R-021)
- posteriors: TZ-1 C 4.8±0.4 kWh/K, R 2.1±0.2 K/kW (identifiability ✓, correlation 0.31); writeback →04 records flagged CALIBRATED with run provenance; prior era-defaults retained
- consequences: twin EIG-DT-000877 revalidation scheduled (VM-TWIN); thermal RDs for affected zones invalidated pending re-estimation (52)

## 23. Completion Criteria
☑ Pipeline/run split with full pinning (code, data, config, environment, seeds) ☑ Reproducibility contract + audits ☑ Trainside/validation boundary ☑ Calibration runs with identifiability + provenance writeback ☑ RL containment + distillation equivalence ☑ Synthetic disclosure verification ☑ Examples close the FLEXENV chain (pipeline → run → release → validation → deployment).
