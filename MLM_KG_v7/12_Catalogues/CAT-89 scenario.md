---
type: catalogue
ontology: EIG-MLM
catalogue_no: 89
catalogue_id: EIG-CAT-089
entity: scenario
band: governance
status: active
tags: [eig-mlm, catalogue, band:governance, entity:scenario]
aliases: ["EIG-CAT-089", "Catalogue 89", "scenario"]
---

# CAT-89 — Scenario

> **EIG-CAT-089** · band: **Aggregation & Governance** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-32 digital_twin]] · [[CAT-42 synthetic_data]] · [[CAT-44 model]] · [[CAT-54 sensitivity]] · [[CAT-58 objective]] · [[CAT-65 benchmark]] · [[CAT-87 district]]
**Used by (downstream):** [[CAT-66 reinforcement_learning]] · [[CAT-77 report]] · [[CAT-90 project]] · [[CAT-91 policy]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-089`
**Entity prefixes:** `EIG-SCN-` (scenarios), `EIG-SCN-R-` (scenario runs)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (planning authority) with AI/ML Lead
**Depends on (upstream):** 32 digital_twin, 42 synthetic_data, 44 model, 54 sensitivity, 58 objective, 65 benchmark, 87 district
**Used by (downstream):** 90 project (scenario-framed pilots), 91 policy (pathway analysis), 77 report (scenario results, labelled), 66 (stress scenarios feed environments)

---

## 1. Definition

The **Scenario Catalogue** registers planning and simulation scenarios (`EIG-SCN-*`): named, versioned bundles of **assumptions**, **inputs**, **events**, a **time horizon**, bound **models**, and produced **outputs** — and their executions (`EIG-SCN-R-*`). A scenario is a coherent "what if": a retrofit pathway for a district, a 2030 tariff landscape, an island electrification trajectory, a cold-snap stress case. Purpose: **simulation and planning** with provenance — every projected number traces to a scenario run whose assumptions are inspectable, whose models are pinned, and whose outputs are permanently labelled as projections (A2). Sense discipline (82-managed homonym): this catalogue owns *planning/simulation scenarios*; data-generation scenarios are 42 EIG-SYN-SCN (referenced as input machinery here, and 42's `linkedPlanningScenario` field points back to EIG-SCN-* records); response events are 56.

## 2. Scope
**In scope:** scenario schema (the spec's seven: ID, assumptions, inputs, events, time horizon, models, outputs); scenario families (pathway, stress, market, climate, policy-what-if); assumption registers (typed, sourced, sensitivity-linked 54); event scripts (timed perturbations within runs); run reproducibility (pinned everything); baseline binding (65/current-state anchors); comparison sets (scenario ensembles); output labelling and consumption rules. **Out of scope:** synthetic data generation mechanics (42), twin execution machinery (32), the decisions scenarios inform (60 DC-PLAN consume results), project planning content (90).

## 3. Classification Structure

### 3.1 Scenario families (`family`)
SF-PATH (pathways: retrofit/electrification trajectories — the 87 district planning core) · SF-STRESS (extremes: cold snaps, outages, price spikes — feeds 66 ENV-SIM stressors) · SF-MKT (market/tariff landscapes, 20/21 what-ifs) · SF-CLM (climate projections, 09-anchored) · SF-POL (policy what-ifs: incentive/obligation changes, 91 pathway analysis).

### 3.2 Assumption classes (typed, per register)
AS-TECH (technology cost/performance curves) · AS-BEH (uptake/behaviour rates — confidence-honest) · AS-MKT (price/tariff trajectories) · AS-CLM (weather years/projections) · AS-POL (instruments assumed, 91/92 refs) · AS-STR (structural: stock growth, boundary changes). Every assumption: value/trajectory + source + uncertainty note + 54 sensitivity link where analysed (which assumptions matter is itself evidence, A4).

### 3.3 Output classes
Projections (time-series/indicator trajectories, COMPUTED class, scenario-labelled) · Comparisons (vs baseline/ensemble members) · Derived recommendations (always routed through 60 DC-PLAN as advisory inputs, never auto-acted).

## 4. Hierarchy
```
Scenario (EIG-SCN-*: assumptions + inputs + events + horizon + models)
  └── Runs (EIG-SCN-R-*: pinned execution → outputs, labelled)
        └── ensembles/comparison sets → planning consumers (60/77/90/91)
```

## 5. Field Groups & Fields

### 5.1 Scenario (`EIG-SCN-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | scenarioID | string | `EIG-SCN-\d{4}` | M | Spec: Scenario ID |
| 2 | name / family | string/enum | §3.1 | M | Identity |
| 3 | subjectScope | ref | →02/87/88/13/30 (what is simulated: district, site, portfolio, network) | M | The simulated world |
| 4 | assumptions | array<json> | §3.2 typed register: {class, statement, value/trajectory, source, uncertainty, sensitivityRef →54} | M | Spec: Assumptions — inspectable, sourced |
| 5 | inputs | array<ref> | baseline state (register snapshots @versions, 65 anchors), weather/profile inputs (→09 / →42 EIG-SYN-SCN generated sets, taint-carried) | M | Spec: Inputs — pinned |
| 6 | events | array<json> | scripted perturbations: {at, what (typed: outage 13, price spike 21, cold snap 09, policy change 91), magnitude} | M | Spec: Events |
| 7 | timeHorizon | json | {start, end, resolution} (P1Y–P25Y pathways; days–weeks stress) | M | Spec: Time horizon |
| 8 | models | array<ref> | →44 releases @versions (SER-01/02/03/10/12 typical; twins 32 as engines) | M | Spec: Models — pinned releases |
| 9 | objectiveLens | ref | →58 set where outcomes are scored (pathway ranking) | C | Evaluation frame |
| 10 | outputSpec | json | indicators produced (→57 formula reuse at scenario grain), comparison structure | M | Spec: Outputs — declared up front |
| 11 | baselineRef | ref | →companion baseline scenario / current-state anchor | M | Comparability |
| 12 | owner / version / status | — | standard | M | Governance |

### 5.2 Scenario run (`EIG-SCN-R-*`)
runID · scenarioRef@version · executionPins {model releases, input snapshots, seeds (42 A7), engine 32/31} · outputs (immutable artefacts, 43-packaged; **scenario-labelled metadata on every series**, A2) · ensembleRef where member · executedAt/by. Supersede-never-edit.

## 6. Controlled Vocabularies
Families (§3.1), assumption classes (§3.2), event types (closed, register-typed), output classes (§3.3). Scenario sense note (82 homonym register) restated in §6 per house rule.

## 7. Applicability Rules
- **A1 — Pinned or it isn't planning.** Runs pin everything (models@versions, input snapshots, seeds); an unreproducible projection is inadmissible in reports/decisions (77 A1 / 60 input rules).
- **A2 — Projections are labelled forever.** Every output series/indicator carries scenario provenance (COMPUTED class + scenarioRef); scenario results never blend into operational truth — dashboards/reports render them with simulated-state labelling (75 §19 / 77 A3); positivity or performance *claims* never cite scenarios (87 A2 chain).
- **A3 — Assumptions are the contract.** Consumers (panels, partners, policy users) read the assumption register first; changing an assumption is a scenario version, not a re-run note.
- **A4 — Sensitivity-honest planning.** Pathway scenarios link key assumptions to 54 analyses (which assumptions move the answer); ensembles span the sensitive axes rather than single-point storytelling.
- **A5 — Synthetic in, taint carried.** 42-generated inputs ride with syntheticLineage (the taint closure 84 EIG-RR-0042 reaches scenario outputs) — labelled simulation on labelled inputs, end to end.
- **A6 — Recommendations route through decisions.** Scenario-derived advice enters operations only via 60 DC-PLAN (advisory) — planning informs, governance disposes.

## 8. Validation Rules
V1 schema completeness (seven spec fields); refs resolve; V2 assumption register typed/sourced; sensitivity links where family demands (SF-PATH); V3 runs reproducible (pin audit, sampled re-runs); V4 output labelling mechanical (metadata on every artefact); taint propagation verified; V5 baseline binding present; cross-scenario comparisons same-method (87 comparability spirit); V6 ensemble declarations complete (members, spanned axes); V7 events typed against registers; V8 scenarios/runs immutable per version.

## 9. Relationships to Other Catalogues
42 (input generation; linkedPlanningScenario backlink; taint) · 32 (twin engines; what-if hosting) · 44/54 (pinned models; assumption sensitivity) · 87/88/30 (subjects: district pathways — the Inis Mór electrification scenarios; site/portfolio planning) · 58/57/65 (scoring lens; indicator formulas; baselines) · 91 (policy what-ifs; pathway evidence) · 90 (project scenario framing) · 66 (SF-STRESS feeds ENV-SIM) · 77 (labelled reporting) · 60 (DC-PLAN consumption).

## 10. Benchmarking Requirements
Scenario practice health: pin-audit pass rate, assumption-register completeness, ensemble coverage of sensitive axes, projection-vs-realised retrospectives where horizons mature (planning humility, recorded).

## 11. Dataset Requirements
Run outputs as 43 packages (DK-ANALYTIC, scenario-labelled); assumption registers exported with publications.

## 12. Feature Requirements
Scenario indicators reuse 57 formulas at scenario grain — projected EUI computes like measured EUI (one formula, two provenances).

## 13. Model Requirements
Pinned 44 releases; scenario-driven model gaps (capabilities pathways need) feed the model census backlog.

## 14. KPI Requirements
Indicator reuse (§12); projected-vs-realised KPI retrospectives (§10) registered.

## 15. Response Derivative Requirements
Flexibility in scenarios uses RD-derived deliverable arithmetic (52 A5 via 55/30) at *current evidence* plus assumption-classed uptake for future stock — projected flexibility never masquerades as evidenced flexibility (the AS-BEH class carries the honesty).

## 16. Decision Requirements
A6: DC-PLAN decisions cite scenario runs + assumption registers; investment rankings show ensemble spread, not single-path certainty (49 renderings include assumption caveats).

## 17. Ontology Mapping
Scenario ↔ eig:PlanningScenario (distinct class from eig-dat generation scenarios — the homonym ontologically separated); runs ↔ PROV activities with pinned-usage qualifications; owned by 79.

## 18. Knowledge Graph Mapping
Nodes `PlanningScenario`, `ScenarioRun`; edges `ASSUMES` {class}, `SIMULATES`→subjects, `PINNED`→44/inputs, `PRODUCED`→outputs {labelled}, `COMPARED_TO`→baseline, `INFORMS`→60/91. "What assumptions underlie this 2030 figure?" is one hop.

## 19. Digital Twin Mapping
Twins are the privileged scenario engines (32 what-if at scale); twin scenario state is partition-labelled; long-horizon pathway runs use twin ensembles per 89 pins.

## 20. Governance
Owner: PI; scenario reviews with domain owners (assumption scrutiny); publication-bound scenarios board-reviewed; retrospective programme (§10) annual.

## 21. Versioning
Scenarios semver (assumption changes = version); runs immutable. Catalogue 1.0.0.

## 22. Example Record

### EIG-SCN-0007 — Inis Mór electrification pathway (SF-PATH)
- subject EIG-DIS-0007 (87); horizon 2026–2040 annual; assumptions: AS-TECH HP cost curve (SEAI-sourced), AS-BEH uptake 3 ensemble levels (54-linked: uptake is the dominant sensitivity), AS-MKT tariff trajectory, AS-POL retrofit grant continuation (91 ref), AS-CLM TMY+projection years (09)
- inputs: district baseline snapshot @2026 + 42 weather sets (taint carried); models: SER-01 stock model + SER-10 island network + SER-12 investment sequencer (@pinned); events: subsea-cable constraint episode scripted (stress overlay)
- outputs: balance trajectory (87 method M-ISL-01 reused), self-sufficiency, peak vs cable rating, cost; ensemble: 3 uptake × 2 tariff; baseline: no-intervention companion
- consumption: 90 ODEON reporting (labelled projections), 60 DC-PLAN investment ranking with spread shown; runs EIG-SCN-R-00031..36 pinned ✓

## 23. Completion Criteria
☑ All seven spec fields (ID, assumptions, inputs, events, time horizon, models, outputs) ☑ Sense separated from 42/56 (vocabulary-managed, ontologically distinct) ☑ Pinned reproducibility + permanent projection labelling + taint carriage ☑ Assumption registers with sensitivity honesty and ensemble discipline ☑ Recommendations routed through governed decisions ☑ The Inis Mór pathway worked on the district stack.
