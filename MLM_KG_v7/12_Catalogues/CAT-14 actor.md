---
type: catalogue
ontology: EIG-MLM
catalogue_no: 14
catalogue_id: EIG-CAT-014
entity: actor
band: actors
status: active
tags: [eig-mlm, catalogue, band:actors, entity:actor]
aliases: ["EIG-CAT-014", "Catalogue 14", "actor"]
---

# CAT-14 — Actor

> **EIG-CAT-014** · band: **Actors & Organisations** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Actor Catalogue

Catalogue ID prefix: **EIG-ACT** · Catalogue number: 14 · Version 1.0 · Status: Active

---

## 1. Definition

The Actor Catalogue defines every actor entity participating in the EIG MLM ecosystem: the parties that own assets, consume and produce energy, trade, provide and procure flexibility, operate networks, regulate, and use the platform. An actor is an *energy-system participant identity* — a legal or functional party in a role-bearing position. Actors are distinct from: organisations (15, the legal entities actors may belong to), roles (16, the permission/responsibility bundles actors hold), and users (17, the authenticated platform accounts that act on behalf of actors).

## 2. Scope

All participants in modelling, market, flexibility, control and governance processes: consumers, prosumers, flexumers, aggregators, suppliers, DSOs, TSOs, market operators, regulators, ESCOs, community actors, balance responsible parties, and platform operators. Applies across all sectors, markets and projects.

## 3. Classification Structure

### 3.1 Actor types (closed)

| Type ID | Actor Type | Definition |
|---|---|---|
| ACT-CON | Consumer | Consumes energy; no generation/flex offering |
| ACT-PRO | Prosumer | Consumes and produces (microgen/storage) |
| ACT-FLX | Flexumer | Prosumer/consumer actively offering flexibility |
| ACT-AGG | Aggregator | Pools distributed resources into market-grade products |
| ACT-SUP | Supplier | Licensed energy retailer |
| ACT-DSO | Distribution System Operator | Operates distribution networks |
| ACT-TSO | Transmission System Operator | Operates transmission system |
| ACT-MKO | Market Operator | Operates a market venue (SEMO, local platform) |
| ACT-BRP | Balance Responsible Party | Carries imbalance responsibility |
| ACT-ESC | ESCO | Energy service company (performance contracting, PaaS) |
| ACT-CEC | Community Energy Actor | REC/CEC entity or member acting collectively |
| ACT-REG | Regulator | Regulatory authority (CRU, EPA…) |
| ACT-PLT | Platform Operator | Operates the MLM platform itself (EIG) |
| ACT-3PS | Third-Party Service Provider | Maintenance, data, forecasting providers |

### 3.2 Participation classes (orthogonal flags)

marketParticipant, flexProvider, flexBuyer, networkOperator, dataProvider, dataConsumer, assetOwner, assetOperator, settlementParty, governanceParty.

## 4. Hierarchy

```
Actor (EIG-ACT-NNNNN)
 ├── belongsToOrg → EIG-ORG (0..1; natural-person actors may have none)
 ├── holdsRoles → EIG-ROL (1..n)
 ├── ownsAssets / operatesAssets → EIG-AST
 ├── partyToContracts → EIG-CON
 ├── participatesInMarkets → EIG-MKT
 ├── enrolledInServices → EIG-FS
 └── representedByUsers → EIG-USR (0..n)
Actor relationships (typed edges): aggregates(AGG→FLX/PRO), supplies(SUP→CON/PRO), balancesFor(BRP→actor), regulates(REG→actor), servesNetworkTo(DSO→actor via CP).
```

An actor may hold multiple types over time (consumer → prosumer → flexumer); type is current-state with history retained.

## 5. Field Groups & Fields

