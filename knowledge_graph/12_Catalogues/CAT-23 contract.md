---
type: catalogue
ontology: EIG-MLM
catalogue_no: 23
catalogue_id: EIG-CAT-023
entity: contract
band: markets
status: active
tags: [eig-mlm, catalogue, band:markets, entity:contract]
aliases: ["EIG-CAT-023", "Catalogue 23", "contract"]
---

# CAT-23 — Contract

> **EIG-CAT-023** · band: **Markets, Tariffs & Settlement** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Contract Catalogue

## 1. Definition

The Contract Catalogue is the authoritative register of contracts and agreements that bind parties within the EIG MLM framework: supply contracts, flexibility contracts, P2P agreements, ESCO contracts, community agreements, market participation agreements, data-sharing agreements and the settlement terms, penalties and performance obligations they carry. A contract is a legally or formally binding agreement between identified parties (actors/organisations) that confers rights and obligations enforceable through settlement, penalties or governance.

This catalogue defines contract identity, typology, parties, term structure, commercial terms (linked tariffs/products), performance obligations, penalty regimes, settlement terms, eligibility effects (what the contract entitles parties to do in the platform) and lifecycle. Contracts are the authorisation substrate for market activity: enrolments (14), tariff bindings (21), product offers (22) and settlements (27) all trace to contract clauses.

## 2. Scope

In scope:

- Supply contracts (supplier↔customer per connection point; import/export terms; tariff plan binding).
- Flexibility contracts (aggregator↔flexumer enrolment agreements; aggregator↔DSO/TSO framework and call-off contracts from LFM tenders; ASM provider agreements).
- P2P agreements (matched-trade master agreements inside MKT-P2P).
- ESCO contracts (energy performance contracts with guaranteed-savings clauses and M&V plans).
- Community agreements (CEC membership terms, sharing-scheme rules, community-asset use agreements).
- Market participation agreements (T&SC accession, LFM platform terms).
- Data-sharing/processing agreements (controller–processor terms; research data-access agreements; links 14 consents, 17 A4, 93).
- Template registry: reusable contract templates with parameter slots.

Out of scope:

- Tariff price structures (21), product physical specs (22), trade instances (25), settlement computation (27) — contracts BIND these by reference.
- General procurement/legal contracts with no platform-operational effect (tracked organisationally, not here).

## 3. Classification Structure

Contract type (`conType`) — closed vocabulary:

| Code | Type |
|---|---|
| CON-SUP | supply_contract |
| CON-FLX-ENR | flexibility_enrolment (aggregator↔end party) |
| CON-FLX-FRM | flexibility_framework (provider↔SO; tender-awarded) |
| CON-FLX-CO | flexibility_call_off (under framework) |
| CON-P2P | p2p_master_agreement |
| CON-ESC | esco_performance_contract |
| CON-CMU | community_agreement |
| CON-MKT | market_participation_agreement |
| CON-DSA | data_sharing_agreement |
| CON-DPA | data_processing_agreement |
| CON-O&M | operations_maintenance (asset service contracts with performance SLAs) |

Form: bilateral, multilateral, accession (party joins standing terms), framework_with_calloffs. Basis: signed_instrument, accepted_terms (click-wrap), statutory_scheme, smart_contract_backed (executable representation EXISTS alongside, never instead of, legal terms).

## 4. Hierarchy

```text
ContractTemplate (EIG-CON-TPL-NNNN)        ← versioned reusable terms with parameter slots
└── Contract (EIG-CON-NNNNNN)              ← executed instance
    ├── Clause register (typed, machine-referenced)
    ├── Schedule/Annex refs (tariff plan, product list, M&V plan, site list)
    └── CallOff children (CON-FLX-CO under CON-FLX-FRM)
Amendment (append-only events on a Contract)
```

## 5. Field Groups & Fields

### 5.1 Identity & parties

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | contractID | string | — | `EIG-CON-NNNNNN` | M | Unique contract ID |
| 2 | templateRef | object | — | {templateID, version} | C | Source template + version pinned |
| 3 | conType | enum | — | §3 codes | M | Contract type |
| 4 | form | enum | — | §3 forms | M | Structural form |
| 5 | basis | enum | — | §3 bases | M | Formation basis |
| 6 | parties | array | — | [{partyRole, actorID, orgID?, signatory: userID/evidence, capacity}] | M | ≥ 2; partyRole from closed list (supplier, customer, aggregator, flex_provider, system_operator, esco, beneficiary, community, member, controller, processor, operator, participant) |
| 7 | governingLaw | string | — | jurisdiction (IE default) | M | Governing law |
| 8 | parentContractID | string | — | EIG-CON | C | For call-offs/amendment chains |
| 9 | status | enum | — | draft, executed, active, suspended, expired, terminated | M | Lifecycle |
| 10 | executionDate | date | — | ISO 8601 | C | When executed |
| 11 | confidentialityClass | enum | — | public, internal, confidential | M | Disclosure class |

