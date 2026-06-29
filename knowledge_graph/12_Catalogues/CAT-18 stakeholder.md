---
type: catalogue
ontology: EIG-MLM
catalogue_no: 18
catalogue_id: EIG-CAT-018
entity: stakeholder
band: actors
status: active
tags: [eig-mlm, catalogue, band:actors, entity:stakeholder]
aliases: ["EIG-CAT-018", "Catalogue 18", "stakeholder"]
---

# CAT-18 — Stakeholder

> **EIG-CAT-018** · band: **Actors & Organisations** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Stakeholder Catalogue

## 1. Definition

The Stakeholder Catalogue is the authoritative register of project, market, community and governance stakeholders relevant to EIG MLM activities. A stakeholder is any party — individual, group, organisation or institutional interest — that affects, is affected by, or has a legitimate interest in a project, platform, market arrangement, community energy initiative or governance process, whether or not they participate operationally.

Stakeholders are an ANALYTICAL and ENGAGEMENT construct, distinct from actors (14 — operational participants), organisations (15 — legal entities) and users (17 — authenticated identities). A stakeholder record MAY reference an actor/org as its formal vehicle, but stakeholders also include parties with no operational footprint: local residents near a substation, heritage bodies, future occupants, media, the general public. This catalogue defines stakeholder identity, typology, interest, influence, responsibilities, benefits, risks, communication needs, and the mapping/engagement structures (interest–influence matrices, engagement plans, RACI) used in stakeholder management and governance.

## 2. Scope

In scope:

- Stakeholders of projects (90): partners, funders, advisory boards, demo-site hosts, local communities, end users.
- Market/regulatory stakeholders: regulators, market operators, network operators, suppliers, consumer bodies — in their capacity as interested/affected parties.
- Community stakeholders: residents, community organisations, schools, local businesses, county councils, island co-operatives.
- Governance stakeholders: ethics committees, data-protection authorities, standards bodies, funding agencies (SEAI, SFI, European Commission), institutional leadership.
- Stakeholder groups and segments (e.g. "Inis Mór households", "public-sector building managers") as collective records.

Out of scope:

- Operational participation (14), legal identity (15), platform access (17).
- Communication content/execution (alerts 76, reports 77 carry the artefacts; this catalogue carries the NEEDS).
- Project work-plan content (90).

## 3. Classification Structure

Stakeholder type (`stkType`) — closed vocabulary:

| Code | Type | Definition |
|---|---|---|
| STK-PRT | project_partner | Consortium/grant partner with contractual stake |
| STK-FND | funder | Funding agency, programme owner, investor |
| STK-REG | regulator_authority | Regulator, DPA, planning/statutory authority as interested party |
| STK-OPR | system_operator | DSO/TSO/market operator as affected/consulted party |
| STK-SUP | supply_chain | Vendors, installers, technology suppliers |
| STK-COM | community | Residents, community groups, local institutions |
| STK-OCC | occupants_end_users | Building occupants and service end users (as a constituency) |
| STK-CIV | civil_society | NGOs, consumer bodies, environmental groups |
| STK-ACA | academic_research | Research community, ethics committees, scientific advisory |
| STK-GOV | government_policy | Departments, local authorities, policy units |
| STK-MED | media_public | Media and the general public |
| STK-INT | internal | Internal institutional stakeholders (leadership, departments, support units) |

Stakeholder form (`stkForm`): individual, named_group, organisation_ref (→ orgID), segment (defined population with membership rule).

Interest–influence classification (Mendelow): `interestLevel` ∈ {low, medium, high}; `influenceLevel` ∈ {low, medium, high}; derived `quadrant` ∈ {monitor (L/L), keep_informed (H interest/L influence), keep_satisfied (L interest/H influence), manage_closely (H/H)} (V6).

Stance (`stance`): champion, supportive, neutral, sceptical, opposed, unknown — time-versioned (stance history retained).

## 4. Hierarchy

```text
StakeholderContext (project | community | market_arrangement | platform | policy_process)
└── Stakeholder record (EIG-STK-NNNNN)   ← stake is ALWAYS contextual
    └── Segment members (for stkForm = segment; membership rule, not enumerated persons)
```

