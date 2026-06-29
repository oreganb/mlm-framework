---
type: catalogue
ontology: EIG-MLM
catalogue_no: 15
catalogue_id: EIG-CAT-015
entity: organisation
band: actors
status: active
tags: [eig-mlm, catalogue, band:actors, entity:organisation]
aliases: ["EIG-CAT-015", "Catalogue 15", "organisation"]
---

# CAT-15 — Organisation

> **EIG-CAT-015** · band: **Actors & Organisations** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Organisation Catalogue

## 1. Definition

The Organisation Catalogue is the authoritative register of every organisational entity known to the EIG MLM framework: companies, utilities, municipalities, community organisations, research bodies, regulators, market operators and project partners. An organisation is a legal or formally constituted entity that can hold obligations, own assets, employ users, enter contracts and be a party to projects. Organisations provide the legal anchor behind actors (catalogue 14): every actor with `legalPersonality: legal_entity` MUST resolve to exactly one organisation record here.

This catalogue defines organisational identity, legal identity, typology, sector, ownership structure, governance structure, contact points and associations to actors, contracts, assets, projects and users. No other catalogue may define organisational identity.

## 2. Scope

In scope:

- All legal entities participating in or governing any EIG MLM activity: energy suppliers, DSOs/TSOs, aggregator companies, ESCOs, technology vendors, facility-management firms, municipalities and local authorities, community energy organisations (CECs/RECs and their legal vehicles), housing bodies, universities and research institutes, regulators, standards bodies, market/exchange operators, project consortia partners, platform operators.
- Internal organisational units (departments, business units, campuses) where they hold distinct responsibilities, budgets or asset portfolios — modelled as child organisations.
- Dissolved/merged organisations retained for audit and settlement history.

Out of scope:

- Natural persons (actor catalogue 14, user catalogue 17).
- Informal stakeholder groupings without legal form (stakeholder catalogue 18).
- Market roles as such (role catalogue 16); an organisation HOLDS roles via its actors.
- Contract content (contract catalogue 23), project content (project catalogue 90).

## 3. Classification Structure

Organisation type (`orgType`) — closed vocabulary, single value:

| Code | Type | Definition |
|---|---|---|
| ORG-COM | commercial_company | Private/public limited company trading for profit |
| ORG-UTL | utility | Licensed energy/water utility (supply, generation, distribution) |
| ORG-DSO | network_operator_dso | Licensed electricity/gas distribution system operator |
| ORG-TSO | network_operator_tso | Licensed transmission system operator |
| ORG-MUN | municipality | Local/regional government authority |
| ORG-GOV | government_body | National government department or state agency |
| ORG-CEO | community_energy_org | Community energy organisation; legal vehicle for CEC/REC (CLG, co-operative) |
| ORG-HSG | housing_body | Approved housing body, housing association, local-authority housing unit |
| ORG-RES | research_body | University, institute, RTO |
| ORG-REG | regulator | Statutory regulatory authority (e.g. CRU, EPA, DPC) |
| ORG-MKO | market_operator_org | Exchange/market/registry operating company (e.g. SEMO, EPEX) |
| ORG-STD | standards_body | Standards development organisation (NSAI, CEN/CENELEC, ISO, IEC) |
| ORG-NGO | ngo_association | Non-profit association, industry body, NGO |
| ORG-CON | consortium | Project consortium or joint venture without (or pending) separate legal personality |
| ORG-UNIT | internal_unit | Child organisational unit of a parent organisation (department, campus, business unit) |

Sector (`sector`) — NACE Rev.2.1 section + division, plus EIG sector tag aligned with building_classification sector axis (catalogue 01) where the organisation occupies buildings.

Legal form (`legalForm`) — closed vocabulary: ltd, plc, dac, clg, ucc_chartered, cooperative, partnership, sole_trader, statutory_body, local_authority, unincorporated, sec_ie (societas europaea), other_eu_form.

