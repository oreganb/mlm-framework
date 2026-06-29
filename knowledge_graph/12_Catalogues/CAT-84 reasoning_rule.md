---
type: catalogue
ontology: EIG-MLM
catalogue_no: 84
catalogue_id: EIG-CAT-084
entity: reasoning_rule
band: semantics
status: active
tags: [eig-mlm, catalogue, band:semantics, entity:reasoning_rule]
aliases: ["EIG-CAT-084", "Catalogue 84", "reasoning_rule"]
---

# CAT-84 — Reasoning Rule

> **EIG-CAT-084** · band: **Semantics & Ontology** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-59 constraint]] · [[CAT-79 ontology]] · [[CAT-80 knowledge_graph]]
**Used by (downstream):** [[CAT-03 occupancy]] · [[CAT-39 metadata]] · [[CAT-40 data_quality]] · [[CAT-52 response_derivative]] · [[CAT-75 dashboard]] · [[CAT-79 ontology]] · [[CAT-80 knowledge_graph]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-084`
**Entity prefix:** `EIG-RR-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** Semantic authority with rule-domain owners
**Depends on (upstream):** 79 ontology (vocabulary/axioms), 80 knowledge_graph (execution substrate), 59 constraint (expression-grammar kinship)
**Used by (downstream):** 80 (inference execution under §3.3 policy), 79 (rules packaged with modules), 39/40/52 (shape-validated registers), 75 (inferred-flag rendering)

---

## 1. Definition

The **Reasoning Rule Catalogue** registers the framework's formal inference and validation rules (`EIG-RR-*`): each with rule expression, inputs, outputs, applicability, validation evidence, and **engine compatibility** — spanning the mandated technologies: **SHACL** (shape validation: conformance over instance data), **SWRL/Datalog-class inference** (derivation of new assertions), and SPARQL-based rules (CONSTRUCT/ASK forms for engines that prefer them). Rules formalise the machine-checkable logic the catalogues declare in prose: evidence bars (52 A1 as a shape), taint propagation (42 V8 as inference), gate status (52 A4 as derivation), staleness (validUntil semantics framework-wide), envelope/precedence logic (59). Purpose: **SWRL, SHACL and inference** as governed artefacts — every inferred assertion and every conformance verdict traces to a versioned rule with tests (A1).

## 2. Scope
**In scope:** rule record schema (the spec's seven: ID, rule expression, inputs, outputs, applicability, validation, engine compatibility); rule classes (shape/derivation/transformation/consistency); expression conventions per engine with cross-engine equivalence where dual-implemented; inferred-assertion marking (never confusable with asserted facts); execution policy interface (80 §3.3 MAT/QT/BATCH); test fixtures and regression discipline; safety-rule designation (the always-materialised set). **Out of scope:** ontology axioms (79 — OWL entailment is the reasoner's job; rules here are beyond-OWL logic), runtime engines (31 deployment), constraint *enforcement* (59 — operational; shapes here may mirror constraints for data validation, single-source A3).

## 3. Classification Structure

### 3.1 Rule classes (`ruleClass`)

| Code | Class | Technology | Examples |
|---|---|---|---|
| RR-SHAPE | Shape/conformance | SHACL (node/property shapes, SPARQL constraints) | RD-grade evidence shape (52 A1), metadata CL-FULL conformance (39), settlement-line completeness (27), frozen-set completeness (48 A1) |
| RR-DERIVE | Derivation/inference | SWRL/Datalog/SPARQL CONSTRUCT | syntheticLineage taint closure (42 V8), sandbox propagation (39 A5), gate-status derivation (52 A4: ACTIVE∧fresh∧conf — as inferred property), profile currency (55 A2), envelope intersection (72 §3.2) |
| RR-CONSIST | Consistency/conflict detection | SHACL-SPARQL / ASK | precedence conflicts (59 V6), homonym misuse (82), passthrough drift (65/83 V3-class), one-ACTIVE-per-key invariants (39 V1, 52 V1, 55 V1) |
| RR-XFORM | Semantic transformation | SPARQL CONSTRUCT / mapping execution | DCAT export shaping (39), SKOS scheme generation (82/83), 81-graduated transforms |

### 3.2 Engine compatibility register
ENG-SHACL (pinned validator + version) · ENG-DATALOG (the KG engine's rule layer) · ENG-SWRL (DL-safe subset, with the reasoner pinned per 79 validation) · ENG-SPARQL (1.1, both deployments per 80 dual-binding). Rules declare primary engine + verified equivalents; dual-implemented rules carry equivalence test evidence (V6).

### 3.3 Safety designation
SAFETY rules (taint, firewall/environment propagation, quarantine reach, life-safety structural exclusions) are: always INF-MAT (80 A5), dual-implemented where the substrate allows, fixture-tested per release, and change-gated at board level (A5) — the rules that must never be wrong run with the most redundancy.

## 4. Hierarchy
```
Rule class (RR-*) → Rule (EIG-RR-*: expression + engines + tests)
                        ├── packaged with → 79 modules (per-domain rule sets)
                        └── executed per → 80 §3.3 policy (MAT/QT/BATCH)
```

## 5. Field Groups & Fields

### 5.1 Rule (`EIG-RR-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | ruleID | string | `EIG-RR-\d{4}` | M | Spec: Rule ID |
| 2 | ruleClass | enum | §3.1 | M | Class |
| 3 | name / sourceLogic | string/ref | the catalogue rule formalised (dct:source → e.g. "52 A1", "42 V8") | M | Prose→formal traceability (A2) |
| 4 | ruleExpression | json | per engine: {ENG-SHACL: shapes TTL ref+hash, ENG-DATALOG/SWRL: rule text, ENG-SPARQL: query} — primary + equivalents | M | Spec: Rule expression |
| 5 | inputs | json | node/edge types (80) / graph patterns consumed; required freshness | M | Spec: Inputs |
| 6 | outputs | json | RR-SHAPE: conformance reports (target registers/badges); RR-DERIVE: asserted-inferred triples/properties (marked, A4); RR-XFORM: artefact shapes | M | Spec: Outputs |
| 7 | applicability | json | environments, type scopes, activation contexts (69 where conditional) | M | Spec: Applicability |
| 8 | executionPolicy | enum | 80 §3.3 INF-{MAT,QT,BATCH} + cadence; SAFETY ⇒ MAT (V5) | M | When it runs |
| 9 | safetyDesignation | bool | §3.3 | M | Redundancy class |
| 10 | testFixtures | array<ref> | reference graphs + expected outputs/violations (43 DK-REF) — incl. negative cases | M | Spec: Validation — regression basis (V4) |
| 11 | engineCompatibility | json | §3.2 register entries verified, with versions; equivalence evidence where dual | M | Spec: Engine compatibility |
| 12 | performanceBudget | json | runtime bounds at reference scale (80 V9 kinship) | M | Operational fitness |
| 13 | owner / version / status | — | standard | M | Governance |

## 6. Controlled Vocabularies
Rule classes (§3.1), engines (§3.2), execution policies, severity of shape violations {Violation, Warning, Info — mapped to register consequences}. Inferred-assertion marker vocabulary (A4).

## 7. Applicability Rules
- **A1 — Governed inference only.** Engines execute registered rules exclusively; ad-hoc inference in pipelines or dashboards is the semantic equivalent of unregistered models (44 A1) — forbidden.
- **A2 — Prose first, rule second.** Every rule formalises a catalogue-stated logic (sourceLogic mandatory); a rule with no catalogue anchor proposes a catalogue change first (79 A1 symmetry) — the registers remain the readable truth.
- **A3 — Mirror constraints, don't fork them.** Shapes that validate data against operational constraints (59-derived) reference the constraint records; the 59 register stays the single source (59 A2), shapes are its data-validation projection.
- **A4 — Inferred is marked.** RR-DERIVE outputs carry inferred-assertion marking (provenance: ruleID@version, derivedAt, input snapshot) — no consumer can mistake derivation for assertion; retraction on input change is automatic per policy class.
- **A5 — Safety rules are special.** §3.3 designation: materialised, redundantly implemented, board-gated changes, release-blocking fixtures — taint/firewall/quarantine/life-safety logic gets the highest assurance tier (the no-fabrication and sandbox chains' formal guardians).
- **A6 — Closed-world honesty.** SHACL's closed-world checks vs OWL's open-world entailment are kept conceptually distinct: shapes validate *records* (registry completeness semantics), reasoners entail over *knowledge* — rules declare which game they play; conflations are review findings.
- **A7 — Performance is a correctness property.** Rules exceeding budgets degrade per declared fallbacks (QT→BATCH with staleness flags) rather than silently timing out (80 A2 freshness honesty).

## 8. Validation Rules
V1 expressions parse per engine; hashes pinned; V2 sourceLogic resolves to catalogue sections; coverage matrix: the catalogues' machine-checkable claims (the V-rules across 01–78 designated formalisable) → rules (the P12 landing obligation, logic side — staged, tracked); V3 inputs/outputs resolve in 80 schema; V4 fixtures pass per release incl. negative cases; regression on rule/ontology/schema changes; V5 SAFETY rules: MAT policy, dual implementation where available, board-gated; V6 dual-engine equivalence evidence current; V7 inferred outputs marked with full provenance; retraction tested; V8 shape severity → register consequence mappings defined (a Violation somewhere means something specific); V9 budgets verified at reference scale; fallbacks declared; V10 rules immutable per version.

## 9. Relationships to Other Catalogues
79 (vocabulary; rules packaged with modules; DL-safe discipline) · 80 (execution substrate; policy classes; INF-MAT safety set) · 59 (constraint mirroring A3; expression-grammar kinship) · 42/39/35 (the safety chains formalised) · 52/55/40/27/48 (evidence/conformance shapes) · 81 (graduated transforms as RR-XFORM) · 82/83 (scheme generation; consistency checks) · 75 (inferred flags surface; badge derivations) · 47 (rule fixtures follow validation-evidence discipline).

## 10. Benchmarking Requirements
Rule-system health: fixture pass rates, inference latency vs budgets, safety-rule audit results, prose→rule coverage progress (V2 matrix), retraction correctness samples.

## 11. Dataset Requirements
Fixture graphs as 43 DK-REF packages, versioned with rules; inference logs retained per consequence (safety-rule executions: audit horizons).

## 12. Feature Requirements
Inferred properties consumed as features (41) cite ruleID lineage — derivation provenance flows into model inputs like any source.

## 13. Model Requirements
n/a directly; model-register invariants (44 V-rules) gain RR-CONSIST formalisations per the V2 coverage programme.

## 14. KPI Requirements
Rule-health KPIs (57 KF-DAT); shape-violation rates per register as data-governance leading indicators.

## 15. Response Derivative Requirements
The RD layer's formal guardians: the **RD-grade shape** (52 A1: evidence count, fitness refs, taint-free, timestamp sources — as SHACL), **gate-status derivation** (52 A4 as inferred property powering 80's INF-QT views and 60's checks), **invalidation propagation** (52 A7 as derivation cascading status), context-cell conformance (69 V4) — the concept's integrity is machine-enforced end to end.

## 16. Decision Requirements
Decision-audit shapes (instance completeness, 60 V8), envelope-intersection derivation (72), demotion-status inference (60 A5 as derived property) — governance state is computable, hence renderable (75) and alertable (76).

## 17. Ontology Mapping
Rules packaged per 79 modules; SWRL DL-safety verified against module profiles; SHACL shapes published alongside ontology releases.

## 18. Knowledge Graph Mapping
Node `ReasoningRule`; edges `FORMALISES`→catalogue logic, `RUNS_ON`→80 types, `DERIVES`/`VALIDATES` outputs, `PACKAGED_WITH`→79. Inferred triples carry `DERIVED_BY` provenance edges (A4).

## 19. Digital Twin Mapping
Twin-state shapes (32 fidelity prerequisites as conformance) and what-if labelling rules (simulated-state marking) execute on twin subgraphs under the same governance.

## 20. Governance
Owner: semantic authority; domain owners co-sign rules formalising their logic (A2); SAFETY changes board-gated (A5); quarterly coverage review (V2 matrix) with the 79 release cycle.

## 21. Versioning
Rules semver; expression hash changes ⇒ MINOR+ with fixture re-run; SAFETY ⇒ MAJOR review. Catalogue 1.0.0.

## 22. Example Records

### EIG-RR-0052 — RD-grade evidence shape (RR-SHAPE, SAFETY-adjacent)
- source 52 A1/V3/V4; SHACL on RDRecord: minCount evidence ≥ spec minEvidence; each EVIDENCED_BY→ResponseEvent with execution-trail-complete; ASSESSED_UNDER fitness=rd per window; **sh:not path through any SYNTHETIC-tainted node** (the no-fabrication clause as a shape)
- outputs: conformance gate on record activation (52 lifecycle); violations → 63 EF-CST-class events; fixtures: the worked EIG-RD-00184421 (pass) + synthetic-tainted negative case (violation) ✓; engines SHACL primary

### EIG-RR-0042 — syntheticLineage taint closure (RR-DERIVE, **SAFETY**)
- source 42 A2/V8; Datalog: tainted(X) ← dataClass(X, Synthetic); tainted(Y) ← derivedFrom(Y, X), tainted(X); output: inferred syntheticLineage=TRUE marked with provenance; policy INF-MAT (80 A5); dual-implemented (Datalog + SPARQL CONSTRUCT, equivalence ✓); board-gated; negative fixtures incl. the laundering-attempt case (caught)

### EIG-RR-0060 — RD citation-gate derivation (RR-DERIVE)
- source 52 A4; derives gateStatus(record, t) from {status=ACTIVE, validUntil>t, confidence≥product floor, context match (69), conditions satisfied}; INF-QT; consumers: 80 EIG-KG-Q-0052 audit, 60 A3 checks, 75 W-FLX badges — one derivation, every surface agrees

## 23. Completion Criteria
☑ All seven spec fields (ID, rule expression, inputs, outputs, applicability, validation, engine compatibility) ☑ SHACL + SWRL/Datalog + SPARQL classes with cross-engine equivalence discipline ☑ Prose-first traceability (every rule anchors a catalogue logic) ☑ Inferred-assertion marking with retraction ☑ SAFETY tier formalising the taint/firewall/quarantine/life-safety chains ☑ Closed-world/open-world honesty ☑ Worked RD-grade shape, taint closure, and gate derivation guarding the framework's core claims.