- A real-world party may hold multiple stakeholder records across contexts (CRU is STK-REG in a market context and STK-FND-adjacent in a sandbox approval context) — records are per-context, linked by sharedPartyRef where the same party (V4).
- Segments define membership by rule (e.g. "all EIG-BLD on EIG-NET-NZ-00017") — never by enumerating natural persons (GDPR minimisation, V9).

## 5. Field Groups & Fields

### 5.1 Identity & classification

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | stkID | string | — | `EIG-STK-NNNNN` | M | Unique stakeholder record ID |
| 2 | stkName | string | — | ≤ 200 chars | M | Name of stakeholder/group/segment |
| 3 | stkType | enum | — | §3 STK-* | M | Stakeholder type |
| 4 | stkForm | enum | — | individual, named_group, organisation_ref, segment | M | Form |
| 5 | contextKind | enum | — | project, community, market_arrangement, platform, policy_process | M | Context of the stake |
| 6 | contextRef | string | — | EIG-PRJ-* / EIG-CMU-* / EIG-MKT-* / platform ID / EIG-POL-* | M | The specific context |
| 7 | orgRef | string | — | EIG-ORG-NNNN | C | Mandatory when stkForm organisation_ref |
| 8 | actorRef | string | — | EIG-ACT-NNNNN | O | Operational counterpart if any |
| 9 | sharedPartyRef | string | — | party UUID | C | Links records of same party across contexts |
| 10 | segmentRule | string | — | membership predicate | C | Mandatory for segments; resolvable against catalogues |
| 11 | status | enum | — | identified, analysed, engaged, dormant, exited | M | Engagement lifecycle |

### 5.2 Interest & influence

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | interests | array | — | [{interestCode, description, salience: low/med/high}] | M | What they care about; codes §6 |
| 2 | interestLevel | enum | — | low, medium, high | M | Overall interest |
| 3 | influenceLevel | enum | — | low, medium, high | M | Capacity to affect outcomes |
| 4 | influenceChannels | array | — | formal_authority, funding, contractual, expertise, social_licence, media, political, operational_dependency | M | How influence operates |
| 5 | quadrant | enum | — | derived (V6) | D | Mendelow quadrant |
| 6 | stance | enum | — | §3 stance values | M | Current stance |
| 7 | stanceHistory | array | — | [{stance, from, to, evidenceRef}] | D | Time-versioned stance |
| 8 | legitimacyUrgency | object | — | {legitimacy: l/m/h, urgency: l/m/h} | O | Mitchell salience supplements |

### 5.3 Responsibilities, benefits & risks

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | responsibilities | array | — | [{description, raci, towardsContext}] | C | Duties the stakeholder holds in context (partners, funders) |
| 2 | expectedBenefits | array | — | [{benefitCode, description, measure?, kpiRef?}] | M | What they should gain; measurable where possible |
| 3 | risksToStakeholder | array | — | [{riskDesc, likelihood: l/m/h, impact: l/m/h, mitigation}] | M | Risks the context poses TO them (incl. privacy, disruption, cost) |
| 4 | risksFromStakeholder | array | — | [{riskDesc, likelihood, impact, mitigation, earlyWarning}] | M | Risks they pose to the context (withdrawal, opposition, veto) |
| 5 | dependencies | array | — | descriptions of mutual dependencies | O | What the context needs from them and vice versa |

### 5.4 Communication needs & engagement

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | engagementLevel | enum | — | inform, consult, involve, collaborate, empower (IAP2 spectrum) | M | Target engagement level |
| 2 | commNeeds | array | — | [{topic, frequency, channel, format, language, accessibility}] | M | Communication requirements |
| 3 | preferredChannels | array | — | meeting, workshop, newsletter, public_event, one_to_one, portal, report, media_release | M | Channel preferences |
| 4 | language | array | — | BCP 47 (en-IE, ga-IE, …) | M | Required languages (ga-IE mandatory for Gaeltacht community stakeholders, V10) |
| 5 | contactRoute | object | — | {via: org_contact/actor/user/public_channel, ref} | M | NEVER personal contact data for individuals/segments; route via org contacts (15) or public channels (V9) |
| 6 | engagementPlanRef | string | — | plan document/workflow ref | C | Mandatory for manage_closely quadrant (V7) |
| 7 | engagementLog | array | — | [{date, activity, attendeesCount, outcomes, sentiment}] | D | Append-only engagement history |
| 8 | feedbackMechanism | array | — | survey, complaint_channel, advisory_seat, co_design_session | C | ≥ 1 for involve+ levels |
| 9 | reviewCycleMonths | integer | months | 1–12 | M | Reassessment cadence (manage_closely ≤ 3) |