## 4. Hierarchy

```text
Organisation (root, parentOrgID = null)
└── Organisation (ORG-UNIT or subsidiary; parentOrgID set)
    └── ... (max depth 4)
```

- Ownership hierarchy (parent/subsidiary, equity) and management hierarchy (internal units) both use `parentOrgID` with `hierarchyKind: ownership | management`.
- Cycles are illegal (validation V4). A subsidiary with its own legal identity is a full record (own `legalRegistrationID`); an internal unit inherits the parent's legal identity (`legalRegistrationID` null, V5).
- Consortia (ORG-CON) reference member organisations via `memberOrgIDs` rather than parentage.

## 5. Field Groups & Fields

### 5.1 Identity

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | orgID | string | — | `EIG-ORG-NNNN` | M | Unique catalogue identifier |
| 2 | orgName | string | — | UTF-8 ≤ 200 chars | M | Registered/official name |
| 3 | tradingName | string | — | UTF-8 ≤ 200 chars | O | Trading-as name if different |
| 4 | orgType | enum | — | §3 codes | M | Organisation type |
| 5 | legalForm | enum | — | §3 legal forms | M | Legal form |
| 6 | parentOrgID | string | — | EIG-ORG-NNNN or null | C | Parent organisation; null for roots |
| 7 | hierarchyKind | enum | — | ownership, management | C | Required if parentOrgID set |
| 8 | memberOrgIDs | array | — | EIG-ORG-NNNN[] | C | ORG-CON only: consortium members |
| 9 | status | enum | — | active, dormant, dissolved, merged, pending | M | Lifecycle status |
| 10 | successorOrgID | string | — | EIG-ORG-NNNN | C | Required if status merged/dissolved with successor |

### 5.2 Legal identity & registration

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | legalRegistrationID | string | — | e.g. CRO number | C | Company/charity registration number; null for ORG-UNIT |
| 2 | legalRegistry | enum | — | cro_ie, charities_reg_ie, companies_house_uk, eu_bris, other, none | C | Registry of record |
| 3 | leiCode | string | — | ISO 17442 LEI, 20 chars | O | Legal Entity Identifier (mandatory for market-settlement parties, V7) |
| 4 | vatNumber | string | — | EU VAT format | O | VAT registration |
| 5 | eoriNumber | string | — | EORI format | O | Customs identifier where relevant |
| 6 | jurisdiction | string | — | ISO 3166-1 alpha-2 + EIG-GEO ref | M | Country + EIG-GEO-* of registered seat |
| 7 | registeredAddress | object | — | address block + Eircode (GL-06/GL-07 ref) | M | Registered office; geocoded to EIG-GEO |
| 8 | incorporationDate | date | — | ISO 8601 | C | Date of incorporation/establishment |
| 9 | licences | array | — | [{licenceType, issuer, ref, validFrom, validTo, status}] | C | Energy supply/generation/network licences etc.; issuer is an ORG-REG org |

`licenceType` vocabulary: electricity_supply, electricity_generation, gas_supply, gas_shipping, dso_licence, tso_licence, market_operator_licence, aggregator_registration, esco_registration, research_accreditation, other.

### 5.3 Sector & activity

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | naceCode | string | — | NACE Rev.2.1 `S.dd.dd` | M | Primary economic activity |
| 2 | naceSecondary | array | — | NACE codes | O | Secondary activities |
| 3 | eigSectorTag | enum | — | building_classification sector codes (cat. 01) | O | EIG sector alignment where org occupies/operates buildings |
| 4 | sizeClass | enum | — | micro, small, medium, large (EU 2003/361/EC) | M | Enterprise size class |
| 5 | employeeCount | integer | persons | ≥ 0 | O | Headcount (FTE) |
| 6 | annualTurnover | decimal | EUR | ≥ 0 | O | Latest annual turnover |
| 7 | publicBody | boolean | — | true/false | M | Subject to public-sector obligations (S.13 / public procurement) |
| 8 | sme | boolean | — | true/false | M | EU SME status (derived from sizeClass, V8) |

