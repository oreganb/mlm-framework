---
type: catalogue
ontology: EIG-MLM
catalogue_no: 92
catalogue_id: EIG-CAT-092
entity: regulation
band: governance
status: active
tags: [eig-mlm, catalogue, band:governance, entity:regulation]
aliases: ["EIG-CAT-092", "Catalogue 92", "regulation"]
---

# CAT-92 — Regulation

> **EIG-CAT-092** · band: **Aggregation & Governance** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-15 organisation]] · [[CAT-91 policy]]
**Used by (downstream):** [[CAT-20 market]] · [[CAT-27 settlement]] · [[CAT-35 edge_infrastructure]] · [[CAT-36 device]] · [[CAT-37 data_source]] · [[CAT-38 data]] · [[CAT-59 constraint]] · [[CAT-77 report]] · [[CAT-86 community]] · [[CAT-93 compliance]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-092`
**Entity prefix:** `EIG-REG-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator with compliance owner (93)
**Depends on (upstream):** 15 organisation (authorities), 91 policy (implemented policies)
**Used by (downstream):** 59 (CC-REG constraint sourcing), 93 (obligation tracking), 77 (RT-REG bindings), 37/38 (GDPR processing bounds), 86 (REC/CEC envelopes), 20/27 (market/settlement rules), 35/36 (security/metering law)

---

## 1. Definition

The **Regulation Catalogue** registers legally binding instruments (`EIG-REG-*`): laws, directives, statutory rules, codes, and licence conditions — each defined by jurisdiction, **legal basis**, obligations, compliance rules, and reporting requirements. Regulations are the hard governance layer: where 91 policies direct and incentivise, regulations bind; the framework's regulatory constraints (59 CC-REG), compliance obligations (93), and statutory reporting (77 RT-REG) all source here. Purpose: **regulatory compliance** by reference — every regulatory claim, constraint, and deadline in the framework traces to a registered instrument, article-level where it matters (A1).

## 2. Scope
**In scope:** regulation schema (the spec's six: ID, jurisdiction, legal basis, obligations, compliance rules, reporting requirements); instrument families (energy market, metering/settlement, building standards, data protection, communities, cybersecurity, environmental reporting); obligation extraction (article-anchored, typed, addressee-resolved); compliance-rule formalisation hooks (machine-evaluable where possible → 59/84); reporting-requirement register (what, to whom, when → 93 deadlines, 77 definitions); transposition chains (EU → national); amendment tracking. **Out of scope:** compliance status/evidence (93), policy intent (91), legal advice (the register records instruments and the framework's reading, flagged as such), contract law (23).

## 3. Classification Structure

### 3.1 Instrument families (`family`)
RF-MKT (electricity market law, network codes, licence conditions — 20/25/26 envelopes) · RF-MET (metering & settlement rules — 27/36 MID-class duties) · RF-BLD (building regulations/energy performance — EPBD line, BER regime) · RF-DAT (data protection — GDPR/ePrivacy: the 37 A7/38 A7 sources) · RF-COM (REC/CEC legal frameworks — the 86 envelopes) · RF-SEC (cybersecurity — NIS2 line, the 35 IEC-62443-adjacent legal layer) · RF-ENV (environmental/climate reporting duties).

### 3.2 Instrument kinds & transposition
IK-EU-REG (directly applicable) · IK-EU-DIR (directive: transposition links to national instruments mandatory) · IK-NAT (acts/SIs) · IK-CODE (grid/market codes) · IK-LIC (licence/authorisation conditions) · IK-DEC (regulator decisions/directions). Transposition chains typed (transposes/transposed-by).

### 3.3 Obligation types
OB-CONDUCT (do/don't — constraint-formalisable → 59 CC-REG) · OB-PROCESS (maintain processes/records) · OB-REPORT (statutory reporting → §5.1.8) · OB-TECH (technical conformance → 94 standards bridges) · OB-CONSENT (data-subject duties).

## 4. Hierarchy
```
Family (RF-*) → Instrument (EIG-REG-*: legal basis + lifecycle)
                    ├── obligations (article-anchored, typed, addressee-resolved)
                    │     ├── → 59 CC-REG constraints (conduct, formalisable)
                    │     ├── → 93 tracked obligations (all)
                    │     └── → 77 RT-REG definitions (reporting)
                    └── transposition/amendment chains
```

## 5. Field Groups & Fields

### 5.1 Regulation (`EIG-REG-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | regulationID | string | `EIG-REG-\d{4}` | M | Spec: Regulation ID |
| 2 | name / family / kind | string/enums | §3.1/§3.2 | M | Identity |
| 3 | jurisdiction | json | EU/IE + competent authority (→15: CRU, DPC, NCSC…) | M | Spec: Jurisdiction |
| 4 | legalBasis | json | official citation {instrument number/year, OJ/SI ref, URI}, enabling acts, in-force/applicability dates | M | Spec: Legal basis — the citable anchor |
| 5 | obligations | array<json> | [{obligationID, article anchor, type §3.3, statement (framework reading, flagged interpretive), addressees (15 org types / framework roles), conditions}] | M | Spec: Obligations — article-anchored extraction |
| 6 | complianceRules | array<json> | [{ruleID, obligation ref, formalisation: →59 EIG-CST-* where machine-evaluable / →84 shapes / procedural (64 workflow ref), verification basis}] | M | Spec: Compliance rules — the formalisation hooks |
| 7 | reportingRequirements | array<json> | [{what, to (authority), cadence/deadline rule, format refs, →77 reportDef binding, →93 deadline tracking}] | M | Spec: Reporting requirements |
| 8 | transposition | array<json> | typed chain links (§3.2) | C | EU↔national lineage |
| 9 | amendments | array<json> | amendment history with consolidated-version pointers | M | Currency |
| 10 | frameworkImpact | json | resolved reach: which registers/operations this instrument binds (the impact map: 27 settlement rules, 37 processing bounds, 86 envelopes…) | M | The "so what" register |
| 11 | owner / version / status | — | in-force lifecycle (pending/in-force/amended/repealed) | M | Governance |

## 6. Controlled Vocabularies
Families (§3.1), kinds (§3.2), obligation types (§3.3), addressee classes, verification bases {automated-constraint, shape-audit, procedural-evidence, attestation}. Interpretation flag (framework reading vs verbatim) mandatory on statements.

## 7. Applicability Rules
- **A1 — Article-anchored or it doesn't bind here.** Obligations cite their articles; framework readings are flagged interpretive (the register is engineering input, not legal advice — counsel reviews flagged readings per governance).
- **A2 — Conduct obligations become constraints.** OB-CONDUCT items formalise into 59 CC-REG entries (single-source mirroring: the constraint cites the obligation cites the article) — regulatory compliance is enforced where everything else is, at the constraint layer with its precedence (SAF>REG>… per 59).
- **A3 — Reporting becomes definitions and deadlines.** OB-REPORT rows bind 77 RT-REG definitions and 93 deadline tracking at registration — a statutory report without a definition+deadline chain is a registration defect, caught before it's a miss.
- **A4 — Amendments ripple.** Amendment entries flag dependent constraints (59), report definitions (77), compliance rules (93), and envelopes (86/20/27) for review — the 91 A6 pattern, with legal force behind it.
- **A5 — GDPR is operationalised, not decorated.** RF-DAT obligations resolve to the concrete machinery: processing bounds (37 A7), data-subject flows (38 A7 hooks), retention/deletion (74 offboarding), consent (03/17) — each compliance rule points at its implementing register control.
- **A6 — Pending instruments prepare.** In-force-pending entries (and 91 IS-OBLIG-PENDING handoffs) carry readiness assessments → 93 preparatory obligations — compliance starts before the deadline does.

## 8. Validation Rules
V1 schema completeness (six spec fields); legal-basis citations resolve (URIs/refs well-formed); V2 obligations article-anchored; interpretation flags present; addressees resolve; V3 compliance-rule hooks live: 59 mirrors drift-checked (59 A2 machinery), procedural rules bind 64 workflows; V4 reporting rows fully chained (77 definition + 93 deadline both resolve); V5 amendment ripple flags fire to dependents (monitored); V6 transposition chains complete for IK-EU-DIR; V7 frameworkImpact maps reviewed per amendment; V8 instruments immutable per version (consolidations supersede).

## 9. Relationships to Other Catalogues
59 (CC-REG sourcing; precedence) · 93 (obligation tracking; deadlines; readiness) · 77 (RT-REG definitions) · 91 (policy↔regulation linkage; pending handoffs) · 37/38/03/17/74 (GDPR operationalisation) · 86 (REC/CEC envelopes) · 20/26/27 (market/gate/settlement rules) · 35/36 (NIS2-line security duties; MID metering) · 94 (OB-TECH bridges to standards) · 15 (authorities) · 64 (procedural compliance workflows).

## 10. Benchmarking Requirements
Register health: article-anchoring completeness, ripple latency on amendments, chain completeness (V4) — the leading indicators of compliance posture (93 consumes).

## 11. Dataset Requirements
Instrument register exports accompany compliance audits (43-packaged with 93 evidence).

## 12. Feature Requirements
Regulatory-state features (applicable-instrument flags per subject/operation) for 60 context where decisions are regime-dependent.

## 13. Model Requirements
n/a directly; models touching regulated computations (27 settlement, BER-line 05) cite their governing rules in assumptions registers (44).

## 14. KPI Requirements
Regulatory KPIs (93-facing): chain completeness, readiness coverage — registered (57).

## 15. Response Derivative Requirements
Flexibility-market participation runs inside RF-MKT envelopes (product/settlement legality); RD-evidence handling of personal-data-adjacent streams respects RF-DAT bounds (37 A7 chain) — the learning loop is lawful by construction, traceably.

## 16. Decision Requirements
Regime-dependent decision behaviour (60) keys on registered instruments via context features (§12); CC-REG constraints bind at their 59 precedence — automation cannot outrank law.

## 17. Ontology Mapping
Regulation ↔ eig:Regulation (ELI alignment candidate per 79 backlog); obligations ↔ deontic-pattern individuals (typed, article-anchored); owned by 79.

## 18. Knowledge Graph Mapping
Node `Regulation`, `Obligation`; edges `ISSUED_BY`→15, `BINDS`→addressees/registers (frameworkImpact), `FORMALISED_AS`→59/84, `REPORTED_VIA`→77, `TRACKED_IN`→93, `TRANSPOSES`, `AMENDS`. "Everything that binds our settlement process" is one traversal.

## 19. Digital Twin Mapping
Regulated-operation twins (metering chains, market interfaces) annotate their governing instruments; compliance views (93) render over twin context.

## 20. Governance
Owner: PI + compliance owner; legal-counsel review loop for interpretive readings (A1); amendment watch assigned per family; quarterly register currency review.

## 21. Versioning
Instruments versioned per consolidation; in-force lifecycle tracked. Catalogue 1.0.0.

## 22. Example Records

### EIG-REG-0004 — Data protection regime (RF-DAT, IK-EU-REG + national act)
- legal basis: GDPR + Irish DP Act (citations @consolidated versions); authority: DPC (15)
- obligations: processing-lawfulness (OB-CONDUCT → 37 A7 bounds as 59 CC-REG mirrors), records-of-processing (OB-PROCESS → 64 workflow), subject-rights handling (OB-PROCESS → 38 A7/74 machinery), breach notification (OB-REPORT → 77 def + 93 72h-class deadline rule)
- compliance rules point at implementing controls (A5); frameworkImpact: 37/38/03/17/74 mapped

### EIG-REG-0011 — REC/CEC legal framework (RF-COM)
- transposes: RED II/IEMD lines → national instruments (chain typed); obligations: community eligibility/activity envelopes (→ 86 §3.1 type bindings), member-protection conducts
- the 86 A2 envelope source; amendment watch armed (community rules evolve)

### EIG-REG-0017 — Security of network & information systems (RF-SEC, NIS2 line)
- obligations: risk-management measures (OB-TECH → 94 IEC 62443 bridge + 35 METIS-128 implementing evidence), incident reporting (OB-REPORT → 77/93 chains with severity-timed deadlines)

## 23. Completion Criteria
☑ All six spec fields (ID, jurisdiction, legal basis, obligations, compliance rules, reporting requirements) ☑ Article-anchored, interpretation-flagged obligations ☑ Conduct→59 constraints, reporting→77/93 chains, GDPR operationalised to concrete controls ☑ Amendment ripple + transposition lineage + pending readiness ☑ Worked GDPR, REC/CEC, and NIS2-line instruments sourcing the framework's existing regulatory machinery.
