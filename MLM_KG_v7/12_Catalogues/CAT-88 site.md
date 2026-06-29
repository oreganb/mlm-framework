---
type: catalogue
ontology: EIG-MLM
catalogue_no: 88
catalogue_id: EIG-CAT-088
entity: site
band: governance
status: active
tags: [eig-mlm, catalogue, band:governance, entity:site]
aliases: ["EIG-CAT-088", "Catalogue 88", "site"]
---

# CAT-88 — Site

> **EIG-CAT-088** · band: **Aggregation & Governance** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-02 building]] · [[CAT-04 space]] · [[CAT-08 geographic]] · [[CAT-11 asset]] · [[CAT-13 utility_network]] · [[CAT-15 organisation]] · [[CAT-35 edge_infrastructure]]
**Used by (downstream):** [[CAT-30 flexibility]] · [[CAT-32 digital_twin]] · [[CAT-62 control_strategy]] · [[CAT-86 community]] · [[CAT-87 district]] · [[CAT-90 project]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-088`
**Entity prefix:** `EIG-SIT-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** Operations with portfolio owners
**Depends on (upstream):** 02 building, 04 space, 08 geographic, 11 asset, 13 utility_network, 15 organisation, 35 edge_infrastructure
**Used by (downstream):** 30 (site portfolios), 32 (site twins), 62 (site strategies), 86/87 (composition), 90 (project sites)

---

## 1. Definition

The **Site Catalogue** registers sites and campuses (`EIG-SIT-*`): operationally coherent multi-building holdings — each defined by buildings (02), shared spaces (04), private networks (13), site assets (11), ownership (15), and boundaries (08). A site is the **operations** aggregate (one gate, one FM regime, often one private network behind shared connection points), distinct from the district (87 — the *accounting/civic* aggregate) and the community (86 — the *organisational-sharing* aggregate); the three compose without confusion because each is typed by its purpose. Purpose: **multi-building operations** — shared metering hierarchies, site-level dispatch and control, edge infrastructure siting (35 SITE pattern), and FM accountability live at this grain (A1).

## 2. Scope
**In scope:** site schema (the spec's seven: ID, buildings, spaces, networks, assets, ownership, boundaries); site types (campus, estate, multi-tenant complex, island-site pattern); metering hierarchy (site main ↔ building subs ↔ asset subs, MPRN/CP reconciliation hooks 13); shared-services allocation (plant rooms, EV hubs, DH loops); private-network ownership/operatorship; edge siting (35 EIG-EDGE-SITE binding); tenancy interfaces (multi-tenant data scoping). **Out of scope:** building internals (02/04), district balances (87), community sharing keys (86), FM workflow content (64 hosts the processes).

## 3. Classification Structure

### 3.1 Site types (`siteType`)
ST-CAMPUS (institutional: university/hospital/research — Tyndall-class) · ST-EST (housing estate/portfolio cluster) · ST-MTC (multi-tenant commercial complex) · ST-IND (industrial site) · ST-ISL (island/remote cluster — the Aran SITE-CLU pattern, 35).

### 3.2 Metering hierarchy classes
MH-FULL (every building + major asset sub-metered, settlement-capable at all grains) · MH-MAIN+ (site main + partial subs — allocation rules required for building grains, declared) · MH-MAIN (site main only — building-grain analytics estimated-class, flagged 40). Hierarchy class caps achievable fitness per grain (A3).

### 3.3 Shared-service allocation forms
Direct-metered · floor-area key · headcount key · usage-modelled (44 release, COMPUTED-class). Allocation outputs never settlement-grade unless direct-metered (27/40 chain).

## 4. Hierarchy
```
Site (EIG-SIT-*) — boundary (08) — ownership/operatorship (15)
  ├── buildings (02) + shared spaces (04) + site assets (11)
  ├── private network segments (13) behind shared CPs (crossing points explicit)
  ├── metering hierarchy (§3.2) → 37 streams
  └── edge site (35 EIG-EDGE-SITE-*) + site strategies (62) + site portfolio (30)
```

## 5. Field Groups & Fields

### 5.1 Site (`EIG-SIT-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | siteID | string | `EIG-SIT-\d{4}` | M | Spec: Site ID |
| 2 | name / siteType | string/enum | §3.1 | M | Identity |
| 3 | boundaries | json | →08 geometry + access points; legal vs operational boundary where they differ (declared) | M | Spec: Boundaries |
| 4 | ownership | json | {owner →15, operator →15 where distinct, FM responsibility, tenancy model} | M | Spec: Ownership |
| 5 | buildings | array<ref> | →02 EIG-BLD-* (membership versioned) | M | Spec: Buildings |
| 6 | sharedSpaces | array<ref> | →04 site-level spaces (plant rooms, car parks/EV hubs, external) | M | Spec: Spaces |
| 7 | networks | json | private segments (13) + shared CPs/MPRNs (crossing register; reconciliation hooks 13 V10 lineage) + DH/thermal loops (19) | M | Spec: Networks |
| 8 | siteAssets | array<ref> | →11 site-grain (central plant, PV arrays, storage, EV infrastructure) | M | Spec: Assets |
| 9 | meteringHierarchy | json | class §3.2 + tree {meter →36 refs, parent links}; sub-to-main balance rule (Σ subs ≤ main + tolerance, 40 RC-CONS lineage) | M | Truth structure |
| 10 | sharedServiceAllocation | json | per service: form §3.3 + key/model refs; tenant-billing interface notes (23) | C | Cost/energy attribution |
| 11 | edgeSiteRef | ref | →35 EIG-EDGE-SITE-* | C | Edge deployment anchor |
| 12 | dataScoping | json | multi-tenant visibility rules (16 scopes per tenancy; own-tenancy pattern) | M | Privacy structure |
| 13 | owner / version / status | — | standard | M | Governance |

## 6. Controlled Vocabularies
Site types (§3.1), metering classes (§3.2), allocation forms (§3.3), tenancy models, boundary kinds (legal/operational).

## 7. Applicability Rules
- **A1 — Operations grain.** Site-level control (62 strategies), dispatch coordination (60 site scope), and FM accountability bind here; building records stay authoritative for building facts (composition by reference, 87 A4 pattern).
- **A2 — Crossing points are the external truth.** Shared CPs/MPRNs enumerate; everything behind them is site-internal accounting; reconciliation with DSO records rides the 13 hooks.
- **A3 — Metering class caps claims.** Fitness per grain follows §3.2: MH-MAIN sites cannot produce building-grain settlement/RD evidence (40 floors); upgrades to hierarchy are the registered path to better claims.
- **A4 — Allocation honesty.** Shared-service allocations declare their form; non-direct-metered outputs carry estimated/COMPUTED class (38) and never enter settlement (27) or RD evidence (52 A1).
- **A5 — Tenant scoping is structural.** Multi-tenant sites enforce own-tenancy data scopes (16) at every surface (75 A3 pattern); landlord aggregate views respect tenant floors.
- **A6 — Island-site discipline.** ST-ISL sites inherit the 35 cluster-gateway/degraded-mode posture; site connectivity plans (Ubiquiti/Starlink lineage 34) register as site network facts.

## 8. Validation Rules
V1 composition refs resolve; membership versioned; V2 metering tree consistent (every building/asset stream parented; balance rule configured per 40); V3 crossing register complete vs 13 CP records; V4 allocation forms declared; class propagation verified (A4); V5 tenant scopes tested (privacy probes); V6 edge-site binding consistent with 35 records; V7 sites immutable per version.

## 9. Relationships to Other Catalogues
02/04/11/13 (composition; crossing/reconciliation) · 15/23 (ownership/operatorship; tenancy terms) · 35/34 (edge sites; connectivity) · 30/62/60 (site portfolios, strategies, dispatch scope) · 86/87 (sites within communities/districts — typed-aggregate composition) · 37/40 (metering streams; balance rules; fitness caps) · 90 (project pilot sites: Aran, Limerick HeatSync-lineage sites) · 32 (site twins).

## 10. Benchmarking Requirements
Site cohorts (65): site EUI, shared-service overheads, metering-class distribution — like-type comparisons (ST-* matched).

## 11. Dataset Requirements
Site hierarchies + crossing registers ship with site datasets (43) so consumers can compute correctly-grained aggregates.

## 12. Feature Requirements
Site features (41): coincidence factors across buildings, shared-plant load shares — for site optimisation models.

## 13. Model Requirements
Site-grain models (SER-02 portfolio, SER-12 site scheduling, SER-10 private-network) bind composition by reference; allocation models (§3.3 usage-modelled) are 44 releases.

## 14. KPI Requirements
Site KPIs (57) at declared grains with metering-class caveats (A3 → QC codes); tenant-facing KPIs scoped (A5).

## 15. Response Derivative Requirements
Site portfolios (30) aggregate building/asset RDs under site simultaneity; site dispatch (60/62) coordinates within per-subject constraints; MH-class determines which subjects can contribute RD evidence at all (A3/A4 — the evidence bar respects metering reality).

## 16. Decision Requirements
Site strategies (62) host coordinated control (central plant + building interplay); site decisions respect tenant boundaries (no cross-tenant actuation without terms, 23/59 CC-CON).

## 17. Ontology Mapping
Site ↔ eig:Site (BOT/SAREF4BLDG site alignment candidates per 79 backlog); metering hierarchy ↔ system-of-systems pattern; owned by 79.

## 18. Knowledge Graph Mapping
Node `Site`; edges `CONTAINS`→02/04/11, `CONNECTS_VIA`→13 CPs, `METERED_BY` tree→36, `OPERATED_BY`→15, `HOSTS_EDGE`→35, `WITHIN`→86/87. Hierarchy traversals power correctly-grained aggregation queries.

## 19. Digital Twin Mapping
Site twins compose building twins + site assets by reference (32 A7); plant-room twins first-class; the site view is the FM operations surface (73 app embedding).

## 20. Governance
Owner: Operations; per-site responsible owner (FM); metering-class upgrade plans tracked; tenancy-scope reviews with DPO where residential.

## 21. Versioning
Sites semver. Catalogue 1.0.0.

## 22. Example Record

### EIG-SIT-0007 — Inis Mór ODEON cluster site (ST-ISL)
- boundary: operational cluster footprint (08); ownership: dwellings individually owned, co-op operatorship for shared systems (EIG-ORG-0044), CFOAT office distinct owner
- buildings: 20 ODEON dwellings + CFOAT; networks: behind NZ-00017 CPs (crossing register = the 13 CP-004388 lineage); metering MH-FULL (TR-A per dwelling — the evidence-grade backbone)
- site assets: cluster PV/storage candidates; edge: EIG-EDGE-SITE-0007 (cluster-gateway LoRaWAN, Ubiquiti+Starlink connectivity per 34); strategies: EIG-CTL-RES-THERM-01 deployments; portfolio EIG-FLX-PF-0007
- dataScoping: own-dwelling scopes; co-op admin aggregate per member terms; the site is the operational chassis under the community (86) and district (87) layers — three typed aggregates, one coherent stack

## 23. Completion Criteria
☑ All seven spec fields (ID, buildings, spaces, networks, assets, ownership, boundaries) ☑ Operations-grain identity distinct from district/community (typed aggregates) ☑ Metering hierarchy with fitness caps + allocation honesty ☑ Crossing-point truth + tenant scoping ☑ Island-site posture ☑ The Aran cluster site closing the spatial stack.