### 5.2 Term & validity

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | effectiveFrom / effectiveTo | date | — | ISO 8601; open-ended allowed | M | Operative window |
| 2 | renewalModel | enum | — | none, auto_renew, renewal_by_agreement, evergreen_with_notice | M | Renewal |
| 3 | noticePeriodDays | integer | days | ≥ 0 | C | Termination notice |
| 4 | coolingOffDays | integer | days | consumer law minimums | C | Mandatory for consumer-facing types (V8) |
| 5 | earlyTerminationTerms | object | — | {feeModel, exceptions[]} | C | Exit economics |
| 6 | suspensionTriggers | array | — | clause refs | O | What suspends performance |

### 5.3 Commercial bindings

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | tariffBindings | array | — | [{tariffID+version policy, scope: CP refs}] | C | CON-SUP: plan per connection point (21 A1) |
| 2 | productBindings | array | — | [{productID, role: may_offer/must_deliver, capacity bounds}] | C | Flex/market contracts |
| 3 | marketBindings | array | — | [{marketID, registrationRef}] | C | CON-MKT accessions |
| 4 | serviceEnrolments | array | — | EIG-FS-* (29) | C | Services activated under this contract |
| 5 | assetScope | array | — | EIG-AST/EIG-BLD refs | C | Assets/buildings committed |
| 6 | revenueSharing | object | — | {model, splits[], floor/cap} | C | Aggregator↔flexumer value sharing (must sum 100%, V9) |
| 7 | priceTerms | object | — | references 21/25 mechanisms; NO free-floating prices outside tariff/trade structures (V10) | C | Commercial price hooks |

### 5.4 Performance obligations & penalties

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | obligations | array | — | [{oblID, party, description, metricRef (57/12/22), target, window, evidence}] | M | Typed, measurable obligations |
| 2 | slaTerms | array | — | [{slaID, metric, threshold, measurementWindow, remedy}] | C | Service levels (O&M, platform, telemetry latency) |
| 3 | penaltyRegime | array | — | [{penID, trigger (obligation/SLA breach, non-delivery grade per 22 thresholds), computation: formula/schedule, cap, currency}] | C | Executable penalty rules (27 applies them) |
| 4 | incentiveRegime | array | — | bonuses/gainshare mirrors of penalties | C | E.g. ESCO gainshare |
| 5 | guarantees | array | — | [{kind: savings/performance/availability, baselineRef (M&V plan), guaranteedValue, sharePct}] | C | ESCO guaranteed savings etc. |
| 6 | mvPlanRef | string | — | M&V plan doc (IPMVP option stated) | C | Mandatory for CON-ESC and baseline-settled flex (V11) |
| 7 | liabilityCaps | object | — | {aggregate, perEvent, exclusions} | M | Liability limits |
| 8 | forceMajeure | string | — | clause ref | M | FM treatment |

### 5.5 Settlement terms

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | settlementCycle | enum | — | monthly_billing, market_cycle, per_event, quarterly_reconciliation | M | Cadence |
| 2 | paymentTermsDays | integer | days | ≥ 0 | M | Payment terms |
| 3 | invoicingParty | string | — | partyRole | M | Who invoices |
| 4 | disputeWindowDays | integer | days | ≥ 0 | M | Challenge window post-statement (27) |
| 5 | interestOnLate | object | — | statutory/contractual rate | O | Late payment |
| 6 | securityRequirements | object | — | deposits/collateral mirrors of 20 credit cover | C | Financial security |
| 7 | settlementDataSpec | array | — | datasets/meters authorised as settlement evidence (38/13) | M | Evidence basis |

### 5.6 Data & consent terms (where applicable)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | dataScopes | array | — | consent-scope codes (14 consent register alignment) | C | What data flows the contract authorises |
| 2 | processingRoles | object | — | {controller: party, processors: parties[]} | C | CON-DPA/DSA mandatory |
| 3 | retentionRef | string | — | 93 schedule | C | Data retention binding |
| 4 | subProcessing | object | — | approval model, register ref | C | DPA chains |

### 5.7 Record metadata

recordVersion, createdDate, modifiedDate, owner (contract manager userID), documentVaultRef (signed instrument location), governanceStatus — programme standard.

## 6. Controlled Vocabularies

Closed: conType, form, basis, partyRole, status, renewalModel, settlementCycle, confidentialityClass, obligation metric-kinds, penalty trigger kinds. External anchors: consumer protection (cooling-off minimums), GDPR Art. 28 (DPA mandatory content), IPMVP options (M&V), T&SC accession forms.

## 7. Applicability Rules

