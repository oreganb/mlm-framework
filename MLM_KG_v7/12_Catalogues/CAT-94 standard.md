---
type: catalogue
ontology: EIG-MLM
catalogue_no: 94
catalogue_id: EIG-CAT-094
entity: standard
band: governance
status: active
tags: [eig-mlm, catalogue, band:governance, entity:standard]
aliases: ["EIG-CAT-094", "Catalogue 94", "standard"]
---

# CAT-94 — Standard

> **EIG-CAT-094** · band: **Aggregation & Governance** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-17 user]] · [[CAT-79 ontology]] · [[CAT-81 semantic_mapping]] · [[CAT-92 regulation]] · [[CAT-93 compliance]]
**Used by (downstream):** [[CAT-34 communication]] · [[CAT-38 data]] · [[CAT-39 metadata]] · [[CAT-79 ontology]] · [[CAT-81 semantic_mapping]] · [[CAT-92 regulation]] · [[CAT-93 compliance]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-094`
**Entity prefix:** `EIG-STD-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator with domain technical leads
**Depends on (upstream):** every catalogue whose §17 cites a standard; 79 ontology (alignment), 81 semantic_mapping, 92 regulation, 93 compliance
**Used by (downstream):** 93 compliance (standard-derived requirements), 79/81 (formal alignment), 34/38/39 (interoperable exchange), 92 (standards referenced by regulation)

---

## 1. Definition

The **Standard Catalogue** registers the formal standards the framework adopts, aligns to, or claims conformance with (`EIG-STD-*`): each with standard body, scope, requirements, applicable entities, and compliance criteria. It is the consolidated register of the technical and semantic standards threaded through the entire estate — the EN/ISO building-physics standards behind the fabric and benchmark catalogues, ASHRAE Guideline 14 behind model validation, MID behind metering, CIM/IEC behind networks, QUDT/UCUM behind units, OWL/SKOS/SHACL behind the semantic layer, RFC 5545 behind scheduling, IEC 62443/TLS behind security. Purpose: **interoperability and formal alignment** — what the framework conforms to (and how conformance is judged) is enumerated, scoped, and bound to the catalogues that implement it, so interoperability claims and conformance assertions rest on a governed register, not folklore (A1).

## 2. Scope
**In scope:** standard schema (the spec's six: ID, standard body, scope, requirements, applicable entities, compliance criteria); standard families (building physics, measurement, semantic web, energy/grid, security, data exchange, ML/AI); adoption levels (conformance-claimed vs aligned-to vs referenced); requirement extraction (the relevant clauses, not the whole standard); applicable-entity binding (which catalogue records the standard governs); compliance criteria (how conformance is evaluated, feeding 93); version tracking (standards revise); the framework's *own* emerging standards (the RD/Symbiotic-Buildings contribution path). **Out of scope:** the standards' full text (referenced, not reproduced); regulatory force (92 — regulations may *mandate* standards; standards themselves are voluntary technical specs); compliance assessment mechanics (93); semantic alignment axioms (79/81 — this catalogue identifies *which* standards, those catalogues *formalise* the alignment).

## 3. Classification Structure

### 3.1 Standard families (`family`)

| Code | Family | Standards (the estate's set) | Implementing catalogues |
|---|---|---|---|
| SF-BLD | Building physics | EN ISO 52016 (energy needs), EN ISO 6946 (U-values), EN ISO 13370 (ground), EN 16798 (IEQ) | 05 fabric, 04 space, 01–07 |
| SF-MSR | Measurement & validation | ASHRAE Guideline 14 (M&V: CVRMSE/NMBE), IPMVP, MID 2014/32/EU (metering) | 47 validation, 36 device, 27 settlement |
| SF-SEM | Semantic web | OWL 2, RDF 1.1, SKOS, SHACL, PROV-O, QUDT, UCUM, DCAT(-AP) | 79–85 semantic layer, 38–43 data |
| SF-GRID | Energy/grid | IEC 61970/61968 (CIM), IEC 62746 (OpenADR/DR), USEF, EN 50491 (HBES), I-SEM/EirGrid grid code | 13 network, 20–28 market, 29–30 flex, 34 |
| SF-SEC | Security | IEC 62443 (ICS security), ISO/IEC 27001, NIS2-aligned controls, TLS 1.3, OAuth 2.0/OIDC | 35 edge (METIS-128), 34 comm, 16 role |
| SF-DEX | Data exchange | RFC 5545 (iCalendar), JSON Schema, OpenAPI, ISO 8601, GeoJSON | 24 schedule, 26 window, 34 API, 08 geo |
| SF-ML | ML/AI | MLS (ML Schema), Model Cards convention, emerging AI-management (ISO/IEC 42001-class) | 44–51 model layer, 66–72 learning |
| SF-EIG | EIG-originated | the Response Derivative specification, the no-fabrication/provenance standard (dataClass + hyperGrid), Symbiotic Buildings concept | 52 RD, 37/42 provenance, framework-wide |

### 3.2 Adoption levels (`adoptionLevel`)

| Code | Level | Meaning |
|---|---|---|
| AL-CONFORM | Conformance claimed | the framework asserts and evidences conformance (93 criteria) |
| AL-ALIGN | Aligned to | semantics/structures align (79/81), full conformance not claimed |
| AL-REFERENCE | Referenced | used as authoritative reference (e.g. unit definitions) without conformance assertion |
| AL-CONTRIB | Originated/contributed | EIG-developed specifications (SF-EIG) intended for external establishment |

### 3.3 Standard status
ADOPTED (current version in force) · SUPERSEDED-PENDING (newer standard version released; migration assessed) · DEPRECATED (withdrawn from framework use, migration recorded). Version tracking is mandatory — standards revise, and stale conformance claims are compliance risks (A4).

## 4. Hierarchy
```
Family (SF-*) → Standard (EIG-STD-*: body, scope, requirements, criteria)
                   ├── applicable entities (→catalogue record types governed)
                   ├── alignment (→79/81 where semantic)
                   └── compliance criteria (→93 requirements where conformance-claimed)
```

## 5. Field Groups & Fields

### 5.1 Standard (`EIG-STD-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | standardID | string | `EIG-STD-[A-Z0-9-]{3,24}` | M | Spec: Standard ID |
| 2 | family / name | enum/string | §3.1 | M | Identity |
| 3 | standardBody | json | {organisation, designation, version/year, citation/URL} | M | Spec: Standard body |
| 4 | scope | json | what the standard covers + which parts the framework uses (relevant clauses, not whole-text) | M | Spec: Scope |
| 5 | requirements | array<json> | [{clauseRef, requirement summary (paraphrased, not reproduced), applies-how}] | M | Spec: Requirements |
| 6 | applicableEntities | array<ref> | catalogue record types this standard governs (e.g. EN ISO 6946 → 05 fabric assemblies) | M | Spec: Applicable entities |
| 7 | adoptionLevel | enum | §3.2 | M | Conformance posture |
| 8 | complianceCriteria | json | how conformance is judged (test methods, evidence); →93 requirement bindings where AL-CONFORM | M | Spec: Compliance criteria |
| 9 | alignmentRef | array<ref> | →79 ALIGN modules / 81 mapping sets (SF-SEM/SF-GRID) | C | Semantic formalisation |
| 10 | version / status | semver/enum | §3.3 standard version + lifecycle | M | Spec: version tracking |
| 11 | owner / supersedes | — | technical lead; standard-version migration chain | M | Governance |

## 6. Controlled Vocabularies
Families (§3.1), adoption levels (§3.2), status (§3.3). Standard designations cite the issuing body's canonical form (e.g. "EN ISO 52016-1:2017"). EIG-originated standard names per 82 (the publication-anchored terms).

## 7. Applicability Rules
- **A1 — Standards are registered, not assumed.** Any interoperability or conformance claim (in reports 77, proposals, papers) cites an EIG-STD record with adoption level and version; "we use CIM" without a register entry stating scope and level is a governance finding.
- **A2 — Relevant clauses, paraphrased.** Requirements extract the clauses the framework actually uses, summarised in EIG's words with citation — standards text is not reproduced (IP/licence respect); the register points to the authoritative source for full text.
- **A3 — Applicable entities are bound.** Each standard names the catalogue record types it governs (EN ISO 6946 ↔ 05 fabric U-value computations; ASHRAE G14 ↔ 47 CVRMSE/NMBE thresholds; MID ↔ 36 metering devices) — so a change to a standard flags exactly which records/methods are affected (the impact query, V6).
- **A4 — Versions tracked, claims kept current.** Standard revisions are monitored; SUPERSEDED-PENDING triggers a migration assessment (does the new version change our methods/criteria?); conformance claims (AL-CONFORM) against withdrawn versions are compliance risks (93) — currency is a governed property.
- **A5 — Conformance criteria feed compliance.** AL-CONFORM standards bind compliance criteria to 93 requirements with evidence — claiming conformance creates a demonstrable obligation, tracked like any other (93 A1).
- **A6 — Semantic standards formalise via 79/81.** SF-SEM/SF-GRID alignments record their formalisation through ontology ALIGN modules and mapping sets — this catalogue says *which* standard at *what* level; 79/81 carry the axioms and correspondences (the division of labour, no duplication).
- **A7 — EIG contributions are first-class.** AL-CONTRIB entries (the Response Derivative specification, the no-fabrication provenance standard, the Symbiotic Buildings concept) are registered as the framework's own standardisation outputs — versioned, scoped, citable, with their establishment path (publication, community adoption) tracked; the RD specification's external-interoperability story (its R-NONE gaps in 81) is its standardisation rationale.

## 8. Validation Rules
V1 standardBody citations well-formed and resolvable; versions explicit; V2 requirements paraphrased with clause refs (no reproduced text — IP check); V3 applicableEntities resolve to real catalogue record types; V4 adoptionLevel consistent with evidence (AL-CONFORM ⇒ complianceCriteria + 93 bindings present); V5 version status tracked; SUPERSEDED-PENDING carries migration assessment; V6 standard-change impact query: which entities/methods affected (the A3 binding made queryable); V7 SF-SEM/SF-GRID entries carry alignmentRef to 79/81 (A6); V8 AL-CONTRIB entries carry establishment-path tracking; V9 records versioned (both standard version and EIG-STD record version distinguished); V10 estate coverage: every catalogue §17 standard citation resolves to an EIG-STD entry (the consolidation matrix — final landing obligation).

## 9. Relationships to Other Catalogues
ALL catalogues citing standards in §17 (consolidation matrix V10) · 79/81 (semantic formalisation A6) · 92 (regulations may mandate standards — the mandate link) · 93 (conformance → compliance requirements A5) · 05/04 (building physics) · 47/27/36 (M&V, metering) · 13/20–34 (grid/market/exchange) · 35/16 (security) · 24/26/08 (data exchange) · 44–51/66–72 (ML/AI) · 52/37/42 (the EIG-originated standards, A7) · 90 (project deliverables may be standards contributions).

## 10. Benchmarking Requirements
Standards posture: conformance-claim currency (% AL-CONFORM on current versions), alignment-formalisation coverage (SF-SEM/GRID entries with 79/81 refs), migration-assessment timeliness, contribution-establishment progress (AL-CONTRIB adoption metrics).

## 11. Dataset Requirements
Standard register exports (43) for proposal/report reuse; conformance-evidence packages bind via 93.

## 12. Feature Requirements
n/a (reference layer); standard-binding metadata informs validation method selection (47) and unit handling (38).

## 13. Model Requirements
ASHRAE G14 (SF-MSR) governs 47 validation thresholds (CVRMSE/NMBE — the 47 metrics' authority); Model Cards convention (SF-ML) shapes 44 release documentation; AI-management standards (SF-ML) inform 66–72 governance and CD-AI compliance (93).

## 14. KPI Requirements
Validation-KPI thresholds (57/47) trace authority to SF-MSR standards; metering-accuracy KPIs to MID — KPIs cite their standard basis where one exists.

## 15. Response Derivative Requirements
The Response Derivative specification is the flagship AL-CONTRIB entry: a registered, versioned EIG-originated standard (the 52 spec, formalised in 79 as eig:ResponseDerivative, gap-documented against external frameworks in 81) — its establishment path (IEECB&SC'26-line publication, Zenodo records, the Symbiotic Buildings community) is tracked here as the framework's bid to standardise conditional-responsiveness semantics; the no-fabrication/provenance standard (dataClass + hyperGrid lineage) is its companion contribution.

## 16. Decision Requirements
AI-management standard alignment (SF-ML) underpins the decision layer's governance claims (60 coupling discipline, 72 oversight) — the framework's autonomy architecture is positioned against emerging AI standards, evidenced through 93 CD-AI.

## 17. Ontology Mapping
Standard ↔ EIG `eig:Standard` with body/version/scope; SF-SEM standards are themselves the alignment targets (79 §3.2 register) — this catalogue and 79's framework register cross-reference (one names standards-as-adopted, the other modules-as-aligned); governed by 79.

## 18. Knowledge Graph Mapping
Node `Standard`; edges `ISSUED_BY`→body, `GOVERNS`→applicable entities, `ALIGNED_VIA`→79/81, `MANDATED_BY`→92, `EVIDENCED_FOR`→93, `SUPERSEDES`. "Which methods change if EN ISO 52016 revises?" is the A3 impact query.

## 19. Digital Twin Mapping
Twin physics engines (32/44 SER-01) cite SF-BLD standards as their computational basis; fidelity claims reference the standards their calculations conform to.

## 20. Governance
Owner: PI + domain technical leads (per family); standard-version monitoring assigned per family; AL-CONFORM claims reviewed at adoption and on revision; AL-CONTRIB establishment overseen by PI (the contribution/publication pipeline); annual register review.

## 21. Versioning
EIG-STD records semver (distinct from the standard's own version, both tracked); migration chains on standard revision. Catalogue 1.0.0.

## 22. Example Records

### EIG-STD-ASHRAE-G14 — ASHRAE Guideline 14 (SF-MSR, AL-CONFORM)
- body: ASHRAE, Guideline 14-2014 (Measurement of Energy, Demand, and Water Savings); scope: M&V statistical criteria (CVRMSE, NMBE) for model calibration/validation
- requirements: monthly CVRMSE ≤15%, NMBE ≤±5% (hourly bounds per clause) — paraphrased with clause refs; applicable entities: 47 validation methods (VM-CAL thresholds), the EIG-VAL records' acceptance criteria
- compliance criteria: 47 records evidence threshold satisfaction → 93 CD-MKT requirement binding; adoption AL-CONFORM; status ADOPTED; the authority behind 47's CVRMSE/NMBE thresholds

### EIG-STD-CIM — IEC 61970/61968 Common Information Model (SF-GRID, AL-ALIGN)
- body: IEC; scope: network topology/asset semantics; applicable entities: 13 network records; adoption AL-ALIGN (not full conformance); alignmentRef: 79 ALIGN-INTENT CIM module + 81 mapping set (the documented-correspondence, formalisation-queued state); status ADOPTED (current IEC version tracked)

### EIG-STD-RD — Response Derivative Specification (SF-EIG, **AL-CONTRIB**)
- body: EIG (originated); version: the 52 spec @ catalogue version; scope: conditional, evidence-backed responsiveness semantics for energy flexibility
- applicable entities: 52 RD records framework-wide; alignmentRef: 79 eig:ResponseDerivative, 81 USEF R-NONE gap (the standardisation rationale — existing standards can't express it); establishment path: publication + Zenodo + Symbiotic Buildings community (tracked); adoption AL-CONTRIB; the framework's flagship standards contribution

### EIG-STD-MID — Measuring Instruments Directive (SF-MSR, AL-CONFORM)
- body: EU 2014/32/EU; scope: legal metrology for billing meters; applicable entities: 36 metering devices, 27 settlement inputs; criteria: MID-certified meters for settlement-grade data → 93 CD-MKT; status ADOPTED

## 23. Completion Criteria
☑ All six spec fields (ID, standard body, scope, requirements, applicable entities, compliance criteria) ☑ Family register consolidating the estate's standards (building/measurement/semantic/grid/security/exchange/ML/EIG-originated) ☑ Adoption levels incl. AL-CONTRIB for EIG's own contributions ☑ Relevant-clause paraphrase (IP-respecting) with applicable-entity binding ☑ Version tracking + conformance→compliance (93) feed ☑ Semantic formalisation via 79/81 (no duplication) ☑ The Response Derivative specification registered as the flagship standards contribution — closing the framework at its own theoretical contribution.
