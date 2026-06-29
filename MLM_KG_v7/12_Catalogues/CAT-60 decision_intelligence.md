---
type: catalogue
ontology: EIG-MLM
catalogue_no: 60
catalogue_id: EIG-CAT-060
entity: decision_intelligence
band: decision
status: active
tags: [eig-mlm, catalogue, band:decision, entity:decision_intelligence]
aliases: ["EIG-CAT-060", "Catalogue 60", "decision_intelligence"]
---

# CAT-60 — Decision Intelligence

> **EIG-CAT-060** · band: **KPIs, Objectives & Decision Intelligence** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-44 model]] · [[CAT-46 forecast]] · [[CAT-47 validation]] · [[CAT-49 explainability]] · [[CAT-52 response_derivative]] · [[CAT-55 response_profile]] · [[CAT-57 kpi]] · [[CAT-58 objective]] · [[CAT-59 constraint]]
**Used by (downstream):** [[CAT-61 action]] · [[CAT-62 control_strategy]] · [[CAT-63 event]] · [[CAT-64 workflow]] · [[CAT-66 reinforcement_learning]] · [[CAT-70 model_selection]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-060`
**Entity prefixes:** `EIG-DEC-` (decision definitions), `EIG-DEC-I-` (decision instances)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (decision policy) with Operations
**Depends on (upstream):** 44 model, 46 forecast, 47 validation, 49 explainability, 52 response_derivative, 55 response_profile, 57 kpi, 58 objective, 59 constraint
**Used by (downstream):** 61 action, 62 control_strategy, 63 event, 64 workflow, 66 reinforcement_learning, 70 model_selection

---

## 1. Definition

The **Decision Intelligence Catalogue** defines how model outputs become actions: decision definitions (`EIG-DEC-*`) — versioned mappings from evidence (source models 44, forecasts 46, KPIs 57, RDs 52) through a **score formula** (objective set 58 within constraint set 59) to an **action table** (61 actions per score/state region), gated by **confidence and risk thresholds**; and decision instances (`EIG-DEC-I-*`) — each evaluation logged with inputs, scores, selected action (or abstention), and explanation link (49). Purpose: **model-output-to-action mapping** — the auditable bridge between the analytics stack and the physical/market world. Every automated or advisory action traces to a decision instance; every instance reconstructs why (A1).

## 2. Scope
**In scope:** decision definition schema (sources, scoring, action tables, gates); coupling modes (advisory / automated-within-envelope, 44 §5.1.24); evaluation cadence and triggers; abstention and fallback semantics; risk scoring; instance logging and audit chains; human-in-the-loop interfaces; degradation ladder (evidence quality → coupling demotion). **Out of scope:** action mechanics (61), continuous control loops (62 — strategies host decisions at control timescales), objective/constraint content (58/59), RL policy internals (66 — promoted policies plug in as scoring sources under the same gates).

## 3. Classification Structure

### 3.1 Decision classes (`decisionClass`)

| Code | Class | Cadence | Examples |
|---|---|---|---|
| DC-DISP | Dispatch | event-driven / PT1M–PT30M | flex activation per subject (the 52/55-driven core) |
| DC-SCHED | Scheduling | PT30M–P1D | pre-heat scheduling, EO-EST estimation slotting (56 §16), bid construction support (25) |
| DC-OPS | Operations | P1D–P1M | maintenance triggers (11 diagnostics), stream trust reviews (37) |
| DC-PLAN | Planning/advisory | P1M+ | retrofit ranking (05 §16), prequal reviews (22) — advisory by nature |
| DC-PROT | Protective | immediate | abort/safe-state selection (59 EP-RT breaches), quarantine responses (35 A4) |

### 3.2 Coupling modes
ADVISORY (recommendation to humans, 64 workflows) · AUTO-ENV (automated within envelopes — requires 44 V7 evidence chain: VM-CL PASS unexpired, constraint sets, readback, explanation per action). Mode is per definition; degradation AUTO-ENV→ADVISORY is automatic on evidence expiry (47 A6), input quality collapse (40), or assumption violation (44 A8).

### 3.3 Gate semantics (the spec's confidence/risk thresholds)
- **confidenceGate:** composite floor over input confidences — forecast confidence (46 §5.3), RD confidence (52 A4 ≥ product minima), model validation freshness (47). Below floor ⇒ abstain or fallback, never "act anyway".
- **riskGate:** scored exposure of the candidate action — {constraint headroom margin, reversibility, blast radius (affected subjects), value at stake} → risk classes R1 (trivial) … R4 (high). Per definition: max auto-executable class; above ⇒ escalate to ADVISORY/human (64).

## 4. Hierarchy
```
Decision definition (EIG-DEC-*) → Instance (EIG-DEC-I-*: evaluation → selected action/abstention)
        sources: 44/46/52/55/57 ─┐
        scoring: 58 set          ├─→ gates (§3.3) → action table → 61 execution / 64 human
        feasibility: 59 set ─────┘
```

## 5. Field Groups & Fields

### 5.1 Decision definition (`EIG-DEC-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | decisionID | string | — | `EIG-DEC-[A-Z0-9-]{2,20}` | M | ID |
| 2 | decisionClass | enum | — | §3.1 | M | Class |
| 3 | couplingMode | enum | — | §3.2 (+ degradation rules) | M | Authority |
| 4 | sourceModels | array<ref> | — | →44 releases (ACTIVE, validated) | M | Spec: source models |
| 5 | sourceForecasts | array<ref> | — | →46 definitions (freshness rules apply, 46 A2) | C | Perishable inputs |
| 6 | sourceKPIs | array<ref> | — | →57 (caveat-aware: QC-DEGRADED ⇒ no auto, 57 §16) | M | Spec: source KPIs |
| 7 | sourceRDs | array<ref> | — | →52 specs (records gate-checked per A4 at evaluation) | C | Spec: source RDs |
| 8 | scoreFormula | json | — | machine-evaluable: objective set ref (58 @version) + evaluation mapping | M | Spec: score formula (goal-traceable, 58 A1) |
| 9 | constraintSetRule | ref | — | →59 resolution rule (subject × context) | M | Feasibility envelope |
| 10 | actionTable | array<json> | — | [{condition: score/state region, actionRef →61, parameters mapping}] + abstain row | M | Spec: action table (total: every region maps, V4) |
| 11 | confidenceGate | json | — | per-source floors + composite rule (§3.3) | M | Spec: confidence thresholds |
| 12 | riskGate | json | — | risk scoring formula + max auto class | M | Spec: risk thresholds |
| 13 | evaluationTrigger | json | — | cadence / event-driven (63 subscriptions) | M | When evaluated |
| 14 | fallback | json | — | on abstention/degradation: hold state / last-known-good ≤ staleness bound / safe default (declared) | M | Never-silent failure |
| 15 | explanationConfig | ref | — | →49 EIG-XAI-CFG-* (per_action for AUTO-ENV, 49 A7) | M | Transparency |
| 16 | humanInterface | json | — | escalation route (64 workflow), approval roles (16) | C | For ADVISORY/escalations |
| 17 | owner / version / status | — | — | standard; AUTO-ENV changes re-trigger VM-CL (47/48 §16) | M | Governance |

### 5.2 Decision instance (`EIG-DEC-I-*`)
decisionRef@version · evaluatedAt · inputSnapshot {source values + versions + confidences + KPI caveats + RD gate-check results + forecast freshness} · constraintSetSnapshot (59 §5.2) · score(s) · gateResults {confidence ✓/✗, risk class} · outcome {actionRef + parameters | ABSTAIN reason | ESCALATED →64 ref} · executionRef (→61 instance) · explanationRef (→49) · environment. Immutable; the audit atom (A1).

## 6. Controlled Vocabularies
Decision classes (§3.1), coupling modes (§3.2), risk classes R1–R4, abstention reasons {confidence_gate, risk_gate, infeasible, input_stale, degraded_inputs, conflict}. Action-table condition grammar = 59 expression grammar.

## 7. Applicability Rules
- **A1 — Total auditability.** Every evaluation logs an instance; every executed action (61) and escalation (64) back-references one; the chain action → decision instance → explanation → sources → validations reconstructs in bounded hops (49 A7 alignment).
- **A2 — Gates precede scoring outcomes.** Confidence and feasibility gates run first; an infeasible or under-confident "best" option is not an option. Abstention with declared fallback is a first-class outcome, never an error.
- **A3 — RD citation discipline.** DC-DISP evaluations re-check RD gates at evaluation time (52 A4: ACTIVE, confidence, recency, context match, inherited conditions) — stale gates abstain; saturation/rebound arithmetic (52 A5) shapes parameters.
- **A4 — Risk-tiered authority.** Auto-execution capped by risk class per definition; R-class computation includes blast radius — portfolio-wide actions rank ≥R3 ⇒ human or staged execution.
- **A5 — Degradation ladder.** Evidence decay anywhere in the chain (47 expiry, 46 staleness, 40 fitness drop, 44 A8 violations) demotes coupling automatically and visibly (75 badges, 63 events); restoration on fresh evidence.
- **A6 — No KPI-direct actuation.** KPI breaches route through decisions (57 A5); decisions own the evidence-weighing, not threshold reflexes.
- **A7 — Learning interface.** Promoted RL policies (66) act as scoring/selection sources inside definitions — same gates, masks (59 A3), explanation duties; the policy never bypasses the decision frame.
- **A8 — Sandbox parity.** NEST runs identical definitions against sandbox sources (firewall-tagged instances) — the four-step handshake promotes *logic*, never data (42 §16).

## 8. Validation Rules
V1 source refs resolve, ACTIVE, validated; V2 scoreFormula cites an ACTIVE 58 set@version; V3 constraint rule resolves deterministically; V4 actionTable total (every score/state region incl. abstain) and conflict-free; V5 gates recomputable from instance snapshots; V6 AUTO-ENV definitions hold unexpired VM-CL + per_action 49 config (44 V7 chain); V7 fallback declared and tested (drill records); V8 instances immutable, complete (no gaps vs evaluation triggers); V9 demotion latency ≤ trigger SLA (A5 monitored); V10 risk scoring recomputable; class boundaries audited.

## 9. Relationships to Other Catalogues
58/59 (goal + envelope — inseparable companions) · 61 (action execution) · 62 (strategies host high-frequency decisions) · 52/55 (dispatch evidence + profile hints: staging, rest periods 55 A4) · 46/57 (perishable + scoreboard inputs) · 47/49 (evidence freshness; per-action explanations) · 63/64 (events trigger; escalations route) · 66/70 (policy sources; selection enforcement) · 27 (settlement consequences of dispatch decisions) · 35/36 (edge-local decision execution within localAuthorityScope).

## 10. Benchmarking Requirements
Decision-quality metrics: realised vs scored outcome (counterfactual where measurable), abstention rates by reason, gate-block rates, escalation latency, demotion incidents. Reviewed at the model board.

## 11. Dataset Requirements
Instance archives are the decision audit corpus (43-packaged for studies); retention per consequence class (settlement-linked ≥7 y).

## 12. Feature Requirements
Decision-context features (41): headroom margins (59), profile axes (55), forecast confidence — registered inputs, no ad-hoc signals.

## 13. Model Requirements
Source models per 44 §16 coupling rules; SER-12 optimiser outputs enter as scored option sets (feasibility re-checked, 59 A1).

## 14. KPI Requirements
Decisions log which KPI values (with caveats) they consumed; decision-quality KPIs themselves register in 57 KF-PLT.

## 15. Response Derivative Requirements
DC-DISP is the RD layer's consumer-in-chief: gate-check (A3) → envelope arithmetic → profile-hinted staging (55) → action with rdID-citing explanation (49 §15). EO-EST scheduling (DC-SCHED) prices evidence value per 58 estimation-mode objectives — decisions fund the learning loop.

## 16. Decision Requirements
(Self) — the normative frame: gates → feasibility → scoring → action/abstain → log → explain. All other catalogues' §16 sections resolve here.

## 17. Ontology Mapping
Decision ↔ EIG `eig:Decision` (PROV Activity) with qualified usage of evidence entities; action selection ↔ `eig:selectedAction`; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `DecisionDefinition`, `DecisionInstance`; edges `EVALUATED_WITH`→sources {values}, `SCORED_BY`→58, `BOUNDED_BY`→59 snapshot, `SELECTED`→61, `EXPLAINED_BY`→49, `ESCALATED_TO`→64. The audit chain is a path query.

## 19. Digital Twin Mapping
Twins pre-play candidate decisions (what-if per action-table row) for ADVISORY review; M4 twins host AUTO-ENV decisions with the kill-switch (32 V9) as the ultimate demotion.

## 20. Governance
Owner: PI (policy) + Operations (execution); AUTO-ENV definition changes: model-board + VM-CL re-trigger; quarterly decision-quality reviews; abstention/demotion trends drive evidence investment (56 campaigns, 47 revalidations).

## 21. Versioning
Definitions semver (action-table/gate changes ⇒ MAJOR for AUTO-ENV); instances immutable. Catalogue 1.0.0.

## 22. Example Records

### EIG-DEC-FLEX-DISPATCH-RES — residential flex dispatch (AUTO-ENV, the EIG-EDGE-DEP-00231 decision)
- class DC-DISP; sources: EIG-MDL-FLEXENV-01@1.4.2 (44), FC-DEF-0021 forecasts (46), RD spec THERM-SHED records (52), profile EIG-RP-0004211 hints (55), KPI FLEX-DELIV context (57)
- scoring: EIG-OBJ-SET-0003@… (58); constraints: 59 resolution (comfort floor 00021, caps 00190, ramp 00134, life-safety structural)
- actionTable: {score>θ₁ ∧ headroom≥request → ACT-SHED-HP(params: kW, duration per 52 A5 arithmetic)}, {partial region → ACT-SHED-PARTIAL}, {else → ABSTAIN}
- gates: RD conf ≥0.8 ∧ recency ∧ conditions (≤3 h, occupied — EIG-VAL-00102 inherited); risk ≤R2 auto (single-asset, reversible); evidence VM-CL EIG-VAL-00077 ✓
- fallback: hold current schedule; explanation per_action (EIG-XAI-CFG-0009); edge execution within localAuthorityScope (35 A3)

### EIG-DEC-I-00834712 — instance behind the 49 worked example
- evaluated 2026-02-03T17:29:58Z; snapshot: RD-00184421 gates ✓, forecast fresh, comfort headroom 0.8 °C, caps 1/2 used; score 0.84; risk R2; outcome ACT-SHED-HP {2.1 kW, 45 min}; execution →61; explanation EIG-XAI-00412877; settlement linkage via event EIG-REV-00337712

### EIG-DEC-EST-SLOT — estimation scheduling (DC-SCHED, ADVISORY→auto-low-risk)
- prices EO-EST events per 58 estimation-mode objectives against market/comfort opportunity cost; outputs campaign slot proposals (56); risk R1 auto within campaign budgets

## 23. Completion Criteria
☑ Definition/instance split with all spec fields (sources models/KPIs/RDs, score formula, action table, confidence + risk thresholds) ☑ Gates-before-scoring, abstention-as-outcome, declared fallbacks ☑ Risk-tiered authority + degradation ladder ☑ Total audit chain (action→instance→explanation→evidence) ☑ RD citation discipline + profile-hinted dispatch ☑ RL-policy containment in the decision frame ☑ Worked dispatch definition+instance closing the cross-layer chain.
