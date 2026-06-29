---
type: catalogue
ontology: EIG-MLM
catalogue_no: 86
catalogue_id: EIG-CAT-086
entity: community
band: governance
status: active
tags: [eig-mlm, catalogue, band:governance, entity:community]
aliases: ["EIG-CAT-086", "Catalogue 86", "community"]
---

# CAT-86 — Community

> **EIG-CAT-086** · band: **Aggregation & Governance** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-02 building]] · [[CAT-11 asset]] · [[CAT-13 utility_network]] · [[CAT-14 actor]] · [[CAT-15 organisation]] · [[CAT-20 market]] · [[CAT-21 tariff]] · [[CAT-23 contract]] · [[CAT-27 settlement]] · [[CAT-30 flexibility]]
**Used by (downstream):** [[CAT-19 energy_carrier]] · [[CAT-25 trade]] · [[CAT-30 flexibility]] · [[CAT-55 response_profile]] · [[CAT-73 application]] · [[CAT-87 district]] · [[CAT-91 policy]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-086`
**Entity prefix:** `EIG-CMU-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator with community governance liaisons
**Depends on (upstream):** 02 building, 11 asset, 13 utility_network, 14/15 actor/organisation, 20/21 market/tariff, 23 contract, 27 settlement, 30 flexibility
**Used by (downstream):** 25 trade (community participation), 55/30 (community portfolios), 73 (EIG-APP-19), 87 district, 91 policy (REC/CEC alignment)

---

## 1. Definition

The **Community Catalogue** registers energy communities (`EIG-CMU-*`): governed collectives — RECs/CECs and analogous arrangements — each defined by members (14/15), assets (11), market participations (20/25), governance structure, **energy sharing** arrangements, and **flexibility** capability (30). A community is an organisational-spatial aggregate: it owns/operates shared assets, allocates shared energy among members under registered sharing keys, participates in markets as an aggregated actor, and holds community-level flexibility portfolios. Purpose: **community energy systems** modelled with the same rigour as buildings and assets — membership, sharing arithmetic, and benefit allocation are register facts, not spreadsheet folklore (A1). The worked anchor: the Aran Islands co-operative (EIG-ORG-0044) community.

## 2. Scope
**In scope:** community schema (the spec's seven: ID, members, assets, markets, governance, energy sharing, flexibility); community types (REC/CEC/informal); membership lifecycle; sharing-key definitions (static, proportional, dynamic) with settlement integration (27); shared-asset ownership models; community governance hooks (decision rights, not internal politics); community-grain KPIs and privacy floors. **Out of scope:** network physics (13), market mechanics (20/25/26), individual contracts (23 — community↔member terms reference contract records), district physical aggregation (87), regulatory definitions themselves (92 — typed against).

## 3. Classification Structure

### 3.1 Community types (`communityType`)
CT-REC (renewable energy community, 92-typed) · CT-CEC (citizen energy community) · CT-COOP (co-operative arrangement, the Aran pattern) · CT-INF (informal sharing group — reduced capabilities, no regulated sharing). Type binds the applicable regulatory envelope (92) and permissible activities matrix.

### 3.2 Sharing-key classes (`sharingKey`)
SK-STATIC (fixed %, registered per member) · SK-PROP (proportional to consumption/contribution per period) · SK-DYN (interval-level dynamic allocation algorithm — registered as a 44 model, settlement-grade inputs only). Keys are versioned; allocation runs are settlement-adjacent computations (27 discipline: supersede-never-edit, TR-A metering).

### 3.3 Membership states
APPLIED → ACTIVE (contract executed 23, metering registered 37) → SUSPENDED → EXITED (allocation closure + data duties per 74 offboarding pattern).

## 4. Hierarchy
```
Community (EIG-CMU-*) — members (14/15 under 23) — shared assets (11) — sharing keys → allocation runs (27-adjacent)
        └── community flexibility portfolio (30 EIG-FLX-PF-*) → market participation (20/25)
```

## 5. Field Groups & Fields

### 5.1 Community (`EIG-CMU-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | communityID | string | `EIG-CMU-\d{4}` | M | Spec: Community ID |
| 2 | name / communityType | string/enum | §3.1 | M | Identity + regulatory envelope (92) |
| 3 | legalEntity | ref | →15 EIG-ORG-* | M | The accountable organisation |
| 4 | members | array<json> | [{actorRef →14, role: member/producer/prosumer, contractRef →23, meteringRefs →37, state §3.3}] | M | Spec: Members |
| 5 | sharedAssets | array<json> | [{assetRef →11, ownershipModel: community/co-owned/leased, shares}] | M | Spec: Assets |
| 6 | networkContext | ref | →13 zones/CPs (proximity criteria where 92 requires) | M | Spatial-electrical footprint |
| 7 | markets | array<ref> | →20 participations + 22 products held + 25 trading arrangements (aggregated-actor pattern, 14 ladder) | M | Spec: Markets |
| 8 | governance | json | decision rights {sharing-key changes, asset investment, market mandates}, approval bodies, member-vote bindings (board refs) | M | Spec: Governance |
| 9 | energySharing | json | {sharingKey §3.2 @version, allocationCadence, settlementIntegration →27 method, surplusRule, deficitRule} | M | Spec: Energy sharing |
| 10 | flexibility | json | {portfolioRef →30 EIG-FLX-PF-*, benefitAllocation key (may differ from energy key — declared), member opt-in register} | M | Spec: Flexibility |
| 11 | kpiSet | array<ref> | →57 community-grain (self-consumption share, shared-energy %, flex revenue/member, CO₂) | M | Scoreboard |
| 12 | owner / version / status | — | standard | M | Governance |

## 6. Controlled Vocabularies
Types (§3.1), sharing keys (§3.2), membership states (§3.3), ownership models, surplus/deficit rules. Benefit-allocation key forms mirror sharing-key classes.

## 7. Applicability Rules
- **A1 — Registered membership and keys.** Allocation runs compute only over ACTIVE members with registered metering (37 TR-A) under the versioned key — sharing arithmetic is auditable settlement-class work (27 A6 discipline applies).
- **A2 — Type binds capability.** communityType activates the 92 regulatory envelope (permissible activities, proximity rules, member classes); activities outside the envelope are 59 CC-REG findings.
- **A3 — One member truth.** Member energy/flex positions reconcile: individual settlement (27) + community allocation sum to metered truth (V4 balance check); double counting is structurally prevented.
- **A4 — Flexibility opt-in is individual.** Community portfolios aggregate only opted-in members (23 entitlement chain per member); community governance cannot conscript a member's flexibility (29 A4 spirit at community scale).
- **A5 — Privacy at community grain.** Member-level data visible to community administrators only per contract terms; published community figures respect cohort floors (n≥10 — small communities publish aggregates with floor handling, 38 A7).
- **A6 — Governance hooks, not governance itself.** The register records decision rights and binding approvals (key changes are governed events with member-vote refs); internal deliberation stays with the community.

## 8. Validation Rules
V1 members' contracts/metering resolve; states lifecycle-consistent; V2 sharing key versioned; allocation runs cite key@version + input snapshot; V3 SK-DYN algorithms are registered 44 releases with 47 validation; V4 allocation balance: Σ member allocations = shared generation − losses (declared) per period; reconciliation vs 27 lines; V5 type/activity envelope conformance (92); V6 flexibility portfolio membership ⊆ opted-in ACTIVE members; V7 KPI publications floor-checked; V8 communities immutable per version; key changes MAJOR with member-approval evidence.

## 9. Relationships to Other Catalogues
15/14/23 (legal entity, members, terms) · 11/13 (shared assets, network footprint/proximity) · 27 (allocation-settlement integration; balance audits) · 30/25/20 (portfolios, aggregated trading) · 37 (member metering) · 92/91 (REC/CEC envelopes; policy incentives) · 87 (communities within districts — composition) · 73 (EIG-APP-19 portal) · 57/65 (community KPIs/benchmarks).

## 10. Benchmarking Requirements
Community cohorts (65 BF-FLX/EC rows): self-consumption, shared-energy share, flex value per member — n≥10 across communities or floor-handled.

## 11. Dataset Requirements
Allocation run archives (43, settlement-class retention); community datasets for research per member consent + floors.

## 12. Feature Requirements
Community-grain features (41): simultaneity (the Aran 0.72 lineage), self-consumption ratios — registered for community models.

## 13. Model Requirements
SK-DYN allocators and community forecasting (SER-02 portfolio grain) under full 44 discipline; community twins (32 community scope) bind here.

## 14. KPI Requirements
§5.1.11 set registered (57); allocation-derived KPIs at settlement fitness.

## 15. Response Derivative Requirements
Community flexibility value rests on member RDs aggregated per 30 (simultaneity-honest, 52 A8); community-level delivery (MET-015 at portfolio grain) is the co-op's market credibility — opt-in registers keep RD evidence consensual.

## 16. Decision Requirements
Community dispatch decisions (60) respect member-level constraints/caps individually (59 resolution per member); benefit allocation transparency via 49 AU-OCC-class renderings ("what the community earned, your share, why").

## 17. Ontology Mapping
Community ↔ eig:EnergyCommunity (FOAF/ORG composition + REC/CEC typing); sharing keys ↔ allocation policy individuals; owned by 79.

## 18. Knowledge Graph Mapping
Node `Community`; edges `HAS_MEMBER` {role, state}, `OWNS`→assets, `ALLOCATES_BY`→key versions, `TRADES_IN`→markets, `HOLDS`→30 portfolio, `WITHIN`→87. Allocation audit traversals registered (80).

## 19. Digital Twin Mapping
Community twins aggregate member/building twins by reference (32 A7); shared-asset twins first-class; allocation what-ifs run on community twin state.

## 20. Governance
Owner: PI; per-community liaison; key-change approvals evidence member votes; annual envelope review vs 92 changes.

## 21. Versioning
Communities semver (membership churn = instance data; structural changes version). Catalogue 1.0.0.

## 22. Example Record

### EIG-CMU-0001 — Aran Islands Energy Co-operative
- type CT-COOP (→ REC envelope assessment per 92); legal entity EIG-ORG-0044; members: ~20 ODEON dwellings + CFOAT (contracts EIG-CON-001207 class; metering EIG-DS-00211 lineage); network NZ-00017 (13, proximity ✓)
- shared assets: community PV + storage candidates (11); sharing SK-PROP monthly, settlement-integrated (27), surplus→export at community tariff (EIG-TRF-0033)
- markets: LFM via aggregated participation (EIG-MKT-LFM-01); flexibility: portfolio EIG-FLX-PF-0007, opt-in register (18/20 members), benefit allocation = energy key (declared)
- governance: key changes by AGM vote (evidence refs); KPIs: self-consumption 64%, shared share 31%, flex revenue/member tracked (settlement fitness); the worked thread from 02→27→30→52→55 lands here as one governed collective

## 23. Completion Criteria
☑ All seven spec fields (ID, members, assets, markets, governance, energy sharing, flexibility) ☑ Type-bound regulatory envelopes ☑ Versioned sharing keys with settlement-class allocation + balance audits ☑ Individual flex opt-in + member privacy floors ☑ The Aran co-op worked end to end.