### 5.4 Ownership & governance structure

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | ownershipModel | enum | — | private, public_listed, state_owned, municipal, cooperative, community_owned, member_owned, academic, mixed | M | Ownership model |
| 2 | owners | array | — | [{ownerOrgID or ownerDesc, sharePct}] | C | Owners ≥ 5% equity; sharePct sums ≤ 100 (V9) |
| 3 | ultimateParentOrgID | string | — | EIG-ORG-NNNN | C | Ultimate beneficial parent if in catalogue |
| 4 | governanceModel | enum | — | board_of_directors, council, committee, member_assembly, sole_director, partnership_board, statutory_board | M | Governing body form |
| 5 | governanceNotes | string | — | ≤ 1000 chars | O | One-member-one-vote, golden share, etc. |
| 6 | decisionAuthorityClass | enum | — | autonomous, parent_constrained, member_mandated, statutory_mandated | M | How freely the org can commit (bounds contract/market decisions, §16) |
| 7 | beneficialOwnershipRegisterRef | string | — | RBO ref | O | Beneficial ownership filing reference |

### 5.5 Contact points

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | contactPoints | array | — | [{contactRole, name?, email, phone?, userID?}] | M | ≥ 1 entry; role-addressed preferred over named persons (GDPR minimisation) |
| 2 | website | string | — | URL | O | Public website |
| 3 | invoiceAddress | object | — | address block | C | If different from registered address |
| 4 | noticeAddress | object | — | address block/email | C | Legal notices address per contracts |

`contactRole` vocabulary: general, legal, billing, technical, data_protection_officer, energy_manager, facilities, market_operations, emergency_24_7, project_coordinator, press. `emergency_24_7` mandatory for ORG-DSO/ORG-TSO/ORG-UTL (V10). `data_protection_officer` mandatory for any org processing personal data on the platform (V11).

### 5.6 Associations

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | actorIDs | array | — | EIG-ACT-NNNNN[] | C | Actors belonging to this org (mirror of actor.belongsToOrg; reconciled, V12) |
| 2 | primaryActorID | string | — | EIG-ACT-NNNNN | C | Default acting identity for the org |
| 3 | contractIDs | array | — | EIG-CON-* refs | O | Contracts the org is party to (maintained by contract catalogue 23) |
| 4 | ownedAssetIDs | array | — | EIG-AST-NNNNNN[] | O | Assets owned (mirror of asset.ownerActor→org; reconciled V13) |
| 5 | ownedBuildingIDs | array | — | EIG-BLD-NNNNNN[] | O | Buildings owned/leased (per building cat. 02 tenure block) |
| 6 | projectIDs | array | — | EIG-PRJ-* refs | O | Projects participated in (catalogue 90) |
| 7 | userIDs | array | — | EIG-USR-* refs | O | Platform users employed/authorised by the org (catalogue 17) |
| 8 | roleIDs | array | — | EIG-ROL-* refs | O | Organisational roles held (catalogue 16), e.g. data_controller |

### 5.7 Audit & metadata

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | recordVersion | string | — | semver | M | Record version |
| 2 | createdDate / modifiedDate | datetime | — | ISO 8601 UTC | M | Timestamps |
| 3 | dataSteward | string | — | userID | M | Responsible steward |
| 4 | sourceOfTruth | enum | — | public_registry, self_declared, contract_pack, project_grant_agreement | M | Provenance of legal data |
| 5 | lastRegistryCheck | date | — | ISO 8601 | C | Last reconciliation against public registry (≤ 12 months for active settlement parties, V14) |
| 6 | governanceStatus | enum | — | draft, in_review, approved, deprecated | M | Catalogue governance state |

## 6. Controlled Vocabularies

