---
type: catalogue
ontology: EIG-MLM
catalogue_no: 82
catalogue_id: EIG-CAT-082
entity: vocabulary
band: semantics
status: active
tags: [eig-mlm, catalogue, band:semantics, entity:vocabulary]
aliases: ["EIG-CAT-082", "Catalogue 82", "vocabulary"]
---

# CAT-82 — Vocabulary

> **EIG-CAT-082** · band: **Semantics & Ontology** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-01 building_classification]] · [[CAT-06 building_benchmark]] · [[CAT-78 use_case]]
**Used by (downstream):** [[CAT-75 dashboard]] · [[CAT-77 report]] · [[CAT-79 ontology]] · [[CAT-80 knowledge_graph]] · [[CAT-81 semantic_mapping]] · [[CAT-83 taxonomy]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-082`
**Entity prefix:** `EIG-VOC-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** Semantic authority with catalogue owners (term domains)
**Depends on (upstream):** every catalogue's §6 (controlled vocabularies declared in place across 01–78)
**Used by (downstream):** 79 (lexical layer of ontology terms), 80 (label consistency), 81 (alias targets), 83 (taxonomy term stock), 75/77 (rendering labels), all forms/schemas generated from catalogues

---

## 1. Definition

The **Vocabulary Catalogue** is the framework's consolidated lexical register: every controlled term (`EIG-VOC-*` entries) with its definition, allowed values where enumerable, synonyms, deprecation state, and owning domain. The individual catalogues' §6 sections *declare* their vocabularies in context; this catalogue *consolidates* them into one SKOS-backed register so that: the same word means one thing everywhere (or its domain-qualified senses are explicit), synonyms resolve, deprecated terms migrate, and machine consumers (form generators, schema builders, ontology lexicalisation 79, UI labels 75) draw from a single source. Purpose: **semantic consistency** — the framework's glossary discipline (the house glossary skill, made registry, A1).

## 2. Scope
**In scope:** term entry schema (the spec's six: term, definition, allowed values, synonyms, deprecated terms, domain); consolidation rules from §6 sources (catalogues remain authoring context; this register is the merge with collision handling); sense management (domain-qualified homonyms); synonym policy (preferred/alternate labels, SKOS); deprecation lifecycle with migration targets; multilingual readiness (label structure, EN authoritative); rendering label bindings (75/77). **Out of scope:** taxonomy hierarchies (83 — terms here, trees there), external-name aliasing (81 SM-ALS), ontology axioms (79 — lexicalisation only), the semantic content of enumerations (owning catalogues govern meaning; this register governs the words).

## 3. Classification Structure

### 3.1 Term kinds (`termKind`)

| Code | Kind | Examples (sources) |
|---|---|---|
| TK-CONCEPT | Named concept | Response Derivative (52), flexibility envelope (30), digital twin fidelity (32), RD-grade (40) |
| TK-ENUM | Enumeration value | dataClass {REAL, CALIBRATED, SYNTHETIC, COMPUTED} (37), qualityFlag set (40 §3.3), severity SEV-1..4 (63) |
| TK-CODE | Code/prefix | EIG-RD-, MLM-L1..L4, SER-01..13 (44), CC-* (59) |
| TK-METRIC | Metric/unit-bearing term | CVRMSE, pinball loss (47), EUI (57) |
| TK-ROLECLASS | Actor/role terms | flexumer (14), steward/approver (16) |

### 3.2 Sense discipline
One preferred label → one sense per domain. Cross-domain homonyms (the framework's known ones, registered): **baseline** (27 settlement counterfactual BL-* vs 51 model baseline EIG-MBM-BL-* — namespace-separated by design, 51 §3.1 note), **event** (56 response event vs 63 operational event — "response event"/"operational event" qualified senses), **benchmark** (06/12/51/65 family — 65 federation note), **context** (52 conditioning vs 69 register — unified by 69 A2 federation), **policy** (71 learned policy vs 91 governance policy — always qualified). Each homonym entry cross-links its senses (V4).

### 3.3 Deprecation lifecycle
ACTIVE → DEPRECATED (migration target + sunset date; renderers show both during window) → RETIRED (rejected at write; historical records render with tooltip migration note). Sources: catalogue version changes, register corrections (e.g. prefix register fixes EIG-FEA→EIG-FT, EIG-DSP dataset sense → EIG-DSET — recorded here as the lexical trail of the programme's own decision log).

## 4. Hierarchy
```
Catalogue §6 declarations (authoring) → consolidation → EIG-VOC register (one merged truth)
                                                     ├── SKOS export (prefLabel/altLabel/definition)
                                                     └── consumers: 79 lexicalisation, 83 stock, 75/77 labels, schema/form generators
```

## 5. Field Groups & Fields

### 5.1 Term entry (`EIG-VOC-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | termID | string | `EIG-VOC-\d{5}` | M | Entry ID |
| 2 | term | string | preferred label (EN authoritative) | M | Spec: Term |
| 3 | termKind | enum | §3.1 | M | Kind |
| 4 | definition | string | ≤500 chars, normative, copied-or-derived from the owning catalogue section (cited) | M | Spec: Definition — single normative wording |
| 5 | domain | json | owning catalogue + section (dct:source) + sense qualifier where homonymous | M | Spec: Domain |
| 6 | allowedValues | array<json> | for TK-ENUM: the value set {code, label, definition} mirrored from the owning §6 (mirror-never-fork, A2) | C | Spec: Allowed values |
| 7 | synonyms | array<json> | [{label, status: alternate \| discouraged}] — internal synonymy only (external names → 81) | M | Spec: Synonyms |
| 8 | deprecatedForms | array<json> | [{term, deprecatedAt, migrationTarget, sunset}] | M | Spec: Deprecated terms |
| 9 | crossLinks | array<ref> | homonym senses (§3.2), related terms, taxonomy nodes (83) | C | Navigation |
| 10 | renderingLabels | json | UI/short/print forms (75/77 bindings); plain-language variant for occupant surfaces (AU-OCC register) | M | Display contract |
| 11 | skosExport | IRI | concept IRI (79 lexical binding) | M | Machine form |
| 12 | owner / version / status | — | §3.3 lifecycle | M | Governance |

## 6. Controlled Vocabularies
Term kinds (§3.1), synonym statuses, lifecycle (§3.3), sense-qualifier format. (Meta-note: this catalogue's own §6 is self-hosted — these meta-terms are entries too.)

## 7. Applicability Rules
- **A1 — One word, one meaning (per declared sense).** New terms check the register before minting; collisions either unify (preferred) or register qualified senses (§3.2) — accidental homonymy is a defect, managed homonymy is documented.
- **A2 — Mirror, never fork.** Enumeration entries mirror their owning catalogue's §6 by citation; semantic changes happen in the owning catalogue (version-bumped) and propagate here — the register is lexical authority, the catalogue is semantic authority (79 A1 symmetry at the word level).
- **A3 — Synonyms resolve inward.** Alternate labels map to preferred terms for search/rendering; *discouraged* synonyms (terms the house style avoids — writing-style governance interface) are flagged so generators never emit them.
- **A4 — Deprecation migrates.** Every deprecated form names its target; sunset windows let renderers dual-label; RETIRED forms are write-rejected framework-wide (schema generators enforce).
- **A5 — Occupant language is registered.** Plain-language variants for occupant-facing surfaces (49 AU-OCC, 75 occupant views, 76 CHN-OCC) are register entries, reviewed with engagement governance — courtesy phrasing is governed phrasing.
- **A6 — Publication terms cite the register.** Papers/proposals establishing framework terminology (Response Derivative, Symbiotic Buildings-line coinages) cite register entries + 79 IRIs — the literature-establishment strategy rides on stable, citable lexical anchors.

## 8. Validation Rules
V1 preferred-label uniqueness per sense; collision detector on additions; V2 definitions cite owning sections; mirror-drift detector vs owning §6 (A2); V3 TK-ENUM allowedValues exactly match owning catalogue (automated diff per catalogue release); V4 homonym senses cross-linked completely (§3.2 register maintained); V5 deprecations carry targets + sunsets; RETIRED enforcement verified in generators; V6 SKOS export round-trips (prefLabel/altLabel/definition lossless); V7 rendering labels present for terms appearing on surfaces (75/77 audit joins); V8 §6 consolidation coverage: every catalogue's declared vocabulary represented (the consolidation matrix — P12 landing obligation, lexical side); V9 entries immutable per version; supersession trails.

## 9. Relationships to Other Catalogues
ALL 01–78 §6 (sources; consolidation matrix V8) · 79 (lexicalisation: skos bindings on IRIs) · 83 (term stock for hierarchies) · 81 (external aliasing lands on these entries) · 75/77 (rendering labels; discouraged-term enforcement in generated text) · 49 (plain-language register) · writing-style governance (discouraged synonyms; house phrasing) · glossary-skill workflow (this register is its living backend).

## 10. Benchmarking Requirements
Lexical health: collision incidents, mirror-drift catches, consolidation coverage %, deprecated-form usage decay (sunset effectiveness).

## 11. Dataset Requirements
Register exports (SKOS TTL + CSV) ship as 43 DK-REF packages per release; publication-cited versions DOI'd with 79 releases.

## 12. Feature Requirements
n/a (lexical layer); generators consume the register for labels/enum codes.

## 13. Model Requirements
Schema/form/UI generators (the catalogue-driven tooling rationale from the programme decision log) draw enum codes and labels here — one register, every artefact consistent.

## 14. KPI Requirements
KPI names/labels (57) are entries; report/dashboard label audits (75 V9/77) join against rendering labels.

## 15. Response Derivative Requirements
"Response Derivative", "RD-grade", "conditional responsiveness", direction terms ("up = reduce net import" — the 30 §6 convention's lexical anchor), context-axis names: all registered with normative definitions and citable IRIs — the concept's terminology is defended at the word level (A6).

## 16. Decision Requirements
Decision-layer terms (abstention, coupling modes, demotion, derogation) registered with plain-language variants — governance speaks consistently from console to occupant notice to regulator pack.

## 17. Ontology Mapping
Entries ↔ skos:Concept with prefLabel/altLabel/definition/scopeNote; bound to 79 IRIs via lexicalisation links; deprecations ↔ owl:deprecated + skos:isReplacedBy pattern.

## 18. Knowledge Graph Mapping
Node `VocabularyTerm`; edges `DEFINES_TERM_OF`→owning catalogue, `SENSE_OF` (homonym links), `REPLACES` (deprecation), `LABELS`→79 IRIs. Label-consistency queries power rendering audits.

## 19. Digital Twin Mapping
Twin UI labels draw from rendering bindings; fidelity/mode terms (32/62) consistent across twin surfaces and reports.

## 20. Governance
Owner: semantic authority; term additions reviewed with owning-catalogue owners; occupant-language entries with engagement governance; quarterly consolidation audit (V8); the glossary-skill workflow operates against this register.

## 21. Versioning
Entries versioned; register releases align with 79 cycles. Catalogue 1.0.0.

## 22. Example Records

### EIG-VOC-00052 — "Response Derivative"
- kind TK-CONCEPT; definition: quantified, conditional, evidence-backed measure of responsiveness — ∂(response)/∂(input)|context (source 52 §1); domain 52; synonyms: "RD" (alternate); discouraged: "sensitivity" in this sense (the 54 boundary, lexically enforced)
- crossLinks: eig:ResponseDerivative IRI (79), Sensitivity entry (disjoint sibling note); rendering: "Response Derivative (RD)"; plain-language: "how much this building/asset responds when asked, in this situation"; A6: publication anchor entry

### EIG-VOC-00103 — "baseline" (managed homonym, §3.2)
- sense 1 (domain 27): settlement counterfactual consumption (BL-* registry); sense 2 (domain 51): model comparison reference (EIG-MBM-BL-*); cross-linked; generators must domain-qualify; collision history note: namespace separation by design (51 §3.1)

### EIG-VOC-00211 — dataClass enumeration (TK-ENUM)
- allowedValues mirror 37 §: REAL / CALIBRATED / SYNTHETIC / COMPUTED with definitions; mirror-drift detector bound to 37 releases; rendering labels incl. badge text (75 §3.2 SYNTHETIC marking); plain-language variants for public surfaces (hyperGrid envelope wording)

### Deprecation trail example
- "EIG-FEA" → DEPRECATED 2026-06, target "EIG-FT" (the programme's own register correction, lexically recorded); sunset: generators dual-recognise through P14 consistency pass, RETIRED after

## 23. Completion Criteria
☑ All six spec fields (term, definition, allowed values, synonyms, deprecated terms, domain) ☑ Consolidation of every catalogue §6 with mirror-never-fork discipline (coverage matrix V8) ☑ Managed homonymy for the framework's known collisions ☑ Deprecation lifecycle with migration enforcement ☑ Occupant plain-language register + discouraged-term hooks for the writing-style line ☑ SKOS-backed, citable, publication-anchoring entries (the RD terminology defended).
