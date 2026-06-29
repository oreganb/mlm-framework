---
type: catalogue
ontology: EIG-MLM
catalogue_no: 87
catalogue_id: EIG-CAT-087
entity: district
band: governance
status: active
tags: [eig-mlm, catalogue, band:governance, entity:district]
aliases: ["EIG-CAT-087", "Catalogue 87", "district"]
---

# CAT-87 — District

> **EIG-CAT-087** · band: **Aggregation & Governance** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-02 building]] · [[CAT-08 geographic]] · [[CAT-11 asset]] · [[CAT-13 utility_network]] · [[CAT-19 energy_carrier]] · [[CAT-28 carbon]] · [[CAT-57 kpi]] · [[CAT-86 community]]
**Used by (downstream):** [[CAT-32 digital_twin]] · [[CAT-73 application]] · [[CAT-89 scenario]] · [[CAT-90 project]] · [[CAT-91 policy]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-087`
**Entity prefix:** `EIG-DIS-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (PED programme lineage: TIPS4PED/TOOLKIT4PED/OPTIX)
**Depends on (upstream):** 02 building, 08 geographic, 11 asset, 13 utility_network, 19 energy_carrier, 28 carbon, 57 kpi, 86 community
**Used by (downstream):** 89 scenario (district planning), 90 project (PED pilots), 91 policy (PED targets), 73 (district views), 32 (district twins)

---

## 1. Definition

The **District Catalogue** registers districts and **Positive Energy Districts** (`EIG-DIS-*`): bounded urban/rural energy areas — each defined by buildings (02), networks (13), assets (11), an **energy balance** account, governance, and KPIs. The PED concept is first-class: a district whose annual primary-energy/carbon balance meets a declared positivity definition within declared boundaries. Purpose: **PED and district modelling** with accountancy rigour — boundary definitions, balance methods, and positivity claims are versioned register facts (the TOOLKIT4PED design layer's data backbone; FLEXUS-line execution feeds the balance), so "is this district positive?" has exactly one computable answer per method version (A2).

## 2. Scope
**In scope:** district schema (the spec's seven: ID, buildings, networks, assets, energy balance, governance, KPIs); boundary classes (geographic, virtual, functional — PED-typology aligned); balance accounting methods (carriers 19, primary-energy factors, carbon 28; annual/monthly matching granularities; import/export treatment); positivity definitions (versioned); district↔community composition (86); district governance hooks; PED lifecycle (design → implementation → operational → certified-class claims). **Out of scope:** building/asset/network internals (02/11/13), planning scenario narratives (89 — consume district records), policy targets themselves (91), the seven-case-study project content (90).

## 3. Classification Structure

### 3.1 District classes (`districtClass`)
DC-PED (positivity-targeting, definition-bound) · DC-PEN (positive-energy-neighbourhood scale variant) · DC-EPD (energy-positive-capable district under assessment) · DC-GEN (general district, accounting without positivity claim).

### 3.2 Boundary classes (`boundaryClass`)
BC-GEO (geographic polygon, 08) · BC-VIRT (virtual: enumerated buildings/CPs possibly non-contiguous — declared rationale) · BC-FUNC (functional: system-defined, e.g. shared-network footprint 13). Boundary changes are MAJOR (balance comparability breaks, V2).

### 3.3 Balance method register (versioned, the accounting truth)
Per method: carrier scope (19), metering basis (37 TR-A for claims), conversion factors (primary-energy + 28 carbon, factor-set version-pinned), matching granularity (annual net / monthly / hourly-matched variants), import/export valuation, embodied-energy inclusion flag, mobility inclusion flag. Positivity = balance ≥ 0 under method@version (claims always cite method, A2).

## 4. Hierarchy
```
District (EIG-DIS-*: boundary + class)
  ├── composition: buildings (02), networks (13), assets (11), communities (86, may span)
  ├── balance accounts (method@version × period → computed results)
  └── KPI set (57) → scenario/planning consumers (89/90)
```

## 5. Field Groups & Fields

### 5.1 District (`EIG-DIS-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | districtID | string | `EIG-DIS-\d{4}` | M | Spec: District ID |
| 2 | name / districtClass | string/enum | §3.1 | M | Identity + claim posture |
| 3 | boundary | json | {boundaryClass §3.2, geometry →08 / enumeration, rationale} @version | M | The accounting perimeter |
| 4 | buildings | array<ref> | →02 EIG-BLD-* (membership versioned) | M | Spec: Buildings |
| 5 | networks | array<ref> | →13 zones/segments within/crossing boundary (crossing flows = import/export points) | M | Spec: Networks |
| 6 | assets | array<ref> | →11 district-level assets (generation, storage, DH plant) | M | Spec: Assets |
| 7 | energyBalance | json | {methodRef §3.3 @version, meteringBasis →37, periods computed, results refs (immutable per run), positivity status per period} | M | Spec: Energy balance — computed, never asserted (A2) |
| 8 | communities | array<ref> | →86 EIG-CMU-* within/overlapping (overlap rules declared) | C | Social-organisational layer |
| 9 | governance | json | district body/authority (15 — e.g. Cork CC pattern EIG-ORG-0061), decision rights over shared assets/targets, stakeholder map (18) | M | Spec: Governance |
| 10 | kpiSet | array<ref> | →57 district-grain: balance ratio, EUI aggregate, self-sufficiency, peak (network value 13), CO₂/m² | M | Spec: KPIs |
| 11 | pedLifecycle | enum | design / implementation / operational / claim-assessed | C | DC-PED journey |
| 12 | owner / version / status | — | standard | M | Governance |

## 6. Controlled Vocabularies
District/boundary classes (§3.1/§3.2), balance-method elements (§3.3 — method register closed-vocabulary per element), lifecycle states, import/export valuation forms.

## 7. Applicability Rules
- **A1 — Boundary is the law of the balance.** All flows account against the versioned boundary; boundary changes version the district and break period comparability explicitly (V2 flags) — no quiet redrawing toward positivity.
- **A2 — Positivity is computed.** Claims cite method@version + metered inputs (37 TR-A) + factor-set versions (28/primary); SYNTHETIC/scenario results never ground operational claims (42 A3 chain) — design-stage projections are labelled projections (89).
- **A3 — One factor truth.** Carbon via 28 registered factors (single avoided-emissions method A4); primary-energy factors version-pinned in the method — district accounting never forks valuation (21 V11 spirit).
- **A4 — Composition by reference.** Buildings/assets/communities are referenced register entities; district aggregates compute from their truths (weakest-link fitness inheritance for claim-grade periods, 40).
- **A5 — Crossing flows are explicit.** Network boundary-crossing points enumerate (13 CPs); import/export metering at those points is the balance's external truth; unmetered crossings cap the achievable claim grade (declared).
- **A6 — Communities and districts compose, not collide.** Overlap rules: community allocations (86) net inside the district balance without double counting (V5 reconciliation).

## 8. Validation Rules
V1 composition refs resolve; membership versioned; V2 boundary versioning enforced; cross-version balance comparisons labelled; V3 balance results recomputable from method+inputs+factors (immutable runs, supersede-never-edit); V4 claim-grade periods: TR-A basis, fitness floors, crossing-point coverage ≥ method minimum; V5 community-district reconciliation (A6); V6 KPI set computed at district grain via 57 formulas (no district-local arithmetic); V7 DC-PED lifecycle transitions evidenced; V8 districts immutable per version.

## 9. Relationships to Other Catalogues
02/11/13/08 (composition + boundary) · 19/28 (carriers, factors) · 37/40 (metering basis, fitness) · 86 (community layer; reconciliation) · 57/65 (KPIs; district cohorts) · 89/90 (planning scenarios; PED pilot projects — TIPS4PED/TOOLKIT4PED case-study lineage) · 91 (PED policy targets typed against) · 32 (district twins aggregate by reference) · 75 (district dashboards; balance views badge-disciplined).

## 10. Benchmarking Requirements
District cohorts (65): balance ratios, self-sufficiency, EUI aggregates — cross-district comparison only under same method class (comparability rule).

## 11. Dataset Requirements
Balance run archives (43, claim-grade retention); district datasets for the PED case-study line (seven-country comparability per method declarations).

## 12. Feature Requirements
District features (41): self-sufficiency rolling, peak coincidence (13 hook), balance trajectory — for 89 planning models.

## 13. Model Requirements
District models (SER-02/03/10/12 at district grain) consume composition by reference; the TOOLKIT4PED design-layer/FLEXUS execution-layer split maps to: methods+scenarios here/89, operations via 60/62.

## 14. KPI Requirements
§5.1.10 set registered; positivity-status KPI renders with method citation always (75 badge-class: method@version visible).

## 15. Response Derivative Requirements
District flexibility value aggregates community/building portfolios (30/86) — district-scale dispatch (network-stress contexts 69) draws on the same RD evidence chains; PED operational strategies price flexibility into the balance (export timing vs CI/price, 28/21).

## 16. Decision Requirements
District-level decisions (60 DC-PLAN/DC-DISP at district grain): investment ranking toward positivity (54 sensitivities + 65 positions), coordinated dispatch under network constraints — within member-level constraint resolution (59 per subject, never overridden by aggregate ambition).

## 17. Ontology Mapping
District ↔ eig:District/eig:PED (PED-typology alignment candidates per 79 backlog); balance ↔ accounting pattern with QUDT quantities; owned by 79.

## 18. Knowledge Graph Mapping
Node `District`; edges `CONTAINS`→02/11/13, `OVERLAPS`→86 {rules}, `BALANCED_BY`→method versions {results}, `GOVERNED_BY`→15, `TARGETED_BY`→91. Balance-audit traversals registered.

## 19. Digital Twin Mapping
District twins (32 scope) compose building/network twins by reference; balance dashboards ride twin aggregation; what-if positivity runs are 89 scenarios on twin state (labelled).

## 20. Governance
Owner: PI (PED programme); per-district authority liaison; method-register changes board-reviewed (claims comparability); annual claim reviews for DC-PED.

## 21. Versioning
Districts semver (boundary/membership structural = MAJOR); balance methods independently versioned. Catalogue 1.0.0.

## 22. Example Record

### EIG-DIS-0007 — Inis Mór island district (DC-EPD under assessment)
- boundary BC-FUNC: NZ-00017 network footprint (13) — island edge is the natural perimeter; buildings: ODEON 20 + CFOAT + non-pilot stock (membership versioned); assets: community PV/storage candidates (86 shared)
- balance method M-ISL-01@1.0: carriers EL+heat fuels (19), annual net + monthly profile view, TR-A at the subsea-cable crossing CP, factors 28 EIG-CO2-EF-IE-GRID + primary set @pinned; current status: negative (importing), trajectory tracked — claims honest (A2)
- communities: EIG-CMU-0001 (reconciliation V5 ✓); governance: co-op + council stakeholder map; KPIs: self-sufficiency 22%, peak coincidence, CO₂/m²; lifecycle: assessment feeding 89 pathway scenarios + 90 ODEON/O-CEI reporting

## 23. Completion Criteria
☑ All seven spec fields (ID, buildings, networks, assets, energy balance, governance, KPIs) ☑ PED first-class with versioned boundaries, methods, and computed-never-asserted positivity ☑ Crossing-flow explicitness + factor single-truth ☑ Community composition without double counting ☑ The Inis Mór district worked honestly (negative today, trajectory governed).
