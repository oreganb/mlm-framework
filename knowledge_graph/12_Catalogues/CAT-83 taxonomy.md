---
type: catalogue
ontology: EIG-MLM
catalogue_no: 83
catalogue_id: EIG-CAT-083
entity: taxonomy
band: semantics
status: active
tags: [eig-mlm, catalogue, band:semantics, entity:taxonomy]
aliases: ["EIG-CAT-083", "Catalogue 83", "taxonomy"]
---

# CAT-83 — Taxonomy

> **EIG-CAT-083** · band: **Semantics & Ontology** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-01 building_classification]] · [[CAT-02 building]] · [[CAT-03 occupancy]] · [[CAT-04 space]] · [[CAT-10 asset_classification]] · [[CAT-44 model]] · [[CAT-82 vocabulary]]
**Used by (downstream):** [[CAT-65 benchmark]] · [[CAT-73 application]] · [[CAT-75 dashboard]] · [[CAT-79 ontology]] · [[CAT-80 knowledge_graph]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-083`
**Entity prefix:** `EIG-TAX-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** Semantic authority with domain classification owners
**Depends on (upstream):** 01 building_classification, 10 asset_classification, 44 §3.2 (series), 82 vocabulary (term stock); every catalogue's hierarchical §3/§4 structures
**Used by (downstream):** 79 (hierarchy axioms/SKOS), 80 (CLASSIFIED_AS traversals), 65 (cohort scoping), 75 (drill hierarchies), search/navigation across applications (73)

---

## 1. Definition

The **Taxonomy Catalogue** registers the framework's hierarchical classification trees (`EIG-TAX-*`): each with parent/child terms, hierarchy rules, version, and scope. The framework's major taxonomies already live where they govern — building classification (01), asset classification (10), the model series register (44 §3.2), constraint classes (59), event families (63) — this catalogue **federates** them (indexed, single-source, 65 §A5 pattern) and **owns** the navigational/secondary hierarchies that have no governing catalogue of their own (topic trees for search, document/report classification, skill/competence trees). Purpose: **classification** as infrastructure: one registry of "what trees exist, what their nodes are, what rules shape them", so drill-downs (75), cohort scoping (65), and SKOS/ontology hierarchies (79) never improvise structure (A1).

## 2. Scope
**In scope:** taxonomy registry schema (the spec's six: ID, parent terms, child terms, hierarchy, version, scope); federation of governing-catalogue hierarchies (passthrough discipline); owned secondary taxonomies; hierarchy rule classes (strict mono-hierarchy vs poly-hierarchy with primary parent; level semantics; leaf rules); node schema; cross-taxonomy bridges (e.g. building class ↔ archetype ↔ template chains); change management for trees (node moves, splits, merges with instance impact). **Out of scope:** the semantics of governed nodes (owning catalogues), term lexicalisation (82), facet *values* (controlled enums are 82 TK-ENUM; taxonomies are trees), ontology axiomatisation mechanics (79 — receives the structures).

## 3. Classification Structure

### 3.1 Taxonomy registry (federated + owned)

| Taxonomy | System of record | Class |
|---|---|---|
| Building classification (sectors → subsectors → archetypal nodes) | **01** (indexed) | TX-GOV |
| Asset classification (function → class → type) | **10** (indexed) | TX-GOV |
| Model layer/series/sub-model (MLM-L* → SER-* → definitions) | **44 §3.2** (indexed) | TX-GOV |
| Constraint classes, event families, severity ladders | **59/63** (indexed) | TX-GOV |
| Geographic containment (country → county → zone → site) | **08** (indexed) | TX-GOV |
| Vocabulary term-kind / context-type / widget-type trees | 82/69/75 (indexed) | TX-GOV |
| Topic/navigation tree (search, knowledge base) | **here** | TX-NAV |
| Document/report classification (77 types → subjects) | **here** | TX-NAV |
| Competence/skill tree (team capability mapping; NEXUS-line interfaces) | **here** | TX-NAV |

TX-GOV rows are passthrough indexes (single-source, drift-detected V3); TX-NAV trees are owned and governed here.

### 3.2 Hierarchy rule classes (`hierarchyRule`)

| Code | Rule | Semantics |
|---|---|---|
| HR-MONO | Strict mono-hierarchy | one parent per node (01/10 pattern); subsumption-clean for 79 ⊑ axioms |
| HR-POLY | Poly-hierarchy with primary parent | multiple parents allowed, one primary (navigation trees); 79 export uses skos:broader, not ⊑ |
| HR-LEVELED | Fixed level semantics | named levels with meanings (sector/subsector; layer/series) — depth is semantic, V5 |
| HR-FREE | Free depth | navigation convenience trees |

### 3.3 Node change classes (instance-impact-aware)
NC-ADD (leaf addition: MINOR) · NC-MOVE (reparent: MAJOR where instances classified — 01 §15-style reclassification consequences cascade) · NC-SPLIT/MERGE (MAJOR with instance migration maps) · NC-RETIRE (leaf only after instance migration; never orphan classified records, V7).

## 4. Hierarchy
```
Taxonomy (EIG-TAX-*: rules + scope)
  └── Nodes (parent/child term refs → 82 entries or governing-catalogue node IDs)
        └── bridges (cross-taxonomy typed links)
              → exports: SKOS schemes (79), drill configs (75), cohort selectors (65)
```

## 5. Field Groups & Fields

### 5.1 Taxonomy (`EIG-TAX-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | taxonomyID | string | `EIG-TAX-\d{3}` | M | Spec: Taxonomy ID |
| 2 | name / class | string/enum | §3.1 TX-GOV/TX-NAV | M | Identity + federation status |
| 3 | systemOfRecord | ref | owning catalogue (TX-GOV) or self | M | Single-source pointer |
| 4 | scope | json | what the tree classifies (record types, content, capabilities) | M | Spec: Scope |
| 5 | hierarchyRule | enum | §3.2 + level register where HR-LEVELED | M | Spec: Hierarchy |
| 6 | nodes | array<json> | [{nodeID, termRef →82 / governing ID, parents (primary flagged where HR-POLY), level, status}] | M | Spec: Parent terms / Child terms — the tree |
| 7 | rootNodes | array<ref> | tree entry points | M | Navigation anchors |
| 8 | bridges | array<json> | typed cross-taxonomy links (e.g. 01 node ↔ 07 archetype ↔ 33 template) with semantics | C | Cross-tree chains |
| 9 | instanceBinding | json | how records classify (which field carries the node ref; e.g. 02 buildings → 01 nodes) | M | Classification contract |
| 10 | skosExport | IRI | scheme IRI (79 binding; broader/narrower or ⊑ per rule) | M | Machine form |
| 11 | version / status | semver/enum | spec: Version | M | Governance |

## 6. Controlled Vocabularies
Taxonomy classes (§3.1), hierarchy rules (§3.2), change classes (§3.3), bridge types {realises, templates, aggregates, corresponds}. Node statuses {ACTIVE, DEPRECATED-with-target, RETIRED}.

## 7. Applicability Rules
- **A1 — Federate, don't duplicate.** TX-GOV trees are indexed passthroughs (65 A5 pattern); structure changes happen in the owning catalogue and propagate; a fork here is a drift defect (V3 detector).
- **A2 — Rules shape exports.** HR-MONO trees may export as ⊑ subsumption (79); HR-POLY exports as skos:broader only — taxonomy looseness never corrupts ontology rigour (79 A7 spirit).
- **A3 — Instances classify against ACTIVE leaves (or declared-classifiable interior nodes per tree policy).** Classification contracts (§5.1.9) are explicit; unclassified records of bound types are coverage findings (01 governance pattern generalised).
- **A4 — Moves cascade honestly.** NC-MOVE/SPLIT/MERGE carry instance migration maps; downstream consequences (benchmark cohorts 65, RD context cells via 01 CX-CLASS — the 52 A6 no-transfer rule!) flag for re-evaluation — tree surgery is governed surgery.
- **A5 — Bridges are typed claims.** Cross-tree links declare semantics (a 01 node *realised-by* 07 archetypes *templated-by* 33) and are validated like any correspondence — navigation chains carry meaning, not vibes.
- **A6 — Levels mean things.** HR-LEVELED trees enforce level semantics (a "series" node sits at the series level); depth violations rejected (V5).

## 8. Validation Rules
V1 trees acyclic; single primary parent where HR-POLY; HR-MONO strict; V2 node terms resolve (82 entries / governing IDs); V3 TX-GOV passthrough integrity vs systems of record (drift detector per their releases); V4 instance bindings live: bound record types carry resolvable node refs; coverage reports per tree; V5 level semantics enforced (HR-LEVELED); V6 bridges resolve both ends with declared types; V7 RETIRE only after instance migration (orphan detector); NC-MOVE maps complete; V8 SKOS exports round-trip; rule-appropriate predicates (A2); V9 RD-relevant reclassifications (01 moves) trigger 52 A6 revalidation flags (the cascade hook, A4); V10 trees immutable per version; change classes drive semver.

## 9. Relationships to Other Catalogues
01/10/44/59/63/08 (federated systems of record) · 82 (node term stock; labels) · 79 (SKOS schemes / subsumption per rules) · 80 (CLASSIFIED_AS edges; drill traversals) · 65 (cohort scoping by node) · 75 (drill-down hierarchies are tree configs) · 52 (CX-CLASS cells; reclassification cascade A4/V9) · 07/33 (bridge chains) · 73 (navigation trees in apps) · 90/NEXUS-line (competence tree consumers).

## 10. Benchmarking Requirements
Tree health: classification coverage per bound type, passthrough-drift catches, orphan incidents (target 0), bridge resolution rates, navigation-tree usage analytics (TX-NAV pruning input).

## 11. Dataset Requirements
Tree exports (SKOS + flat closure tables) ship as 43 DK-REF packages; migration maps retained with versions.

## 12. Feature Requirements
Node-membership features (41: classification one-hots, ancestor flags) generate from closure tables — model applicability filtering (01/44 A3) and cohort features share one structure.

## 13. Model Requirements
The 44 layer/series tree is the model register's backbone (indexed); applicability matrices key on 01 nodes — tree changes flag model applicability reviews (A4 cascade).

## 14. KPI Requirements
KPI threshold overrides key on 01 nodes (57 §3.2); cohort KPI views drill along trees (75) — thresholds and drills agree on structure by construction.

## 15. Response Derivative Requirements
CX-CLASS (52 §3.2) binds to 01 tree nodes; the no-transfer rule across classification boundaries is tree-aware: node moves/splits trigger RD revalidation flags (V9) — taxonomy surgery cannot silently invalidate or falsely preserve RD evidence.

## 16. Decision Requirements
Decision applicability/routing keyed on tree nodes (60 scopes, 69 routing) consumes closure tables — "applies to residential" resolves identically everywhere.

## 17. Ontology Mapping
Trees ↔ skos:ConceptScheme (HR-POLY) or class hierarchies (HR-MONO, ⊑) per A2; nodes lexicalised via 82; governed by 79.

## 18. Knowledge Graph Mapping
Nodes `Taxonomy`, `TaxonomyNode`; edges `BROADER`/`SUBCLASS` per rule, `CLASSIFIES`→instances, `BRIDGES` {type}. Closure tables materialised (INF-MAT class, 80 §3.3) for traversal performance.

## 19. Digital Twin Mapping
Twin navigation (portfolio → site → building → zone → asset) rides the 08/01/10 federated chains; twin template applicability (33) keys on the same nodes.

## 20. Governance
Owner: semantic authority; TX-GOV changes via owning catalogues; TX-NAV via this catalogue's review; instance-impacting changes (NC-MOVE+) require migration sign-off with affected-register owners (01-pattern reclassification governance generalised).

## 21. Versioning
Trees semver per §3.3 change classes. Catalogue 1.0.0.

## 22. Example Records

### EIG-TAX-001 — Building classification tree (TX-GOV, indexes 01)
- system of record 01; HR-MONO + HR-LEVELED {sector, subsector, node}; instance binding: 02 buildings → node refs; bridges: nodes *realised-by* 07 archetypes *templated-by* 33 EIG-DTT-*
- exports: ⊑ hierarchy (79 eig-bld); closure table feeds 65 cohorts + 57 overrides + 52 CX-CLASS; V9 cascade armed

### EIG-TAX-007 — MLM layer/series tree (TX-GOV, indexes 44 §3.2)
- levels {layer, series, definition}; the 139-census navigable; mlm_v6 visualiser drill structure; drift detector bound to 44 releases

### EIG-TAX-101 — Knowledge/topic navigation tree (TX-NAV, owned)
- HR-POLY (topics cross-cut); scope: documentation, reports (77), use cases (78) classification for search (73 apps); primary parents for breadcrumbing; usage-pruned annually

## 23. Completion Criteria
☑ All six spec fields (ID, parent terms, child terms, hierarchy, version, scope) ☑ Federation of governing hierarchies with drift detection (no duplication) ☑ Hierarchy rule classes gating ontology export rigour ☑ Instance-impact-aware change classes with migration maps ☑ The RD reclassification cascade hook (taxonomy surgery cannot corrupt RD evidence) ☑ Typed bridges (class→archetype→template chains) ☑ Worked building, MLM, and navigation trees.
