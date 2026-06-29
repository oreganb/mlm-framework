---
type: catalogue
ontology: EIG-MLM
catalogue_no: 71
catalogue_id: EIG-CAT-071
entity: learning_policy
band: rl_agents
status: active
tags: [eig-mlm, catalogue, band:rl_agents, entity:learning_policy]
aliases: ["EIG-CAT-071", "Catalogue 71", "learning_policy"]
---

# CAT-71 — Learning Policy

> **EIG-CAT-071** · band: **RL, Context & Agents** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-13 utility_network]] · [[CAT-44 model]] · [[CAT-47 validation]] · [[CAT-50 training]] · [[CAT-51 model_benchmark]] · [[CAT-66 reinforcement_learning]] · [[CAT-67 reward]] · [[CAT-68 penalty]] · [[CAT-69 context]] · [[CAT-72 agent]]
**Used by (downstream):** [[CAT-60 decision_intelligence]] · [[CAT-62 control_strategy]] · [[CAT-70 model_selection]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-071`
**Entity prefix:** `EIG-LP-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead with model board
**Depends on (upstream):** 44 model (SER-13 releases), 47 validation, 50 training (RK-RL), 51 model_benchmark (TT-CTRL), 66 reinforcement_learning, 67 reward, 68 penalty, 69 context, 72 agent
**Used by (downstream):** 60 decision_intelligence (A7 policy sources), 62 control_strategy (CT-RL bindings), 70 model_selection

---

## 1. Definition

The **Learning Policy Catalogue** governs learned policies as deployable, accountable artefacts: each record (`EIG-LP-*`) binds a trained policy to its **agent** (72), **state** and **action** space versions (66), **reward** composition version (67/68 via 66), **confidence**, **version** lineage, and **performance** accounting — the spec's eight fields — plus the governance that RL demands: promotion gates, validity domains, frozen-exploitation attestation, rollback machinery, and retirement. Purpose: **RL governance** — a policy is never "the network we trained last week"; it is a versioned artefact with evidence, boundaries, and an owner. Where 66 defines the learning *problem* and 44 holds the *release artefact* (SER-13), this catalogue is the policy's *operating licence* (A1).

## 2. Scope
**In scope:** policy record schema; lineage (problem version, episode sets, reward versions); validity domains (contexts 69, subjects, action subsets); confidence accounting (sim-eval, shadow, production attainment); promotion/rollback/retirement lifecycle; frozen-exploitation attestation; drift and anomaly monitoring duties; multi-policy coexistence rules (per slot 70 / per strategy 62). **Out of scope:** problem formalisation (66), training mechanics (50), artefact storage (44 release), deployment surfaces (62/60), agent identity (72).

## 3. Classification Structure

### 3.1 Policy lifecycle (`status`)

TRAINED (50 RK-RL complete; sim-eval done) → CANDIDATE (TT-CTRL 51 + 47 evaluation passing) → SHADOW (parallel scoring on production streams, no authority) → LICENSED (operating within validity domain via 62/60) → DEGRADED (confidence below floor — authority suspended, advisory shadow continues) → ROLLED-BACK / RETIRED (closed to authority; retained for audit). Downward transitions automatic on triggers; upward through gates only.

### 3.2 Validity domain dimensions
Per policy: contexts (69 truth combinations trained/evaluated for), subject scope (the trained population — transfer to new subjects requires evaluation, A4), action subset (the masked space version), seasonal/regime coverage (episode-set evidence). Outside-domain states ⇒ the hosting strategy/decision falls back (62 MODE ladder / 60 abstain) — policies don't extrapolate authority.

### 3.3 Confidence composition
`policyConfidence = f(sim-eval scores (51 TT-CTRL), shadow agreement/attainment, production attainment vs expectation (67 outcome rewards), anomaly rate, evidence age)` — declared monotone function, recomputed on rolling windows; floors per deployment consequence class.

## 4. Hierarchy
```
66 problem (@version) → 50 RK-RL runs → 44 SER-13 release → **EIG-LP-* policy licence**
                                                                  └── deployments (62 CT-RL / 60 A7) within validity domain
```

## 5. Field Groups & Fields

### 5.1 Policy record (`EIG-LP-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | policyID | string | `EIG-LP-\d{5}` | M | Spec: Policy ID |
| 2 | agentRef | ref | →72 EIG-AGT-* | M | Spec: Agent — whose policy |
| 3 | problemRef | ref | →66 EIG-RL-* @version (carries state/action/reward structure) | M | Lineage root |
| 4 | stateSpaceVersion | ref | 66 §5.1.4 @version | M | Spec: State |
| 5 | actionSpaceVersion | ref | 66 §5.1.5 @version (mask spec pinned) | M | Spec: Action |
| 6 | rewardVersion | ref | 66 composition @version (67/68 components pinned) | M | Spec: Reward — evidence does not transfer across reward versions (66 V8) |
| 7 | releaseRef | ref | →44 SER-13 modelID@semver (artefact hash) | M | The weights |
| 8 | trainingLineage | json | episode sets (66 EIG-RL-EP-*), environments + versions, RK-RL runs (50) | M | What it learned from |
| 9 | validityDomain | json | §3.2 dimensions with evidence refs per dimension | M | Operating boundaries (A4) |
| 10 | confidence | json | §3.3 composition: current value + components + floors per consequence class | M | Spec: Confidence |
| 11 | performance | json | spec: Performance — sim-eval (51), shadow record, production attainment (outcome-reward accounting, shaping excluded 67 A5), anomaly/shield-activation rates | M | The track record |
| 12 | frozenAttestation | json | exploitation-only verification (66 A2/V9): config hashes in deployments, no-online-update proof | M | Containment evidence |
| 13 | explanationBinding | ref | →49 config (Q-value/advantage renderings; per-action where AUTO-ENV) | M | Transparency duty |
| 14 | rollback | json | predecessor policy / non-learned fallback (62 ladder), trigger conditions, drill records | M | Exit ramp (A6) |
| 15 | deployments | array<ref> | →62 EIG-CTL-I-* / 60 definitions consuming | M | Where licensed |
| 16 | version / status / supersedes | semver/enum/ref | §3.1 lifecycle | M | Spec: Version + chain |
| 17 | owner / approvals | refs | model board sign-off (SoD vs trainer) | M | Governance |

## 6. Controlled Vocabularies
Lifecycle (§3.1), validity dimensions (§3.2), confidence components, degradation/rollback triggers {confidence_floor, anomaly_rate, shield_rate, domain_exit_frequency, reward_version_change, mask_change}, consequence classes (aligned to 60 risk classes).

## 7. Applicability Rules
- **A1 — Licence before authority.** No policy acts (62 CT-RL binding, 60 A7 source) without a LICENSED record; deployments pin policyID@version; the strategy/decision configs cite it (62 V1 / 60 V1 chains).
- **A2 — Full lineage pinning.** Problem/state/action/reward versions + episode sets + release hash: any change upstream ⇒ evidence non-transfer ⇒ new policy record through the full gate ladder (no "small retrain" shortcut — 50/47/48 §16 alignment).
- **A3 — Gate ladder is mandatory.** TRAINED→CANDIDATE: 51 TT-CTRL + 47 evaluation (incl. zero constraint violations — mask integrity, 66 V7); CANDIDATE→SHADOW→LICENSED: shadow evidence per consequence class + VM-CL where the hosting surface requires (62 A7); board sign-off with SoD.
- **A4 — Domain discipline.** Authority only inside the validity domain; domain-exit states trigger hosting fallbacks; domain *extensions* (new subjects, new seasons, new contexts) require targeted evaluation evidence, not optimism — transfer is earned (52 A6 spirit applied to policies).
- **A5 — Frozen means verified.** Exploitation-only attested per deployment (config hashes, no-update proofs); production experience flows to offline learning (66 A2) producing *successor* records.
- **A6 — Rollback is rehearsed.** Predecessor/fallback paths drilled (records in §5.1.14); degradation triggers fire automatically (downward §3.1) and loudly (63 events, 75 badges); restoration through gates.
- **A7 — Performance honesty.** Production attainment accounts on outcome rewards (settled/measured truth via 67 A2/A5), segmented by context (69); sim-vs-production gaps tracked as reality-gap evidence (66 A6) feeding environment recalibration.

## 8. Validation Rules
V1 lineage refs resolve and pin (problem/state/action/reward/release versions consistent — one coherent tuple); V2 release hash matches deployments' loaded artefacts (35 A6 discipline); V3 validity domain dimensions each carry evaluation evidence refs; V4 confidence recomputable from logged components; floors per consequence class declared; V5 frozen attestation present and audited (sampled config inspections); V6 rollback paths resolve; drills in-date (≤12 mo); V7 lifecycle transitions evidence-complete (gate artefacts attached); downward triggers tested; V8 deployments bidirectionally consistent (62/60 configs cite this record); V9 records immutable; succession chains complete; retired policies retained ≥ consequence horizons; V10 shield/anomaly telemetry flowing (zero-violation expectation monitored — any violation is a 66 containment incident, not a policy statistic).

## 9. Relationships to Other Catalogues
66 (problem lineage; containment) · 44 (SER-13 artefacts) · 50 (producing runs) · 47/51 (gate evidence: VM-CL, TT-CTRL) · 67/68 (reward versions; outcome accounting) · 69 (validity contexts; segmentation) · 72 (agent identity/permissions — the licence names the licensee) · 62/60 (deployment surfaces; fallback ladders) · 70 (policies as slot candidates where multiple licensed) · 49 (explanations) · 63 (degradation/rollback events) · 35 (artefact hash pinning at edge).

## 10. Benchmarking Requirements
Policy-generation comparisons on frozen TT-CTRL suites (51); production attainment league per problem; gate-ladder cycle time (training → licence) as an ops metric.

## 11. Dataset Requirements
Shadow comparison corpora + production attainment joins packaged (43); experience corpora lineage (66 §5.3) cited per record.

## 12. Feature Requirements
Policy telemetry features (41): shield-activation rate, domain-exit frequency, attainment vs expectation — registered for monitoring models and board dashboards.

## 13. Model Requirements
SER-13 releases per 44 discipline; policy distillation to edge (50 RK-DISTIL) produces linked records with equivalence evidence (50 A8).

## 14. KPI Requirements
RL-governance KPIs (57 KF-MDL): licensed-policy coverage of CT-RL deployments (must be 100%), degradation incidents, frozen-attestation audit pass rate.

## 15. Response Derivative Requirements
Policies consuming RD state (FC-FLX features) inherit RD freshness as confidence components; policy actions that constitute response stimuli flow through the standard chain (61→56→53) — policy performance on flexibility tasks reconciles to settled delivery truth (67 §15), never to its own value estimates.

## 16. Decision Requirements
60 A7 operationalised: the policy proposes within the decision frame (gates, masks, explanations); LP confidence feeds 60 confidence gates; DEGRADED status = source degradation (60 A5 ladder).

## 17. Ontology Mapping
Policy ↔ EIG `eig:LearnedPolicy` with lineage qualifications (PROV) to problem/runs/rewards; licence states as individuals; alignment owned by 79.

## 18. Knowledge Graph Mapping
Node `LearnedPolicy`; edges `OF_AGENT`→72, `SOLVES`→66 problem, `EMBODIED_BY`→44 release, `LEARNED_FROM`→episode sets, `REWARDED_UNDER`→67/68 versions, `LICENSED_FOR`→validity domain, `DEPLOYED_IN`→62/60, `SUPERSEDES`. "What is this agent allowed to do, on what evidence?" is the licence query.

## 19. Digital Twin Mapping
Twin shadow hosting (62 A7) generates shadow evidence; twin-replays stress candidate policies on historical episodes; M4 twin authority is itself a licensed policy deployment with the kill-switch as ultimate rollback (32 V9).

## 20. Governance
Model board owns the gate ladder and licence approvals (SoD vs trainers); degradation reviews within 5 working days; annual licence renewals re-attest frozen status and domain currency.

## 21. Versioning
Records immutable per version; succession via supersedes. Catalogue 1.0.0.

## 22. Example Records

### EIG-LP-00031 — battery dispatch policy (the EIG-CTL-STO-SITE-01 licence)
- agent EIG-AGT-… (72); problem EIG-RL-0003@2.1; state/action/reward versions pinned (reward @2.1: RWD-0001 + PEN-0003 composition); release SER-13 DQN@1.2.0 hash sha256:…
- lineage: episode sets {ENV-TWIN site ×40k episodes, ENV-SIM price-stress ×15k}; RK-RL runs refs
- validity: contexts {all-season evidence ✓, FEEDER_CONSTRAINED evaluated ✓}; subjects: this site's storage only (transfer unearned); action mask @ pinned
- confidence 0.91 {sim 0.94, shadow 30 d agreement 0.92, production attainment 0.97 of expectation, anomaly 0}; floors: ≥0.85 for AUTO-ENV class
- frozen attested (config hash audit 2026-05); rollback: predecessor @1.1.0 + rule-based fallback (62 ladder), drilled 2026-03; status LICENSED; board approval SoD ✓

### Degradation example
- 2026-04: shoulder-season attainment dipped (sim-gap on PV patterns) → confidence 0.83 < floor → DEGRADED auto (63 event, 62 fell to rule fallback) → environment recalibration (66 A6) → successor @1.3 trained on extended episodes → gates → re-licensed; full chain on record

## 23. Completion Criteria
☑ All eight spec fields (policy ID, agent, state, action, reward, confidence, version, performance) ☑ Licence model: gate ladder, validity domains, frozen attestation, rehearsed rollback ☑ Full lineage pinning with evidence non-transfer ☑ Confidence composition with consequence-class floors ☑ Performance honesty on outcome truth, context-segmented ☑ Worked battery licence + degradation cycle closing the 62/66 chain.
