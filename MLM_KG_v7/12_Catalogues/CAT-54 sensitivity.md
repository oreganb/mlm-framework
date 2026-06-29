---
type: catalogue
ontology: EIG-MLM
catalogue_no: 54
catalogue_id: EIG-CAT-054
entity: sensitivity
band: response_derivative
status: active
tags: [eig-mlm, catalogue, band:response_derivative, entity:sensitivity]
aliases: ["EIG-CAT-054", "Catalogue 54", "sensitivity"]
---

# CAT-54 — Sensitivity

> **EIG-CAT-054** · band: **Response Derivative Family** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-38 data]] · [[CAT-41 feature]] · [[CAT-44 model]] · [[CAT-47 validation]]
**Used by (downstream):** [[CAT-44 model]] · [[CAT-49 explainability]] · [[CAT-52 response_derivative]] · [[CAT-58 objective]] · [[CAT-60 decision_intelligence]] · [[CAT-70 model_selection]] · [[CAT-89 scenario]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-054`
**Entity prefix:** `EIG-SEN-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 38 data, 41 feature, 44 model, 47 validation
**Used by (downstream):** 44 model design, 49 explainability, 52 response_derivative (priors only), 58 objective, 60 decision_intelligence, 70 model_selection, 89 scenario

---

## 1. Definition

The **Sensitivity Catalogue** registers analytical sensitivity relationships: quantified `∂(target)/∂(input)` measures derived by analysis — model-based sensitivity analysis, simulation perturbation, regression coefficients, elasticity estimation, or analytical differentiation — each with method, units, confidence, context, and validity range. Sensitivities answer "what matters and how much, within these bounds?" for **model design and decision analysis**.

**The RD boundary (critical):** a sensitivity is *not* a Response Derivative (52). RDs are decision-grade, evidence-backed conditional responsiveness measured from real stimulated events, gated for products and settlement. Sensitivities are analytical quantities — they may come from simulations, fitted models, or synthetic scenarios; they inform design, screening, scenario analysis, and RD *priors* — but they never gate products, never enter settlement, and never substitute for RD evidence (A1). A sensitivity that graduates to measured-event backing under a 52 spec *becomes* an RD record there; it does not get promoted in place.

> **v1.1 (2026-06-13) — boundary clarification.** Both a sensitivity and an RD share the form ∂(output)/∂(input). The distinction is *not* the maths, it is the basis: a sensitivity is computed analytically (the "220 kph" speedometer figure — what the system *could* do per the model); an RD is the same derivative evaluated from **realised behaviour under prevailing conditions** (how fast the car *actually* goes right now), evidence-backed and gated (52 §1, A9). An analytical sensitivity is the legitimate *prior* for an RD, never its substitute — calling an analytical rate-of-change an "RD" is the nameplate-leak the RD catalogue's V11 gate exists to catch.

## 2. Scope

**In scope:** sensitivity record schema; method taxonomy (local/global, analytical/empirical/simulation); units and dimensional discipline; validity ranges and extrapolation prohibition; uncertainty/confidence; context conditioning (lighter than 52 — declared, not gated); screening matrices (which inputs matter per target); elasticities (price/behavioural); use-boundary enforcement vs 52.

**Out of scope:** RD records and gates (52), feature importance as explanation (49 — attribution of a *model's* behaviour; sensitivities here are claims about the *system*, via stated methods), scenario narratives (89).

## 3. Classification Structure

### 3.1 Method classes (`method`)

| Code | Method | Nature | Typical source |
|---|---|---|---|
| SM-AN | Analytical | closed-form differentiation of L1 models | physics equations (e.g. ∂HeatLoss/∂U = A·ΔT) |
| SM-LOC | Local perturbation | one-at-a-time finite difference around an operating point | simulation (32 twin runs), model probes |
| SM-GSA | Global sensitivity analysis | variance-based (Sobol indices), Morris screening | simulation campaigns over input distributions |
| SM-REG | Regression-derived | fitted coefficients/partial effects with CIs | L2 models on observational data (confounding caveat mandatory) |
| SM-ELAS | Elasticity | % change target per % change input | price/behavioural studies (21/25 data) |
| SM-ADJ | Adjoint/autodiff | gradients through differentiable models | L3 models |

### 3.2 Sensitivity kinds (`kind`)

| Code | Kind | Output form |
|---|---|---|
| SK-PT | Point sensitivity | single ∂T/∂I at operating point + validity box |
| SK-CURVE | Sensitivity curve | ∂T/∂I as function of input level (nonlinearity explicit) |
| SK-IDX | Importance index | Sobol S1/ST, Morris μ*/σ — dimensionless rankings |
| SK-ELAS | Elasticity | dimensionless %/% |

### 3.3 Evidence basis (`basis`)

| Code | Basis | Confidence ceiling |
|---|---|---|
| SB-PHYS | Analytical physics | high (within model validity) |
| SB-SIM | Simulation (incl. synthetic 42 — permitted here, flagged) | medium-high; SYNTHETIC basis flagged, never claims observational support |
| SB-OBS | Observational regression | medium (confounding-caveated) |
| SB-EXP | Experimental/measured | high — and a candidate for 52 graduation where a spec exists |

## 4. Hierarchy

```
Sensitivity record (EIG-SEN-*)        ← target × input × method × context × validity range
  └── Screening matrix views          ← per target: ranked input sensitivities (model design aid)
```

## 5. Field Groups & Fields

### 5.1 Sensitivity record (`EIG-SEN-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | sensitivityID | string | — | `EIG-SEN-\d{6}` | M | ID |
| 2 | targetVariable | ref | — | →38 EIG-VAR-* or KPI ref (57) | M | What changes |
| 3 | inputVariable | ref | — | →38 EIG-VAR-* / parameter ref (05/11 registries) | M | What is varied |
| 4 | kind | enum | — | §3.2 | M | Output form |
| 5 | method | enum | — | §3.1 + procedure detail/refs | M | How derived |
| 6 | basis | enum | — | §3.3 | M | Evidence nature (SB-SIM synthetic flag where 42 inputs used) |
| 7 | sourceRef | ref | — | →44 model release / 32 twin run / study record | M | Producing analysis (reproducible) |
| 8 | value | json | sensUnit | per kind: point + CI / curve points / indices | M | The quantity with uncertainty |
| 9 | sensUnit | string | UCUM | [target]/[input] or dimensionless (SK-IDX/ELAS) | M | V2 dimensional check |
| 10 | context | json | — | declared conditioning (operating point, season, configuration) | M | Where it was derived |
| 11 | validityRange | json | input units | {inputMin, inputMax, otherBounds} | M | Extrapolation boundary (A3) |
| 12 | confidence | decimal | — | [0,1], ceiling per basis (§3.3) | M | Honest scoring |
| 13 | confoundingNote | string | — | mandatory for SM-REG/SB-OBS | C | Causal caveat (A4) |
| 14 | interactions | array<json> | — | known input interactions (GSA second-order) | C | Nonadditivity disclosure |
| 15 | rdLinkage | json | — | {feedsPriorOf: →52 spec, graduationCandidate: bool} | C | RD boundary bookkeeping (A1) |
| 16 | estimatedAt / validUntil | datetime | — | UTC / source-model expiry coupling | M | Currency |
| 17 | environment | enum | — | production-analysis, sandbox | M | Firewall tag |
| 18 | version / status | semver/enum | — | ACTIVE, STALE, SUPERSEDED | M | Lifecycle |

### 5.2 Screening matrix (derived view, per target)

| # | Field | Description |
|---|---|---|
| 1 | targetRef | The screened target |
| 2 | rankedInputs | inputs ordered by |sensitivity| or ST index, with kind/basis badges |
| 3 | negligibleSet | inputs below screening threshold (model-design pruning aid, 44 §12) |
| 4 | coverageNote | inputs not yet analysed (honesty about gaps) |

## 6. Controlled Vocabularies
method (§3.1), kind (§3.2), basis (§3.3), lifecycle. Sign convention: sensitivities follow natural variable signs (no 30 §6 direction overlay — that is RD-specific; a translation note is mandatory where a sensitivity feeds an RD prior).

## 7. Applicability Rules

- **A1 — The RD boundary.** Sensitivities never: gate products (22), enter bids as capability claims (25), feed settlement (27), or substitute for 52 records in dispatch decisions. Permitted RD interaction: seeding priors (52 A6 transferPolicy) with the linkage recorded, and flagging graduation candidates (SB-EXP records meeting a 52 spec's evidence bar route to 52 estimation — the record itself stays here as analysis).
- **A2 — Method-stated, reproducible.** Every record cites its producing analysis (sourceRef) with enough pinning to reproduce (50-style discipline for simulation campaigns).
- **A3 — Validity ranges are hard.** Consumers must check input ranges; extrapolated use is flagged invalid at consumption (60/89 runtime checks). SK-PT records near nonlinear regimes should be superseded by SK-CURVE.
- **A4 — Causal honesty.** SM-REG/SB-OBS records carry confounding notes; renderings phrase as association-derived; causal claims require experimental basis or identified causal design (declared).
- **A5 — Synthetic transparency.** SB-SIM records with 42 inputs flag synthetic basis (taint visible); useful for design, never claimed as observational evidence (no-fabrication chain).
- **A6 — Currency coupling.** Records derived from a model release go STALE when that release is superseded/invalidated (44 A4 notifications); twin-derived records couple to twin fidelity status (32).
- **A7 — Screening drives design honesty.** 44 model definitions justify excluded inputs against the screening matrix (negligible or covered) — "we didn't think of it" becomes visible as a coverage gap.

## 8. Validation Rules

- **V1:** target/input refs resolve; sensUnit dimensionally = [target]/[input] (or dimensionless for SK-IDX/ELAS).
- **V2:** value uncertainty present (CI or index SE); point values without uncertainty rejected.
- **V3:** validityRange present and consistent with the analysis sweep actually performed.
- **V4:** confidence ≤ basis ceiling (§3.3 mechanical).
- **V5:** confoundingNote present where required (A4).
- **V6:** sourceRef reproducibility sampled in quarterly audits.
- **V7:** SK-CURVE monotone-segment declarations consistent with curve points.
- **V8:** rdLinkage targets resolve in 52; graduation candidates meet the cited spec's variable pair.
- **V9:** records immutable; refinement supersedes.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 52 RD | boundary (A1); prior seeding; graduation routing — never substitution |
| 44 model | design screening (A7); source releases; currency coupling |
| 49 explainability | distinct concept (model attribution vs system sensitivity); cross-reference where both exist for the same pair |
| 32 digital_twin | simulation campaigns as sources; fidelity coupling |
| 42 synthetic_data | SB-SIM synthetic flagging |
| 58/60 | objective weighting and decision robustness analysis consume sensitivities within validity ranges |
| 89 scenario | scenario stress design uses GSA rankings |
| 05/11 | parameter sensitivities (∂load/∂U-value, ∂COP/∂T) inform retrofit/asset analysis |
| 57 KPI | KPI sensitivity to drivers (reporting context) |

## 10. Benchmarking Requirements
Coverage metrics: % of 44 model input candidates screened per target; agreement checks where multiple methods cover the same pair (analytical vs GSA discrepancies investigated).

## 11. Dataset Requirements
Simulation campaign outputs archived as datasets (43, SB-SIM provenance); observational study data cited at RELEASED versions.

## 12. Feature Requirements
Screening matrices inform 41 feature engineering priorities; high-ST inputs without registered features are flagged opportunities.

## 13. Model Requirements
44 §12/§16 hooks: input justification vs screening; assumption sensitivity (how outputs move if assumptions slip) recorded as SM-LOC studies per major release.

## 14. KPI Requirements
KPI driver analyses (57/77 reporting): "EUI sensitivity to occupancy hours" class records, rendered with basis badges and validity ranges — no naked coefficients in reports.

## 15. Response Derivative Requirements
The division of labour, stated once more for downstream clarity: 54 = analytical "what would matter", 52 = evidenced "what does respond, gated for action". Sensitivities seed RD priors (52 A6), identify promising flexibility (which subjects merit RD estimation events, 56 campaign design), and contextualise RD results (is the measured RD consistent with physics-based sensitivity? — discrepancy triggers investigation).

## 16. Decision Requirements
60 uses sensitivities for: decision robustness (how outcome rankings move within input uncertainty), option screening (retrofit ranking gradients, 05 §16), and guardrail design — always within validity ranges (A3 runtime check), never as dispatch capability claims (A1).

## 17. Ontology Mapping
Sensitivity ↔ EIG ontology `eig:Sensitivity` (sibling, not subclass, of `eig:ResponseDerivative` — the boundary is ontological too); method/basis as individuals; QUDT-typed values; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `Sensitivity`; edges `OF_TARGET`/`TO_INPUT`→38, `DERIVED_BY`→source, `SEEDS_PRIOR`→52 spec, `SCREENS_FOR`→44 definition. The screening matrix is a KG view per target.

## 19. Digital Twin Mapping
Twins are the primary SM-LOC/SM-GSA engines: perturbation campaigns on calibrated twins (F3) yield SB-SIM records at the higher-confidence end; what-if UI surfaces sensitivity curves with validity shading.

## 20. Governance
Owner: AI/ML Lead; methodology review for new method admissions; quarterly coverage review against 44 register; boundary breaches (A1 violations found in 22/25/27 citations) are severity-1 audit findings.

## 21. Versioning
Records immutable with supersession; screening views regenerate. Catalogue 1.0.0.

## 22. Example Records

### EIG-SEN-000412 — heating load sensitivity to wall U-value (SB-PHYS)
- target heating_load_kwh (38); input wall U (05 assembly param); kind SK-PT; method SM-AN: ∂Q/∂U = A·HDD·24/1000
- value 212 m² × context HDD ⇒ 1,540 kWh/yr per W/m²K (ci from area survey ±3%); validity: U ∈ [0.15, 0.6], steady-state regime
- context: EIG-BLD-000877 winter; confidence 0.92; rdLinkage none (design quantity); feeds retrofit ranking (05 §16)

### EIG-SEN-000877 — GSA screening for residential HH load (SB-SIM)
- target EIG-VAR-0102; method SM-GSA Sobol over calibrated twin ensemble (32 F3) + 42 weather sampling (synthetic-basis flagged)
- indices: hdh ST 0.41, occupancy ST 0.24, tariff-shift behaviour ST 0.09, solar gain ST 0.07, interactions hdh×occ S2 0.06; negligible set documented
- consumed by 44 SER-02 input justification (A7) and 41 feature priorities

### EIG-SEN-001203 — price elasticity of evening consumption (SM-ELAS, SB-OBS)
- target evening kWh; input ToU price; elasticity −0.11 [−0.16, −0.06]; basis observational (EIG-DSET-ARAN-ELEC-HH@4.1 + tariff change natural experiment)
- confoundingNote: weather/season controls applied, engagement-selection caveat; validity: price Δ ≤ 30%
- rdLinkage: feedsPriorOf RDD-PRC spec (52) — prior seed only; graduation would require stimulated price events (56)

## 23. Completion Criteria
☑ Method/kind/basis taxonomy with confidence ceilings ☑ Units + validity-range hard bounds ☑ The RD boundary explicit and enforced (A1, ontological sibling) ☑ Confounding/synthetic honesty ☑ Screening matrices wired to model design ☑ Currency coupling to source models/twins ☑ Examples span physics, GSA, and elasticity with correct boundary handling.
