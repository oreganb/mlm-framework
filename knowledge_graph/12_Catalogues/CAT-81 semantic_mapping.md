---
type: catalogue
ontology: EIG-MLM
catalogue_no: 81
catalogue_id: EIG-CAT-081
entity: semantic_mapping
band: semantics
status: active
tags: [eig-mlm, catalogue, band:semantics, entity:semantic_mapping]
aliases: ["EIG-CAT-081", "Catalogue 81", "semantic_mapping"]
---

# CAT-81 — Semantic Mapping

> **EIG-CAT-081** · band: **Semantics & Ontology** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-79 ontology]] · [[CAT-82 vocabulary]]
**Used by (downstream):** [[CAT-34 communication]] · [[CAT-43 dataset]] · [[CAT-77 report]] · [[CAT-79 ontology]] · [[CAT-80 knowledge_graph]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-081`
**Entity prefix:** `EIG-SM-` (mapping sets), `EIG-SM-C-` (correspondences)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** Semantic authority (79 governance)
**Depends on (upstream):** 79 ontology (modules + framework register), 82 vocabulary (term aliases)
**Used by (downstream):** 79 ALIGN modules (formalised output), 80 (cross-graph federation), 34/43 (interoperable exchange), 77 (interop claims in reports), external integrations

---

## 1. Definition

The **Semantic Mapping Catalogue** registers fine-grained correspondences between EIG semantics and external ontologies: mapping **sets** (`EIG-SM-*`) per source↔target pair, containing **correspondence records** (`EIG-SM-C-*`) — class mappings, property mappings, each with relation type, confidence, documented **gaps**, and validation. It is the evidence layer beneath 79's alignment statuses: an ALIGN-FORMAL module is a mapping set graduated to axioms; an ALIGN-INTENT entry is a mapping set awaiting graduation (or honest about partiality). It also owns the **internal alias machinery** (term aliasing cited by 57 §6 and 38 A1: external/legacy names → registered terms). Purpose: **interoperability** — data and semantics cross system boundaries with stated, validated meaning, and the limits of every alignment are written down (A4).

## 2. Scope
**In scope:** mapping set and correspondence schemas (the spec's seven: source ontology, target ontology, class mappings, property mappings, confidence, gaps, validation); correspondence relation vocabulary (SKOS/aligned: exact/close/broad/narrow/related; subsumption for axiom-grade); transformation notes (unit/structure adjustments needed beyond term mapping); gap register (untranslatable concepts, lossy directions); graduation workflow (set → 79 ALIGN axioms); internal aliasing (legacy/system synonyms → register IDs); round-trip validation. **Out of scope:** module-level alignment status (79 §3.2), ontology authoring (79), lexical synonyms within EIG vocabulary (82 — aliases here map *external* names; 82 handles internal synonymy).

## 3. Classification Structure

### 3.1 Mapping set classes (`setClass`)

| Code | Class | Purpose |
|---|---|---|
| SM-EXT | External ontology alignment | EIG ↔ SAREF/CIM/USEF/Brick/etc. (the 79 §3.2 register's evidence) |
| SM-STD | Standards/data-model mapping | exchange schemas (34 message schemas ↔ external profiles, e.g. OpenADR payloads; DCAT-AP export shapes 39) |
| SM-ALS | Internal alias sets | external system names → EIG registered terms (38 A1 aliases, 57 §6: e.g. SCADA tags, legacy PrisM fields, partner CSV headers → EIG-VAR-*) |
| SM-X | Cross-version | EIG term migrations across MAJOR catalogue/ontology versions (deprecation bridges) |

### 3.2 Correspondence relations (`relation`)

| Code | Relation | Semantics | Axiom-gradable |
|---|---|---|---|
| R-EQ | exactMatch | interchangeable in this scope | ⊑/≡ candidates |
| R-CLOSE | closeMatch | substitutable with noted caveats | with guard notes |
| R-BROAD / R-NARROW | broader/narrower | subsumption direction stated | ⊑ one way |
| R-REL | related | meaningful link, not substitutable | annotation only |
| R-NONE | documented non-mapping | the concept does NOT map (gap register entry) | — (the honest row) |

### 3.3 Confidence semantics
[0,1] per correspondence: evidence-based (definitional analysis, instance-level testing, authority documentation), with class floors for use: ≥0.9 for axiom graduation; ≥0.8 for automated data transformation; below ⇒ human-review-gated use only (A3).

## 4. Hierarchy
```
Mapping set (EIG-SM-*: source ⟷ target @versions)
  └── Correspondences (EIG-SM-C-*: term-level, typed, confident, validated)
        ├── gaps (R-NONE register + lossy-direction notes)
        └── graduation → 79 ALIGN module axioms (SM-EXT) / transformation configs (SM-STD/ALS)
```

## 5. Field Groups & Fields

### 5.1 Mapping set (`EIG-SM-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | mappingSetID | string | `EIG-SM-\d{4}` | M | Set ID |
| 2 | setClass | enum | §3.1 | M | Class |
| 3 | sourceOntology | json | EIG module IRI @version (79) or system schema ref | M | Spec: Source ontology — version-pinned both ends |
| 4 | targetOntology | json | external IRI/spec @version (or EIG for SM-ALS direction) | M | Spec: Target ontology |
| 5 | scopeNote | string | what portion is covered; what is out of scope | M | Honesty header |
| 6 | correspondences | array<ref> | →EIG-SM-C-* | M | The content |
| 7 | gapRegister | array<json> | [{concept, direction, why unmappable/lossy, impact}] | M | Spec: Gaps — mandatory even when empty-claimed (V5) |
| 8 | transformationNotes | json | unit conversions (QUDT paths), structural reshaping, value-vocabulary maps needed for data flow | C | Beyond-term mapping |
| 9 | validation | json | method (definitional review / instance round-trip / authority cite), results, reviewer, date | M | Spec: Validation |
| 10 | graduationStatus | enum | DRAFT → VALIDATED → GRADUATED (79 axioms / live transforms) → SUPERSEDED | M | Lifecycle to 79 |
| 11 | owner / version / status | — | standard | M | Governance |

### 5.2 Correspondence (`EIG-SM-C-*`)
correspondenceID · sourceTerm (IRI/field) · targetTerm · termKind {class, objectProperty, datatypeProperty, individual/value, field} · relation §3.2 · confidence §3.3 + evidence note · caveats (R-CLOSE guards) · transformation (per-term where needed: unit, datatype, value map) · validationResult (per-term where instance-tested) · status. Spec coverage: class mappings and property mappings are correspondence subsets by termKind (V2 ensures both populated where the set claims coverage).

## 6. Controlled Vocabularies
Set classes (§3.1), relations (§3.2 — SKOS-aligned, frozen), confidence floors (§3.3), graduation states, termKinds. Gap impact classes {blocking, lossy, cosmetic}.

## 7. Applicability Rules
- **A1 — Version-pinned both ends.** Sets bind exact versions of both ontologies/schemas; either side's version change ⇒ set review (drift is the silent killer of interop claims).
- **A2 — Evidence per correspondence.** Every row carries its relation, confidence, and evidence basis; bulk "everything matches" sets are rejected — mapping is term-by-term work, recorded as such.
- **A3 — Confidence gates use.** Floors per §3.3: axiom graduation ≥0.9 (79 V-chain), automated transformation ≥0.8, else human-gated; transformations cite their enabling correspondences (auditable interop, V7).
- **A4 — Gaps are deliverables.** R-NONE entries and lossy-direction notes are first-class content — the panel-ready story for selective alignment (79 A2) rests on documented gaps, not silence; an interop claim in a report (77) cites the set including its gaps.
- **A5 — Aliases never relabel.** SM-ALS sets map names, not semantics: an external tag aliases to an EIG-VAR with unit/semantics verification (38 A1/A2); aliasing cannot upgrade dataClass, trust, or fitness (37 A2 chain — no laundering through renaming).
- **A6 — Round-trip where data flows.** SM-STD sets supporting bidirectional exchange validate round-trips on reference instances (export→import preserves declared semantics within stated loss).
- **A7 — Graduation is the goal.** VALIDATED SM-EXT sets feed the 79 ALIGN-INTENT backlog burn-down; sets stuck pre-graduation surface in the quarterly review (79 §20) with reasons.

## 8. Validation Rules
V1 both-end version pins resolve; review flags on either-side change; V2 class+property coverage consistent with scopeNote claims; V3 relations/confidence per row; evidence notes present; V4 R-CLOSE rows carry caveats; R-BROAD/NARROW direction explicit; V5 gap register present (explicit empty-with-attestation allowed, lazily-empty rejected); V6 round-trip tests for bidirectional SM-STD (fixtures + results); V7 live transformations trace to enabling correspondences ≥ floor; V8 SM-ALS unit/semantics verification recorded per alias (38 A2 dimensional check); V9 graduation artefacts: GRADUATED sets reference their 79 axiom module / transform config @versions; V10 sets immutable per version; supersession on re-review.

## 9. Relationships to Other Catalogues
79 (alignment evidence → ALIGN modules; framework register statuses) · 82 (internal synonymy distinct; SM-ALS lands external names onto 82-registered terms) · 38/57 (alias machinery resolved: variable/KPI external names) · 34 (message-schema interop: FLEX-DISPATCH ↔ external DR profiles) · 39/43 (DCAT-AP export mappings; dataset interop) · 80 (federated-query mappings where external graphs are reached) · 77 (interop claims cite sets + gaps) · 13/20 (CIM/USEF candidate sets per 79 backlog).

## 10. Benchmarking Requirements
Interop health: graduation throughput (79 backlog burn-down), round-trip pass rates, alias-resolution error rates at ingestion (37), gap-impact distribution per set.

## 11. Dataset Requirements
Reference instance fixtures (43 DK-REF) per validated set; exchange test corpora retained with results.

## 12. Feature Requirements
n/a beyond alias resolution feeding clean variable binding (41 inputs arrive register-named).

## 13. Model Requirements
External-data-consuming models rely on SM-ALS-resolved inputs — mapping errors surface as 40 quality findings with set-level traceback.

## 14. KPI Requirements
57 §6 alias rule operationalised: KPI name aliases live as SM-ALS rows; external reporting name requirements (regulator templates) map without forking formulas.

## 15. Response Derivative Requirements
RD interop (sharing RD semantics with external flexibility platforms/USEF-style frameworks) flows through SM-EXT sets where eig:ResponseDerivative's *partial* correspondences to external flexibility concepts are documented with gaps — the novel class's distinctiveness is itself evidenced by the R-NONE rows (what existing ontologies *cannot* express is the contribution's footprint).

## 16. Decision Requirements
Decision-relevant external signals (DSO constraint feeds, market messages) enter through validated SM-STD transformations — interop never bypasses the registered-input rules (60/62 A3).

## 17. Ontology Mapping
Correspondences serialise as SKOS mapping properties / OWL axioms per relation gradability; sets ↔ alignment artefacts (Alignment-format export supported); governed by 79.

## 18. Knowledge Graph Mapping
Nodes `MappingSet`, `Correspondence`; edges `MAPS` {relation, confidence}→external terms, `GRADUATED_TO`→79 modules, `ENABLES`→transformations. Gap queries ("what can't we say in CIM?") are first-class.

## 19. Digital Twin Mapping
Twin interop (external BIM/IFC-adjacent exchanges via SAREF4BLDG paths) cites its sets; fidelity-relevant semantic loss appears in gap registers.

## 20. Governance
Owner: semantic authority; set reviews with domain owners (both-end expertise); graduation sign-off per 79 release board; alias additions reviewed by data management (38 owners).

## 21. Versioning
Sets/correspondences semver, both-end-pinned. Catalogue 1.0.0.

## 22. Example Records

### EIG-SM-0003 — eig-ast ↔ SAREF (GRADUATED, evidence behind EIG-ONT-ALIGN-SAREF)
- pins: eig-ast@1.0 ↔ SAREF v3.x family; correspondences: Device R-EQ saref:Device (0.95), Sensor/Actuator R-EQ (0.95), eig-dec:Action R-CLOSE saref:Command (0.85, caveat: EIG verification ladder richer — guard note), measurement pattern R-CLOSE via SOSA bridge
- gaps: METIS-128 lifecycle states R-NONE (no SAREF security lifecycle — impact: cosmetic, modelled EIG-side); validation: definitional + instance tests on EIG-DEV-TYP-0012 fixtures ✓

### EIG-SM-0011 — Aran SCADA/legacy tags → EIG variables (SM-ALS)
- aliases: `HP01_PWR_W` → EIG-VAR-0240 (unit verified W, TS-INST ✓), `MTR_KWH_TOT` → EIG-VAR-0102 cumulative register (TS-CUM semantics enforced — alias verification caught a would-be averaging misuse, 38 A4)
- A5 ✓: aliasing carried no class/trust upgrade; ingestion (37) resolves via this set

### EIG-SM-0017 — eig-flx ↔ USEF flexibility concepts (VALIDATED, pre-graduation)
- partial: FlexResource R-CLOSE, trade/settlement roles R-BROAD; **eig:ResponseDerivative R-NONE** (USEF has no conditional-responsiveness construct — the gap that marks the contribution); graduation queued per 79 backlog; cited in interop sections of 77 RT-PRJ deliverables with gaps stated

## 23. Completion Criteria
☑ All seven spec fields (source ontology, target ontology, class mappings, property mappings, confidence, gaps, validation) ☑ Relation vocabulary with axiom-gradability + confidence-gated use ☑ Gaps as first-class deliverables (incl. the RD R-NONE story) ☑ Internal alias machinery resolving 38/57 citations without laundering ☑ Round-trip validation for data exchange ☑ Graduation pipeline feeding 79's backlog ☑ Worked SAREF, SCADA-alias, and USEF sets.
