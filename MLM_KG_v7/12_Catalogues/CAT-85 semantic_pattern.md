---
type: catalogue
ontology: EIG-MLM
catalogue_no: 85
catalogue_id: EIG-CAT-085
entity: semantic_pattern
band: semantics
status: active
tags: [eig-mlm, catalogue, band:semantics, entity:semantic_pattern]
aliases: ["EIG-CAT-085", "Catalogue 85", "semantic_pattern"]
---

# CAT-85 — Semantic Pattern

> **EIG-CAT-085** · band: **Semantics & Ontology** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-79 ontology]] · [[CAT-80 knowledge_graph]] · [[CAT-84 reasoning_rule]]
**Used by (downstream):** [[CAT-79 ontology]] · [[CAT-80 knowledge_graph]] · [[CAT-84 reasoning_rule]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-085`
**Entity prefix:** `EIG-SP-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** Semantic authority
**Depends on (upstream):** 79 ontology (instantiates patterns), 80/84 (graph/rule idioms)
**Used by (downstream):** 79 module authoring, 80 schema design, 84 rule authoring, future catalogue/ontology extensions (the design-pattern library for 95+)

---

## 1. Definition

The **Semantic Pattern Catalogue** registers reusable ontology design patterns (`EIG-SP-*`): named, documented modelling solutions — classes, properties, axiom shapes, and usage guidance — that recur across the framework's semantics. Patterns are the *grammar* the modules (79) are written in: the registry/record split, the spec/instance split, supersede-never-edit chains, evidence linkage, context qualification, the firewall tag, lifecycle status machines. Purpose: **ontology engineering** discipline — extensions (new catalogues, new domains, project-specific modules for ALTUS/NEST/ODEON-class work) reuse proven patterns instead of reinventing structure, keeping the whole semantic estate parseable by the same tools and minds (A1).

## 2. Scope
**In scope:** pattern record schema (the spec's six: ID, pattern type, classes, properties, reuse guidance, examples); the core pattern library (extracted from 01–84's recurring structures); pattern composition rules; anti-pattern register (what we deliberately avoid, with reasons); conformance notes (how 79 modules attest pattern use); external ODP alignment (where a pattern matches a published Ontology Design Pattern, cited). **Out of scope:** module content (79), concrete rules (84 — patterns may *ship* rule templates), instance data (80).

## 3. Classification Structure

### 3.1 Pattern types (`patternType`)

| Code | Type | Concern |
|---|---|---|
| PT-STR | Structural | how things and their records relate (registry/record, spec/instance, definition/release) |
| PT-LIFE | Lifecycle | status machines, supersession, versioning-in-data |
| PT-EVID | Evidential | evidence linkage, validation binding, confidence carriage |
| PT-CTX | Contextual | context qualification, applicability, conditional truth |
| PT-GOV | Governance | ownership, approval, SoD, licence/authority envelopes |
| PT-SAFE | Safety/integrity | provenance class carriage, taint, environment partitioning |
| PT-AGG | Aggregation | composition, summarisation with weakest-link semantics, cohort floors |

### 3.2 The core pattern library (normative inventory; each fully documented per §5)

| Pattern | Type | Distilled from |
|---|---|---|
| **Registry/Record** | PT-STR | every catalogue: governed register entries with IDs, owners, lifecycle |
| **Spec/Instance** | PT-STR | 52 RD-SPEC/RD, 46 FC-DEF/FC, 56 CMP/REV, 60 DEC/DEC-I… (definition templates × occurrence records) |
| **Definition/Release** | PT-STR/LIFE | 44 (name-stable definition; immutable versioned artefacts) |
| **Supersede-Never-Edit** | PT-LIFE | 27 A6 lineage (settlement, runs, reports, records framework-wide) |
| **Status Machine** | PT-LIFE | lifecycle enums with evidenced transitions (71 §3.1, 63 §3.3, 78 §3.4) |
| **Evidence Linkage** | PT-EVID | claims carry typed links to evidence records with minimum-cardinality bars (52 A1, 47, 78 §5.1.12) |
| **Confidence Carriage** | PT-EVID | recomputable composed confidence with component logging (46 §5.3, 52 §5.3, 71 §3.3) |
| **Context Qualification** | PT-CTX | mandatory conditioning with declared axes + UNKNOWN honesty (52 §3.2, 69) |
| **Validity Domain** | PT-CTX | authority bounded by evidenced coverage; transfer-is-earned (71 A4, 52 A6) |
| **Duty Quartet** | PT-GOV | owner/steward/reviewer/approver with SoD (16 A6 everywhere) |
| **Authority Envelope** | PT-GOV | intersection-of-factors authority with automatic suspension (72 §3.2) |
| **DataClass Carriage** | PT-SAFE | per-value/record provenance class, origin-immutable (37 A2, 38 A6) |
| **Taint Closure** | PT-SAFE | derived contamination propagation, never laundered (42 A2, 84 EIG-RR-0042) |
| **Environment Partition** | PT-SAFE | sandbox/production tagging with one-way promotion of logic only (20 A2 chain) |
| **Weakest-Link Aggregation** | PT-AGG | composite confidence/fitness = min member (55 A3, 43 A3) |
| **Cohort Floor** | PT-AGG | n≥10 disclosure gating at every aggregation surface (38 A7, 65 A2, 75 A5) |
| **Badge Projection** | PT-SAFE/AGG | register truth → non-suppressible rendering markers with worst-of (75 §3.2) |
| **Single-Source Mirror** | PT-STR | reference-don't-copy with drift detection (59 A2, 65 A5, 82 A2, 83 A1) |

### 3.3 Anti-pattern register (deliberate avoidances, documented)
ANTI-1 implicit defaults ("library default" values — 48 A1's enemy) · ANTI-2 prose-only rules (everything machine-evaluable or explicitly judgement-flagged) · ANTI-3 confidence laundering through averaging (weakest-link instead) · ANTI-4 hard limits as weights (59 A3) · ANTI-5 anonymous edges/assertions (receipts everywhere, 80 A3) · ANTI-6 local minting where reuse exists (79 A3). Each with the failure story that motivated it.

## 4. Hierarchy
```
Pattern type (PT-*) → Pattern (EIG-SP-*: structure + guidance + examples)
                          ├── composition notes (patterns that stack: Spec/Instance + Evidence Linkage + Context Qualification ≈ the RD shape)
                          └── conformance attestations ← 79 modules declare patterns used
```

## 5. Field Groups & Fields

### 5.1 Pattern (`EIG-SP-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | patternID | string | `EIG-SP-\d{3}` | M | Spec: Pattern ID |
| 2 | name / patternType | string/enum | §3.1 | M | Spec: Pattern type |
| 3 | problem | string | the recurring modelling problem solved | M | Motivation |
| 4 | classes | array<json> | participating class roles (abstract: «Register», «Record», «Evidence») with 79 anchor examples | M | Spec: Classes |
| 5 | properties | array<json> | participating property roles (e.g. supersedes, evidencedBy, conditionedOn) with characteristics (functional, transitive…) | M | Spec: Properties |
| 6 | axiomTemplate | string | OWL/SHACL template (parameterised) instantiable per use | M | The formal shape |
| 7 | ruleTemplates | array<ref> | 84 rule skeletons shipped with the pattern (e.g. taint-closure template) | C | Logic companions |
| 8 | reuseGuidance | json | when to apply / when NOT / composition with other patterns / parameter choices | M | Spec: Reuse guidance |
| 9 | knownUses | array<ref> | catalogue/module instantiations (≥2 required for ACTIVE — patterns are extracted, not invented, A2) | M | Spec: Examples |
| 10 | externalODP | json | published ODP correspondence where exists (cited) / declared novel | C | Literature anchoring |
| 11 | antiPatternLinks | array<ref> | §3.3 entries this pattern defends against | C | Negative space |
| 12 | owner / version / status | — | standard | M | Governance |

## 6. Controlled Vocabularies
Pattern types (§3.1), class/property role vocabulary («Register», «Record», «Spec», «Instance», «Evidence», «Context», «Envelope»…), anti-pattern register (§3.3). Conformance attestation format for 79 modules.

## 7. Applicability Rules
- **A1 — Patterns before invention.** New modelling (catalogue extensions, project modules) consults the library first; pattern-conformant designs fast-track 79 review; novel structures require a gap argument and, on second use, extraction into a new pattern (the library grows by harvest).
- **A2 — Extracted, not speculative.** ACTIVE patterns cite ≥2 real instantiations; the library documents what the framework *does*, generalised — speculative patterns stay DRAFT.
- **A3 — Composition is explicit.** Stacked patterns declare their composition (the RD modelling = Spec/Instance + Evidence Linkage + Context Qualification + Confidence Carriage + Validity Domain — worked as the flagship composition example); compositions with interaction subtleties get their own guidance notes.
- **A4 — Anti-patterns are enforced negatively.** Reviews check designs against §3.3; an anti-pattern occurrence in a proposed module is a blocking finding with the registered failure story as rationale.
- **A5 — Templates instantiate, never copy-paste-drift.** Axiom/rule templates are parameterised artefacts; instantiations record their parameters (pattern provenance on modules) so template fixes propagate knowingly.
- **A6 — Literature honesty.** External ODP correspondences cited where they exist; genuinely novel patterns (the RD composition, Authority Envelope, Taint Closure as governed-AI patterns) are declared novel — publishable ontology-engineering contributions in their own right.

## 8. Validation Rules
V1 pattern records complete (six spec fields); axiom templates parse parameterised; V2 ≥2 knownUses resolving for ACTIVE; V3 79 modules carry pattern attestations; attestation↔knownUses bidirectional; V4 template instantiations record parameters (provenance); template version bumps flag instantiations for review; V5 composition declarations acyclic and guidance-complete for registered stacks; V6 anti-pattern reviews logged per module release; V7 external ODP citations resolve; novelty claims reviewed; V8 patterns immutable per version.

## 9. Relationships to Other Catalogues
79 (modules instantiate; attestations; review fast-track) · 84 (rule templates shipped; e.g. taint-closure skeleton behind EIG-RR-0042) · 80 (graph idioms: receipts-on-edges, INF-MAT safety set follow patterns) · ALL 01–78 (the harvest source — §3.2 distillations cite their origins) · 90 (project modules for ALTUS/NEST/ODEON-class extensions consume the library) · publications (A6: the pattern library as ontology-engineering output).

## 10. Benchmarking Requirements
Library health: pattern reuse rates in new modules, anti-pattern catch rate at review, template-drift incidents (target 0 via A5), harvest backlog (recurring structures awaiting extraction).

## 11. Dataset Requirements
Pattern library releases (templates + docs) as 43 DK-REF packages alongside 79 releases.

## 12. Feature Requirements
n/a (design layer).

## 13. Model Requirements
n/a directly; the Definition/Release and Evidence Linkage patterns *are* the model-register grammar (44/47) — extensions to new model families inherit it by instantiation.

## 14. KPI Requirements
Library KPIs (reuse, catch rates) register (57 KF-DAT adjacency).

## 15. Response Derivative Requirements
The RD modelling is the library's flagship composition (A3): documented end to end as the worked example of five patterns stacking — making the framework's theoretical contribution *reproducible as method*: a future "Response Derivative for water systems" (or any conditional-responsiveness domain) instantiates the same composition with domain parameters — the concept travels.

## 16. Decision Requirements
The decision-layer grammar (gates-before-scoring, abstention-as-outcome, audit-chain) is patterned (PT-GOV/PT-EVID compositions) so governed-autonomy extensions replicate the safety architecture by construction, not by vigilance.

## 17. Ontology Mapping
Patterns ↔ ODP conventions (cpannotationschema-style annotations on 79 modules); templates publishable in ODP portals where novel (A6).

## 18. Knowledge Graph Mapping
Node `SemanticPattern`; edges `INSTANTIATED_IN`→79 modules {parameters}, `COMPOSES`, `DEFENDS_AGAINST`→anti-patterns, `CORRESPONDS_TO`→external ODPs. "Where is Supersede-Never-Edit applied?" is one query — pattern audits made cheap.

## 19. Digital Twin Mapping
Twin semantics (template/instance 33/32, composition-by-reference, fidelity status machines) attest their patterns; new twin domains instantiate rather than re-derive.

## 20. Governance
Owner: semantic authority; harvest proposals from any module steward; novelty/publication decisions with PI (the contribution pipeline); anti-pattern additions require a documented failure story (no hypothetical prohibitions).

## 21. Versioning
Patterns semver; template changes cascade per A5/V4. Catalogue 1.0.0.

## 22. Example Records

### EIG-SP-001 — Registry/Record (PT-STR, the foundation)
- problem: governed collections of identifiable, owned, lifecycled things; classes «Register» (implicit per catalogue), «Record» (eig:Record root); properties: identifier (functional), owner→16, status, version
- axiom template: Record ⊑ ∃identifier ⊓ ∃ownedBy ⊓ ∃status; guidance: every catalogue entity instantiates; known uses: all 78; anti-links: ANTI-5

### EIG-SP-007 — Taint Closure (PT-SAFE, declared novel as governed-AI pattern)
- problem: contamination semantics that survive derivation (synthetic data, but generalises: privacy taint, sandbox taint); properties: taintSource, derivedFrom (transitive carrier); rule template: the 84 EIG-RR-0042 skeleton parameterised by taint predicate
- guidance: pair with DataClass Carriage; never provide an untaint operation (the laundering anti-path is structural); known uses: 42 syntheticLineage, environment partition propagation; ODP: none found — novelty reviewed ✓

### EIG-SP-012 — The RD Composition (flagship, A3)
- composition: Spec/Instance + Evidence Linkage (min-cardinality) + Context Qualification (mandatory axes, UNKNOWN-honest) + Confidence Carriage (recomputable) + Validity Domain (transfer-is-earned)
- instantiated: 52 (the origin), 71 policies (the same stack for learned policies — the composition's second use proving its generality); guidance: the five interact (context axes bound validity domains; evidence freshness feeds confidence) — interaction notes documented; the method by which conditional-responsiveness concepts are built (§15)

## 23. Completion Criteria
☑ All six spec fields (ID, pattern type, classes, properties, reuse guidance, examples) ☑ Core library extracted from the 84-catalogue estate (structural/lifecycle/evidential/contextual/governance/safety/aggregation) ☑ Anti-pattern register with failure stories ☑ Extraction-not-speculation + ≥2-uses discipline ☑ Parameterised templates with drift-proof instantiation provenance ☑ The RD composition documented as reproducible method ☑ Novelty honesty positioning publishable contributions.