Closed (changes require catalogue release): orgType, legalForm, hierarchyKind, status, legalRegistry, licenceType, ownershipModel, governanceModel, decisionAuthorityClass, contactRole, sizeClass, sourceOfTruth, governanceStatus.

External authorities: NACE Rev.2.1 (Eurostat), ISO 17442 (LEI/GLEIF), EU 2003/361/EC (SME), ISO 3166-1, CRO/RBO registries.

## 7. Applicability Rules

| Rule | Statement |
|---|---|
| A1 | Every actor with legalPersonality legal_entity MUST reference an organisation here (actor.belongsToOrg) |
| A2 | ORG-DSO/ORG-TSO/ORG-UTL/ORG-MKO require ≥ 1 active licence record matching their type |
| A3 | ORG-CEO must declare ownershipModel ∈ {cooperative, community_owned, member_owned} and governanceModel member_assembly or committee |
| A4 | ORG-UNIT may not hold licences, LEIs or market registrations; these attach to the legal parent |
| A5 | ORG-CON requires memberOrgIDs (≥ 2) and a coordinator contactPoint (project_coordinator) |
| A6 | Settlement counterparties (via their actors' BRP/market registrations) require leiCode and lastRegistryCheck ≤ 12 months |
| A7 | publicBody = true triggers public-sector decarbonisation obligations context (links PATH2ZERO-style policy contexts, catalogues 91–93) |

## 8. Validation Rules

| ID | Rule | Severity |
|---|---|---|
| V1 | orgID unique, immutable, pattern-valid | error |
| V2 | orgName + jurisdiction unique among status ≠ dissolved (duplicate-entity guard) | error |
| V3 | status transitions only along pending→active→{dormant→active, merged, dissolved}; merged/dissolved terminal | error |
| V4 | No cycles in parentOrgID graph; depth ≤ 4 | error |
| V5 | ORG-UNIT ⇒ legalRegistrationID null AND parentOrgID set; non-UNIT root ⇒ legalRegistrationID present unless legalForm unincorporated | error |
| V6 | licences[].issuer must be an ORG-REG organisation; validTo ≥ validFrom | error |
| V7 | leiCode present & GLEIF-valid for orgs whose actors hold settlementParty flag | error |
| V8 | sme consistent with sizeClass; sizeClass consistent with employeeCount/turnover where given | warning |
| V9 | owners[].sharePct each ∈ (0,100], sum ≤ 100 | error |
| V10 | emergency_24_7 contact present for ORG-DSO/TSO/UTL | error |
| V11 | DPO contact present where org's userIDs/actors process personal data | error |
| V12 | actorIDs mirror actor.belongsToOrg exactly (bidirectional reconciliation) | error |
| V13 | ownedAssetIDs reconcile with asset catalogue ownership chain | error |
| V14 | lastRegistryCheck ≤ 12 months for active settlement parties | warning |
| V15 | successorOrgID required and resolvable when status merged; asset/contract reassignment workflow completed before merge finalises | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 14 actor | Org ←belongsTo— Actor; org acts only through its actors |
| 16 role | Org holds organisational roles (data_controller, project_coordinator, scheme_operator) |
| 17 user | Users are employed/authorised by orgs; org offboarding cascades user review |
| 18 stakeholder | Stakeholder records may reference an orgID as their formal vehicle |
| 02 building / 11 asset | Ownership/tenure references resolve to orgIDs (via actors) |
| 13 utility_network | Network operator orgs own networks; licence A2 gate |
| 19–28 market layer | Market/registry operators, BRP companies, settlement counterparties are orgs; LEI gate V7 |
| 23 contract | Contract parties resolve to org legal identities |
| 86–88 community/district/site | Community orgs (ORG-CEO) are legal vehicles of communities |
| 90 project | Project partners are orgs; grant-agreement data feeds sourceOfTruth |
| 91–94 policy/regulation/compliance/standard | Regulators (ORG-REG) and standards bodies (ORG-STD) issue instruments; publicBody flag binds obligations |

## 10. Benchmarking Requirements

Organisations are cohort dimensions, not benchmark subjects: building (06) and asset (12) benchmark cohorts may stratify by orgType, sizeClass, ownershipModel, publicBody (e.g. "municipal building stock vs commercial"). Portfolio-level KPI roll-ups aggregate over ownedBuildingIDs/ownedAssetIDs. Min cohort n = 10 applies; org-identified statistics require org consent or public-body status.

## 11. Dataset Requirements

Org reference data joins datasets via orgID. Required for: portfolio analytics (org→buildings→meters), market datasets (counterparty dimension), project reporting (partner dimension). Registry snapshots (CRO/GLEIF) are versioned reference datasets with lastRegistryCheck lineage. Personal names inside contactPoints are GDPR personal data — excluded from analytical datasets; role-addressed contacts only.

## 12. Feature Requirements

Allowed model features: orgType, sizeClass, ownershipModel, publicBody, sector (NACE section), portfolio aggregates (building count, total floor area, asset mix). Forbidden as features: legal identifiers (orgID one-hot beyond portfolio models, LEI, VAT), contact data. Org features enter building/asset models only as cohort/context variables, never as primary predictors of physical behaviour.

## 13. Model Requirements

Portfolio optimisation and public-sector decarbonisation pathway models (PATH2ZERO class) operate at org scope: inputs = org's building/asset registers + obligations from policy catalogue. Market models use org credit/size attributes for counterparty risk. ABS models may instantiate organisational agents (aggregator companies, suppliers) parameterised by decisionAuthorityClass.

## 14. KPI Requirements

Org-scope KPIs: portfolio energy intensity, portfolio emissions (location/market-based), renovation rate, obligation compliance %, flexibility revenue by org, project delivery KPIs. KPI records (57) carry orgID scope dimension; public-body KPIs feed statutory reporting (compliance catalogue 93).

## 15. Response Derivative Requirements

No RDs at org level directly; aggregator-company RDs live on their aggregator ACTOR pool (14/52). However org decisionAuthorityClass bounds RD utilisation: member_mandated orgs (co-ops) may require assembly-approved envelopes before pool RDs are offered to markets — encoded as RD utilisation constraints (59).

## 16. Decision Requirements

Decisions binding an organisation (contract execution, market registration, capex) validate decisionAuthorityClass and the deciding user's role authority (16/17). parent_constrained orgs require parent-org approval workflow. Public bodies route procurement-relevant decisions through compliance checks (93).

## 17. Ontology Mapping

Classes: Organisation ⊐ {Company, Utility, NetworkOperator, Municipality, GovernmentBody, CommunityEnergyOrganisation, HousingBody, ResearchBody, Regulator, MarketOperatorOrg, StandardsBody, NGO, Consortium, InternalUnit}.
Object properties: hasParentOrganisation, hasMember (consortium), employs (→ User), hasActor, ownsBuilding, ownsAsset, partyToContract, participatesInProject, holdsLicence, regulatedBy, hasContactPoint.
Datatype properties: orgID, orgName, orgType, legalForm, leiCode, naceCode, sizeClass, publicBody, ownershipModel, decisionAuthorityClass, status.
Alignment: W3C ORG ontology (org:Organization, org:OrganizationalUnit, org:subOrganizationOf ≡ hasParentOrganisation⁻¹), FOAF, schema.org Organization, GLEIF L1 vocabulary.

## 18. Knowledge Graph Mapping

Nodes: Organisation. Relationships: PARENT_OF, MEMBER_OF (consortium), EMPLOYS, HAS_ACTOR, OWNS (→ Building/Asset), PARTY_TO, PARTICIPATES_IN (→ Project), HOLDS_LICENCE (→ Regulation node), REGULATED_BY, SUCCEEDED_BY. Keys: orgID; secondary keys legalRegistrationID, leiCode. Contact personal data excluded from KG.

## 19. Digital Twin Mapping

Organisations are not twinned. Building/Asset/Community/Market twins carry an OrgContext (owner org, operator org, supplier org, publicBody flag, decisionAuthorityClass) resolved from this catalogue; refreshed on org-state change events (merger, licence lapse → twin flags affected market capabilities).

## 20. Governance

Owner: EIG Catalogue Owner; legal data co-owned by Legal/Contracts function. Registry reconciliation job (CRO/GLEIF) runs monthly for settlement parties, annually otherwise. Mergers/dissolutions execute a guarded workflow: reassign assets, novate contracts, transfer users, set successorOrgID, then close. DPO reviews contactPoints quarterly for data minimisation.

## 21. Versioning

Record-level semver; legal-identity changes (name, registration, LEI) are minor versions with registry evidence attached; orgType changes are major versions requiring approval. Dissolved/merged records immutable thereafter; retention per compliance catalogue (93).

## 22. Example Records

```text
orgID: EIG-ORG-0044 | orgName: Aran Islands Energy Co-operative Society Ltd | tradingName: Comharchumann Fuinnimh Oileáin Árann
orgType: ORG-CEO | legalForm: cooperative | status: active | jurisdiction: IE / EIG-GEO-IE-CTY-0009 (Galway)
legalRegistrationID: CRO-654321 | legalRegistry: cro_ie | naceCode: D.35.11 | sizeClass: micro | sme: true | publicBody: false
ownershipModel: member_owned | governanceModel: member_assembly | decisionAuthorityClass: member_mandated
registeredAddress: Cill Rónáin, Inis Mór, Co. na Gaillimhe (GL-06: H91) | incorporationDate: 2014-09-02
licences: [{aggregator_registration, issuer: EIG-ORG-0102 (CRU), ref: AGR-2025-117, 2025-04-01, 2028-03-31, active}]
contactPoints: [{general, eolas@..., —}, {project_coordinator, …}, {data_protection_officer, dpo@…}]
actorIDs: [EIG-ACT-00031] | primaryActorID: EIG-ACT-00031 | ownedAssetIDs: [EIG-AST-007740 (community battery)]
projectIDs: [EIG-PRJ-0007 (ODEON/O-CEI pilot)] | dataSteward: EIG-USR-00012 | recordVersion: 1.3.0 | governanceStatus: approved
```

```text
orgID: EIG-ORG-0102 | orgName: Commission for Regulation of Utilities | orgType: ORG-REG | legalForm: statutory_body
status: active | jurisdiction: IE | publicBody: true | sizeClass: medium | ownershipModel: state_owned
governanceModel: statutory_board | decisionAuthorityClass: statutory_mandated | naceCode: O.84.13
contactPoints: [{general}, {market_operations}] | sourceOfTruth: public_registry | governanceStatus: approved
```

```text
orgID: EIG-ORG-0061 | orgName: Cork City Council | orgType: ORG-MUN | legalForm: local_authority | publicBody: true
jurisdiction: IE / EIG-GEO-IE-CTY-0004 | ownershipModel: municipal | governanceModel: council
decisionAuthorityClass: statutory_mandated | ownedBuildingIDs: [412 buildings incl. EIG-BLD-001203…]
roleIDs: [EIG-ROL-DCTRL (data_controller, public lighting telemetry)] | naceCode: O.84.11 | sizeClass: large
```

## 23. Completion Criteria

Complete when: every legal-entity actor resolves to exactly one org; all licence-gated org types carry valid licences; settlement parties carry GLEIF-valid LEIs; bidirectional actor/asset reconciliations are zero-diff; merger/dissolution workflow tested; contact registers satisfy V10/V11; Phase 14 confirms no other catalogue defines organisational identity.
