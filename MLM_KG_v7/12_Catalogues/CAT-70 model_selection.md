---
type: catalogue
ontology: EIG-MLM
catalogue_no: 70
catalogue_id: EIG-CAT-070
entity: model_selection
band: rl_agents
status: active
tags: [eig-mlm, catalogue, band:rl_agents, entity:model_selection]
aliases: ["EIG-CAT-070", "Catalogue 70", "model_selection"]
---

# CAT-70 — Model Selection

> **EIG-CAT-070** · band: **RL, Context & Agents** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-44 model]] · [[CAT-45 algorithm]] · [[CAT-46 forecast]] · [[CAT-47 validation]] · [[CAT-51 model_benchmark]] · [[CAT-55 response_profile]] · [[CAT-69 context]]
**Used by (downstream):** [[CAT-44 model]] · [[CAT-46 forecast]] · [[CAT-60 decision_intelligence]] · [[CAT-62 control_strategy]] · [[CAT-66 reinforcement_learning]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-070`
**Entity prefixes:** `EIG-MS-` (selection rules/slots), `EIG-MS-I-` (selection decisions)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead (model board secretariat)
**Depends on (upstream):** 44 model, 45 algorithm, 46 forecast, 47 validation, 51 model_benchmark, 55 response_profile, 69 context
**Used by (downstream):** 44 (enforcement point for A3), 46 (definition routing), 60 decision_intelligence, 62 control_strategy, 66 (RL-CB selection problems)

---

## 1. Definition

The **Model Selection Catalogue** governs dynamic, adaptive model use: selection **slots** (`EIG-MS-*`) — named decision points where, for a given task × subject × context (69), one model release must be chosen from candidates — with selection criteria, performance-history bindings, confidence requirements, and switching rules; and selection **decisions** (`EIG-MS-I-*`) — logged choices with rationale. Purpose: **adaptive algorithm use** — the right model for the situation, switching as evidence shifts, without chaos: champion/challenger discipline (51 A7), hysteresis against thrashing, and full auditability of "which model answered, and why" (A1).

This catalogue is the *enforcement point* for model applicability (44 A3) and the operational consumer of the 45 selection matrix, 51 leaderboards, and 47 evidence freshness.

## 2. Scope
**In scope:** slot schema (the spec's six: context, candidate algorithms/models, selection criteria, performance history, confidence, switching rules); selection modes (static champion, context-routed, performance-adaptive, bandit-learned); rolling performance accounting; switching hysteresis and shadow requirements; fallback ladders per slot; decision logging. **Out of scope:** benchmark mechanics (51), validation evidence (47), context resolution (69), bandit training (66 RL-CB problems — registered learners plug in here under containment).

## 3. Classification Structure

### 3.1 Selection modes (`mode`)

| Code | Mode | Mechanism |
|---|---|---|
| MS-CHAMP | Static champion | 51 leaderboard champion holds the slot; challengers via shadow → promotion |
| MS-CTX | Context-routed | deterministic routing table over 69 contexts (e.g. occupied→model A, unoccupied→model B) |
| MS-PERF | Performance-adaptive | rolling-window metric comparison switches within candidate set under hysteresis |
| MS-CB | Bandit-learned | 66 RL-CB policy selects (contained: candidate set fixed, exploration sim/shadow only, A5) |

### 3.2 Candidate eligibility (gates before any candidacy)
ACTIVE release (44) · applicability verdict ≠ not_applicable for the subject (01/44 A3) · unexpired validation for the slot's claim type (47) · current benchmark entry where suites exist (51) · context compatibility (69 routing/assumptions). Eligibility is recomputed on every gate-relevant change (44 A4 notifications).

### 3.3 Switching discipline
Hysteresis: challenger must exceed incumbent by declared margin over declared window; dwell minimums; shadow period mandatory for consequence-bearing slots (decisions/dispatch) before authority transfer; switches are evented (63) and logged with evidence. Thrash detection freezes the slot pending review.

## 4. Hierarchy
```
Slot (EIG-MS-*: task × scope × mode × candidates × rules)
  └── Selection decision (EIG-MS-I-*: context → chosen release + rationale)
        └── consumed by 46 definitions / 60 evaluations / 62 strategies
```

## 5. Field Groups & Fields

### 5.1 Selection slot (`EIG-MS-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | slotID | string | `EIG-MS-[A-Z0-9-]{3,24}` | M | Slot ID |
| 2 | task | json | claim type + target (e.g. SER-02 forecast for subject class; SER-07 RD estimation per spec) | M | What is being chosen for |
| 3 | scope | json | subject selector (01/10) × grain | M | Where |
| 4 | contextDimensions | array<ref> | →69 contexts the selection may condition on | M | Spec: Context |
| 5 | candidates | array<ref> | →44 releases (and their 45 algorithms) meeting §3.2 gates; closed set, governed additions | M | Spec: Candidate algorithms |
| 6 | mode | enum | §3.1 | M | Mechanism |
| 7 | selectionCriteria | json | primary metric (47 register) + tie-breakers (compute 45, edge-fit 35, explainability class) per 45 §5.2 matrix | M | Spec: Selection criteria |
| 8 | performanceHistory | json | rolling accounting spec: metric, window, per-context segmentation, source (46 V9 skill / 53 deviations / 51 suites) | M | Spec: Performance history — the evidence stream |
| 9 | confidenceRequirement | json | minimum rolling confidence per candidate to *hold* the slot; below ⇒ fallback ladder | M | Spec: Confidence |
| 10 | switchingRules | json | margins, windows, dwell, shadow requirements, thrash freeze (§3.3) | M | Spec: Switching rules |
| 11 | fallbackLadder | array<ref> | ordered: simpler/robust models → naive baselines (51 BL) → abstain signal to consumers | M | Degradation path (A4) |
| 12 | banditBinding | ref | →66 RL-CB problem (MS-CB only; containment per 66) | C | Learned selection |
| 13 | owner / version / status | — | standard | M | Governance |

### 5.2 Selection decision (`EIG-MS-I-*`)
slotRef@version · timestamp/window · contextSnapshot (69) · eligibleSet (gates evaluated) · performanceEvidence (rolling figures consulted) · chosen release · rationale {mode mechanics, margins} · switchFlag (+ shadow evidence ref where switch) · consumers notified. Logged per switch and per periodic confirmation; consequence-bearing consumers (60) embed the current decision ref.

## 6. Controlled Vocabularies
Modes (§3.1), eligibility gates (§3.2 — closed), switching rule forms, fallback ladder element types, thrash-freeze states. Rationale codes for decisions.

## 7. Applicability Rules
- **A1 — Every adaptive use answers "why this model".** Tasks with >1 eligible release route through a slot; the consuming record (46 run, 60 instance) carries the selection decision ref — "which model answered" is never ambiguous.
- **A2 — Gates are continuous.** Eligibility recomputes on dependency changes (44 A4): validation expiry, benchmark regression, applicability change, drift alerts (41 V9) — an ineligible incumbent is replaced or the slot falls down its ladder immediately, evented.
- **A3 — Switching is disciplined.** No switch without margin + window + dwell satisfied; consequence-bearing slots require shadow evidence (62 A7 pattern); switches are evented (63) with consumer notification — silent model swaps are forbidden.
- **A4 — Fallback to robust, then honest.** Ladders end in naive baselines and finally an abstain signal (consumers handle per their fallback policies, 46/60) — a slot never serves a model it can't justify.
- **A5 — Bandit containment.** MS-CB learners select among gated candidates only (the bandit cannot nominate); exploration in shadow/sim per 66 A1; production selection is exploitation of the promoted policy; the slot's switching/thrash rules still bind (the bandit proposes, the slot disposes).
- **A6 — Per-context honesty.** Performance accounting segments by context (69) where the slot conditions on it — a model great in winter and poor in shoulder seasons is two facts, not one average.
- **A7 — Board oversight.** The model board reviews slot health (challenger turnover, freeze incidents, ladder dwell time) — a slot nobody can challenge or that lives on its ladder is broken (51 §10 spirit).

## 8. Validation Rules
V1 candidates meet §3.2 gates at registration and continuously (A2 monitors); V2 selection criteria reference registered metrics (47) and the 45 matrix dimensions; V3 performance history recomputable from sources (46/53/51); per-context segmentation consistent with contextDimensions; V4 switching rules complete (margin+window+dwell+shadow where consequence-bearing); thrash detector configured; V5 fallback ladder resolves; terminal abstain present; V6 decisions logged per switch + confirmation cadence; consumer embedding verified (46/60 records carry refs); V7 MS-CB bindings: 66 problem containment verified; candidate set immutability per policy version; V8 switch events (63) + notifications delivered (63 V10); V9 slots immutable per version; candidate additions governed (board sign-off).

## 9. Relationships to Other Catalogues
44 (the A3 enforcement point; release lifecycles) · 51 (champions/challengers; leaderboards as evidence) · 47 (validation freshness gates) · 45 (selection matrix criteria) · 69 (context conditioning) · 46 (forecast definition routing: the definition's modelRef updates via slot decisions, definition MINOR per 46 §5.1.5) · 60/62 (consequence-bearing consumers; embedded refs) · 66 (RL-CB learners under containment) · 41 (drift alerts as gate triggers) · 55 (responder-type routing inputs, 55 §13) · 63 (switch/freeze events).

## 10. Benchmarking Requirements
Slot-health metrics (A7): switch frequency, shadow-to-promotion conversion, ladder dwell, per-context coverage of performance accounting; selection-quality retrospectives (would the alternative have done better? — counterfactual scoring where computable).

## 11. Dataset Requirements
Decision logs + performance accounting packaged (43) for selection research; shadow comparison corpora retained per switch evidence requirements.

## 12. Feature Requirements
Slot-state features (41): incumbent identity, time-in-slot, rolling margin — for meta-analysis and 66 RL-CB state.

## 13. Model Requirements
(Core consumer of 44.) Candidate releases unchanged by selection; retraining/retiring flows through 44 lifecycles; slots adapt via gates (A2).

## 14. KPI Requirements
KF-MDL selection KPIs (57): % adaptive tasks slot-governed, switch discipline adherence, ladder incidents.

## 15. Response Derivative Requirements
SER-07 estimator slots implement the 51 TT-RD champion mechanics with 52's staged re-estimation duty: estimator switches trigger prioritised RD re-estimation (settlement-cited first, 52 §13) — the slot decision ref appears in re-estimation provenance.

## 16. Decision Requirements
60 evaluations embed current selection refs for their source models; selection abstain signals propagate as input-degradation (60 A5 ladder) — model uncertainty becomes decision conservatism, mechanically.

## 17. Ontology Mapping
Slot ↔ EIG `eig:ModelSelectionSlot`; decisions ↔ PROV activities choosing among `mls:Model` candidates with criteria qualification; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `SelectionSlot`, `SelectionDecision`; edges `CHOOSES_AMONG`→44 releases, `CONDITIONED_ON`→69, `EVIDENCED_BY`→51/46/53, `SERVES`→46/60/62, `SWITCHED` {evidence}. "Why was model X answering in February?" is one query.

## 19. Digital Twin Mapping
Twin-bound model sets (33 requirements) resolve through slots where alternatives exist; twin views render the active selection + margin per subject task.

## 20. Governance
Owner: AI/ML Lead; the model board owns candidate admissions, freeze resolutions, and slot-health review (quarterly); MS-CB deployments additionally per 66 governance.

## 21. Versioning
Slots semver; decisions immutable. Catalogue 1.0.0.

## 22. Example Records

### EIG-MS-LOADFC-RES — residential load forecasting slot (MS-CHAMP, the 51 example operationalised)
- task: SER-02 quantile HH forecast; scope SEC-01 residential; candidates {LOADFC-RES-01@2.0.1 (incumbent), LSTM challenger@…} (gates ✓)
- criteria: MT-PINB primary (suite EIG-MBM-SUITE-0003), tie-break compute CC-2 edge-fit; history: rolling 46 V9 skill, winter/shoulder segmented (A6)
- switching: margin 3% over 28 d + dwell 14 d + shadow 14 d (consequence: feeds bids); ladder: → BL-SNAIVE → abstain
- status: incumbent holds (51 result: leaders indistinguishable; tie-break retains champion); decision log confirms quarterly

### EIG-MS-RD-THERM — thermal-shed estimator slot (MS-CHAMP, SER-07)
- candidates per EIG-MBM-SUITE-0011; confidence requirement: VM-RDV unexpired (EIG-VAL-00102 conditions ride along); switch duty: staged RD re-estimation (52 §13) — settlement-cited records first; decisions cited in re-estimation provenance

### EIG-MS-CTL-MODE-RES — context-routed control core (MS-CTX)
- routes 62 EIG-CTL-RES-THERM-01's MPC core: occupied contexts → full R2C2+correction; unoccupied → simplified setback model; MODE-LOCAL → cached rule core (deterministic table over 69 contexts; no performance switching — routing only)

## 23. Completion Criteria
☑ All six spec fields (context, candidates, selection criteria, performance history, confidence, switching rules) ☑ Four modes incl. contained bandit selection ☑ Continuous eligibility gates as the 44 A3 enforcement point ☑ Disciplined switching (margin/dwell/shadow/thrash-freeze) with events ☑ Honest fallback ladders ending in abstain ☑ Per-context performance honesty ☑ Worked slots operationalising the 51 leaderboard and the RD estimator champion duty.