| Rule | Statement |
|---|---|
| A1 | No platform-operational entitlement without contractual basis: tariff enrolment, product offering, service enrolment, market registration and settlement participation each trace to an active contract clause (the entitlement chain) |
| A2 | CON-FLX-ENR must align with the actor consent register (14): dataScopes ⊆ granted consents; consent withdrawal suspends dependent contract performance per clause |
| A3 | Framework→call-off: call-offs cannot exceed framework capacity/price envelopes; aggregate call-off commitments tracked against framework caps |
| A4 | CON-ESC requires mvPlanRef with named IPMVP option and baseline dataset references before guarantees activate |
| A5 | Penalty regimes must be computable by settlement (27) from registered evidence alone — no manually-asserted penalty inputs |
| A6 | CON-DPA mandatory wherever a processor party handles personal data (joins 15 V11, 17 GDPR fields); research access requires CON-DSA per 17 A4 |
| A7 | Community agreements bind members to scheme rules (86) and allocation tariffs (21 TRF-CMU); membership exit triggers allocation re-baselining |
| A8 | Smart-contract-backed basis: executable code is a REPRESENTATION; legal text prevails; code hash pinned in record |

## 8. Validation Rules

| ID | Rule | Severity |
|---|---|---|
| V1 | contractID unique; parties ≥ 2 distinct; party actors/orgs active at execution | error |
| V2 | Status transitions draft→executed→active→{suspended↔active, expired, terminated}; terminal states immutable | error |
| V3 | effectiveFrom ≤ effectiveTo; child call-off windows ⊆ framework window | error |
| V4 | templateRef version exists; deviations from template logged as clause diffs | warning |
| V5 | Bindings resolve: tariff plans published, products active, markets live, services registered | error |
| V6 | Obligations measurable: metricRef resolves to KPI/benchmark/product-threshold definitions | error |
| V7 | Penalty formulas evaluable against declared evidence spec; caps present | error |
| V8 | coolingOffDays ≥ statutory minimum for consumer parties | error |
| V9 | revenueSharing splits sum to 100%; floors/caps consistent | error |
| V10 | No price terms outside tariff/trade/penalty structures (single pricing truth, 21 V11 alignment) | error |
| V11 | mvPlanRef present for guarantee-bearing contracts | error |
| V12 | DPA content complete per Art. 28 checklist where processingRoles set | error |
| V13 | Entitlement chain integrity: every active enrolment/registration in 14/20/21/29 cites an active contract clause (Phase 14 cross-check) | error |
| V14 | Amendments append-only; superseded clauses retained with effective dating | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 14/15 actor/org | Parties, signatories, consent alignment (A2), credit standing |
| 16/17 role/user | Signature authority (role approval limits), contract-manager ownership |
| 20 market | Participation agreements; credit cover mirroring |
| 21 tariff | Plan bindings per connection point |
| 22 product | Offer/delivery obligations; threshold-linked penalties |
| 25 trade | Trades execute under master/participation agreements |
| 27 settlement | Settlement terms executed; penalties computed; dispute windows |
| 29 flexibility_service | Enrolments activated under contracts |
| 86 community | Membership agreements; scheme rules |
| 90 project | Grant/consortium agreements as contracts where operationally binding |
| 93 compliance | Retention, audit, statutory-content checks |

## 10. Benchmarking Requirements

Contract-portfolio metrics (internal): penalty incidence by contract type, obligation attainment, renewal rates, dispute frequency — cohorts min n = 10; no party-identified disclosure beyond the parties themselves.

## 11. Dataset Requirements

Contract registry with clause-level machine references; obligation/penalty evidence datasets pinned per contract (settlement-grade lineage); amendment event log; template corpus versioned. Signed instruments in document vault; catalogue stores structured terms + vault refs.

## 12. Feature Requirements

Features (portfolio/risk models only): contract type, remaining term, penalty exposure headroom, obligation attainment history. Never features in energy/physical models. Personal signatory data excluded.

## 13. Model Requirements

Penalty-risk models forecast breach probability vs obligations (joins delivery-risk 22 §13); ESCO guarantee models implement the contracted M&V baseline exactly (no model substitution without amendment); churn models for enrolment contracts use engagement features (14) under consented scopes.

## 14. KPI Requirements

KPIs: obligation attainment %, penalty € incurred/avoided, guarantee performance vs baseline, enrolment retention, time-to-execution, dispute resolution time. Contract dimension on all settlement-affecting KPIs.

## 15. Response Derivative Requirements

Contracts cap RD utilisation: enrolment contracts state maximum activation frequency/duration/comfort bounds that constrain RD-driven dispatch regardless of technical capability (the contractual envelope ≤ technical envelope). RD evidence supports guarantee verification where M&V plans admit it (A4/27 baseline registry).

## 16. Decision Requirements

