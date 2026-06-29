---
type: catalogue
ontology: EIG-MLM
catalogue_no: 47
catalogue_id: EIG-CAT-047
entity: validation
band: models
status: active
tags: [eig-mlm, catalogue, band:models, entity:validation]
aliases: ["EIG-CAT-047", "Catalogue 47", "validation"]
---

# CAT-47 — Validation

> **EIG-CAT-047** · band: **Models & Learning** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-40 data_quality]] · [[CAT-41 feature]] · [[CAT-42 synthetic_data]] · [[CAT-43 dataset]] · [[CAT-44 model]] · [[CAT-45 algorithm]] · [[CAT-46 forecast]]
**Used by (downstream):** [[CAT-32 digital_twin]] · [[CAT-44 model]] · [[CAT-50 training]] · [[CAT-51 model_benchmark]] · [[CAT-52 response_derivative]] · [[CAT-60 decision_intelligence]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-047`
**Entity prefixes:** `EIG-VAL-MTH-` (validation methods), `EIG-VAL-` (validation records)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead (scientific assurance) with Data Management Lead (data validation)
**Depends on (upstream):** 40 data_quality, 41 feature, 42 synthetic_data, 43 dataset, 44 model, 45 algorithm, 46 forecast
**Used by (downstream):** 32 digital_twin (fidelity gates), 44 promotion, 50 training, 51 model_benchmark, 52 response_derivative, 60 decision_intelligence

---

## 1. Definition

The **Validation Catalogue** defines how models and derived analytics are proven fit: the **methods** (`EIG-VAL-MTH-*`) — named, versioned validation procedures with metrics and thresholds — and the **records** (`EIG-VAL-*`) — immutable evidence of a method applied to a specific model release (44) on a specific dataset (43), with results, verdict, and reviewer. Validation is the framework's scientific and operational assurance layer: nothing is promoted, no twin fidelity class is claimed, no RD estimator is trusted, and no automated coupling is enabled without a passing EIG-VAL record (A1). Data validation (rule-level) lives in 40; this catalogue validates *models and their claims*.

## 2. Scope

**In scope:** method taxonomy (holdout, cross-validation, backtesting, calibration tests, physics/engineering validation, twin fidelity, closed-loop trials, synthetic-augmentation checks); metric register with formulas; threshold semantics; verdict logic; reviewer duties (SoD); revalidation triggers; rolling operational validation (46 skill).

**Out of scope:** input-data quality rules (40), benchmark *comparison* across models (51 — consumes records from here), training mechanics (50).

## 3. Classification Structure

### 3.1 Method classes (`methodClass`)

| Code | Class | Applies to | Core design |
|---|---|---|---|
| VM-HOLD | Holdout test | L2/L3 supervised | frozen test split (43 DK-TRAIN), never touched in training/tuning |
| VM-CV | Cross-validation | L2/L3, small data | k-fold / grouped (by building/site to prevent leakage) / blocked time-series CV |
| VM-BT | Backtesting | forecasting (46) | rolling-origin evaluation under issue-time discipline (46 A3) |
| VM-CAL | Calibration test | probabilistic outputs | coverage tests, PIT histograms, reliability diagrams |
| VM-PHY | Physics/engineering validation | L1 | analytical benchmarks, inter-model comparison, standard test cases (EN ISO reference cases) |
| VM-TWIN | Twin fidelity validation | 32 twins | CVRMSE/NMBE vs measured (F3 gate: CVRMSE ≤15%, NMBE ±5% — 32) |
| VM-CL | Closed-loop trial | decisionCoupling=automated | supervised live/sandbox trial with rollback; envelope adherence, override behaviour, safety interlocks |
| VM-SYN | Synthetic-augmentation check | 42 A8 models | TSTR + real-only ablation comparison |
| VM-RDV | RD validation | SER-07 / 52 | event-based delivered-vs-predicted on measured data (40 rd fitness), latency/saturation verification |
| VM-OPS | Rolling operational | live models | continuous skill monitoring (46 V9), drift response |

### 3.2 Metric register (`metric`) — formulas normative

| Code | Metric | Formula/definition | Use |
|---|---|---|---|
| MT-MAE / MT-RMSE / MT-MAPE | error metrics | standard | point accuracy |
| MT-NMBE | normalised mean bias error | Σ(ŷ−y)/((n−p)·ȳ) ×100% | twin fidelity (ASHRAE G14 convention) |
| MT-CVRMSE | CV of RMSE | RMSE/ȳ ×100% (n−p dof) | twin fidelity |
| MT-PINB | pinball loss | quantile loss averaged over set | probabilistic forecasts |
| MT-CRPS | continuous ranked probability score | standard | distributional forecasts |
| MT-COV | empirical coverage | % observations inside declared interval | calibration |
| MT-SKILL | skill score | 1 − metric/metric_baseline (51 baseline) | relative skill |
| MT-F1 / MT-AUC | classification | standard | anomaly/classification models |
| MT-RDERR | RD delivery error | |delivered − RD-predicted| / predicted per event | VM-RDV |
| MT-ENV | envelope adherence | % control intervals inside declared envelope (30) | VM-CL |
| MT-OVR | override rate | occupant overrides per event (03 hook) | VM-CL |

### 3.3 Verdicts

PASS (all thresholds met) · PASS-CONDITIONAL (pass with documented limitations binding on consumers) · FAIL (any hard threshold breached) · INCONCLUSIVE (evidence insufficient — never treated as pass).

## 4. Hierarchy

```
Method (EIG-VAL-MTH-*)                ← procedure + metrics + thresholds, versioned
  └── Validation record (EIG-VAL-*)   ← method × model release × dataset(s), immutable
        └── Metric results + verdict + reviewer sign-off
```

## 5. Field Groups & Fields

### 5.1 Method (`EIG-VAL-MTH-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | methodID | string | — | `EIG-VAL-MTH-\d{4}` | M | ID |
| 2 | methodClass | enum | — | §3.1 | M | Class |
| 3 | procedure | string | — | step-by-step, machine-followable where possible | M | The protocol (splits, windows, event selection) |
| 4 | metrics | array<json> | — | [{metric: §3.2, threshold, hard\|soft, direction}] | M | Pass criteria; ≥1 hard |
| 5 | baselineRefs | array<ref> | — | →51 baselines | C | For MT-SKILL |
| 6 | datasetRequirements | json | — | {kind: 43 DK-*, fitness: 40 purpose, minVolume, splits} | M | Evidence-data bar |
| 7 | leakageControls | array<string> | — | grouping keys, temporal blocking, issue-time rule | M | Anti-leakage declaration |
| 8 | applicability | array<ref> | — | 44 series / layer / coupling classes | M | Which models this validates |
| 9 | revalidationTriggers | array<enum> | — | retraining, dataset_version_change, drift_alert (41 V9), assumption_violation (44 A8), context_change (e.g. retrofit 05 §15), calendar (≤12 mo) | M | When evidence expires |
| 10 | reviewerRole | ref | — | →16 role (≠ model author, SoD) | M | Who signs |
| 11 | version / status | semver/enum | — | — | M | Lifecycle |

### 5.2 Validation record (`EIG-VAL-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | validationID | string | `EIG-VAL-\d{5}` | M | Record ID |
| 2 | methodRef | ref | →EIG-VAL-MTH-* @version | M | Procedure applied |
| 3 | subjectRelease | ref | →44 modelID@semver | M | Exactly one release |
| 4 | validationDatasets | array<ref> | →43 EIG-DSET-*@version (+split hash) | M | Evidence corpora (RELEASED only, 43 §11) |
| 5 | executionRef | ref | →50-style run log (code+seed+env hash) | M | Reproducibility of the validation itself |
| 6 | results | array<json> | [{metric, value, threshold, met}] | M | All declared metrics reported (no cherry-picking, V4) |
| 7 | verdict | enum | §3.3 | M | Outcome |
| 8 | conditions | array<string> | — | C | Binding limitations for PASS-CONDITIONAL |
| 9 | reviewer / reviewedAt | ref/datetime | →17 (role per method) / UTC | M | SoD sign-off |
| 10 | validUntil | datetime | per revalidation triggers + calendar cap | M | Evidence expiry |
| 11 | supersedes | ref | prior record | C | Chain on revalidation |
| 12 | environment | enum | production, sandbox | M | Evidence environment (sandbox evidence never suffices for production promotion alone, A5) |

## 6. Controlled Vocabularies
methodClass (§3.1), metric register (§3.2 — additions require formula + review), verdicts (§3.3), revalidation triggers, hard/soft threshold semantics (hard breach ⇒ FAIL regardless of other results).

## 7. Applicability Rules

- **A1 — No promotion without evidence.** 44 CANDIDATE→ACTIVE requires PASS (or PASS-CONDITIONAL with consumer-acknowledged conditions) on every obligation declared in 44 §5.1.20, evidence unexpired.
- **A2 — Evidence-data integrity.** Validation datasets: RELEASED (43), required fitness (40), disjoint from training data per the method's leakage controls; test splits frozen before any tuning (48).
- **A3 — Right method for the claim.** Forecasting claims ⇒ VM-BT under issue-time discipline; probabilistic claims ⇒ VM-CAL additionally; twin fidelity ⇒ VM-TWIN on measured data only (synthetic comparisons never count, 42 A3); automated coupling ⇒ VM-CL mandatory (44 V7); synthetic-augmented training ⇒ VM-SYN mandatory (42 A8).
- **A4 — RD validation bar.** VM-RDV runs on measured event data at 40 rd fitness with syntheticLineage=FALSE; per-event MT-RDERR distribution and latency/saturation checks; sample ≥ declared event count before any RD record (52) cites the estimator as validated.
- **A5 — Sandbox evidence is preliminary.** Sandbox-environment records support sandbox operation and de-risking; production promotion requires production-data evidence (chain 20 A2 …; 44 A6).
- **A6 — Expiry is enforced.** Past validUntil, the release's promotion state degrades: automated coupling drops to advisory, forecasts flag degraded confidence (46 §5.3 context factor → reduced), until revalidated.
- **A7 — Full disclosure.** Records report every declared metric; selective reporting voids the record (V4). FAIL records are retained and visible — negative evidence is evidence.

## 8. Validation Rules

- **V1:** record references resolve: method version ACTIVE, release exists, datasets RELEASED.
- **V2:** dataset/training disjointness machine-checked via 43 split hashes + 50 training citations.
- **V3:** verdict consistent with results (hard threshold logic recomputable).
- **V4:** results cover 100% of method metrics; missing metric ⇒ INCONCLUSIVE.
- **V5:** reviewer ≠ author of the subject release (SoD via 16); reviewer role matches method.
- **V6:** executionRef reproducibility: re-run hash-matches results within declared numeric tolerance (sampled audit, quarterly).
- **V7:** validUntil ≤ issuance + 12 months (calendar cap) and ≤ nearest trigger horizon.
- **V8:** VM-TWIN records compute MT-CVRMSE/MT-NMBE with stated n, p, period; period ≥ declared minimum (typically 12 months or full heating season per 32 gate).
- **V9:** VM-CL records include rollback log, envelope adherence (MT-ENV), override handling evidence (29 A4), and zero life-safety interlock breaches (36 A6) — any breach ⇒ FAIL.
- **V10:** records immutable; corrections supersede (27 A6 discipline).

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 44 model | promotion gates; obligations declared there, evidence here; expiry degrades coupling |
| 43 dataset | evidence corpora; split-hash disjointness |
| 40 data_quality | evidence-data fitness bar; rd fitness for VM-RDV |
| 42 synthetic_data | VM-SYN (TSTR + ablation); measured-only rules for twin/RD |
| 46 forecast | VM-BT/VM-CAL methodology; rolling skill (VM-OPS) feeds 46 V9 |
| 51 model_benchmark | consumes records for cross-model comparison; baselines referenced |
| 32 digital_twin | F-class gates are VM-TWIN obligations |
| 52 RD | VM-RDV is the estimator-trust gate behind RD records |
| 50 training | training/validation disjointness; execution reproducibility pattern shared |
| 16 role | reviewer SoD |

## 10. Benchmarking Requirements
Validation-system KPIs: evidence freshness (% ACTIVE releases unexpired), reproducibility audit pass rate, FAIL/PASS ratios per series (a series that never fails is under-tested).

## 11. Dataset Requirements
Canonical validation corpora maintained per series (e.g. EIG-DSET-ODEON-ASSET-TELEM@2.3 test split for SER-04/06/07); corpus version changes are revalidation triggers.

## 12. Feature Requirements
Validation uses the release's pinned input contract (41) — validating with different features validates a different model (rejected at V1-level coherence check).

## 13. Model Requirements
Per 44: obligations enumerated at definition time; method applicability matched to claims (A3); assumption-violation monitoring (44 A8) wired to revalidation triggers.

## 14. KPI Requirements
Published model-performance KPIs (57) cite validation records, not ad-hoc evaluations; KPI dashboards (75) badge evidence age.

## 15. Response Derivative Requirements
The chain is: VM-RDV validates the estimator (44 SER-07) → 52 RD records cite the validated estimator + measured events → 22 product gates check RD confidence/recency → 27 settlement uses BL-* baselines whose methods cite validated estimators. Any break in this chain (expired VM-RDV, FAIL verdict) suspends downstream RD trust automatically.

## 16. Decision Requirements
decisionCoupling=automated requires unexpired VM-CL PASS; expiry ⇒ advisory degradation (A6). Decision audit (60) traces each automated action to the covering validation record.

## 17. Ontology Mapping
Method ↔ MLS `mls:EvaluationProcedure`; record ↔ `mls:ModelEvaluation` + `mls:EvaluationMeasure`; verdicts ↔ EIG extension; PROV for execution lineage; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `ValidationMethod`, `ValidationRecord`; edges `VALIDATES`→44 release, `ON_DATASET`→43, `SIGNED_BY`→17, `SUPERSEDES`, `GATES`→{promotion, twin fidelity, RD trust, automated coupling}. "What evidence covers this automated action?" is a one-hop query.

## 19. Digital Twin Mapping
Twin fidelity claims (32 F2/F3/F4) are standing VM-TWIN obligations with seasonal revalidation; twins auto-degrade one fidelity class on expiry (32 hook), restoring on fresh PASS.

## 20. Governance
Owners: AI/ML Lead (model methods), Data Management Lead (evidence-data integrity). Method changes are MAJOR if thresholds loosen (requires board sign-off) — thresholds ratchet by default. Quarterly reproducibility audits (V6).

## 21. Versioning
Methods semver; records immutable with supersession chains. Catalogue 1.0.0.

## 22. Example Records

### EIG-VAL-MTH-0007 — Flexibility envelope closed-loop trial (VM-CL)
- applicability: SER-06 automated-coupling releases; procedure: 14-day supervised trial, staged ring (35), envelope adherence + override + rollback drills
- metrics: MT-ENV ≥ 99% hard; MT-OVR ≤ 0.10 soft; zero interlock breaches hard; comfort floor violations = 0 hard (29 A7)
- datasets: live telemetry at rd fitness; reviewer: platform owner role (≠ author); revalidation: retraining, envelope-definition change, ≤12 mo

### EIG-VAL-00077 — record for EIG-MDL-FLEXENV-01@1.4.2 (resolves 39/44 refs)
- method EIG-VAL-MTH-0007@1.1 + VM-HOLD companion EIG-VAL-MTH-0003
- datasets [EIG-DSET-ODEON-ASSET-TELEM@2.3 test split]; execution sha256:…
- results: MT-ENV 99.6% ✓; MT-OVR 0.06 ✓; interlocks 0 ✓; comfort violations 0 ✓; holdout MAE 0.21 kW ✓
- verdict PASS; reviewer EIG-USR-… (ops lead); validUntil 2027-03-01 (calendar cap + retraining trigger); environment production
- consumers: 44 promotion of @1.4.2; EIG-EDGE-DEP-00231 authorisation

### EIG-VAL-00102 — VM-RDV record for thermal-shed RD estimator (SER-07)
- 41 measured events (Aran winter 25/26), rd fitness verified per event (EIG-DQ-R-021); MT-RDERR p50 6.8%, p90 14.1% ✓ (hard ≤20% p90); latency prediction within ±90 s ✓; saturation curve verified to 2.5 h events
- verdict PASS-CONDITIONAL: valid for events ≤ 3 h, occupied-state contexts only (03 §15 rule) — conditions bind 52 records citing this estimator

## 23. Completion Criteria
☑ Ten method classes incl. twin/closed-loop/RD/synthetic ☑ Metric register with formulas (CVRMSE/NMBE per twin gates) ☑ Verdict + SoD + expiry semantics ☑ Leakage/disjointness machine checks ☑ Full-disclosure + negative-evidence retention ☑ EIG-VAL-00077 resolves 39/44 refs; RD chain to 52/22/27 explicit.
