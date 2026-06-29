---
type: catalogue
ontology: EIG-MLM
catalogue_no: 79
catalogue_id: EIG-CAT-079
entity: ontology
band: semantics
status: active
tags: [eig-mlm, catalogue, band:semantics, entity:ontology]
aliases: ["EIG-CAT-079", "Catalogue 79", "ontology"]
---

# CAT-79 — Ontology

> **EIG-CAT-079** · band: **Semantics & Ontology** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-01 building_classification]] · [[CAT-17 user]] · [[CAT-78 use_case]]
**Used by (downstream):** [[CAT-80 knowledge_graph]] · [[CAT-81 semantic_mapping]] · [[CAT-84 reasoning_rule]] · [[CAT-85 semantic_pattern]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-079`
**Entity prefixes:** `EIG-ONT-` (ontology artefacts/modules)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (semantic authority)
**Depends on (upstream):** every catalogue 01–78 (each §17 declares its mapping; this catalogue owns them)
**Used by (downstream):** 80 knowledge_graph, 81 semantic_mapping, 84 reasoning_rule, 85 semantic_pattern

---

## 1. Definition

The **Ontology Catalogue** registers the framework's formal semantic artefacts (`EIG-ONT-*`): the **EIG core ontology** and its modules — classes, properties, axioms, rules, mappings, versions, and validation evidence — serialised as RDF/OWL 2. It is where the §17 declarations accumulated across all 78 catalogues *land as engineering*: every "alignment owned by 79" resolves to a module, an alignment axiom set, and a validation record here. Lineage: the SHIFT/MLM ontology line (`mlm_v5_protege.ttl` ancestry), now governed: the historical state of design-intent connections without formal `owl:imports` is recorded honestly, and this catalogue mandates the formalisation path (A2). The novel class **`eig:ResponseDerivative`** — the framework's theoretical contribution — is anchored here with its sibling `eig:Sensitivity` (54 §17 boundary made ontological).

## 2. Scope
**In scope:** ontology module register (the spec's eight: ID, classes, properties, axioms, rules, mappings, version, validation); namespace governance; module architecture (core + domain modules + alignment modules); external framework register (the ~22 connected frameworks) with import/alignment status per framework; axiom and serialisation conventions; validation machinery (reasoner consistency, SHACL conformance, competency questions); publication (IRI dereferencing, documentation). **Out of scope:** instance data (80 KG), term-level vocabulary entries (82 — lexical layer), mapping records to external ontologies at fine grain (81 — this catalogue owns the *modules*, 81 the correspondence records), reasoning rule operations (84).

## 3. Classification Structure

### 3.1 Module architecture (`EIG-ONT-*`)

| Module | Namespace stem | Content |
|---|---|---|
| EIG-ONT-CORE | `eig:` | upper module: Entity, Subject, Record patterns; provenance/lifecycle scaffolding; dataClass individuals {Real, Calibrated, Synthetic, Computed} |
| EIG-ONT-BLD | `eig-bld:` | building domain (01–07): Building, Zone, Fabric, Archetype |
| EIG-ONT-AST | `eig-ast:` | assets/devices/networks (10–13, 36) |
| EIG-ONT-MKT | `eig-mkt:` | market/commercial (19–28): Market, Tariff, Contract, Settlement, CarbonRecord |
| EIG-ONT-FLX | `eig-flx:` | flexibility & **RD** (29–30, 52–56): `eig:ResponseDerivative` (conditional responsiveness, context-qualified), `eig:Sensitivity` (sibling), ResponseEvent, ResponseProfile, BehaviourObservation |
| EIG-ONT-DATA | `eig-dat:` | data layer (37–43): Stream, Variable, DataEntity, Dataset, QualityAssessment, Metadata |
| EIG-ONT-ML | `eig-ml:` | model/learning layers (44–51, 66–72): ModelDefinition/Release, Validation, Policy, Agent, RewardComponent |
| EIG-ONT-DEC | `eig-dec:` | decision layer (57–65): KPI, Objective, Constraint, Decision, Action, ControlStrategy, OperationalEvent, Workflow, Benchmark |
| EIG-ONT-APP | `eig-app:` | application layer (73–78): Application, Service, Dashboard, Alert, Report, UseCase |
| EIG-ONT-ALIGN-* | per external | alignment modules: one per external framework (§3.2), holding only mapping axioms |

### 3.2 External framework register (the connected-frameworks set, status-honest)

| Framework | Used for (source §17s) | Status |
|---|---|---|
| W3C PROV-O | lineage/provenance everywhere (39, 50, 56, 77…) | ALIGN-FORMAL (imports + axioms) |
| DCAT(-AP) + dct | datasets/reports (39, 43, 77) | ALIGN-FORMAL |
| DQV | data quality (40) | ALIGN-FORMAL |
| QUDT/UCUM | units/quantities (38 A2 everywhere) | ALIGN-FORMAL |
| SOSA/SSN | observations/sensing (36, 38, 46, 53) | ALIGN-FORMAL |
| SAREF (+ 4EE/4BLDG/4ENER) | devices/buildings/energy (11, 36, 61, 62, 74) | ALIGN-FORMAL |
| MLS / MEX | ML metadata (44–51) | ALIGN-FORMAL |
| ODRL | access policies (43) | ALIGN-FORMAL |
| SHACL | shapes/constraints (59, 84) | ENGINE (not aligned-to; used) |
| SKOS | vocab/taxonomy backbone (82/83) | ENGINE |
| CIM (IEC 61970/61968), USEF, OpenADR, SEAS, Brick, BOT, DUL, FOAF/ORG, GoodRelations, p-plan, IEC 62746, EN 50491 set | network/market/building/org alignments (13, 15, 20, 25, 34, 64, 74) | **ALIGN-INTENT → scheduled** (the SHIFT historical state: design-intent connections; formalisation backlog ordered by consumption, A2) |

Status vocabulary: ALIGN-FORMAL (owl:imports or IRI-level axioms in an ALIGN module, validated) · ALIGN-INTENT (documented correspondence, no axioms yet — honest backlog) · ENGINE (technology used, not an alignment target).

### 3.3 Axiom conventions
OWL 2 DL profile target (EL fallback per module where reasoner performance demands, declared); naming: classes UpperCamel, properties lowerCamel, individuals from register IDs; annotation: every class/property carries `dct:source` → catalogue section (the 01–78 files are the documentation of record); no anonymous restrictions where a named pattern exists (85).

## 4. Hierarchy
```
EIG-ONT-CORE ← imported by all domain modules
  └── domain modules (BLD/AST/MKT/FLX/DATA/ML/DEC/APP)
        └── ALIGN-* modules (import domain + external; axioms only)
              └── consumed by 80 KG schema, 84 rule engines
```

## 5. Field Groups & Fields

### 5.1 Ontology module (`EIG-ONT-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | ontologyID / namespace | string/IRI | §3.1 register; IRIs dereferenceable (A6) | M | Spec: Ontology ID |
| 2 | classes | array<json> | [{IRI, label, definition, sourceCatalogue §, superclasses}] | M | Spec: Classes — every class traces to a catalogue section |
| 3 | properties | array<json> | [{IRI, kind: object/datatype/annotation, domain, range, characteristics}] | M | Spec: Properties |
| 4 | axioms | json | counts + artefact ref (TTL); restriction inventory; profile conformance (DL/EL) | M | Spec: Axioms |
| 5 | rules | array<ref> | →84 rules packaged with this module | M | Spec: Rules |
| 6 | mappings | array<ref> | →81 correspondence sets / ALIGN modules + §3.2 status | M | Spec: Mappings |
| 7 | imports | array<IRI> | owl:imports closure (versioned IRIs only — no floating imports, V3) | M | Dependency truth |
| 8 | serialisation | json | TTL canonical + RDF/XML/JSON-LD exports; artefact hashes | M | Pinned files (mlm_v5→eig lineage noted in CORE) |
| 9 | competencyQuestions | array<json> | CQ register: question + SPARQL + expected-shape (validation basis, A4) | M | Fit-for-purpose evidence |
| 10 | validation | json | reasoner consistency (engine+version), SHACL conformance refs (84), CQ pass results, date | M | Spec: Validation |
| 11 | version / status | semver/enum | spec: Version; lifecycle standard | M | Governance |

## 6. Controlled Vocabularies
Module register (§3.1), alignment statuses (§3.2 — honest, V5), profile targets, serialisations. Namespace policy: one stem per module; external terms never minted locally (reuse-first, A3).

## 7. Applicability Rules
- **A1 — Catalogues are the source of truth; the ontology is their formalisation.** Classes/properties derive from catalogue registers (annotated dct:source); semantic drift between a catalogue and its module is a defect against the catalogue (fix the module). New modelling needs route through catalogue changes first.
- **A2 — Intent becomes axioms on a schedule.** ALIGN-INTENT entries (the SHIFT historical state) carry a formalisation backlog ordered by actual consumption (frameworks whose mappings 80/81 consumers exercise get formalised first); the panel-ready justification for not wholesale-importing externals stands (selective alignment over monolithic import), but every claimed connection must either become axioms or be reclassified honestly.
- **A3 — Reuse before minting.** Where an adequate external class/property exists in an ALIGN-FORMAL framework, EIG terms subclass/subproperty it rather than duplicating; minting requires a recorded gap note.
- **A4 — Competency-question validation.** Modules ship CQs derived from their catalogues' §18 query promises ("which models break if EIG-VAR-0240 changes?" etc.); a module change that breaks a CQ fails validation.
- **A5 — The RD anchor.** `eig:ResponseDerivative` is axiomatised as the catalogue defines it (52): context-qualified (mandatory `eig:conditionedOn`), evidence-linked (`eig:evidencedBy` → ResponseEvent, min cardinality per spec), with `eig:Sensitivity` a disjoint sibling (54 boundary as a disjointness axiom) — the theoretical contribution is formally inspectable and citable.
- **A6 — Published and dereferenceable.** Released module IRIs dereference to documentation + serialisations; publications citing the ontology cite versioned IRIs.
- **A7 — Profile discipline.** Modules declare and verify their OWL profile; reasoning-performance constraints (80/84 operations) are design inputs, not afterthoughts.

## 8. Validation Rules
V1 every catalogue (01–78) §17 mapping resolves to a module/ALIGN entry (coverage matrix complete — the P12 landing obligation); V2 classes/properties carry dct:source annotations resolving to catalogue sections; V3 imports closure versioned and resolvable; no cycles; V4 reasoner consistency per release (engine pinned); profile conformance verified; V5 alignment statuses honest: ALIGN-FORMAL entries have axiom artefacts; INTENT entries have backlog tickets; V6 CQ suite passes per release; V7 RD axioms match 52 (cardinalities, disjointness with Sensitivity — drift detector against catalogue versions); V8 serialisation hashes pinned; canonical TTL parses; V9 namespace policy enforced (no local minting where reuse mandated, A3 audit); V10 modules immutable per version; deprecations carry migration notes.

## 9. Relationships to Other Catalogues
ALL 01–78 (§17 sources; the coverage matrix V1) · 80 (KG schema derives from modules) · 81 (fine-grained correspondences; ALIGN module content) · 82/83 (lexical/taxonomic layers bound via SKOS) · 84 (rules packaged with modules; SHACL shapes) · 85 (patterns the axioms instantiate) · 77 (publications cite versioned IRIs) · 90 (project deliverables: ontology releases as outputs).

## 10. Benchmarking Requirements
Semantic-layer health: §17 coverage 78/78, ALIGN-INTENT backlog burn-down, CQ pass rates, reasoner performance per module (profile discipline evidence).

## 11. Dataset Requirements
Ontology releases ship as 43 packages (TTL + docs + CQ results, DK-REF kind); DOIs for cited releases.

## 12. Feature Requirements
n/a at instance level; module metadata feeds 80 schema registries.

## 13. Model Requirements
MLM ontology line (mlm_v5 ancestry) is EIG-ONT-ML's documented lineage; the paper-facing architecture ("A Multi-Layered Machine Learning Architecture…") cites these IRIs.

## 14. KPI Requirements
Coverage/backlog/CQ KPIs register (57 KF-DAT adjacency).

## 15. Response Derivative Requirements
A5: the RD class anchored, axiomatised, disjoint from Sensitivity, evidence-cardinality-constrained — the concept's formal home for academic establishment (the "Symbiotic Buildings"-adjacent literature strategy: citable, versioned, inspectable semantics).

## 16. Decision Requirements
Decision-layer semantics (eig-dec:) preserve the gates/coupling/abstention structure so 80 queries over decisions reflect governance reality, not a flattened log.

## 17. Ontology Mapping
(Self) — this catalogue *is* the §17 target. Coverage matrix maintained as §8 V1.

## 18. Knowledge Graph Mapping
Modules → 80 schema (node/edge type derivation rules there); CQ SPARQL doubles as 80 validation queries.

## 19. Digital Twin Mapping
Twin semantic interface (32/33 §17s) lands in eig-bld/eig-ast + SAREF4BLDG alignment; twin composition-by-reference (32 A7) mirrors module composition.

## 20. Governance
Semantic authority: PI; module stewards per domain (catalogue owners); release board review (CQ + consistency + coverage evidence); ALIGN backlog reviewed quarterly.

## 21. Versioning
Modules semver with versioned IRIs; ALIGN modules version with both ends noted. Catalogue 1.0.0.

## 22. Example Records

### EIG-ONT-FLX @1.0.0 — flexibility & RD module
- classes incl. `eig:ResponseDerivative` (dct:source → 52 §1; subClassOf eig:Record; mandatory conditionedOn ≥1 per context axis set; evidencedBy ≥ minEvidence pattern via 84 rule), `eig:Sensitivity` (source 54; **disjointWith eig:ResponseDerivative**), `eig:ResponseEvent` (prov:Activity), `eig:ResponseProfile` (summarises pattern, 85)
- imports: CORE, eig-dat (evidence), PROV-O, SOSA, QUDT; rules: RD-grade evidence shape (84 SHACL), staleness inference; CQs: "ACTIVE RDs citable for product P now?" (the 52 A4 gate as SPARQL) ✓
- validation: HermiT consistent; DL ✓; serialisation TTL sha256:…; lineage note: SHIFT/mlm_v5 ancestry

### EIG-ONT-ALIGN-SAREF @1.0.0
- axioms: eig-ast:Device ⊑ saref:Device; eig-dec:Action alignment to saref:Command (61 §17); status ALIGN-FORMAL; both-end versions pinned

### ALIGN-INTENT example — CIM
- 13 network §17 correspondence documented (NetworkZone↔CIM topology classes); no axioms yet; backlog ticket ordered by 80 network-query demand; honesty per §3.2

## 23. Completion Criteria
☑ All eight spec fields (ID, classes, properties, axioms, rules, mappings, version, validation) ☑ Module architecture landing every §17 from 01–78 (coverage matrix V1) ☑ External framework register with honest ALIGN-FORMAL/INTENT statuses + formalisation backlog (the SHIFT state governed) ☑ `eig:ResponseDerivative` anchored with Sensitivity disjointness and evidence cardinality ☑ CQ-based validation + reasoner/profile discipline ☑ Reuse-first, dereferenceable, versioned-IRI publication.
