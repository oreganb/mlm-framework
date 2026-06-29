---
type: catalogue
ontology: EIG-MLM
catalogue_no: 42
catalogue_id: EIG-CAT-042
entity: synthetic_data
band: data
status: active
tags: [eig-mlm, catalogue, band:data, entity:synthetic_data]
aliases: ["EIG-CAT-042", "Catalogue 42", "synthetic_data"]
---

# CAT-42 — Synthetic Data

> **EIG-CAT-042** · band: **Data, Features & Datasets** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-07 building_archetype]] · [[CAT-09 weather_climate]] · [[CAT-13 utility_network]] · [[CAT-20 market]] · [[CAT-37 data_source]] · [[CAT-38 data]] · [[CAT-39 metadata]] · [[CAT-40 data_quality]]
**Used by (downstream):** [[CAT-20 market]] · [[CAT-31 platform]] · [[CAT-43 dataset]] · [[CAT-44 model]] · [[CAT-47 validation]] · [[CAT-50 training]] · [[CAT-89 scenario]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-042`
**Entity prefixes:** `EIG-SYN-GEN-` (generators), `EIG-SYN-SCN-` (generation scenarios), `EIG-SYN-DS-` (synthetic dataset instances)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 07 building_archetype, 09 weather_climate, 13 utility_network, 20 market, 37 data_source, 38 data, 39 metadata, 40 data_quality
**Used by (downstream):** 43 dataset, 44 model, 47 validation, 50 training, 89 scenario, NEST sandbox catalogue chain (20/31)

---

## 1. Definition

The **Synthetic Data Catalogue** governs artificially generated data: the **generators** (`EIG-SYN-GEN-*`) that produce it, the **scenarios** (`EIG-SYN-SCN-*`) that parameterise generation runs, and the **synthetic dataset instances** (`EIG-SYN-DS-*`) they emit. Synthetic data exists to enable simulation, model testing, sandbox experimentation (NEST/SYNTH_GRID_IE), privacy-preserving sharing, and rare-event augmentation — and it is governed by one absolute principle inherited from 37 A2 (the hyperGrid **no-fabrication provenance standard**):

> **dataClass=SYNTHETIC is assigned at generation and can never be relabelled, laundered, or upgraded — no matter how realistic, calibrated, or validated the output is.**

A generator is itself a registered, versioned artefact with declared assumptions, statistical fidelity targets, and validation evidence; synthetic outputs without a registered generator are inadmissible framework-wide (A1).

## 2. Scope

**In scope:** generator taxonomy and registration; scenario parameterisation; output schema binding to the 38 variable register; statistical fidelity metrics and validation; privacy guarantees (when synthesised from real data); permitted/forbidden use matrix; sandbox firewall obligations; seeded reproducibility.

**Out of scope:** scenario *narratives* for planning studies (89 — scenarios there reference generators here); dataset packaging (43); model-based forecasting (46 — forecasts are COMPUTED, not SYNTHETIC); twin simulation state (32 — twins may *consume* synthetic inputs under sandbox rules).

## 3. Classification Structure

### 3.1 Generator types (`generatorType`)

| Code | Type | Method | Examples |
|---|---|---|---|
| GT-PHYS | Physics-based simulator | Engineering models, building physics | R2C2/archetype thermal simulation; network power-flow synthesis |
| GT-STAT | Statistical/profile-based | Sampling from fitted distributions, BER-driven profiles | BER-archetype load profile generator (GeoCoHorT v4 lineage) |
| GT-ML | ML generative | GAN/VAE/diffusion/copula | smart-meter GAN; weather-coupled copula sampler |
| GT-AGENT | Agent-based | Behavioural agents on networks | occupancy/EV-charging behaviour swarms |
| GT-HYB | Hybrid | Composed of the above | SYNTH_GRID_IE national pipeline (physics + statistical calibration) |
| GT-PERT | Perturbation/augmentation | Noise/scenario perturbation of real seeds | stress augmentation for rare events — output still SYNTHETIC |

### 3.2 Realism/fidelity tiers (`fidelityTier`)

| Code | Tier | Bar | Permitted uses (with §3.3) |
|---|---|---|---|
| SF-1 | Illustrative | Plausible shapes only; no statistical claims | demos, UI development, documentation |
| SF-2 | Statistically representative | Marginals + key correlations within declared tolerance of reference | model prototyping, sandbox market trials (20 EIG-MKT-SBX-01), load studies |
| SF-3 | Calibrated-validated | Joint distributions, temporal autocorrelation, cross-variable physics validated vs reference (40-style assessment) | training augmentation (declared), counterfactual studies, NEST grid-scale runs |

No tier reaches settlement, RD evidence, compliance disclosure, or regulated reporting — ever (A3).

### 3.3 Use matrix (`permittedUse` / forbidden)

| Use | Permitted? | Conditions |
|---|---|---|
| Sandbox experimentation (NEST/SYNTH_GRID_IE) | ✔ | environment=sandbox, firewall chain (20 A2 … 39 A5) |
| Model prototyping/unit tests | ✔ | SF-2+ |
| Training augmentation | ✔ | SF-3; synthetic share disclosed in model metadata (39 MC-LIN); real-only ablation retained (47) |
| Privacy-preserving release | ✔ | privacy guarantees §5.1; DPIA where seeded from personal data |
| Rare-event/stress testing | ✔ | GT-PERT/GT-PHYS; scenario documented |
| Public dashboards | ✔ | provenance envelope labels SYNTHETIC visibly (hyperGrid standard) |
| **Settlement (27)** | ✘ | absolute |
| **RD evidence (52)** | ✘ | absolute (40 A5 RD-grade excludes SYNTHETIC) |
| **Carbon/compliance disclosure (28/93)** | ✘ | 28 V9 |
| **Calibration ground truth (32 F3 gates)** | ✘ | twins calibrate on REAL/CALIBRATED only |

## 4. Hierarchy

```
Generator (EIG-SYN-GEN-*)            ← versioned artefact + assumptions + validation
  └── Scenario (EIG-SYN-SCN-*)       ← parameter bundle (seed, period, population, stressors)
        └── Synthetic dataset instance (EIG-SYN-DS-*)
              → registered as 37 stream (DSP-SYN provider) → 38 entities → 43 package
```

## 5. Field Groups & Fields

### 5.1 Generator (`EIG-SYN-GEN-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | generatorID | string | — | `EIG-SYN-GEN-\d{4}` | M | ID |
| 2 | name | string | — | ≤120 chars | M | Human name |
| 3 | generatorType | enum | — | §3.1 | M | Method family |
| 4 | description | string | — | ≤2000 chars | M | What it produces, how |
| 5 | outputSchema | array<ref> | — | →38 EIG-VAR-* + grain | M | Outputs bind to the variable register — no ad-hoc variables (V2) |
| 6 | assumptions | array<json> | — | {id, statement, basis, sensitivity} | M | Enumerated, each with basis (literature/data/expert) |
| 7 | referenceData | array<ref> | — | →43 EIG-DSET-* / →37 streams | C | Real data used to fit/calibrate (mandatory for GT-STAT/GT-ML/GT-PERT) |
| 8 | fidelityTier | enum | — | §3.2 | M | Claimed tier |
| 9 | fidelityEvidence | json | — | metrics + assessment refs (§5.4) | M | Proof of tier (V4) |
| 10 | privacyMechanism | json | — | {method: dp_epsilon/k_anon/copula_decoupling/none, params, dpiaRef} | C | Mandatory when referenceData contains personal grain (A5) |
| 11 | seedPolicy | json | — | {rngSeedRequired: TRUE, seedLogging: TRUE} | M | Reproducibility (V5) |
| 12 | codeArtefact | string | — | repo ref + version + SHA-256 | M | Pinned implementation (35 A6 hash discipline) |
| 13 | computeProfile | json | — | {runtime, resources} | O | Operational planning |
| 14 | attribution | string | — | e.g. "OPTICS project, PULSE Programme" for PSR-lineage generators | C | 31 A6 attribution rule |
| 15 | owner / steward / reviewer / approver | ref | — | →16 roles | M | Catalogue duties (16 A6) |
| 16 | version / status | semver/enum | — | DRAFT, ACTIVE, DEPRECATED, RETIRED | M | Lifecycle |

### 5.2 Scenario (`EIG-SYN-SCN-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | scenarioID | string | `EIG-SYN-SCN-\d{5}` | M | ID |
| 2 | generatorRef | ref | →EIG-SYN-GEN-* @version | M | Pinned generator |
| 3 | narrative | string | ≤2000 chars | M | What-if being generated |
| 4 | population | json | archetype mix (07), geography (08), network (13), counts | M | Who/where simulated |
| 5 | period / resolution | interval/duration | ISO 8601 | M | Coverage and grain |
| 6 | weatherCoupling | ref | →09 series (actual year / TRY / stochastic) | C | Weather driver |
| 7 | marketCoupling | ref | →20/21 price series or synthetic price model | C | Price driver |
| 8 | stressors | array<json> | {type, magnitude, timing} | C | Rare-event injections |
| 9 | rngSeed | int/string | logged | M | Reproducibility |
| 10 | linkedPlanningScenario | ref | →89 EIG-SCN-* | C | Planning-study linkage |
| 11 | version / status | semver/enum | — | M | Lifecycle |

### 5.3 Synthetic dataset instance (`EIG-SYN-DS-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | synDatasetID | string | `EIG-SYN-DS-\d{5}` | M | ID |
| 2 | scenarioRef | ref | →EIG-SYN-SCN-* @version | M | Generating run |
| 3 | generatedAt / generatedBy | datetime/ref | UTC / →17 USR-S-* | M | Provenance (39 MC-LIN/PRV) |
| 4 | streamRegistration | ref | →37 EIG-DS-* (DSP-SYN provider) | M | Every output enters via 37 (A1) |
| 5 | entityRefs | array<ref> | →38 EIG-DE-* (dataClass SYNTHETIC per value) | M | Materialised entities |
| 6 | datasetPackage | ref | →43 EIG-DSET-* | C | If packaged for release |
| 7 | fidelityAssessment | ref | §5.4 record | M | Run-level fidelity result |
| 8 | environment | enum | sandbox (default), production-labelled | M | production-labelled only for visibly-marked public display (hyperGrid); never mixed into production analytics (A4) |
| 9 | retention | duration | per scenario purpose | M | Disposal schedule |

### 5.4 Fidelity assessment metrics (recorded per generator version + per run)

| # | Metric | Applies to | Definition |
|---|---|---|---|
| 1 | marginalDistance | SF-2+ | KS / Wasserstein per variable vs reference, tolerance declared |
| 2 | correlationError | SF-2+ | ‖corr_syn − corr_ref‖ over declared variable pairs |
| 3 | autocorrError | SF-3 | ACF/PACF deviation at declared lags (load: 1, 48, 336) |
| 4 | physicsConsistency | GT-PHYS/HYB | balance/COP/ramp checks via 40 RC-PLAUS rules applied to synthetic output |
| 5 | downstreamUtility | SF-3 | TSTR (train-synthetic-test-real) performance ratio vs train-real baseline |
| 6 | privacyLeakage | when seeded | nearest-neighbour distance ratio / membership-inference AUC ≤ declared bound |

## 6. Controlled Vocabularies
generatorType (§3.1), fidelityTier (§3.2), use matrix rows (§3.3 — extension requires approver sign-off), privacy mechanisms, environment values. dataClass for all outputs: **SYNTHETIC, fixed** (no vocabulary choice).

## 7. Applicability Rules

- **A1 — Registered generators only.** Synthetic data from unregistered generators is inadmissible; ingestion (37) rejects DSP-SYN streams lacking a generatorRef.
- **A2 — Class immutability.** Every value carries dataClass=SYNTHETIC at generation (38 A6 per-value class); downstream derivations over synthetic inputs remain SYNTHETIC-tainted in lineage (39 A3) — COMPUTED-over-SYNTHETIC is flagged `syntheticLineage=TRUE` and inherits the §3.3 forbidden uses.
- **A3 — Hard exclusions.** Settlement, RD evidence, compliance/carbon disclosure, twin F3 calibration: forbidden at any fidelity tier.
- **A4 — Sandbox default.** Synthetic data lives in environment=sandbox (firewall chain 20 A2 ⇄ 37 A5 ⇄ 38 V9 ⇄ 39 A5). The single exception: visibly-labelled public display (hyperGrid provenance envelope shows SYNTHETIC per series) — never blended into production analytics or decision paths.
- **A5 — Privacy duty when seeded.** Generators fitted on personal-grain data (38 A7) MUST declare a privacy mechanism with quantified leakage bound (§5.4 m6) and DPIA reference; cohort floor n≥10 applies to any reference-data conditioning.
- **A6 — Claimed tier = proven tier.** fidelityTier is the highest tier with passing evidence (V4); marketing a higher tier is a governance breach.
- **A7 — Seeded reproducibility.** Every run logs rngSeed + generator version + scenario version; identical triple ⇒ identical output (V5).
- **A8 — Training augmentation transparency.** Models trained with synthetic share >0% disclose the share and TSTR evidence in model metadata (39 MC-LIN); a real-only ablation result is retained in validation (47).

## 8. Validation Rules

- **V1:** generatorID/scenario/instance ID patterns; one ACTIVE version per generator name.
- **V2:** outputSchema variables resolve in the 38 register; unknown variables rejected.
- **V3:** GT-STAT/GT-ML/GT-PERT require referenceData refs; GT-PHYS requires physics assumption set ≥ declared minimum.
- **V4:** fidelityEvidence metrics present and within declared tolerances for the claimed tier; missing metric ⇒ tier demotion.
- **V5:** reproducibility audit: quarterly re-run of a sampled scenario must hash-match prior output.
- **V6:** environment of all instance entities = sandbox unless production-labelled display exemption documented (A4).
- **V7:** privacyLeakage bound satisfied where A5 applies; failure ⇒ instance quarantined (40 flag).
- **V8:** syntheticLineage taint propagation verified across 39 lineage DAG (no laundering path).
- **V9:** attribution string present for PSR/PULSEse-lineage generators (OPTICS/PULSE rule).
- **V10:** retired generators: instances remain queryable for audit but closed to new runs.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 37 data_source | DSP-SYN provider class; stream registration mandatory; EIG-DS-09102 pattern |
| 38 data | outputs bind to variable register; per-value SYNTHETIC class; sandbox V9 |
| 39 metadata | EIG-MET-STD-SYN profile; generator attribution; lineage taint |
| 40 data_quality | physics-consistency via RC-PLAUS; quarantine on privacy failure |
| 43 dataset | packaging of synthetic releases with access rules + visible labelling |
| 44/50/47 | training augmentation disclosure; TSTR validation; ablation retention |
| 20 market / 31 platform | NEST sandbox (EIG-MKT-SBX-01, EIG-PLT-NEST) consumes SF-2+ instances |
| 07/09/13 | population archetypes, weather coupling, network topologies for scenarios |
| 89 scenario | planning scenarios reference generation scenarios |

## 10. Benchmarking Requirements
Generator league: fidelity metrics per version, TSTR utility, compute cost per simulated unit-year. Regression: new generator versions must not degrade fidelity metrics beyond tolerance (47-style gate).

## 11. Dataset Requirements
Synthetic releases (43) carry: visible SYNTHETIC labelling in title/description, generator+scenario refs, fidelity assessment, licence (typically open for SYNTH_GRID_IE-class outputs), and the §3.3 forbidden-use notice verbatim.

## 12. Feature Requirements
Features (41) computed over synthetic entities inherit syntheticLineage and are confined to sandbox/prototyping serving; they never enter SV-ON production contracts.

## 13. Model Requirements
44/50 obligations: augmentation share disclosure (A8); sandbox-trained models promoted to production only after real-data validation (47) — synthetic-only validation never suffices.

## 14. KPI Requirements
KPIs over synthetic data are sandbox KPIs: rendered with SYNTHETIC badge (hyperGrid envelope), excluded from organisational reporting (57 caveat codes).

## 15. Response Derivative Requirements
Synthetic data may *prototype* RD algorithms in sandbox (GT-PHYS thermal scenarios are explicitly useful for RD method development), but no RD value computed over SYNTHETIC inputs is RD evidence, feeds product gates (22 conf ≥0.8), or enters settlement baselines (27 BL-*). 52 MUST check syntheticLineage=FALSE on every evidence path.

## 16. Decision Requirements
Decision/dispatch paths (60/61) reject synthetic-tainted inputs outright; sandbox decision trials (NEST) run the same logic against synthetic feeds inside the firewall — the four-step handshake/integrity model governs promotion of logic, never of data.

## 17. Ontology Mapping
Generator ↔ PROV-O `prov:SoftwareAgent` + `prov:Activity` (generation run); synthetic instance ↔ `prov:Entity` with `eig:dataClass eig:Synthetic`; fidelity assessment ↔ DQV measurement; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `SyntheticGenerator`, `GenerationScenario`, `SyntheticDataset`; edges `GENERATED_BY`, `PARAMETERISED_BY`, `SEEDED_FROM`→reference datasets (taint-traceable), `REGISTERED_AS`→37 stream. Taint queries ("anything downstream of EIG-SYN-GEN-0004?") are first-class.

## 19. Digital Twin Mapping
Twins (32) consume synthetic inputs only in sandbox mode (32 A6); twin templates (33) may declare synthetic-input test suites for conformance testing; F-class fidelity claims never rest on synthetic comparisons.

## 20. Governance
Owner: EIG AI/ML Lead; approver sign-off for use-matrix extensions and tier claims; quarterly reproducibility audit (V5); privacy reviews with Data Management Lead where A5 applies. Public-facing synthetic releases (hyperGrid) reviewed against the no-fabrication standard before publication.

## 21. Versioning
Generators semver (fidelity evidence re-run on MAJOR/MINOR); scenarios versioned; instances immutable (regenerate, never edit). Catalogue 1.0.0.

## 22. Example Records

### EIG-SYN-GEN-0004 — SYNTH_GRID_IE national electricity generator (resolves 37 EIG-DS-09102 ref)
- type GT-HYB (statistical archetype profiles + network physics calibration); fidelityTier SF-3
- outputSchema: EIG-VAR-0102 active_import_kwh @ building×PT30M; network flows @ CP×PT30M (13 topology); 437M-row scale class
- referenceData: SEAI/CSO/EirGrid/ESB Networks public statistics (25-source register, hyperGrid provenance); no personal-grain seeding ⇒ privacyMechanism none required, A5 n/a
- assumptions: A-01 archetype mix per 07 BER distributions (basis: SEAI BER db); A-02 simultaneity per 30 (Aran 0.72 class evidence); A-03 network losses per 13 zone parameters; sensitivity notes per assumption
- fidelityEvidence: marginalDistance KS<0.05 per variable vs published aggregates; correlationError <0.08; autocorrError lags 1/48/336 within 10%; physicsConsistency RC-PLAUS pass 99.7%
- seedPolicy enforced; codeArtefact synth_grid_ie v2.1 sha256:…; environment sandbox; public display via hyperGrid with SYNTHETIC envelope per series
- duties: owner AI/ML Lead; steward Data Mgmt; reviewer external advisory; approver PI

### EIG-SYN-SCN-00031 — Aran winter peak stress scenario
- generator EIG-SYN-GEN-0004@2.1; population: EIG-GEO-CZ-IE-ISL, 07 island archetypes ×320 dwellings, network NZ-00017 (13)
- period 2026-12-01/2027-02-28 PT30M; weatherCoupling 09 stochastic cold-snap ensemble; stressors: {type: demand_spike, +18%, evening peaks} + {type: ferry_outage_supply_constraint}
- rngSeed 88421; linkedPlanningScenario EIG-SCN-… (89)

### EIG-SYN-DS-00102 — instance
- scenario EIG-SYN-SCN-00031; stream EIG-DS-09102 (37); entities EIG-DE-SYN-ARAN-PEAK-L2 (SYNTHETIC per value)
- fidelityAssessment pass SF-3; environment sandbox; retention P3Y; used by NEST market trial under EIG-MKT-SBX-01

## 23. Completion Criteria
☑ Generator/scenario/instance model with registration mandate ☑ No-fabrication immutability + lineage taint ☑ Fidelity tiers with metric evidence ☑ Use matrix with absolute exclusions (settlement/RD/disclosure) ☑ Privacy duty for seeded generators ☑ Seeded reproducibility ☑ EIG-SYN-GEN-0004 fully defined (resolves 37 ref) ☑ Sandbox firewall honoured end-to-end.