### 5.5 Record metadata

recordVersion, createdDate, modifiedDate, owner (engagement owner userID), dataSteward, governanceStatus — programme standard.

## 6. Controlled Vocabularies

**Interest codes** (closed, INT-NN): INT-01 cost_savings, INT-02 comfort_wellbeing, INT-03 reliability_security_of_supply, INT-04 decarbonisation, INT-05 revenue_business_opportunity, INT-06 regulatory_compliance, INT-07 data_privacy, INT-08 local_economic_benefit, INT-09 energy_justice_affordability, INT-10 research_knowledge, INT-11 heritage_landscape, INT-12 safety, INT-13 grid_stability, INT-14 reputation_visibility, INT-15 skills_employment, INT-16 autonomy_self_sufficiency.

**Benefit codes** (closed, BEN-NN): BEN-01 bill_reduction, BEN-02 comfort_improvement, BEN-03 flex_revenue, BEN-04 emissions_reduction, BEN-05 network_deferral, BEN-06 knowledge_publication, BEN-07 policy_evidence, BEN-08 community_fund, BEN-09 service_improvement, BEN-10 market_access, BEN-11 capacity_building, BEN-12 compliance_demonstration.

Closed: stkType, stkForm, contextKind, status, interest/influence levels, influenceChannels, stance, engagementLevel (IAP2), preferredChannels, quadrant. External: IAP2 Spectrum of Public Participation, Mendelow matrix, Mitchell–Agle–Wood salience model, PMBOK/PM² stakeholder management.

## 7. Applicability Rules

| Rule | Statement |
|---|---|
| A1 | Every project (90) MUST maintain stakeholder records covering at minimum: all partners, the funder, demo-site communities, end-user constituency, and relevant regulator(s) |
| A2 | Every community energy context (86) MUST include resident/occupant segment stakeholders with energy-justice interests assessed (INT-09) |
| A3 | manage_closely stakeholders require an engagement plan, named engagement owner and review ≤ 3 months |
| A4 | Segments referencing households/persons are rule-defined only; engagement at segment level uses public/community channels |
| A5 | Stakeholder analysis informs but never overrides statutory consultation duties (planning, GDPR consultation, regulatory processes per 92) |
| A6 | Opposed/sceptical stances trigger risk records (risksFromStakeholder) with mitigation and early-warning indicators |
| A7 | Internal stakeholders (STK-INT) tracked for platform/institutional contexts (e.g. executive leadership for strategic initiatives) |

## 8. Validation Rules

| ID | Rule | Severity |
|---|---|---|
| V1 | stkID unique; (stkName, contextRef) unique | error |
| V2 | contextRef resolves to existing context entity of contextKind | error |
| V3 | orgRef/actorRef resolve where present; stkForm organisation_ref ⇒ orgRef | error |
| V4 | sharedPartyRef consistent: same party's records carry same UUID | warning |
| V5 | Status transitions: identified→analysed→engaged→{dormant↔engaged, exited}; engaged requires §5.2 complete | error |
| V6 | quadrant correctly derived from interestLevel × influenceLevel | error |
| V7 | manage_closely ⇒ engagementPlanRef present, reviewCycleMonths ≤ 3, owner assigned | error |
| V8 | expectedBenefits ≥ 1 with measurable KPI link for STK-COM/STK-OCC in funded projects | warning |
| V9 | No personal contact data on individual/segment records; contactRoute via org/public channels | error |
| V10 | ga-IE included in language for Gaeltacht-area community stakeholders (geo ref check) | error |
| V11 | Engagement log entries append-only; sentiment from closed scale | error |
| V12 | risksFromStakeholder with high/high requires escalation entry in project risk register (90) | error |
| V13 | Review overdue > 2× cycle ⇒ status flagged stale | warning |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 14 actor / 15 organisation | Optional operational/legal counterparts (actorRef/orgRef); contact routing via org contact points |
| 17 user | Engagement owners are users; stakeholders are NOT users unless separately registered |
| 57 kpi | expectedBenefits link to KPIs (benefit realisation tracking) |
| 64 workflow | Engagement plans execute as workflows; consultation steps reference stakeholder records |
| 76 alert / 77 report | Communication artefacts target commNeeds; report distribution lists derive from engagement levels |
| 86–88 community/district/site | Community contexts anchor community stakeholder sets |
| 89 scenario | Scenario assessments include stakeholder impact dimensions |
| 90 project | Primary context; A1 minimum coverage; risk register linkage V12 |
| 91–93 policy/regulation/compliance | Statutory consultees identified here; A5 boundary respected |

