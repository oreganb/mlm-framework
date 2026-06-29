---
type: catalogue
ontology: EIG-MLM
catalogue_no: 91
catalogue_id: EIG-CAT-091
entity: policy
band: governance
status: active
tags: [eig-mlm, catalogue, band:governance, entity:policy]
aliases: ["EIG-CAT-091", "Catalogue 91", "policy"]
---

# CAT-91 — Policy

> **EIG-CAT-091** · band: **Aggregation & Governance** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-58 objective]] · [[CAT-86 community]] · [[CAT-87 district]] · [[CAT-92 regulation]]
**Used by (downstream):** [[CAT-57 kpi]] · [[CAT-65 benchmark]] · [[CAT-77 report]] · [[CAT-89 scenario]] · [[CAT-90 project]] · [[CAT-93 compliance]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-091`
**Entity prefix:** `EIG-POL-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (policy liaison)
**Depends on (upstream):** 58 objective, 86 community, 87 district, 92 regulation
**Used by (downstream):** 57/65 (policy-target KPIs/benchmarks SC-REG), 89 (policy what-ifs), 90 (policy-aligned projects), 77 (policy reporting), 93 (where policies harden into obligations)

---

## 1. Definition

The **Policy Catalogue** registers public policies (`EIG-POL-*`): governmental and programmatic instruments — strategies, plans, schemes, targets — each defined by jurisdiction, scope, requirements, **incentives**, related regulations (92), and affected entities. Policies are the softer governance layer: they set directions and targets and fund behaviours (grants, schemes, obligations-to-come), where regulations (92) bind legally. Purpose: **policy alignment** — the framework's subjects, objectives, and projects align to registered policy facts: a district's PED target traces to the policy that set it (87→here), a retrofit objective cites the scheme that incentivises it, a scenario tests a policy variant (89 SF-POL). Sense discipline (82-managed homonym): *public policy* here; *learned policy* is 71 — always qualified.

## 2. Scope
**In scope:** policy schema (the spec's seven: ID, jurisdiction, scope, requirements, incentives, related regulations, affected entities); policy families (climate/energy strategies, renovation, communities/RECs, flexibility/markets, data/digital); target registers (quantified policy targets with horizons); incentive registers (grants/schemes with eligibility hooks); policy↔regulation linkage (which instruments implement which policies); alignment mappings (framework entities/objectives → policy lines); change tracking (policy revisions ripple to objectives/targets). **Out of scope:** legal obligations (92), compliance tracking (93), internal organisational policies (20-governance class), policy advocacy content.

## 3. Classification Structure

### 3.1 Policy families (`family`)
PF-CLIM (climate action plans — CAP-class) · PF-REN (renovation/retrofit strategies and schemes) · PF-COM (energy communities/REC enablement) · PF-FLX (flexibility/demand-side/market reform) · PF-PED (district/urban energy — PED programme lines) · PF-DATA (energy data/digitalisation policy).

### 3.2 Jurisdiction levels
JL-EU · JL-NAT (IE) · JL-REG (regional) · JL-LOC (county/city — Cork CC-class) · JL-PRG (programme-level: funder strategic policies). Hierarchical: lower levels implement/transpose higher (linkage typed).

### 3.3 Instrument strength
IS-TARGET (quantified target, non-binding per se) · IS-SCHEME (funded incentive with rules) · IS-PLAN (strategy/roadmap) · IS-OBLIG-PENDING (announced future obligation — watchlist class feeding 92 when enacted).

## 4. Hierarchy
```
Family (PF-*) → Policy (EIG-POL-*: jurisdiction + strength)
                    ├── targets (quantified, horizon-bound) → 57/65 SC-REG anchors
                    ├── incentives (schemes) → eligibility hooks (subjects/projects)
                    ├── implements/implemented-by → 92 regulations
                    └── alignment mappings ← framework objectives (58), districts (87), communities (86), projects (90)
```

## 5. Field Groups & Fields

### 5.1 Policy (`EIG-POL-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | policyID | string | `EIG-POL-\d{4}` | M | Spec: Policy ID |
| 2 | name / family / strength | string/enums | §3.1/§3.3 | M | Identity |
| 3 | jurisdiction | json | level §3.2 + authority (→15 org-typed: DECC, SEAI, Cork CC…) | M | Spec: Jurisdiction |
| 4 | scope | json | sectors (01 nodes), entity classes, spatial reach (08) | M | Spec: Scope |
| 5 | requirements | array<json> | policy asks/targets: [{statement, quantified target {metric →57-mappable, value, horizon}, strength}] | M | Spec: Requirements — targets registered, measurable where quantified |
| 6 | incentives | array<json> | [{scheme, mechanism: grant/tariff support/tax, eligibility expression (subjects/works), value structure, window, sourceRef}] | M | Spec: Incentives |
| 7 | relatedRegulations | array<json> | [{regRef →92, relation: implements / implemented-by / amends-context}] | M | Spec: Related regulations |
| 8 | affectedEntities | json | resolved framework reach: classifications (01), communities (86), districts (87), projects (90), org types (15) | M | Spec: Affected entities |
| 9 | sourceDocuments | array<json> | official citations (title, version/year, URI) — policies cite their texts | M | Provenance |
| 10 | alignmentMappings | array<json> | framework objectives (58) / district targets (87) / project objectives (90) ↔ policy lines (the alignment register, A2) | M | The point of the catalogue |
| 11 | watchFlags | json | revision watch, IS-OBLIG-PENDING → 92 handoff triggers | M | Change vigilance |
| 12 | owner / version / status | — | standard | M | Governance |

## 6. Controlled Vocabularies
Families (§3.1), jurisdiction levels (§3.2), strengths (§3.3), incentive mechanisms, relation types (§5.1.7). Policy sense note (vs 71) restated per house rule.

## 7. Applicability Rules
- **A1 — Sourced, not summarised from memory.** Policy entries cite official documents with versions; paraphrased requirements link their source lines — the register is checkable against the texts.
- **A2 — Alignment is explicit.** Framework objectives/targets claiming policy alignment record the mapping (§5.1.10); "aligned with the Climate Action Plan" in a proposal resolves to mapped lines (proposal-writing draws from here — checkable claims, the house craft's evidence base).
- **A3 — Targets become anchors, labelled.** Quantified policy targets may anchor 57 thresholds / 65 SC-REG rows — always labelled policy-sourced (basis declared, 65 A6), distinct from empirical cohort references.
- **A4 — Incentive eligibility is computable where possible.** Scheme eligibility expressions evaluate against registers (01 classes, works types, 86 membership) so "which subjects qualify for scheme S" is a query — advisory outputs (60 DC-PLAN retrofit ranking) can price incentives correctly (05 §16 economics hooks).
- **A5 — Pending obligations are watched.** IS-OBLIG-PENDING entries carry handoff triggers: enactment converts the watch into a 92 regulation record (lineage kept) — the framework sees obligations coming.
- **A6 — Revisions ripple.** Policy version changes flag dependent alignments (58 sets citing targets, 87 district targets, 89 SF-POL scenarios, 90 objectives) for review — alignment claims don't silently go stale.

## 8. Validation Rules
V1 schema completeness (seven spec fields); sources cited with versions; V2 quantified targets metric-mapped (57-formula-compatible) or explicitly narrative-flagged; V3 regulation links resolve (92) with typed relations; V4 eligibility expressions parse against registers where computable; non-computable flagged; V5 alignment mappings bidirectional (58/87/90 records carry the back-reference); V6 watch/handoff machinery tested (A5); V7 revision ripple flags fire (A6 monitors); V8 entries immutable per version.

## 9. Relationships to Other Catalogues
92 (the legal layer; implements/implemented-by; pending handoffs) · 58 (objective alignment — policy-aligned sets cite lines) · 87/86 (PED/community targets and enablement policies) · 90 (project policy alignment — the proposal-craft evidence base) · 57/65 (target anchors, SC-REG, labelled) · 89 (SF-POL what-ifs test variants) · 05/60 (incentive-aware retrofit economics and ranking) · 77 (policy reporting; RT-CMU/PUB policy context) · 15 (authorities).

## 10. Benchmarking Requirements
Alignment coverage: % framework targets with mapped policy lines; incentive-capture metrics (eligible vs captured value at portfolio grain); watchlist conversion latency.

## 11. Dataset Requirements
Policy registers exported with proposal/report packages (43) — alignment claims ship their evidence.

## 12. Feature Requirements
Eligibility flags as registered features (41) for ranking models (A4 → 60 consumption).

## 13. Model Requirements
Policy-variant scenarios (89) parameterise from this register; incentive economics in SER-12 investment models cite scheme rows @versions.

## 14. KPI Requirements
Policy-target attainment KPIs (57, policy-anchored thresholds per A3) render with source labels.

## 15. Response Derivative Requirements
Flexibility-policy lines (PF-FLX: demand-side schemes, market reform) map to the framework's flexibility objectives — the RD layer's value narrative aligns to registered policy direction (proposal craft: the contribution meets the policy ask, checkably).

## 16. Decision Requirements
DC-PLAN decisions price incentives (A4) and respect policy-target lenses where governance adopts them (58 sets citing policy lines) — policy informs through registered channels, never as ambient assumption.

## 17. Ontology Mapping
Policy ↔ eig:PublicPolicy (ELI/EU vocab alignment candidates per 79 backlog; distinct from eig:LearnedPolicy — the homonym ontologically separated); owned by 79.

## 18. Knowledge Graph Mapping
Node `PublicPolicy`; edges `SET_BY`→15, `TARGETS` {quantified}, `INCENTIVISES` {eligibility}, `IMPLEMENTED_BY`→92, `ALIGNED_WITH`→58/87/90. "Which policy lines does this proposal's alignment section rest on?" is one query.

## 19. Digital Twin Mapping
District twins render policy-target overlays (trajectory vs target, labelled policy-sourced); scenario runs on twins test variants (89).

## 20. Governance
Owner: PI; family watchers assigned (revision vigilance); alignment-mapping changes reviewed with objective/project owners; annual register refresh against official publications.

## 21. Versioning
Policies semver tracking official revisions. Catalogue 1.0.0.

## 22. Example Record

### EIG-POL-0003 — National retrofit & heat policy line (PF-REN, JL-NAT, illustrative register shape)
- jurisdiction: IE national (authority org-typed); sources: official plan documents @versions cited
- requirements: quantified retrofit and HP-deployment targets {metrics 57-mappable, horizons}; strength IS-TARGET + IS-SCHEME components
- incentives: grant scheme rows {eligibility: dwelling classes (01) × works types, value structure, window} — computable eligibility (A4) feeding 05 §16/60 ranking
- related regulations: implements ↔ building-standards instruments (92); affected: residential classifications, 90 project objectives (ODEON-line alignment mappings recorded)
- watch: scheme-revision flag armed; alignment: 58 retrofit objective sets + EIG-SCN-0007 AS-POL assumption cite specific lines (A2/89 chain)

## 23. Completion Criteria
☑ All seven spec fields (ID, jurisdiction, scope, requirements, incentives, related regulations, affected entities) ☑ Sense separated from 71 (vocabulary + ontology) ☑ Sourced entries with explicit, bidirectional alignment mappings (the checkable-claims base for proposals) ☑ Computable incentive eligibility + labelled target anchors ☑ Pending-obligation watch with 92 handoff ☑ Revision ripple protection.
