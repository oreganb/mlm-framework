---
type: catalogue
ontology: EIG-MLM
catalogue_no: 66
catalogue_id: EIG-CAT-066
entity: reinforcement_learning
band: rl_agents
status: active
tags: [eig-mlm, catalogue, band:rl_agents, entity:reinforcement_learning]
aliases: ["EIG-CAT-066", "Catalogue 66", "reinforcement_learning"]
---

# CAT-66 — Reinforcement Learning

> **EIG-CAT-066** · band: **RL, Context & Agents** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-13 utility_network]] · [[CAT-32 digital_twin]] · [[CAT-42 synthetic_data]] · [[CAT-44 model]] · [[CAT-45 algorithm]] · [[CAT-50 training]] · [[CAT-56 response_event]] · [[CAT-58 objective]] · [[CAT-59 constraint]] · [[CAT-67 reward]] · [[CAT-68 penalty]] · [[CAT-69 context]]
**Used by (downstream):** [[CAT-60 decision_intelligence]] · [[CAT-62 control_strategy]] · [[CAT-71 learning_policy]] · [[CAT-72 agent]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-066`
**Entity prefixes:** `EIG-RL-` (RL problem definitions), `EIG-RL-ENV-` (environments), `EIG-RL-EP-` (training episode sets)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 32 digital_twin, 42 synthetic_data, 44 model (SER-13), 45 algorithm (AC-RL), 50 training (RK-RL), 56 response_event, 58 objective, 59 constraint, 67 reward, 68 penalty, 69 context
**Used by (downstream):** 60 decision_intelligence, 62 control_strategy (CT-RL), 71 learning_policy, 72 agent

---

## 1. Definition

The **Reinforcement Learning Catalogue** defines the framework's RL structures: RL problem definitions (`EIG-RL-*`) — the formal tuple binding an **agent** (72), **state space**, **action space**, **reward function** (67/68 composition derived from objectives 58), **policy** family, **environment** (`EIG-RL-ENV-*`: twin/simulator/sandbox), and **training episode** regimes (`EIG-RL-EP-*`). Purpose: **adaptive optimisation** — letting policies improve from experience — under the framework's non-negotiable containment: exploration happens in simulation/sandbox only, constraints are masked/shielded (never learned through violation), and trained policies enter production exclusively via the promotion chain (50 RK-RL → 47 → 71 → 62/60).

## 2. Scope
**In scope:** problem formalisation (MDP/POMDP/contextual bandit declaration); state-space schemas (registered features 41 + context 69); action-space schemas (masked subsets of 61 actions / setpoint ranges); reward composition rules (67 rewards − 68 penalties, objective-derived 58 A6); environment registry (fidelity, reality-gap accounting); episode/experience management (56 linkage); off-policy evaluation; safe-exploration machinery. **Out of scope:** algorithm internals (45 AC-RL), training run mechanics (50 RK-RL), policy artefact governance (71), agent identity/permissions (72), reward component definitions (67/68).

## 3. Classification Structure

### 3.1 Problem classes (`problemClass`)

| Code | Class | Formalism | Examples (SER-13) |
|---|---|---|---|
| RL-MDP | Full MDP | state fully observed | storage dispatch with SoC telemetry |
| RL-POMDP | Partial observability | belief over hidden state (occupancy, thermal state) | zone control with sparse sensing |
| RL-CB | Contextual bandit | single-step, context → action → reward | model selection (70 hook), notification timing (RDD-OCC) |
| RL-MULTI | Multi-agent | coordinated/independent learners | portfolio dispatch coordination (30 simultaneity) |

### 3.2 Environment classes (`EIG-RL-ENV-*` types)

| Code | Type | Basis | Permitted use |
|---|---|---|---|
| ENV-TWIN | Calibrated twin | 32 F3 twins (measured-calibrated) | primary training; smallest reality gap |
| ENV-SIM | Physics/synthetic simulator | 44 SER-01 engines + 42 scenarios | scale training, rare events, stress |
| ENV-SBX | Sandbox live | NEST synthetic feeds (20/31 firewall) | integration-grade trials |
| ENV-PROD-OBS | Production observation | live experience capture, **exploitation only** | experience collection, never exploration (A2) |

### 3.3 Exploration regimes
EXPLORE-FREE (ENV-TWIN/SIM/SBX: ε-greedy, entropy, etc. as configured) · EXPLOIT-FROZEN (production: promoted policy, no exploration, no online weight updates — learning happens offline from collected experience, A2/A4).

## 4. Hierarchy
```
Problem (EIG-RL-*: agent 72 × spaces × reward composition × environments)
  ├── Environment (EIG-RL-ENV-*)
  ├── Episode sets (EIG-RL-EP-*: training regimes + experience corpora)
  └── → policies trained (50 RK-RL) → governed (71) → deployed (62/60)
```

## 5. Field Groups & Fields

### 5.1 RL problem (`EIG-RL-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | rlProblemID | string | `EIG-RL-\d{4}` | M | ID |
| 2 | problemClass | enum | §3.1 | M | Formalism (spec: agent/state/action/reward/policy/environment framing) |
| 3 | agentRef | ref | →72 EIG-AGT-* | M | Spec: Agent — the acting identity |
| 4 | stateSpace | json | schema: [→41 features (SV-ON-capable), →69 context refs, belief spec for POMDP]; dims + bounds | M | Spec: State space — registered signals only (A3) |
| 5 | actionSpace | json | discrete: masked subset of →61 actions; continuous: bounded setpoint ranges (59-derived bounds); mask spec (59 A3) | M | Spec: Action space — constraint-masked by construction |
| 6 | rewardComposition | json | Σ wᵢ·rewardᵢ (→67) − Σ wⱼ·penaltyⱼ (→68); normalisation; objective derivation refs (58 A6) | M | Spec: Reward function — composed, never bespoke (A5) |
| 7 | policyFamily | json | →45 AC-RL algorithm + architecture class; →71 governance binding | M | Spec: Policy |
| 8 | environments | array<ref> | →EIG-RL-ENV-* with training/eval roles | M | Spec: Environment |
| 9 | horizonSpec | json | episode length, discount γ, termination conditions | M | Temporal structure |
| 10 | safetySpec | json | shields (supervisory 62 A1), mask verification, fallback on anomaly | M | Containment (A1) |
| 11 | evaluationProtocol | json | off-policy evaluation method, sim-eval suites (51 TT-CTRL), reality-gap checks | M | Pre-promotion evidence basis |
| 12 | experienceCapture | json | production tuples: (state, action, outcome, reward-computed) ← 56/53 linkage; retention | M | Offline learning feed |
| 13 | owner / version / status | — | standard | M | Governance |

### 5.2 Environment (`EIG-RL-ENV-*`)
envID · type §3.2 · basisRefs (→32 twin / 44 engines + 42 scenarios / NEST) · fidelityEvidence (twin F-class 32; simulator validation 47 VM-PHY; 42 SF tiers) · realityGapAccount {known discrepancies, sim-to-real corrections} · seeding/reproducibility (42 A7 discipline) · status.

### 5.3 Episode set (`EIG-RL-EP-*`)
episodeSetID · problemRef · environmentRef · regime {episodes, scenario mix (42), curriculum, seeds} · experienceCorpusRef (→43 packaged tuples) · trainingRunRefs (→50 RK-RL) · outcomes {learning curves, eval scores}. Episode sets are the reproducibility unit for "what did this policy learn from?".

## 6. Controlled Vocabularies
Problem classes (§3.1), environment types (§3.2), exploration regimes (§3.3), termination condition forms, off-policy evaluation methods {IS, WIS, doubly-robust, FQE}. Tuple schema fixed: (s, a, r, s′, done, context 69, provenance).

## 7. Applicability Rules
- **A1 — Containment is absolute.** Exploration only in ENV-TWIN/SIM/SBX (45 A6, 50 A7); production is EXPLOIT-FROZEN; constraint masking/shielding verified before any environment interaction (59 A3 — violations are not learnable experiences, they are prevented experiences).
- **A2 — No online learning in production.** Deployed policies are frozen artefacts (71); production experience is captured (§5.1.12) and learned from offline, with retrained policies re-entering via the full promotion chain — no silent self-modification.
- **A3 — Registered state only.** State spaces compose registered features (41 SV-ON) and contexts (69); raw side-channel signals are forbidden (62 A3 alignment); POMDP belief updaters are themselves registered models (44).
- **A4 — Reward integrity.** Reward computation at experience time uses the pinned composition version; reward values logged per tuple with component breakdown (auditable against 67/68 definitions); reward changes ⇒ new problem version ⇒ existing policies' evidence does not transfer (71 V-chain).
- **A5 — Composed rewards only.** rewardComposition references registered 67/68 components derived from 58 objectives (58 A6); hand-rolled reward code inside training scripts is a 47-audit failure.
- **A6 — Reality-gap honesty.** Sim-trained policies carry reality-gap accounts; promotion requires ENV-SBX or shadow evidence in addition to sim eval (47 VM-CL chain via 62 A7); persistent sim-vs-real divergence triggers environment recalibration (32) before further training.
- **A7 — Multi-agent coordination declared.** RL-MULTI problems declare the coordination mechanism and joint-constraint handling (30 portfolio limits as shared masks); emergent competition against framework objectives is a design-review item.
- **A8 — Sandbox parity.** NEST RL trials run identical problem definitions against sandbox environments; the handshake promotes policies, never data (42/60 A8 chain).

## 8. Validation Rules
V1 spaces schema-valid; state features SV-ON-capable; action masks derive from 59 resolution (verified, not asserted); V2 reward composition resolves (67/68 refs @versions); component weights logged; V3 environment fidelity evidence present per type; reality-gap account current; V4 episode sets reproducible (seeds + scenario versions + env versions); V5 experience tuples complete per schema with provenance (56/53 links for production capture); V6 evaluation protocol includes off-policy method for production-experience learning; V7 safety spec verified: mask coverage 100% of action space, shield tested (drill records); V8 problem version changes invalidate policy evidence transfer (71 enforcement); V9 EXPLOIT-FROZEN verified in deployment configs (62 CT-RL bindings); V10 multi-agent joint masks consistent with portfolio constraints (30).

## 9. Relationships to Other Catalogues
72 (agents act) · 67/68 (reward/penalty components) · 58 (objective derivation) · 59 (masks/shields) · 61 (action space basis) · 69 (context in state) · 32/42 (environments; twin/synthetic discipline) · 50 (RK-RL runs) · 47/51 (evaluation; TT-CTRL suites) · 71 (policy governance — the artefact side) · 62/60 (deployment surfaces under supervisory frames) · 56/53 (production experience capture) · 70 (RL-CB selection problems).

## 10. Benchmarking Requirements
TT-CTRL suites (51) per problem: episodic return, constraint-violation count (must be 0 by construction — violations indicate mask failures), comfort/service metrics; policy generations compared on frozen suites.

## 11. Dataset Requirements
Experience corpora packaged (43) with tuple schemas + environment/scenario versions; production experience retained per consequence horizons.

## 12. Feature Requirements
State features per 41 contracts (edge-computable where policies deploy to edge, 41 A8); reward-relevant signals registered before use.

## 13. Model Requirements
Policies are SER-13 releases under full 44 discipline; belief models, world models (if model-based RL), and reward models register as 44 entries.

## 14. KPI Requirements
RL-ops KPIs (57): policy generation cadence, sim-eval vs production attainment gap, experience capture coverage, shield activation rate (leading indicator).

## 15. Response Derivative Requirements
RD values enter state (FC-FLX features) and reward shaping (delivery success terms via 67), but RL outcomes never modify RD records — RD evidence remains measured-event-only (52 A1); experience tuples cite event/behaviour records (56/53) as their outcome ground truth.

## 16. Decision Requirements
Promoted policies act inside decision/strategy frames (60 A7, 62 CT-RL) — gates, masks, supervisory shields, per-action explanations (49: Q-value/advantage renderings for XC-HYB policies); the RL layer proposes, the decision layer disposes.

## 17. Ontology Mapping
Problem ↔ EIG `eig:RLProblem` with MDP structure individuals; environments ↔ `eig:LearningEnvironment` (twin/synthetic provenance); tuples ↔ PROV activity chains; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `RLProblem`, `Environment`, `EpisodeSet`; edges `ACTS_AS`→72, `STATES_FROM`→41/69, `ACTS_ON`→61 (masked), `REWARDED_BY`→67/68, `TRAINED_IN`→environments, `PRODUCED`→71 policies. "What did this policy learn from, and under which reward version?" is a path query.

## 19. Digital Twin Mapping
ENV-TWIN is the privileged trainer (calibrated, reality-gap-minimal); twins also host shadow evaluation (62 A7) and pre-promotion soak; M4 twin autonomy is a deployed policy under this catalogue's containment plus the kill-switch (32 V9).

## 20. Governance
Owner: AI/ML Lead; problem definitions reviewed at the model board (reward composition + safety spec scrutiny); shield drills semi-annual; multi-agent designs additionally reviewed for emergent-behaviour risks.

## 21. Versioning
Problems/environments/episode sets semver; reward composition changes ⇒ problem MAJOR (A4/V8). Catalogue 1.0.0.

## 22. Example Records

### EIG-RL-0003 — site battery dispatch (the EIG-CTL-STO-SITE-01 policy's problem)
- class RL-MDP; agent EIG-AGT-… (72); state: [SoC, load/PV forecasts (41 h+4), tariff band, CI band, portfolio signal (30)]; actions: continuous charge/discharge ∈ masked [−P, +P] (59: SoC/ramp/CC-MKT masks)
- reward: w1·cost-saving (67, from OD-COST) + w2·flex-revenue − w3·cycle-wear penalty (68, CC-AST-linked) − w4·CI term; composition @2.1 (58 A6 derivation logged)
- environments: ENV-TWIN site twin (F3) primary; ENV-SIM 42 price/weather scenarios for stress; eval: FQE off-policy + TT-CTRL suite; production EXPLOIT-FROZEN via 62, shielded
- experience: tuples from production exploitation (56/53-linked) feed quarterly offline retrains (50 RK-RL) → 71 → re-promotion

### EIG-RL-ENV-0007 — Aran community twin environment
- type ENV-TWIN; basis 32 community-scope twin + 42 EIG-SYN-SCN-00031-class stressors; fidelity F3 evidence; reality-gap account: comms latency idealised (correction noted); seeds logged

## 23. Completion Criteria
☑ All spec elements (agent, state space, action space, reward function, policy, environment, training episodes) formalised ☑ Containment absolute: sandbox/sim exploration, frozen production, masked actions, shields ☑ Composed objective-derived rewards with audit logging ☑ Reality-gap honesty + promotion chain ☑ Experience capture wired to 56/53 ☑ Worked battery problem chaining to the 62 deployment.