## 10. Benchmarking Requirements

Engagement effectiveness measured against project/programme norms: engagement completion vs plan, sentiment trend, benefit-realisation rate. No cross-party "stakeholder benchmarking" of individuals/communities. Internal cohort comparisons (project-to-project engagement maturity) permitted, min n = 10 contexts.

## 11. Dataset Requirements

Stakeholder registers are project-governance datasets: versioned snapshots at project gates (proposal, kickoff, mid-term, final). Engagement logs feed funder reporting (dissemination/communication metrics). No personal data beyond named professional representatives via org contacts. Sentiment data aggregated; free-text outcomes screened for personal data before storage.

## 12. Feature Requirements

Stakeholder attributes may feature in PROJECT-level models only: engagement-risk scoring (stance, quadrant, log-derived activity features), benefit-realisation forecasting. Never as features in energy/asset/occupant models. Segment rules may SELECT populations for energy models, but stakeholder attributes do not enter those models.

## 13. Model Requirements

Optional NLP support: engagement-log summarisation and sentiment assistance with human review (no automated stance changes — stance edits require human confirmation with evidence). ABS community-adoption models may use stance distributions as scenario parameters (89), clearly labelled synthetic.

## 14. KPI Requirements

KPIs: stakeholder coverage (A1 completeness), % manage_closely with current plans, engagement activities delivered vs planned, sentiment index trend, benefit-realisation %, complaint resolution time. Reported per context; roll-ups to programme level.

## 15. Response Derivative Requirements

None directly. Community stance and engagement quality are leading indicators for flexibility programme recruitment and retention — they contextualise pool RD coverage (14 §15) but never enter RD computation.

## 16. Decision Requirements

Decisions with community/occupant impact (DR programme design, tariff pilots, construction works) MUST cite stakeholder analysis: affected stakeholders, engagement evidence, objections and responses. manage_closely opposition unresolved ⇒ decision escalates per governance workflow. Statutory consultation evidence attached where A5 applies.

## 17. Ontology Mapping

Classes: Stakeholder ⊐ {ProjectPartner, Funder, RegulatorAuthority, SystemOperator, SupplyChain, Community, OccupantsEndUsers, CivilSociety, AcademicResearch, GovernmentPolicy, MediaPublic, Internal}, StakeholderContext, Interest, Benefit, StakeholderRisk, EngagementActivity, EngagementPlan.
Object properties: hasStakeInContext, representedByOrganisation, hasOperationalCounterpart (→ Actor), hasInterest, expectsBenefit (→ Benefit → linksToKPI), exposedToRisk, posesRisk, engagedThrough (→ EngagementActivity), hasEngagementPlan, sharesPartyWith.
Datatype properties: stkID, stkType, stkForm, interestLevel, influenceLevel, quadrant, stance, engagementLevel, reviewCycleMonths, status.
Alignment: IAP2 spectrum values as named individuals; PM² / PMBOK stakeholder concepts; schema.org Audience for segment patterns.

## 18. Knowledge Graph Mapping

