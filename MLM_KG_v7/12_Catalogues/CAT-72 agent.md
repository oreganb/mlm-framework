---
type: catalogue
ontology: EIG-MLM
catalogue_no: 72
catalogue_id: EIG-CAT-072
entity: agent
band: rl_agents
status: active
tags: [eig-mlm, catalogue, band:rl_agents, entity:agent]
aliases: ["EIG-CAT-072", "Catalogue 72", "agent"]
---

# CAT-72 — Agent

> **EIG-CAT-072** · band: **RL, Context & Agents** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-16 role]] · [[CAT-17 user]] · [[CAT-58 objective]] · [[CAT-59 constraint]] · [[CAT-61 action]] · [[CAT-66 reinforcement_learning]] · [[CAT-69 context]] · [[CAT-71 learning_policy]]
**Used by (downstream):** [[CAT-60 decision_intelligence]] · [[CAT-62 control_strategy]] · [[CAT-66 reinforcement_learning]] · [[CAT-71 learning_policy]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-072`
**Entity prefixes:** `EIG-AGT-` (agent definitions), `EIG-AGT-I-` (agent instances)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (agent policy) with model board
**Depends on (upstream):** 16 role, 17 user (service identities), 58 objective, 59 constraint, 61 action, 66 reinforcement_learning, 69 context, 71 learning_policy
**Used by (downstream):** 60 decision_intelligence, 62 control_strategy, 66 (agents act in problems), 71 (licences name agents)

---

## 1. Definition

The **Agent Catalogue** registers autonomous decision agents: bounded, accountable acting identities (`EIG-AGT-*`) — each defined by agent type, objective binding (58), state space, action space, learning method, **permissions** (16), and **constraints** (59) — and their running instances (`EIG-AGT-I-*`) deployed at platform or edge. An agent is the *who* of autonomy: the identity that holds policies (71), acts through registered actions (61), is permissioned like any principal (16 RBAC — agents are service identities, not exceptions), and is accountable through the same audit chains as everything else. Purpose: **autonomous decision agents** that are governable: every autonomous behaviour in the framework traces to a registered agent with explicit boundaries (A1).

## 2. Scope
**In scope:** agent definition schema (the spec's eight: ID, type, objective, state space, action space, learning method, permissions, constraints); agent type taxonomy (reflex/rule, optimising, learning, coordinating); identity and permissioning (16/17 integration); authority envelopes (the intersection: permissions ∩ constraints ∩ policy validity domains ∩ deployment scopes); multi-agent coordination registers; instance lifecycle and heartbeating; accountability chains. **Out of scope:** policy artefacts (71), learning problems (66), decision/strategy logic the agent operates within (60/62), human users (17 — agents are USR-S-* service principals referenced, not redefined).

## 3. Classification Structure

### 3.1 Agent types (`agentType`)

| Code | Type | Decision core | Examples |
|---|---|---|---|
| AG-RULE | Reflex/rule agent | deterministic rules (62 CT-RULE cores) | thermostat-class zone agents, watchdog agents |
| AG-OPT | Optimising agent | solver-driven (SER-12, 58 sets) | MPC controllers' acting identity, schedule optimisers |
| AG-LRN | Learning agent | licensed policies (71) | battery dispatch agent, zone-control RL agent |
| AG-CRD | Coordinating agent | aggregates/arbitrates sub-agents | portfolio dispatch coordinator (30 simultaneity, RL-MULTI 66) |

Type declares the *core*; many agents are layered (AG-LRN inside AG-OPT scheduling inside supervisory rules — declared per 62 stacking).

### 3.2 Authority envelope (the operative boundary)
Effective authority = permissions (16 grants to the agent's service identity) ∩ resolved constraints (59) ∩ action-space masks (66/61 trigger lists) ∩ policy validity domains (71, AG-LRN) ∩ deployment scope (62/35). Computed, cached (edge), and auditable; the agent cannot exceed any factor (A2).

### 3.3 Instance states
PROVISIONED → ACTIVE (heartbeating, within envelope) → SUSPENDED (envelope factor lost: permission revoked, policy degraded 71, quarantine 35 A4) → DECOMMISSIONED. Heartbeat loss ⇒ hosting supervisory fallback (62 ladder) + 63 event.

## 4. Hierarchy
```
Type (AG-*) → Agent definition (EIG-AGT-*: identity + boundaries)
                  └── Instance (EIG-AGT-I-*: deployment at 31/35, envelope cache, heartbeat)
                        └── acts via 61 within 60/62 frames, holding 71 licences (AG-LRN)
```

## 5. Field Groups & Fields

### 5.1 Agent definition (`EIG-AGT-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | agentID | string | `EIG-AGT-\d{4}` | M | Spec: Agent ID |
| 2 | agentType | enum | §3.1 (+ stacking declaration) | M | Spec: Agent type |
| 3 | name / purpose | string | — | M | Identity |
| 4 | serviceIdentity | ref | →17 USR-S-* (the RBAC principal) | M | One agent, one identity (A3) |
| 5 | objectiveBinding | ref | →58 EIG-OBJ-SET-* @version | M | Spec: Objective — agents optimise registered goals only |
| 6 | stateSpace | json | →66 §5.1.4 schema (AG-LRN) / input register (38/69) for others | M | Spec: State space — registered signals (66 A3) |
| 7 | actionSpace | json | →61 actions on whose trigger lists this agent appears + masks (59) | M | Spec: Action space — closed and masked |
| 8 | learningMethod | json | AG-LRN: →66 problem + 71 licence binding; AG-OPT: →45 AC-OPT + 44 SER-12; AG-RULE: none (declared) | M | Spec: Learning method |
| 9 | permissions | array<expr> | 16 grammar grants to serviceIdentity (least-privilege, A3) | M | Spec: Permissions |
| 10 | constraintBinding | ref | →59 resolution rule for the agent's scope | M | Spec: Constraints |
| 11 | coordination | json | AG-CRD: sub-agents, arbitration rules, joint masks (30 portfolio limits); membership for coordinated agents | C | Multi-agent structure (A6) |
| 12 | accountability | json | owner role (16), explanation binding (49), audit chain endpoints (60/61 instance linkage) | M | Who answers for it |
| 13 | deploymentSurfaces | array<ref> | →62 strategies / 60 definitions / 31/35 hosts | M | Where it may run |
| 14 | owner / version / status | — | standard | M | Governance |

### 5.2 Agent instance (`EIG-AGT-I-*`)
agentRef@version · host (→31 service / 35 node) · envelopeCache {computed §3.2 + snapshot hashes, refresh cadence} · heldLicences (→71 current) · heartbeat config + state §3.3 · activity linkage (61/60 instances accrue to this instance) · environment. Suspension/decommission evented (63).

## 6. Controlled Vocabularies
Types (§3.1), instance states (§3.3), envelope factors (closed list §3.2), heartbeat/suspension triggers, arbitration rule forms (priority, market-internal, consensus — declared per AG-CRD).

## 7. Applicability Rules
- **A1 — No anonymous autonomy.** Every autonomous behaviour executes as a registered agent instance; actions (61) log the acting agent; unattributed automation is a security finding (EF-SEC).
- **A2 — Envelope is the law.** Effective authority is the intersection §3.2; any factor's loss suspends the affected authority immediately (71 degradation, 16 revocation, 59 changes, quarantine) — agents degrade with their evidence, like everything else (60 A5 symmetry).
- **A3 — Least privilege, one identity.** Permissions granted minimally to the agent's own service identity (no shared credentials, no privilege inheritance from owners); reviews per 16 cadence; the agent's grants are part of its public record.
- **A4 — Objectives and constraints are bound, not chosen.** Agents pursue their registered objective set within their constraint binding; self-modification of goals or boundaries is structurally impossible (these live in 58/59 registries the agent cannot write — write-permissions audited, V3).
- **A5 — Frozen learning in production.** AG-LRN agents hold licensed policies (71) under frozen exploitation (66 A2); experience flows offline; the agent never self-updates in place.
- **A6 — Coordination is registered.** Multi-agent interactions (AG-CRD) declare arbitration and joint masks; emergent inter-agent dynamics are reviewed (66 A7); coordinated agents cannot jointly exceed portfolio constraints (30) — joint masks enforce, not etiquette.
- **A7 — Heartbeat or fallback.** Instances heartbeat; loss triggers hosting supervisory fallback (62) and events (63) — a silent agent is a failed agent, handled.
- **A8 — Sandbox parity.** NEST agents are full registrations against sandbox surfaces (firewall-tagged); promotion moves definitions/policies through gates, never identities across the firewall.

## 8. Validation Rules
V1 serviceIdentity resolves (17 USR-S-*), 1:1 with agent; V2 action space ⊆ actions listing this agent as trigger (61 A2 bidirectional); masks derive from constraint binding (59); V3 agent identity holds no write grants on 58/59/16 registries (A4 structural check); V4 AG-LRN: licence bindings current (71 LICENSED); learning method consistent with problem (66); V5 envelope computation reproducible from factor snapshots; cache refresh ≤ SLA; V6 AG-CRD: joint masks consistent with portfolio constraints (30); arbitration deterministic; V7 heartbeat configured; suspension paths tested (drills); V8 accountability complete: owner role, explanation binding, audit linkage verified (61/60 instances attribute); V9 instances' activity fully attributed (no orphan actions in 61 logs); V10 definitions immutable per version; envelope-affecting changes are MAJOR with re-review.

## 9. Relationships to Other Catalogues
16/17 (identity + permissions — agents as principals) · 58/59 (bound goals + limits; write-prohibition) · 61 (action attribution; trigger lists) · 66/71 (learning problems; policy licences) · 60/62 (operating frames; the agent acts *within* decisions/strategies, never around them) · 69 (state contexts; validity domains) · 30 (joint portfolio masks for coordination) · 63 (lifecycle/suspension events) · 49 (explanation duties per accountability) · 35 (edge hosting; envelope caches; quarantine suspension) · 32 (M4 twin autonomy = an agent instance with the twin as host).

## 10. Benchmarking Requirements
Agent-fleet health: attribution completeness (V9), envelope-suspension incidents, heartbeat reliability, objective attainment per agent (58 A7 accounting at agent grain).

## 11. Dataset Requirements
Agent activity corpora (attributed 61/60 instances) packaged (43) for behaviour audits; envelope snapshots retained per consequence horizons.

## 12. Feature Requirements
Agent-state features (41): suspension history, attainment trends — for fleet monitoring and AG-CRD arbitration inputs.

## 13. Model Requirements
Agent decision cores are registered artefacts (44 SER-12/13, 71 policies); no unregistered logic acts (A1 + 44 A1 closure).

## 14. KPI Requirements
Autonomy KPIs (57 KF-PLT): % automated actions agent-attributed (100% target), suspension MTTR, fleet attainment.

## 15. Response Derivative Requirements
Agents executing flexibility dispatch are the accountable identities behind RD-consuming decisions (60 A3) and stimulus actions (61→56): the chain "which agent shed this heat pump, under what licence, on what RD evidence" resolves end-to-end — autonomy with receipts.

## 16. Decision Requirements
Agents operate inside decision/strategy frames (60/62) — proposing, tracking, executing — never replacing the frame; agent confidence/licence states feed the frames' gates (71 §16).

## 17. Ontology Mapping
Agent ↔ EIG `eig:AutonomousAgent` (PROV `prov:SoftwareAgent` specialisation) with envelope qualifications; instances ↔ deployed agent activities; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `AgentDefinition`, `AgentInstance`; edges `IDENTIFIED_AS`→17, `PURSUES`→58, `BOUNDED_BY`→59/masks, `HOLDS`→71 licences, `ACTS_VIA`→61 {instances}, `HOSTED_AT`→31/35, `COORDINATES`→sub-agents. The accountability query ("everything agent X did in February, and why") is first-class.

## 19. Digital Twin Mapping
Twin-hosted agents (shadow and M4) register identically; the M4 kill-switch (32 V9) is an envelope factor (host authority) whose loss suspends instantly — twin autonomy is just agency under this catalogue, nothing special.

## 20. Governance
Owner: PI (agent policy); model board approves AG-LRN/AG-CRD definitions; permission reviews per 16; fleet review quarterly; new agent types (beyond §3.1) are constitutional changes.

## 21. Versioning
Definitions semver (envelope changes MAJOR); instances lifecycle-tracked. Catalogue 1.0.0.

## 22. Example Records

### EIG-AGT-0007 — Aran residential dispatch agent (AG-LRN-in-OPT stack)
- identity USR-S-… (16 grants: dispatch:flex:execute:portfolio-pf0007, read scopes — least-privilege ✓); objective EIG-OBJ-SET-0003@…
- state: 66 problem state schema; actions: [ACT-SHED-HP, ACT-SETPOINT-ADJ, ACT-RESTORE-BASELINE] (trigger lists ✓) masked per 59 resolution
- learning: holds EIG-LP-class licences for its zone policies; MPC layer per EIG-CTL-RES-THERM-01; deployment: EIG-EDGE-NODE-0007 instance with envelope cache (refresh PT15M, MODE-LOCAL conservative per 59 A5)
- accountability: owner ops role; explanations EIG-XAI-CFG-0009; every 61 instance attributes (the worked EIG-ACTN-I-0192277 chain names this agent)

### EIG-AGT-0011 — portfolio coordinator (AG-CRD)
- coordinates zone/site agents for EIG-FLX-PF-0007; arbitration: priority by 55 staging hints + fairness rotation; joint mask: portfolio Σ commitments ≤ envelope (30 A5) enforced as shared constraint — coordination cannot oversubscribe
- no direct actuation permissions (acts by allocation to sub-agents); RL-MULTI problem binding (66) for allocation policy, licensed (71)

### Suspension example
- 2026-02 quarantine drill (35 A4): node isolation ⇒ EIG-AGT-I instance SUSPENDED (envelope host factor), 62 fallback held comfort, 63 events, reinstated post Verify→Rejoin — the agent degraded exactly like its evidence

## 23. Completion Criteria
☑ All eight spec fields (ID, type, objective, state space, action space, learning method, permissions, constraints) ☑ Agents as least-privilege RBAC principals with 1:1 identities ☑ Authority-envelope intersection with automatic suspension ☑ Structural goal/boundary immutability (no self-modification) ☑ Registered coordination with joint masks ☑ Full attribution + accountability chains ☑ Worked Aran agent + coordinator closing Phase 10's autonomy story.