Dispatch/offer/settlement decisions validate the entitlement chain (A1) and contractual envelopes (§15) pre-execution; breach-risk warnings surface before commitments that would exceed obligations; terminations execute guarded workflows (unwind enrolments, final settlement, data-scope revocation per A2).

## 17. Ontology Mapping

Classes: Contract ⊐ by conType; ContractTemplate; Party (reified role-binding); Clause ⊐ {Obligation, SLA, Penalty, Incentive, Guarantee, DataTerm, SettlementTerm}; Amendment.
Object properties: hasParty (→ Actor/Organisation, with partyRole), instantiates (→ Template), binds (→ TariffPlan/Product/Market/Service/Asset), hasClause, obligates (Party→Obligation), penalises, governsTrade, hasAmendment, supersedes.
Datatype properties: contractID, conType, status, effectiveFrom/To, paymentTermsDays, liabilityCap.
Alignment: ODRL (odrl:Agreement, Duty/Permission/Prohibition for executable clause semantics), schema.org/Order-Agreement patterns, GDPR Art. 28 controlled content as named clause types.

## 18. Knowledge Graph Mapping

Nodes: Contract, Clause, Template. Relationships: HAS_PARTY (temporal, role-attributed), INSTANTIATES, BINDS (→ Tariff/Product/Market/Service/Asset/Building), OBLIGATES, PENALISES, SETTLED_BY (→ Settlement records), AMENDED_BY, CHILD_OF (call-offs). Keys: contractID. Confidential terms flagged; KG stores structure + references, not confidential values where class demands.

## 19. Digital Twin Mapping

Twins enforce contractual envelopes in simulation and live recommendation (no twin-suggested action may violate enrolment bounds); guarantee tracking twins (ESCO) run the contracted M&V baseline continuously; what-if contract terms tested in sandbox before amendment.

## 20. Governance

Owner: EIG Catalogue Owner; legal/contracts function co-owns templates and execution workflow; signature authority via role approval limits (16); template changes legal-reviewed; quarterly entitlement-chain audit (V13); consent-alignment monitor (A2) event-driven.

## 21. Versioning

Templates semver; contracts versioned via append-only amendments with effective dating; terminal contracts immutable; all dependent records (enrolments, settlements) pin contract version + clause IDs in force at event time.

## 22. Example Records

```text
contractID: EIG-CON-001207 | conType: CON-FLX-ENR | form: accession | basis: accepted_terms
template: EIG-CON-TPL-0009 v2.1 | parties: [{aggregator, EIG-ACT-00031, EIG-ORG-0044}, {flex_provider, EIG-ACT-00214}]
status: active | effectiveFrom: 2025-06-10 | renewalModel: evergreen_with_notice (30 d) | coolingOff: 14 d
productBindings: [{EIG-PRD-FLX-SHED-01, must_deliver_on_accepted_dispatch, ≤ 4 kW}]
serviceEnrolments: [EIG-FS-DR-01] | assetScope: [EIG-AST-004211, EIG-AST-005102 @ EIG-BLD-000877 (IE_B0009 ref)]
contractualEnvelope: ≤ 2 events/week, ≤ 2 h/event, comfort floor 19 °C, override always permitted
revenueSharing: {70% provider / 30% aggregator, floor €0 provider} | dataScopes: [telemetry_15min, rd] (= consents, A2)
penaltyRegime: [{PEN-01, non-delivery < 70% (22 thresholds), forfeit event revenue, cap €50/event}]
settlement: monthly_billing, 14 d terms, dispute window 30 d | mv: baseline_delta per BL-LFM-01
```

```text
contractID: EIG-CON-000884 | conType: CON-FLX-FRM | parties: [{flex_provider, EIG-ACT-00031}, {system_operator, EIG-ACT-00052}]
basis: signed_instrument (LFM tender award) | window: 2025-11-01→2026-10-31 | capacity envelope: ≤ 500 kW, price cap per tender
children: [EIG-CON-000901 call-off W08, …] | penalty: availability shortfall schedule, aggregate cap 15% contract value
securityRequirements: performance bond per 20 creditCoverModel
```

```text
contractID: EIG-CON-000412 | conType: CON-ESC | parties: [{esco, EIG-ORG-0058}, {beneficiary, EIG-ORG-0061 Cork CC}]
guarantees: [{savings, mvPlan MV-0412 (IPMVP Option C, baseline FY2024 weather-normalised per 09 HDD), 18% kWh, gainshare 50/50 above}]
assetScope: 14 buildings | settlementCycle: quarterly_reconciliation | liabilityCaps: {aggregate 1× annual fee}
```

## 23. Completion Criteria

Complete when: entitlement chain closed (V13 zero-diff across 14/20/21/29); all penalty/obligation structures machine-computable from registered evidence; consent alignment live; templates versioned with legal sign-off; M&V plans attached to all guarantee-bearing contracts; Phase 14 confirms no operational entitlement or penalty exists outside this catalogue.
