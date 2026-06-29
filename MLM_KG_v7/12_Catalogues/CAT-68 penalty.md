---
type: catalogue
ontology: EIG-MLM
catalogue_no: 68
catalogue_id: EIG-CAT-068
entity: penalty
band: rl_agents
status: active
tags: [eig-mlm, catalogue, band:rl_agents, entity:penalty]
aliases: ["EIG-CAT-068", "Catalogue 68", "penalty"]
---

# CAT-68 — Penalty

> **EIG-CAT-068** · band: **RL, Context & Agents** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-11 asset]] · [[CAT-23 contract]] · [[CAT-27 settlement]] · [[CAT-53 response_behaviour]] · [[CAT-57 kpi]] · [[CAT-58 objective]] · [[CAT-59 constraint]]
**Used by (downstream):** [[CAT-60 decision_intelligence]] · [[CAT-63 event]] · [[CAT-66 reinforcement_learning]] · [[CAT-71 learning_policy]] · [[CAT-72 agent]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-068`
**Entity prefix:** `EIG-PEN-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead with constraint-class authorities (59)
**Depends on (upstream):** 11 asset, 23 contract, 27 settlement, 53 response_behaviour, 57 kpi, 58 objective, 59 constraint
**Used by (downstream):** 60 decision_intelligence, 63 event, 66 reinforcement_learning, 71 learning_policy, 72 agent

---

## 1. Definition

The **Penalty Catalogue** registers negative incentive signal components (`EIG-PEN-*`): the governed *stick register* composing with rewards (67) in RL reward functions (66) and decision scoring (60). Each component fixes: penalty type, trigger, machine-evaluable function, weight semantics, severity, linked constraints (59), and linked actions (61). Purpose: **safe and constrained learning** — but with the framework's cardinal distinction kept sharp: **penalties discourage costly-but-permitted behaviour; constraints (59) prevent forbidden behaviour**. Hard limits are masked/shielded, never priced (59 A3) — a penalty on a CC-SAF violation would imply the violation is purchasable, which it is not. Penalties live in the lawful interior of the constraint set.

## 2. Scope
**In scope:** penalty component schema (the spec's seven: type, trigger, function, weight, severity, linked constraints, linked actions); type taxonomy (wear, rebound, shortfall, opportunity, proximity, intervention); the constraint/penalty boundary; proximity-penalty rules (discouraging operation near limits without pricing crossings); severity alignment with 59/63; composition interface (66); reconciliation with contractual/settlement penalties (23/27 — real-world charges vs learning signals, distinguished). **Out of scope:** constraint enforcement (59), reward components (67), composition mechanics (66), contractual penalty clauses themselves (23 — referenced as sources).

## 3. Classification Structure

### 3.1 Penalty types (`penaltyType`)

| Code | Type | Discourages | Examples |
|---|---|---|---|
| PN-WEAR | Asset wear | costly duty | storage cycle depth/count cost (11 SoH models), HP short-cycling |
| PN-RBND | Rebound | post-event payback burdens | recovery energy beyond model band (52 rebound, 53 DV-REBOUND+) |
| PN-SHORT | Delivery shortfall | under-delivery vs commitment | settled shortfall charges mirrored (27), OB-PART/FAIL outcomes (53) |
| PN-OPP | Opportunity cost | foregone value | unused headroom during high-price windows (priced via 21) |
| PN-PROX | Constraint proximity | operating at the edge | margin-shrink penalties approaching comfort/SoC/ramp bounds (inside the mask, A2) |
| PN-INT | Human intervention burden | nuisance | override events (53 OB-OVR — gentle weighting, A4), fallback-mode entries (62) |

### 3.2 Severity alignment
Penalty severity classes map to 59 violationSeverity and 63 SEV semantics for *reporting* coherence — but note: an actual constraint violation (EP-RT/EP-POST breach) is an incident (63) and a 59/68-policy review trigger, not a tuple penalty; if a violation occurred, the mask failed, and the fix is the mask (A1).

### 3.3 Temporal classes
Mirrors 67 §3.3: TERMINAL · DENSE · DELAYED (settled charges back-fill, V5).

## 4. Hierarchy
```
Type (PN-*) → Penalty component (EIG-PEN-*) → composed (66, negative terms) / decision scoring costs (60)
                     boundary: 59 constraints (prevented) ≠ 68 penalties (discouraged)
```

## 5. Field Groups & Fields

### 5.1 Penalty component (`EIG-PEN-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | penaltyID | string | — | `EIG-PEN-\d{4}` | M | Component ID |
| 2 | penaltyType | enum | — | §3.1 | M | Spec: Penalty type |
| 3 | name / description | string | — | — | M | Identity |
| 4 | trigger | json | — | machine-evaluable condition over measured/settled quantities (53/27/11) or state proximity (PN-PROX: margin expressions vs 59 bounds) | M | Spec: Trigger |
| 5 | function | string | — | magnitude expression; sign convention: contributions ≤ 0 in compositions | M | Spec: Function |
| 6 | unit | string | UCUM/EUR | natural unit + normalisation (67-aligned contract) | M | Comparability |
| 7 | weightSemantics | json | — | default + range; authority (58 set governance) | M | Spec: Weight |
| 8 | severity | enum | — | §3.2 classes | M | Spec: Severity |
| 9 | linkedConstraints | array<ref> | — | →59 EIG-CST-* (PN-PROX margins reference their bounds; boundary declaration: this penalty operates inside these constraints) | M | Spec: Linked constraints |
| 10 | linkedActions | array<ref> | — | →61 actions whose use this penalty prices (e.g. PN-WEAR ↔ ACT-CHARGE-STO) | M | Spec: Linked actions |
| 11 | objectiveDerivation | ref | — | →58 (penalties derive from objectives too — wear from OD-AST, etc.) | M | Anchor (58 A6) |
| 12 | realWorldMirror | json | — | {mirrors: →23 clause / →27 charge type \| learning-only} — distinguishes priced reality from shaped preference (A5) | M | Honesty about money |
| 13 | temporalClass | enum | — | §3.3 + back-fill rule | M | When realised |
| 14 | validation | json | — | evidence the penalty discourages the intended behaviour without perverse avoidance (sim audits, 53 trend studies) | M | Trust basis |
| 15 | owner / version / status | — | — | standard | M | Governance |

## 6. Controlled Vocabularies
Types (§3.1), severity classes (§3.2), temporal classes (§3.3), sign convention (≤0 contributions; positive incentives belong in 67 — symmetry of 67 A6/V2). Margin expression grammar = 59 grammar.

## 7. Applicability Rules
- **A1 — The boundary.** Penalties never price constraint violations: anything in 59 is masked/shielded (59 A3); penalty triggers fire only on permitted-but-costly outcomes. A tuple containing a constraint violation is a containment incident (66 V7 mask failure), not a learning signal.
- **A2 — Proximity, not crossing.** PN-PROX shapes behaviour away from limits using margin functions defined strictly inside bounds (zero at the declared comfort margin, never extending past the bound — the bound is unreachable by construction).
- **A3 — Objective-anchored, Goodhart-paired.** Like rewards (67 A1/A3): derivation mandatory; pairing analyses recorded (e.g. PN-SHORT pairs with RW-FLX so the under-promise/over-deliver gaming axis is closed from both sides).
- **A4 — Human-burden penalties stay gentle.** PN-INT terms (overrides, complaints) weight conservatively and are reviewed with occupancy governance (03/DPO): the goal is courteous automation, not training agents to fear/suppress legitimate human agency — overrides remain honoured absolutely (29 A4), the penalty only teaches anticipation.
- **A5 — Real-money honesty.** Components mirroring actual charges (23 contract clauses, 27 settlement penalties) reconcile to settled truth (DELAYED back-fill, V5); learning-only shaped penalties are flagged and excluded from financial reporting — the books and the learning signals never blur.
- **A6 — Asset stewardship grounding.** PN-WEAR magnitudes derive from asset degradation models (11 SoH / 44 SER-04) — priced wear, not vibes; model updates cascade penalty versions.

## 8. Validation Rules
V1 trigger/function parse; references resolve; V2 sign convention (≤0); V3 PN-PROX margins strictly interior to linked 59 bounds (static check); V4 linkedConstraints/linkedActions bidirectionally consistent (59 V9 / 61 registers); V5 DELAYED reconciliation to 23/27 settled charges within tolerance; V6 objectiveDerivation resolves; pairing analyses present (A3); V7 PN-INT weights within governance caps; DPO review recorded; V8 validation evidence before ACTIVE; V9 components immutable per version; function changes cascade 66 problem versions; V10 no severity class implies violation pricing (A1 audit — any tuple penalty coinciding with a 63 EF-CST violation event triggers mask review, not penalty accounting).

## 9. Relationships to Other Catalogues
59 (the boundary; proximity bounds) · 67 (sibling; pairing) · 66 (composition; mask-failure semantics) · 60 (scoring costs) · 23/27 (real-world charge mirrors; reconciliation) · 11/44 (wear models) · 53 (shortfall/override/rebound outcomes) · 62 (fallback-entry penalties; supervisory interplay) · 63 (violations are events, not penalties) · 72 (agent incentive transparency).

## 10. Benchmarking Requirements
Deterrence-health metrics: behaviour trends vs penalised patterns (53 statistics), perverse-avoidance incidents, reconciliation error rates, PN-INT weight review outcomes.

## 11. Dataset Requirements
Penalty computation logs ride experience corpora (66/43) with component breakdowns; charge reconciliation joins retained per 27 horizons.

## 12. Feature Requirements
Penalty-relevant quantities registered (41/38): cycle counters, margin distances, override rates — shared with 60 context features.

## 13. Model Requirements
Wear/degradation models (SER-04) grounding PN-WEAR are 44 releases; penalty approximators (if any) flagged as approximations with reconciliation.

## 14. KPI Requirements
Penalty-trend KPIs (57): wear-cost per delivered kWh, override-burden trends — the cost side of incentive accounting alongside 67's value side.

## 15. Response Derivative Requirements
PN-RBND grounds in RD rebound models (52) and measured deviations (53 DV-REBOUND+): the penalty that keeps "magnitude alone overstates deliverable flexibility" (52 A5) economically true inside learning; PN-SHORT closes the loop on RD promises from the cost side.

## 16. Decision Requirements
60 scoring costs draw from this register; decision audits show penalty terms alongside rewards (49 renderings include "what this option costs and why").

## 17. Ontology Mapping
Penalty ↔ EIG `eig:PenaltyComponent` (sibling of RewardComponent) with `eig:boundedBy` → constraints; alignment owned by 79.

## 18. Knowledge Graph Mapping
Node `PenaltyComponent`; edges `DERIVES_FROM`→58, `INTERIOR_TO`→59, `PRICES_USE_OF`→61, `MIRRORS`→23/27, `PAIRED_WITH`→67. The constraint/penalty boundary is queryable (anything penalised must be permitted).

## 19. Digital Twin Mapping
Twin training computes penalties identically to production logging (reward parity, 67 §19); twin what-ifs render penalty exposure per candidate action (decision support).

## 20. Governance
Owner: AI/ML Lead; constraint-class authorities review linked components; PN-INT jointly with DPO/occupancy governance; PI signs penalty-policy changes; boundary audits (V10) at the model board.

## 21. Versioning
Components semver; cascades per 66. Catalogue 1.0.0.

## 22. Example Records

### EIG-PEN-0003 — storage cycle wear (PN-WEAR)
- trigger: per charge/discharge cycle (depth-weighted); function: −€ per equivalent-full-cycle from SoH model (44 SER-04 @release); linked constraints: SoC bounds (interior); linked actions: ACT-CHARGE-STO/DISCHARGE
- objective: OD-AST (58); realWorldMirror: learning-only (wear is real but unbilled — flagged); the EIG-RL-0003 composition's w3 term

### EIG-PEN-0008 — delivery shortfall (PN-SHORT)
- trigger: settled shortfall (27 lines; 53 OB-PART/FAIL); function: mirrors contract charge (23 clause) + shaped margin; DELAYED back-fill reconciles to settled truth (V5)
- paired with EIG-RWD-0001 (A3 — both sides of the promise priced); severity aligned major

### EIG-PEN-0011 — comfort-margin proximity (PN-PROX)
- trigger: margin(zone_temp − floor) < 1.0 °C while under control authority; function: smooth ramp zero→max over [1.0, 0.3] °C, **zero at and below 0.3 °C margin only because the mask makes ≤0.3 unreachable** (interior check V3 vs EIG-CST-00021)
- teaches policies to keep distance; the floor itself remains 59-enforced; validation: sim audit shows trained policies hold ≥0.9 °C median margin

### EIG-PEN-0014 — occupant override burden (PN-INT)
- trigger: OB-OVR (53); gentle default weight (governance-capped); DPO-reviewed; objective: COMFORT/engagement stewardship; teaches anticipation (pre-notification timing, gentler ramps) — never suppression (overrides honoured absolutely, 29 A4)

## 23. Completion Criteria
☑ All seven spec fields (type, trigger, function, weight, severity, linked constraints, linked actions) ☑ The constraint/penalty boundary sharp and audited (prevented ≠ priced) ☑ Proximity penalties strictly interior ☑ Real-money honesty with settled reconciliation ☑ Gentle human-burden terms with DPO governance ☑ Pairing with rewards closing gaming axes ☑ Examples ground the battery, shortfall, comfort-margin, and override cases.