### 5.1 Identity

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | actorID | string | — | EIG-ACT-NNNNN | M | Unique actor ID |
| 2 | actorName | string | — | UTF-8; pseudonymised where natural person | M | Display name |
| 3 | actorType | enum | — | §3.1 | M | Current type |
| 4 | actorTypeHistory | array | — | [{type, from, to}] | M | Type transitions |
| 5 | legalPersonality | enum | — | natural_person, legal_entity, collective_unincorporated | M | GDPR-relevant class |
| 6 | belongsToOrgID | string | — | EIG-ORG | C (legal_entity actors) | Parent organisation |
| 7 | jurisdiction | string | — | EIG-GEO GL-01 | M | Home jurisdiction |
| 8 | gdprDataClass | enum | — | none, pseudonymised, personal_data, special_category_prohibited | M | Data handling class |
| 9 | status | enum | — | prospective, active, suspended, exited | M | Lifecycle |

### 5.2 Participation & capability

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 10 | participationFlags | array | — | §3.2 flags | M | Capability profile |
| 11 | marketRegistrations | array | — | [{marketID(EIG-MKT), registrationID, status, from}] | C (marketParticipant) | Market memberships |
| 12 | licenceRefs | array | — | regulatory licence IDs (supply licence, etc.) | C (SUP/DSO/TSO/MKO) | Licences |
| 13 | brpActorID | string | — | EIG-ACT (type BRP) | C (marketParticipant) | Who balances for this actor |
| 14 | aggregatedByActorID | string | — | EIG-ACT (type AGG) | C (FLX in pools) | Aggregation relationship |
| 15 | creditStanding | enum | — | not_assessed, adequate, watch, inadequate | C (settlementParty) | Settlement credit class |

### 5.3 Asset & service linkage

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 16 | ownedAssetIDs / operatedAssetIDs | array | — | EIG-AST | C (assetOwner/Operator) | Asset links (mirror of asset.ownerOrgID chain) |
| 17 | ownedBuildingIDs | array | — | EIG-BLD | C | Building ownership |
| 18 | serviceEnrolments | array | — | [{fsID(EIG-FS), role: provider/buyer, from, to}] | C | Flexibility service participation |
| 19 | contractIDs | array | — | EIG-CON | C | Contracts party to |
| 20 | connectionPointIDs | array | — | EIG-NET-CP | C (consuming/producing actors) | Network connections |

### 5.4 Permissions & responsibilities

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 21 | roleIDs | array | — | EIG-ROL | M (≥1) | Held roles (permissions derive from roles, never stored directly on actor) |
| 22 | responsibilities | array | — | responsibility codes from role definitions + actor-specific addenda | M | Effective responsibility set |
| 23 | decisionRights | array | — | EIG-DI decision-right refs | C | Decision authority held |
| 24 | dataSharingConsents | array | — | [{scope, grantedTo(actorID), basis(consent/contract/legitimate_interest), from, to}] | M (gdprDataClass ≠ none) | Consent register |
| 25 | controlAuthorityCeiling | enum | — | monitor_only…autonomous_within_envelope | C (assetOperator) | Max authority grantable on owned/operated assets |

### 5.5 Audit

| 26 | version/owner/createdDate/modifiedDate/source | standard audit block | M |

## 6. Controlled Vocabularies

Actor types (14, closed). Participation flags (10, closed). legalPersonality, gdprDataClass, status, creditStanding — closed per §5. Responsibility codes: defined in role catalogue (16) as the authority; actor-specific addenda use prefix RSP-ADD-*.

## 7. Applicability Rules

1. Type transitions follow the ladder: CON→PRO requires ≥ 1 FN-PRODUCE or FN-STORE asset owned; PRO/CON→FLX requires ≥ 1 active service enrolment as provider; reversions automatic when conditions lapse.
2. ACT-AGG actors must hold aggregator role + market registration in every market they bid pooled products into; their pool membership lists must reconcile with members' aggregatedByActorID.
3. Natural-person actors: gdprDataClass ≥ pseudonymised; their records expose only pseudonymised identifiers to models and benchmarks; re-identification keys held outside the catalogue (platform vault).
4. ACT-REG and ACT-PLT actors never appear in market/settlement structures as counterparties.
5. controlAuthorityCeiling caps every controlAuthorityGranted on the actor's assets (validated in 11/V-chain); building controlAuthorityLevel ≤ owner-actor ceiling.

