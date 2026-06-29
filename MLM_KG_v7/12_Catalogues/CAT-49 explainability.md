---
type: catalogue
ontology: EIG-MLM
catalogue_no: 49
catalogue_id: EIG-CAT-049
entity: explainability
band: models
status: active
tags: [eig-mlm, catalogue, band:models, entity:explainability]
aliases: ["EIG-CAT-049", "Catalogue 49", "explainability"]
---

# CAT-49 — Explainability

> **EIG-CAT-049** · band: **Models & Learning** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-41 feature]] · [[CAT-44 model]] · [[CAT-45 algorithm]] · [[CAT-47 validation]]
**Used by (downstream):** [[CAT-60 decision_intelligence]] · [[CAT-70 model_selection]] · [[CAT-75 dashboard]] · [[CAT-77 report]] · [[CAT-93 compliance]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-049`
**Entity prefixes:** `EIG-XAI-CFG-` (explanation configurations), `EIG-XAI-` (explanation records)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 41 feature, 44 model, 45 algorithm (AC-XAI methods), 47 validation
**Used by (downstream):** 60 decision_intelligence, 70 model_selection, 75 dashboard, 77 report, 93 compliance (AI transparency duties)

---

## 1. Definition

The **Explainability Catalogue** governs model transparency: the **configurations** (`EIG-XAI-CFG-*`) that bind an explanation method (45 AC-XAI) to a model release (44) with scope, audience, and presentation rules; and the **explanation records** (`EIG-XAI-*`) — generated explanations (global model behaviour, cohort patterns, or single predictions/actions) with feature attributions, rule renderings, confidence/faithfulness scores, and audience targeting. Purpose: **model transparency** — every opaque model with consequences can answer "why?", and every automated action (60) is traceable to a human-readable account.

## 2. Scope

**In scope:** explanation method application (SHAP, LIME, permutation importance, PDP/ICE, counterfactuals, surrogate rules, attention rendering); global/cohort/local scopes; audience classes and presentation duties; faithfulness/stability scoring; explanation freshness; intrinsic-model rendering (L1 physics, linear coefficients) as first-class explanations; compliance hooks (EU AI Act-style transparency, 93).

**Out of scope:** method theory (45), validation of model accuracy (47 — though faithfulness checks live here), dashboard rendering mechanics (75), causal inference claims (explanations attribute model behaviour, not real-world causation — A5).

## 3. Classification Structure

### 3.1 Explanation scopes (`scope`)

| Code | Scope | Question answered |
|---|---|---|
| XS-GLB | Global | What drives this model overall? (importance rankings, PDPs) |
| XS-COH | Cohort | How does behaviour differ for this segment? (per classification node 01, archetype 07) |
| XS-LOC | Local | Why this prediction/action for this subject at this time? (SHAP values, counterfactual) |

### 3.2 Method bindings (from 45 AC-XAI)

| Code | Method | Scope fit | Model fit |
|---|---|---|---|
| XM-SHAP | SHAP (Tree/Kernel/Deep) | GLB/COH/LOC | trees native (TreeSHAP), NN via DeepSHAP, model-agnostic Kernel |
| XM-PERM | Permutation importance | GLB | any |
| XM-PDP | PDP/ICE | GLB/COH | any (correlated-feature caveat declared) |
| XM-LIME | LIME | LOC | any (stability caveat; secondary to SHAP) |
| XM-CF | Counterfactual | LOC | classification/threshold decisions ("what would have changed the action") |
| XM-RULE | Surrogate rules | GLB | distilled rule set with declared fidelity |
| XM-INTR | Intrinsic rendering | GLB/LOC | L1 physics equations with live values; linear coefficients; Q-table slices (45 XC-INTR/XC-HYB) |

### 3.3 Audience classes (`audience`)

| Code | Audience | Presentation duty |
|---|---|---|
| AU-DS | Data scientist | full attributions, raw values, diagnostics |
| AU-OPS | Operator/energy manager | top-k drivers, plain units, action linkage |
| AU-OCC | Occupant/end user (17) | one-sentence reason, no jargon, no personal-data leakage (A6) |
| AU-REG | Regulator/auditor (93) | method disclosure, faithfulness evidence, decision traceability |
| AU-EXEC | Executive/report (77) | aggregated drivers, trend framing |

### 3.4 Faithfulness/confidence scoring

`explanationConfidence` ∈ [0,1] composed of: method faithfulness (surrogate fidelity / SHAP additivity check), stability (perturbation consistency), and coverage (share of output variance attributed). Low-confidence explanations are labelled as such — an unreliable explanation presented confidently is worse than none (A4).

## 4. Hierarchy

```
Configuration (EIG-XAI-CFG-*)            ← method × model release × scope × audience set
  └── Explanation record (EIG-XAI-*)     ← generated explanation instance
        └── Renderings (per audience class)
```

## 5. Field Groups & Fields

### 5.1 Configuration (`EIG-XAI-CFG-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | configID | string | — | `EIG-XAI-CFG-\d{4}` | M | ID |
| 2 | modelRelease | ref | — | →44 modelID@semver | M | Subject release |
| 3 | methods | array<enum> | — | §3.2 (≥1 primary) | M | Bound methods |
| 4 | scopes | array<enum> | — | §3.1 | M | Generated scopes |
| 5 | backgroundData | ref | — | →43 dataset slice (for SHAP baselines) | M | Reference distribution, pinned |
| 6 | featureNamesHuman | json | — | EIG-FT-* → human label + unit (41) | M | Translation layer (no raw feature codes to AU-OPS/OCC) |
| 7 | audiences | array<enum> | — | §3.3 + per-audience template refs | M | Rendering duties |
| 8 | cadence | json | — | {global: per release + quarterly, local: on_demand \| per_action} | M | Generation schedule (A2 freshness) |
| 9 | faithfulnessChecks | array<json> | — | declared checks + thresholds (§3.4) | M | Quality bar |
| 10 | privacyRules | json | — | suppression of personal-grain attributions for AU-OCC/public (38 A7) | M | A6 enforcement |
| 11 | status / version | enum/semver | — | — | M | Lifecycle |

### 5.2 Explanation record (`EIG-XAI-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | explanationID | string | `EIG-XAI-\d{8}` | M | ID |
| 2 | configRef | ref | →EIG-XAI-CFG-* @version | M | Configuration |
| 3 | scope | enum | §3.1 | M | This record's scope |
| 4 | subjectRef | ref | prediction ref (46 run step / 60 action / 44 release for global) | M | What is explained |
| 5 | method | enum | §3.2 | M | Method used |
| 6 | featureImportance | array<json> | [{featureRef (41), attribution, unit-context, direction}] ordered | M | Global/cohort rankings or local SHAP values |
| 7 | shapValues | json/ref | per-feature signed values + base value (additivity-checkable) | C | For XM-SHAP locals |
| 8 | ruleExplanations | array<string> | human-readable rules/conditions (XM-RULE/XM-INTR/XM-CF) | C | e.g. "shed authorised because headroom 3.2 kW > floor AND price > trigger" |
| 9 | counterfactual | json | minimal change set that flips outcome | C | XM-CF |
| 10 | explanationConfidence | decimal | [0,1] per §3.4 + component scores | M | Labelled honesty |
| 11 | renderings | array<json> | per audience: text/visual artefact refs | M | Audience outputs |
| 12 | generatedAt / modelReleaseCopy | datetime/string | UTC / audit copy | M | Provenance; explanation expires with release (A2) |
| 13 | environment | enum | production, sandbox | M | Firewall tag |

## 6. Controlled Vocabularies
scope (§3.1), methods (§3.2), audiences (§3.3), confidence components. Direction vocabulary for attributions: {increases, decreases} the named output in stated units — sign conventions fixed per output variable (38).

## 7. Applicability Rules

- **A1 — Pairing duty.** XC-POST releases with decisionCoupling≠none MUST have an ACTIVE configuration before promotion (45 A3 / 44 V7 chain); automated actions without explanation capability do not deploy.
- **A2 — Freshness.** Explanations are release-specific: a new release invalidates prior global explanations (regenerate per cadence); local explanations cite the exact release that produced the prediction/action.
- **A3 — Intrinsic models still explain.** XC-INTR models (L1 physics) render XM-INTR explanations (equation + live values) — "it's physics" is rendered, not asserted.
- **A4 — Confidence honesty.** explanationConfidence published with every record; below declared floor ⇒ rendering carries an explicit unreliability label; AU-REG renderings always include component scores.
- **A5 — Attribution ≠ causation.** All renderings phrase attributions as model behaviour ("the model weighted X"), not real-world causal claims, unless a separately validated causal analysis is cited.
- **A6 — Privacy in explanations.** AU-OCC and public renderings suppress attributions that would reveal personal-grain data of others (38 A7 cohort floor applies to cohort explanations); occupant-facing reasons reference the occupant's own context only.
- **A7 — Action traceability.** Every automated action (60) links to a local explanation record (generated per_action for automated coupling, on_demand acceptable for advisory) — the audit chain is action → explanation → release → validation (47).

## 8. Validation Rules

- **V1:** configuration references resolve; background data pinned to a RELEASED dataset version.
- **V2:** SHAP additivity: Σ shapValues + base ≈ prediction within tolerance (machine check per record sample).
- **V3:** featureImportance refs resolve in 41; human labels present for non-DS audiences.
- **V4:** surrogate rule fidelity ≥ declared threshold (XM-RULE) measured and recorded.
- **V5:** stability check: repeated local explanation under input perturbation within declared variance (LIME especially).
- **V6:** modelReleaseCopy matches the producing release of the explained prediction (no cross-release explanation).
- **V7:** privacy rules applied: automated scan for personal-grain leakage in AU-OCC/public renderings.
- **V8:** per_action coverage: 100% of automated actions have linked records (60 audit query).
- **V9:** confidence components recomputable from logged checks.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 44 model | explainabilityClass duties; pairing gate at promotion; release-bound freshness |
| 45 algorithm | AC-XAI method register; XC-* classes |
| 41 feature | attribution targets; human-label translation |
| 47 validation | faithfulness checks recorded; explanation quality in promotion review |
| 60 decision | per-action explanations; audit chain (A7) |
| 70 model_selection | global importance informs selection reviews; degenerate attributions (single-feature dominance) flag overfitting |
| 75/77 | renderings consumed by dashboards/reports with confidence labels |
| 93 compliance | AU-REG disclosure pack; AI-transparency obligations mapped |
| 03 occupancy / 17 user | occupant-facing reason design; engagement-aware phrasing |

## 10. Benchmarking Requirements
Explanation-quality metrics tracked per configuration: faithfulness scores, stability, generation latency (per_action explanations must not delay dispatch beyond LAT budget — pre-computation patterns where needed).

## 11. Dataset Requirements
Background datasets pinned (V1); cohort explanations computed on cohorts meeting n≥10 (38 A7); explanation archives stored as L3 entities for audit horizons matching their actions (60).

## 12. Feature Requirements
Attribution operates on the release's pinned feature set (41); engineered-feature attributions may be rolled up to human concepts via declared groupings (e.g. all lag features → "recent consumption pattern") with the grouping recorded.

## 13. Model Requirements
Per 44 §5.1.21: explainabilityClass drives duties; model cards (44 §5.2.9) embed the current global explanation summary; degenerate importance patterns trigger model review (70).

## 14. KPI Requirements
Transparency KPIs (57): per-action explanation coverage (V8), confidence-floor pass rate, occupant comprehension feedback where surveyed (03 feedback channels).

## 15. Response Derivative Requirements
RD-derived decisions explain in RD terms: local explanations for flex dispatch cite the RD record (52), its confidence/recency (22 gates), and the envelope arithmetic (30) — the occupant-facing rendering of "why did my heat pump pause?" is a first-class deliverable of this catalogue.

## 16. Decision Requirements
60 consumes: per-action local explanations (A7), counterfactual capability for contested actions ("what would have prevented this?"), and AU-REG packs for any disputed automated action — assembled as action → explanation → validation → contract entitlement (23) chain.

## 17. Ontology Mapping
Explanation ↔ MEX/MLS evaluation extensions + PROV (`prov:wasExplainedBy` EIG extension); attributions ↔ feature refs with QUDT-typed magnitudes; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `ExplanationConfig`, `ExplanationRecord`; edges `EXPLAINS`→{46 run step, 60 action, 44 release}, `ATTRIBUTES`→41 features {value, direction}, `RENDERED_FOR`→audience. "Top drivers of Aran portfolio forecast this month" is a KG aggregation over local records.

## 19. Digital Twin Mapping
Twin-embedded models expose explanations through the twin interface (32): zone-level "why is this zone pre-heating?" renders the controlling model's local explanation with twin-state context; twin what-if runs pair with XM-CF counterfactuals.

## 20. Governance
Owner: AI/ML Lead; AU-OCC rendering templates reviewed with stakeholder representatives (18); AU-REG pack format reviewed against 93 obligations; privacy scans (V7) audited by Data Management Lead.

## 21. Versioning
Configurations semver; records immutable, release-bound (A2 expiry). Catalogue 1.0.0.

## 22. Example Records

### EIG-XAI-CFG-0009 — FLEXENV automated-dispatch explanations
- release EIG-MDL-FLEXENV-01@1.4.2 (XC-HYB: L1 envelope + L3 correction); methods [XM-INTR primary, XM-SHAP for correction term]; scopes [GLB, LOC per_action]
- background: EIG-DSET-ODEON-ASSET-TELEM@2.3 reference slice; audiences [AU-DS, AU-OPS, AU-OCC, AU-REG]
- privacy: occupant renderings reference own building only; cadence: global per release, local per_action (pre-computed envelope arithmetic, ≤50 ms)

### EIG-XAI-00412877 — local record for a dispatch action
- subject: 60 action (shed 2.1 kW, EIG-DEV-001847, 2026-02-03T17:30Z); method XM-INTR + XM-SHAP
- rules: "Shed authorised: headroom 3.2 kW (envelope EIG-MDL-FLEXENV-01) > requested 2.1 kW; comfort floor 19 °C maintained (predicted zone min 19.8 °C); contract cap 2/week not exceeded (event 1 of 2, EIG-CON-001207); LFM price signal active."
- SHAP (correction term): thermal_mass_proxy +0.4 kW, recent_cop +0.2 kW (additivity ✓)
- confidence 0.93; renderings: AU-OCC "Your heat pump paused for 45 minutes to support the local grid; your home stays above 19 °C and you earn credit under your flexibility plan." AU-REG pack links EIG-VAL-00077 + RD record + contract clause
- audit chain complete (A7 ✓)

### EIG-XAI-00007 — global record for LOADFC-RES-01@2.0.1
- XM-SHAP global on background slice: top drivers load_lag_48 (32%), hdh_15p5 (21%), hour encodings (17%), tariff_rate_now (9%)…; PDPs attached; confidence 0.88; embedded in model card

## 23. Completion Criteria
☑ Scope/method/audience model with per-audience rendering duties ☑ Faithfulness/stability/confidence honesty ☑ Pairing gate for opaque automated models ☑ Per-action traceability chain ☑ Privacy rules in explanations ☑ Attribution≠causation discipline ☑ Examples cover automated dispatch (occupant + regulator renderings) and global forecasting drivers.
