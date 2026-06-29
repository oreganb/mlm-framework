---
type: catalogue
ontology: EIG-MLM
catalogue_no: 80
catalogue_id: EIG-CAT-080
entity: knowledge_graph
band: semantics
status: active
tags: [eig-mlm, catalogue, band:semantics, entity:knowledge_graph]
aliases: ["EIG-CAT-080", "Catalogue 80", "knowledge_graph"]
---

# CAT-80 — Knowledge Graph

> **EIG-CAT-080** · band: **Semantics & Ontology** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-18 stakeholder]] · [[CAT-39 metadata]] · [[CAT-79 ontology]]
**Used by (downstream):** [[CAT-01 building_classification]] · [[CAT-75 dashboard]] · [[CAT-78 use_case]] · [[CAT-84 reasoning_rule]] · [[CAT-85 semantic_pattern]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-080`
**Entity prefixes:** `EIG-KG-` (graph schema elements: node/edge types), `EIG-KG-Q-` (registered queries)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Data Management Lead with semantic authority (79)
**Depends on (upstream):** 39 metadata (lineage edges), 79 ontology (schema source); every catalogue's §18 (node/edge declarations)
**Used by (downstream):** 75 dashboard (KG-view widgets), 84 reasoning_rule (inference over the graph), 85 semantic_pattern, all impact/audit queries cited across 01–78

---

## 1. Definition

The **Knowledge Graph Catalogue** defines the framework's operational graph: the typed node and relationship registers derived from the ontology (79) and populated from the catalogue registries — plus **inference rules** (with 84), **provenance** handling, **registered queries** (`EIG-KG-Q-*`), and **validation**. It is where the §18 declarations across all 78 catalogues land as one queryable structure: dependency graphs (44 A4), lineage DAGs (39), audit chains (60 A1), impact questions ("which models break if EIG-VAR-0240 changes?"), and the MLM visualiser lineage (mlm_v6 renders from this graph). Purpose: **semantic reasoning** over the whole framework — the registers are the truth; the graph is the truth made traversable (A1).

## 2. Scope
**In scope:** node-type and relationship-type registers (the spec's six: node types, relationship types, inference rules, provenance, graph queries, validation); population pipelines (registry → graph, with freshness contracts); provenance-on-edges discipline; inference materialisation policy (with 84); the registered-query catalogue (every §18 query promise becomes a maintained, tested query); access control projection (16 scopes on graph reads); graph validation (shape conformance, referential closure vs registries). **Out of scope:** ontology axioms (79), rule definitions (84 — execution policy here), storage engine choice (31), vocabulary lexical layer (82).

## 3. Classification Structure

### 3.1 Node-type register (derived from 79 modules; population source per type)

| Domain | Node types (≈) | Source registries |
|---|---|---|
| Building/spatial | Building, Zone, FabricElement, Archetype, GeoArea, WeatherStation | 01–09 |
| Assets/networks | AssetClass, Asset, Device, NetworkZone, ConnectionPoint, EdgeNode | 10–13, 35–36 |
| Actors | Actor, Organisation, Role, User, Stakeholder, **Agent** | 14–18, 72 |
| Market | Market, Tariff, Product, Contract, Trade, TradeWindow, SettlementLine, CarbonRecord | 19–28 |
| Flexibility/RD | FlexService, FlexResource/Portfolio, **RDSpecification, RDRecord**, BehaviourObservation/Statistics, Sensitivity, ResponseProfile, EventCampaign, ResponseEvent | 29–30, 52–56 |
| Platform/twin | Platform, DigitalTwin, TwinTemplate, Channel/API, Deployment | 31–34 |
| Data | Provider, Stream, Variable, DataEntity, MetadataRecord, QualityRegime/Assessment, Feature/FeatureSet, SyntheticGenerator/Scenario/Dataset, Dataset | 37–43 |
| Model/learning | ModelDefinition/Release, Algorithm, ForecastDefinition/Run, ValidationMethod/Record, Surface/Campaign/FrozenSet, XAI Config/Record, Pipeline/Run, Suite/Baseline/Result, RLProblem/Environment/EpisodeSet, Reward/PenaltyComponent, Context, SelectionSlot/Decision, **LearnedPolicy** | 44–51, 66–71 |
| Decision/ops | KPIDefinition/Value, Objective/Set, Constraint/Set, DecisionDefinition/Instance, ActionDefinition/Instance, ControlStrategy/Deployment, EventType/Instance, Workflow/Instance, Benchmark/ValueSet | 57–65 |
| Application | Application, Service, Dashboard, AlertDefinition/Channel, Report, UseCase | 73–78 |

Every node carries: registry ID (the join key — graph nodes mirror, never own, identity), version where versioned, environment tag, lifecycle status.

### 3.2 Relationship-type register (the §18 edge vocabulary, consolidated)
Canonical families (each edge typed, directed, with declared properties): structural (`PART_OF`, `LOCATED_IN`, `IN_SERIES`, `MEMBER_OF`), provenance/lineage (`DERIVED_FROM`, `GENERATED_BY`, `TRAINED_ON`, `SNAPSHOT`, `SUPERSEDES`), evidence (`EVIDENCED_BY`, `VALIDATED_BY`, `ACCEPTED_BY`, `ASSESSED_UNDER`), dependency (`DEPENDS_ON`, `CONSUMES`, `FEEDS`, `EMBEDS`), governance (`OWNED_BY`, `APPROVED_BY`, `LICENSED_FOR`, `BOUNDED_BY`, `GATES`), operations (`TRIGGERED`, `EXECUTED`, `STIMULATED`, `OBSERVED_AS`, `SETTLED_AS`, `EXPLAINED_BY`, `ESCALATED_TO`), situational (`IN_CONTEXT`, `IN_MODE`, `CLASSIFIED_AS`, `POSITIONS`). The full per-catalogue edge list is the union of §18s; additions register here first (V2).

### 3.3 Inference classes (execution policy; definitions in 84)
INF-MAT (materialised on write: cheap, high-fan-out — e.g. transitive PART_OF closure, syntheticLineage taint propagation 42 V8) · INF-QT (query-time: contextual — e.g. current-gate-status of RD records) · INF-BATCH (scheduled: heavy — e.g. impact closures, coverage matrices). Policy per rule declared; taint and firewall inferences are always INF-MAT (safety properties don't wait for queries, A5).

## 4. Hierarchy
```
79 modules → KG schema (node/edge types) → population pipelines (registries → graph)
                                                ├── inference (84 rules under §3.3 policy)
                                                ├── registered queries (EIG-KG-Q-*)
                                                └── projections (75 views, mlm_v6 visualiser, 16-scoped reads)
```

## 5. Field Groups & Fields

### 5.1 Node/edge type entries (`EIG-KG-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | kgTypeID | string | `EIG-KG-[NE]-[A-Z_]+` | M | Type ID (N=node, E=edge) |
| 2 | ontologyBinding | IRI | →79 class/property (every type grounded, V1) | M | Schema source |
| 3 | sourceRegistry | ref | catalogue + record pattern populating it | M | Spec: provenance of structure |
| 4 | keyFields / properties | json | registry ID join key; carried properties (typed) | M | Node/edge payload contract |
| 5 | populationPipeline | json | sync mode {event-driven (63 hooks) \| batch}, freshness SLA, transform ref | M | Registry→graph contract (A2) |
| 6 | provenanceFields | json | per-edge: source record ref, assertedAt, environment, dataClass where value-bearing | M | Spec: Provenance — edges carry receipts (A3) |
| 7 | accessProjection | expr | 16 scope rules for reads (own-subject, cohort floors at query layer 53 §18) | M | Lawful traversal |
| 8 | validationShapes | array<ref> | →84 SHACL shapes over this type | M | Spec: Validation |
| 9 | version / status | — | standard | M | Governance |

### 5.2 Registered query (`EIG-KG-Q-*`)
queryID · question (plain language, traceable to a catalogue §18 promise) · expression (SPARQL/Cypher per deployment binding, both maintained where dual) · inputs/outputs schema · consumers (75 widgets, 44 A4 notifications, audits) · performance budget · test fixtures (expected results on reference data) · version. Every §18 query promise across 01–78 resolves to a registered query (V6 — the P12 landing obligation, query side).

## 6. Controlled Vocabularies
Edge families (§3.2 — closed; additions V2-governed), inference classes (§3.3), sync modes, freshness SLA classes. Direction conventions documented per edge type (no ambiguous arrows).

## 7. Applicability Rules
- **A1 — Registries own truth; the graph mirrors.** Nodes/edges carry registry IDs as join keys; writes flow registry→graph only (pipelines), never graph-first; a graph/registry divergence is a pipeline defect (closure audits V4).
- **A2 — Freshness is contracted.** Each type declares sync mode + SLA; consumers of staleness-sensitive queries (60 audit, 44 A4 impact) see freshness metadata; breaches event (63).
- **A3 — Edges carry receipts.** Every edge asserts its source record, time, environment — the graph is auditable assertion-by-assertion (PROV-grounded via 79); unsourced edges are rejected at write.
- **A4 — Scoped traversal.** Reads project through 16 scopes; privacy floors (n≥10 cohort rules) enforce at the query layer for behaviour/occupancy reaches (53 §18); the graph never becomes a permission bypass.
- **A5 — Safety inferences materialise.** syntheticLineage taint (42), sandbox environment propagation (the firewall chain), quarantine reach (35) are INF-MAT — always current, never computed lazily.
- **A6 — Queries are governed artefacts.** Operational/audit queries register with tests and budgets; ad-hoc exploration is fine for analysts, but anything a dashboard, notification, or audit *depends on* is an EIG-KG-Q with fixtures (75 A2 symmetry at the graph).
- **A7 — Environment partitioning.** Sandbox subgraphs are partition-tagged end to end; cross-environment edges exist only as explicitly-typed firewall-documentation edges (e.g. PROMOTED_LOGIC_FROM), never data-flow edges.

## 8. Validation Rules
V1 every type grounds in a 79 IRI; schema-coverage matrix vs §18 declarations complete (node/edge side of the landing obligation); V2 edge-type additions register before use; unknown types rejected at write; V3 provenance completeness on edges (A3, write-time); V4 referential closure audits: graph↔registry sampled joins (IDs resolve both ways; counts reconcile per type SLA); V5 INF-MAT currency: taint/firewall closures verified after pipeline runs; V6 every catalogue §18 query promise → registered query with passing fixtures (coverage matrix, query side); V7 access projections tested per role class (privacy floor probes); V8 shape conformance (84 SHACL) per type per release; V9 performance budgets met on reference scale (437M-row-class lineage realism for data-layer reaches); V10 schema immutable per version; migrations scripted with closure re-audit.

## 9. Relationships to Other Catalogues
79 (schema source; CQ↔query alignment) · 84 (inference rules + shapes; execution policy here) · 39 (lineage DAG = first-class edges) · ALL 01–78 §18 (the declarations landed: node/edge/query coverage matrices V1/V6) · 75 (KG-view widgets; mlm_v6 visualiser renders the model dependency subgraph) · 63 (event-driven sync hooks; freshness breach events) · 16 (scoped reads) · 42/35 (taint/quarantine materialisation) · 44 (dependency-graph notifications run on registered impact queries).

## 10. Benchmarking Requirements
Graph health: closure-audit pass rates, freshness SLA attainment per type, query fixture pass rates, INF-MAT latency after registry writes, scale headroom vs reference volumes.

## 11. Dataset Requirements
Graph snapshots (per release) packaged (43 DK-REF) for reproducible research queries; fixture datasets versioned with queries.

## 12. Feature Requirements
Graph-derived features (centrality, dependency fan-out — used in 70 reviews, 54 screening context) register in 41 with EIG-KG-Q lineage.

## 13. Model Requirements
The 44 dependency graph and A4 notification machinery run on registered impact queries; model-board packs (77 RT-GOV) render from governed queries, not ad-hoc traversals.

## 14. KPI Requirements
Coverage/health KPIs register (57); KPI drill-downs (75 A4) traverse the graph through registered queries.

## 15. Response Derivative Requirements
The RD layer's queryability lands here: gate-status views (52 A4 as INF-QT), evidence chains (RDRecord→EVIDENCED_BY→ResponseEvent→OBSERVED_AS→BehaviourObservation), coverage matrices (52/56 cells — the campaign-design feed), and the citation audit (52 V10) as a registered query with fixtures — the RD concept's auditability is operational, not aspirational.

## 16. Decision Requirements
The 60 A1 audit chain (action→instance→explanation→evidence) is a registered path query with a bounded-hops guarantee; decision-quality retrospectives (60 §10) run on governed traversals.

## 17. Ontology Mapping
Schema derives from 79 (V1 grounding); RDF deployment is native; property-graph deployment carries IRI annotations per type (dual-binding documented).

## 18. Knowledge Graph Mapping
(Self) — the §18 target. Coverage matrices (types V1, queries V6) maintained as validation artefacts.

## 19. Digital Twin Mapping
Twin views traverse the graph for subject-centred context (asset→RD→profile→contract reaches); the mlm_v6 visualiser pattern generalises: register-driven, forward-traceable renderings.

## 20. Governance
Owners: Data Management (operations) + semantic authority (schema); type/query additions reviewed; closure-audit findings escalate as pipeline defects; quarterly health review with 79 release cycle.

## 21. Versioning
Schema semver locked to 79 module versions; queries semver with fixtures. Catalogue 1.0.0.

## 22. Example Records

### EIG-KG-Q-0044 — model impact query (the 44 A4 promise, registered)
- question: "which model definitions, feature sets, forecasts, decisions, and applications are affected if variable X changes?"; expression: parameterised traversal Variable→CONSUMES/FEEDS closure (INF-BATCH-assisted); consumers: 44 notification service, board packs; fixtures: EIG-VAR-0240 reference case (expected: FLEXENV chain, EIG-FT-0047, EIG-DEC-FLEX-DISPATCH-RES, APP-14) ✓; budget: <5 s at reference scale

### EIG-KG-Q-0052 — RD citation audit (52 V10, registered)
- question: "do all product/bid/settlement citations in window W resolve to RD records satisfying gates at citation time?"; INF-QT gate reconstruction from receipts; fixtures: the worked EIG-RD-00184421 citations ✓; consumers: 27 audit, 22 prequal review

### Node example — RDRecord type
- ontology eig-flx:ResponseDerivative; source 52 register; keys {rdID, version}; properties {direction, magnitude+CI, confidence, validUntil, status, conditions}; provenance per edge; INF-MAT taint guard: no SYNTHETIC-tainted EVIDENCED_BY may exist (write-rejected) — the no-fabrication standard enforced in the graph itself

## 23. Completion Criteria
☑ All six spec elements (node types, relationship types, inference rules policy, provenance, graph queries, validation) ☑ Node/edge registers landing every §18 from 01–78 with grounding in 79 (coverage matrices V1/V6) ☑ Registry-mirrors-truth + receipts-on-edges + scoped traversal ☑ Safety inferences materialised (taint/firewall/quarantine) ☑ Governed query register with fixtures incl. the model-impact and RD-audit exemplars ☑ Environment partitioning honouring the firewall end to end.