## 8. Validation Rules

| # | Rule |
|---|---|
| V1 | actorID unique; type valid; ≥ 1 role held |
| V2 | Type ladder conditions satisfied (§7.1) — checked nightly, auto-flag on lapse |
| V3 | marketParticipant flag ⇒ ≥ 1 active marketRegistration and a brpActorID |
| V4 | aggregatedByActorID target is active ACT-AGG; bidirectional pool reconciliation |
| V5 | Licence-requiring types (SUP/DSO/TSO/MKO) carry valid licenceRefs |
| V6 | dataSharingConsents present for every cross-actor data flow involving personal data; expired consents block flows |
| V7 | ownedAssetIDs consistent with asset.ownerOrgID/actor chain (no orphan claims) |
| V8 | exited actors: no active enrolments, registrations, or control authority |

## 9. Relationships to Other Catalogues

| Direction | Catalogue | Relationship |
|---|---|---|
| ▶ depends on | organisation (15), role (16) | belongsToOrg, holdsRoles |
| ▶ depends on | geographic (08) | jurisdiction |
| ▶ depends on | asset (11), building (02), utility_network (13) | ownership/operation/connection links |
| ▶ depends on | market (20), contract (23), flexibility_service (29) | participation (forward refs) |
| ◀ consumed by | user (17) | users represent actors |
| ◀ consumed by | stakeholder (18) | stakeholders may map to actors |
| ◀ consumed by | trade (25), settlement (27) | counterparties |
| ◀ consumed by | agent (72) | software agents act on behalf of actors |
| ◀ consumed by | decision (60–64) | decision rights, authority ceilings |
| ◀ consumed by | community (86) | membership |

## 10. Benchmarking Requirements

Actor-level benchmarking only on consented, pseudonymised cohorts: flexumer delivery reliability, prosumer self-consumption, aggregator pool coherence. Cohort minimum n = 10; natural persons never individually identifiable in releases. Actor benchmarks reuse EIG-ABM release machinery.

## 11. Dataset Requirements

Actor master dataset pseudonymised at source for natural persons; joins to telemetry only via assetID/buildingID with consent lineage recorded. Consent register exported with every dataset containing personal-data-derived fields; datasets lacking consent lineage for in-scope rows fail ingestion.

## 12. Feature Requirements

FEA-ACT-*: actor type one-hot, participation flag vector, tenure (days active), enrolment count, pool membership flag, historical delivery reliability, override propensity (links EIG-OCC overrideRate where actor=occupant), consent coverage ratio.

## 13. Model Requirements

Behaviour models (engagement, churn, override prediction) train on pseudonymised actor features only; gdprDataClass gates feature availability. Agent-based models (ABS) instantiate agents 1:1 or stratified-synthetic from actor records; synthetic actors flagged and never settle.

## 14. KPI Requirements

Actor KPIs: flexibility delivery rate, market settlement accuracy, consent coverage, engagement score. Platform KPIs: active actors by type, type-ladder progression rate (CON→PRO→FLX conversion), aggregation pool sizes.

## 15. Response Derivative Requirements

RDs attach to assets but condition on actor behaviour: flexumer override propensity and engagement class modulate RD reliability. Aggregator-level RDs = coherence-weighted pool aggregates; pool membership churn forces RD recomputation. Actor consent scope bounds which RDs may be computed at all (no consent → asset RD computed for network purposes only, anonymised).

## 16. Decision Requirements

Every decision affecting an actor's assets validates: actor status active, authority ceiling, consent scope, contract permissions. Decisions are attributable: decision records carry the responsible actorID (human or via agent). Disputes route to governance workflow with full actor-context snapshot.

