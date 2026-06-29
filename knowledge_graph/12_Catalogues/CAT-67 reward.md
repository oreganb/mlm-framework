---
type: catalogue
ontology: EIG-MLM
catalogue_no: 67
catalogue_id: EIG-CAT-067
entity: reward
band: rl_agents
status: active
tags: [eig-mlm, catalogue, band:rl_agents, entity:reward]
aliases: ["EIG-CAT-067", "Catalogue 67", "reward"]
---

# CAT-67 — Reward

> **EIG-CAT-067** · band: **RL, Context & Agents** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-21 tariff]] · [[CAT-27 settlement]] · [[CAT-28 carbon]] · [[CAT-53 response_behaviour]] · [[CAT-57 kpi]] · [[CAT-58 objective]]
**Used by (downstream):** [[CAT-60 decision_intelligence]] · [[CAT-66 reinforcement_learning]] · [[CAT-68 penalty]] · [[CAT-71 learning_policy]] · [[CAT-72 agent]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-067`
**Entity prefix:** `EIG-RWD-`
**Version:** 2.0.0 (regenerated to canonical structure from the v1 conceptual template, per programme decision log)
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead with PI (reward policy)
**Depends on (upstream):** 21 tariff, 27 settlement, 28 carbon, 53 response_behaviour, 57 kpi, 58 objective
**Used by (downstream):** 60 decision_intelligence (scoring terms), 66 reinforcement_learning, 68 penalty (sibling), 71 learning_policy, 72 agent

---

## 1. Definition

The **Reward Catalogue** registers positive incentive signal components (`EIG-RWD-*`): the building blocks from which RL reward functions (66 §5.1.6), decision scoring bonuses (60), and adaptive-optimisation incentives are composed. Each component fixes: reward type, source quantity, machine-evaluable function, weight semantics, units, the objective it derives from (58 — derivation is mandatory, 58 A6), applicability, and validation status. Rewards are the *carrot register*; penalties (68) are the sibling *stick register*; both compose under 66 rules. Purpose: **RL, decision intelligence, and adaptive optimisation** share one governed incentive vocabulary — no bespoke reward code anywhere (66 A5).

## 2. Scope
**In scope:** reward component schema (the spec's eight: type, source, function, weight, units, objective, applicability, validation); type taxonomy; source quantity classes; normalisation and scaling rules; shaping vs terminal rewards; Goodhart/manipulation review duties (57 A6); composition interface (66); value-truth bindings (21/28). **Out of scope:** penalties (68), composition mechanics and tuple logging (66), objective definitions (58), KPI formulas (57).

## 3. Classification Structure

### 3.1 Reward types (`rewardType`)

| Code | Type | Nature | Examples |
|---|---|---|---|
| RW-ECON | Economic | € value realised/avoided | cost saving vs baseline tariff spend, flex revenue earned |
| RW-CO2 | Carbon | emissions avoided | tCO₂e avoided (28 single avoided-emissions truth A4) |
| RW-SVC | Service/comfort | service quality maintained | comfort-band occupancy share, DHW availability |
| RW-FLX | Flexibility performance | delivery quality | delivery success (53 outcomes), RD promise adherence |
| RW-LRN | Learning value | information gained | evidence-cell advancement (56 A6 coverage), exploration bonuses (sim-only) |
| RW-OPS | Operational | system health contributions | availability maintained, graceful degradations avoided |

### 3.2 Source classes (`sourceClass`)

| Code | Source | Ground truth |
|---|---|---|
| SRC-SET | Settled quantities | 27 settlement lines (strongest: money/delivery actually settled) |
| SRC-MEAS | Measured outcomes | 53 observations / 38 rd-fitness data |
| SRC-KPI | KPI values | 57 registered values (caveat-aware) |
| SRC-PRC | Priced estimates | 21 pricing library / 28 factors applied to measured quantities |
| SRC-SHAPE | Shaping signals | dense intermediate signals (potential-based, A5) — sim/training use |

### 3.3 Temporal classes
TERMINAL (episode-end outcome) · DENSE (per-step) · DELAYED (settled later: SRC-SET rewards arrive at settlement, tuple back-fill rules declared, V6).

## 4. Hierarchy
```
Type (RW-*) → Reward component (EIG-RWD-*) → composed in 66 rewardComposition (with 68 penalties)
                                            → consumed by 60 scoring / 71 policy evidence
```

## 5. Field Groups & Fields

### 5.1 Reward component (`EIG-RWD-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | rewardID | string | — | `EIG-RWD-\d{4}` | M | Component ID |
| 2 | rewardType | enum | — | §3.1 | M | Spec: Reward type |
| 3 | name / description | string | — | — | M | Identity |
| 4 | source | json | — | sourceClass §3.2 + refs (→27/53/57/21/28 quantities) | M | Spec: Source — ground-truth binding |
| 5 | function | string | — | machine-evaluable over source quantities; sign convention: rewards ≥ 0 contributions | M | Spec: Function |
| 6 | unit | string | UCUM/EUR | natural unit before normalisation | M | Spec: Units |
| 7 | normalisation | json | — | scaling to composition range (declared method, per 66 composition contract) | M | Comparability across components |
| 8 | weightSemantics | json | — | default weight + permitted range; weight authority (58 set governance) | M | Spec: Weight — values live in 66 compositions, semantics here |
| 9 | objectiveDerivation | ref | — | →58 EIG-OBJ-* (mandatory; 58 A6) | M | Spec: Objective — the anchor |
| 10 | temporalClass | enum | — | §3.3 + back-fill rule for DELAYED | M | When realised |
| 11 | applicability | json | — | problem classes (66), decision classes (60), subject scopes; sim-only flag for RW-LRN exploration terms | M | Spec: Applicability |
| 12 | goodhartReview | json | — | manipulation analysis + mitigations (57 A6 duty) + review date | M | Gaming defence (A3) |
| 13 | validation | json | — | spec: Validation — evidence the signal incentivises the intended outcome (correlation with objective KPI, no perverse cases found in sim audits) + status | M | Trust basis |
| 14 | owner / version / status | — | — | standard | M | Governance |

## 6. Controlled Vocabularies
Types (§3.1), source classes (§3.2), temporal classes (§3.3), normalisation methods, sign convention (rewards non-negative; reductions are penalties 68 — no negative rewards smuggling penalties past 68 governance, V2).

## 7. Applicability Rules
- **A1 — Objective-anchored.** Every component derives from a registered objective (58 A6); orphan rewards are inadmissible in any composition (66 V2).
- **A2 — Ground truth preferred.** Production-experience rewards use SRC-SET/SRC-MEAS where available; SRC-PRC estimates only where settlement lags operations (DELAYED back-fill reconciles to settled truth, V6); training-time shaping (SRC-SHAPE) never leaks into production reward logging.
- **A3 — Goodhart defence.** Components carry manipulation analyses with mitigations (e.g. delivery-success rewards pair with 68 rebound penalties so gaming via under-promising or rebound-shifting doesn't pay); reviews refresh on evidence of gaming (53 anomalies, 60 audit findings).
- **A4 — Value-truth bindings.** Economic terms price via the pricing library (21 V11); carbon terms use registered factors and the single avoided-emissions method (28 A4) — reward arithmetic never forks valuation.
- **A5 — Shaping discipline.** Dense shaping signals are potential-based or formally argued not to change optimal policies; declared sim-only where appropriate; shaping terms are excluded from reported performance (71 performance uses outcome rewards).
- **A6 — Sign discipline.** Rewards contribute ≥ 0; negative incentive belongs in 68 with its severity/constraint governance — keeps the stick register's safety review unavoidable.

## 8. Validation Rules
V1 function parses; references resolve; V2 sign convention (≥0 contributions); V3 unit/normalisation coherent with composition contract; V4 objectiveDerivation resolves; bidirectional with 58 §5.1.9; V5 Goodhart review present + in-date (≤12 mo or post-incident); V6 DELAYED back-fill reconciliation: tuple rewards match settled truth within tolerance (audited); V7 validation evidence present before ACTIVE (correlation study + sim audit refs); V8 sim-only flags enforced in production reward logging (66 V5 tuple audit); V9 components immutable per version; weight-range changes are MINOR, function changes MAJOR (66 problem version cascade, 66 V8).

## 9. Relationships to Other Catalogues
58 (derivation anchor) · 68 (sibling sticks; pairing mitigations) · 66 (composition + tuple logging) · 60 (scoring bonus terms under the same register) · 27/53/57 (ground-truth sources) · 21/28 (value truths) · 71 (policy performance accounting) · 56 (learning-value rewards price evidence gains) · 72 (agent incentive transparency).

## 10. Benchmarking Requirements
Incentive-health metrics: reward-objective correlation per component (validation refresh), gaming incidents detected, back-fill reconciliation error rates.

## 11. Dataset Requirements
Reward computation logs ride experience corpora (66/43) with component breakdowns; settled-truth reconciliation joins retained per 27 horizons.

## 12. Feature Requirements
Reward-relevant quantities are registered features/variables before use (41/38); no reward-only side computations.

## 13. Model Requirements
Reward models (learned reward approximators, if any) are 44 releases with full validation — and flagged as approximators in compositions (ground truth still reconciles).

## 14. KPI Requirements
SRC-KPI rewards consume caveat-aware values (QC-DEGRADED excluded from production reward logging); every component's anchor objective has its measurement KPI (58 V-level) — closing incentive → outcome verification.

## 15. Response Derivative Requirements
RW-FLX delivery rewards score against RD promises using measured outcomes (53) and settled quantities (27) — rewarding *kept* promises; RW-LRN evidence rewards price RD coverage advancement (56 A6), making the learning loop economically visible inside the incentive system itself.

## 16. Decision Requirements
60 scoring bonuses draw from this register under the same anchoring/Goodhart rules — decision incentives and learning incentives cannot silently diverge.

## 17. Ontology Mapping
Reward ↔ EIG `eig:RewardComponent` with `eig:derivesFrom` → objective; QUDT-typed units; alignment owned by 79.

## 18. Knowledge Graph Mapping
Node `RewardComponent`; edges `DERIVES_FROM`→58, `SOURCED_FROM`→27/53/57, `COMPOSED_IN`→66 problems, `PAIRED_WITH`→68 mitigations. "What incentivised this policy?" resolves to component-level breakdowns.

## 19. Digital Twin Mapping
Twin training environments (66 ENV-TWIN) compute rewards identically to production logging (same component versions) — reward parity is part of the reality-gap account.

## 20. Governance
Owner: AI/ML Lead; PI signs reward-policy changes (incentives are policy); Goodhart reviews at the model board; pairing requirements (A3) checked at 66 composition review.

## 21. Versioning
Components semver; function changes cascade problem versions (66). Catalogue 2.0.0 (regeneration supersedes the v1 bullet-list template: fields now typed with allowed values and required flags; content semantics preserved).

## 22. Example Records

### EIG-RWD-0001 — flex revenue earned (RW-ECON, SRC-SET)
- source: 27 settlement credit lines for the acting subject's events; function: settled_credit_eur(event); unit EUR; temporal DELAYED (back-fill at settlement run, reconciliation V6)
- objective: OD-COST/flex-revenue (58); weight semantics: default per EIG-OBJ-SET-0003 mapping; Goodhart: paired with 68 rebound + shortfall penalties (gaming via over-promising settles negative anyway — settlement truth is the defence); validation: correlation with realised revenue 1.0 by construction

### EIG-RWD-0007 — comfort maintained (RW-SVC, SRC-MEAS)
- function: occupied-hours share within band (04 zone series, rd-fitness); unit dimensionless [0,1]; DENSE per step; objective COMFORT-MAX (the lexicographic guard's positive twin); Goodhart: paired with sensor-integrity checks (manipulating the sensor is an EF-SEC event, not a reward strategy)

### EIG-RWD-0012 — evidence-cell advancement (RW-LRN, SRC-SHAPE/MEAS hybrid)
- function: Δ(qualifying events toward minEvidence) per RD context cell (52/56); applicability: EO-EST scheduling problems (60 DC-SCHED, 66 RL-CB); sim exploration bonus variant flagged sim-only; objective: estimation-mode set terms (58 §3.3)

## 23. Completion Criteria
☑ All eight spec fields (type, source, function, weight, units, objective, applicability, validation) in canonical typed tables ☑ Objective anchoring mandatory + Goodhart defence with pairing mitigations ☑ Ground-truth preference with settled back-fill reconciliation ☑ Sign/shaping discipline separating rewards from penalties ☑ Value-truth bindings (21/28) ☑ Regeneration supersedes v1 template per decision log ☑ Examples chain to settlement, comfort, and the RD learning loop.