Nodes: Stakeholder, Interest, Benefit, EngagementActivity. Relationships: HAS_STAKE_IN (→ Project/Community/Market/Policy nodes), REPRESENTED_BY (→ Organisation), COUNTERPART_OF (→ Actor), INTERESTED_IN (→ Interest), EXPECTS (→ Benefit → MEASURED_BY → KPI), POSES_RISK_TO, EXPOSED_TO_RISK_FROM, ENGAGED_VIA (→ EngagementActivity, temporal), SAME_PARTY_AS. Keys: stkID. Personal data excluded; segment nodes carry rules not members.

## 19. Digital Twin Mapping

Community/district twins (86/87) surface stakeholder overlays: segment footprints (rule-resolved building sets), engagement status heat, planned-works consultation zones. Scenario runs (89) report stakeholder-impact summaries against this register. No individual-level visualisation.

## 20. Governance

Owner: EIG Catalogue Owner; per-context engagement owners accountable for currency. Stakeholder analyses reviewed at project gates and on material context changes (new regulation, incident, leadership change). Ethics oversight for research-participant-adjacent stakeholder work. Engagement logs auditable for funder review.

## 21. Versioning

Record semver; stance and engagement logs append-only with effective dating; snapshot releases at project gates are immutable. Context closure (project end) freezes records as archived; lessons-learned extracts feed the next context's identification step.

## 22. Example Records

```text
stkID: EIG-STK-00112 | stkName: Inis Mór resident households | stkType: STK-COM | stkForm: segment
contextKind: project | contextRef: EIG-PRJ-0007 (ODEON/O-CEI pilot) | status: engaged
segmentRule: buildings where networkZone = EIG-NET-NZ-00017 AND occupancyFamily ∈ residential
interests: [{INT-03 reliability, high}, {INT-01 cost_savings, high}, {INT-07 data_privacy, med}, {INT-16 autonomy, med}]
interestLevel: high | influenceLevel: high (social_licence, operational_dependency) | quadrant: manage_closely | stance: supportive
expectedBenefits: [{BEN-01 bill_reduction, kpiRef: EIG-KPI-…}, {BEN-03 flex_revenue via community fund BEN-08}]
risksToStakeholder: [{privacy concerns re 15-min telemetry, m/m, mitigation: pseudonymisation + consent register (14)}]
risksFromStakeholder: [{participation withdrawal, m/h, mitigation: co-design + opt-out clarity, earlyWarning: override rate ↑}]
engagementLevel: involve | language: [ga-IE, en-IE] | preferredChannels: [public_event, one_to_one, newsletter]
contactRoute: {via: org_contact, ref: EIG-ORG-0044 general} | engagementPlanRef: EIG-WF-ENG-0007 | reviewCycleMonths: 3
engagementLog: [{2025-09-14, public meeting Cill Rónáin, 42, supportive; concerns: data privacy, boat-schedule works}, …]
```

```text
stkID: EIG-STK-00031 | stkName: SEAI (RD&D Programme) | stkType: STK-FND | stkForm: organisation_ref
orgRef: EIG-ORG-0019 | contextKind: project | contextRef: EIG-PRJ-0007 | status: engaged
interestLevel: high | influenceLevel: high (funding, formal_authority) | quadrant: manage_closely | stance: champion
responsibilities: [{grant oversight, accountable}] | engagementLevel: collaborate
commNeeds: [{progress, quarterly, report, formal template, en-IE}] | reviewCycleMonths: 3
```

```text
stkID: EIG-STK-00078 | stkName: Tyndall executive leadership | stkType: STK-INT | stkForm: named_group
contextKind: platform | contextRef: EIG-PLATFORM | status: analysed | stance: neutral→supportive (2026-04)
interestLevel: medium | influenceLevel: high (formal_authority) | quadrant: keep_satisfied
engagementLevel: consult | commNeeds: [{strategic value & data-centre linkage, biannual, one_to_one briefing}]
```

## 23. Completion Criteria

Complete when: every active project/community context meets A1/A2 coverage; all manage_closely records carry live plans (V7); benefit links to KPIs in place for funded contexts; risk linkage to project registers verified; no personal contact data present (V9 audit clean); Gaeltacht language rule satisfied; Phase 14 confirms stakeholder records never substitute for actor/org/user identity.