## 17. Ontology Mapping

Classes: Actor ⊐ {Consumer, Prosumer, Flexumer, Aggregator, Supplier, DSO, TSO, MarketOperator, BRP, ESCO, CommunityActor, Regulator, PlatformOperator, ServiceProvider}.
Object properties: belongsTo (→ Organisation), holdsRole, ownsAsset, operatesAsset, aggregatedBy, suppliedBy, balancedBy, regulatedBy, partyTo (→ Contract), participatesIn (→ Market), enrolledIn (→ FlexService), consentsTo.
Datatype properties: actorID, actorType, legalPersonality, gdprDataClass, status, creditStanding.
Alignment: FOAF foaf:Agent, ORG ontology, USEF/HEMRM role model (skos:closeMatch per type: e.g. ACT-BRP ≈ HEMRM BalanceResponsibleParty), SAREF4ENER actor patterns.

## 18. Knowledge Graph Mapping

Nodes: Actor. Relationships: BELONGS_TO, HOLDS_ROLE, OWNS, OPERATES, AGGREGATED_BY, SUPPLIED_BY, BALANCED_BY, REGULATED_BY, PARTY_TO, PARTICIPATES_IN, ENROLLED_IN, CONSENTS_TO, REPRESENTED_BY (→ User). Keys: actorID. Personal-data attributes excluded from KG; pseudonymous node IDs only.

## 19. Digital Twin Mapping

Actors are not twinned, but every Building/Asset/Community twin carries an ActorContext (owner, operator, supplier, aggregator, authority ceiling, consent scope) resolved from this catalogue, refreshed on actor-state change events.

## 20. Governance

Owner: EIG Catalogue Owner; DPO co-owns GDPR fields (gdprDataClass, consents). Onboarding/offboarding workflows (64) maintain lifecycle integrity. Consent changes propagate within 24 h to data pipelines (hard SLA). Regulator/market-operator records maintained from public registries.

## 21. Versioning

Record semver; type history append-only; consent register append-only with effective dating; exits retain full history (settlement/audit obligations) under retention schedule from compliance catalogue (93).

## 22. Example Records

```text
actorID: EIG-ACT-00214 | name: PSEUD-7F3A (householder, IE_B0009) | type: ACT-FLX | legalPersonality: natural_person
typeHistory: [{CON, 2023-01, 2024-03}, {PRO, 2024-03, 2025-06}, {FLX, 2025-06, —}]
gdprDataClass: pseudonymised | jurisdiction: EIG-GEO-IE-CRY-0001 | belongsToOrg: — | status: active
participationFlags: [assetOwner, flexProvider, dataProvider] | aggregatedBy: EIG-ACT-00031
roles: [EIG-ROL-OWN-RES, EIG-ROL-FLEXP] | ownedAssets: [EIG-AST-004211, EIG-AST-006102]
serviceEnrolments: [{EIG-FS-DR-01, provider, 2025-06, —}] | controlAuthorityCeiling: supervised_control
consents: [{scope: telemetry_15min+rd, grantedTo: EIG-ACT-00031, basis: contract, from: 2025-06}]
```

```text
actorID: EIG-ACT-00031 | name: Aran Community Energy Aggregation CLG | type: ACT-AGG | legalPersonality: legal_entity
belongsToOrg: EIG-ORG-0044 | participationFlags: [marketParticipant, flexProvider, settlementParty]
marketRegistrations: [{EIG-MKT-LFM-01, REG-2025-117, active, 2025-04}] | brpActorID: EIG-ACT-00007
pool: 38 members (reconciled) | creditStanding: adequate | status: active
```

## 23. Completion Criteria

Complete when: all 14 actor types instantiable with full field sets; every asset owner/operator, market counterparty, enrolment party and consent grantor in the system resolves to an actor; type-ladder automation live; consent register complete for all personal-data flows; pool reconciliations zero-diff; Phase 14 confirms no other catalogue defines participant identities.
