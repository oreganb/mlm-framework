---
type: catalogue
ontology: EIG-MLM
catalogue_no: 28
catalogue_id: EIG-CAT-028
entity: carbon
band: markets
status: active
tags: [eig-mlm, catalogue, band:markets, entity:carbon]
aliases: ["EIG-CAT-028", "Catalogue 28", "carbon"]
---

# CAT-28 — Carbon

> **EIG-CAT-028** · band: **Markets, Tariffs & Settlement** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID prefix:** `EIG-CO2` (sub-families: `EIG-CO2-EF-*` emission factors · `EIG-CO2-CI-*` intensity series · `EIG-CO2-AV-*` avoided-emissions records · `EIG-CO2-CR-*` credits · `EIG-CO2-BG-*` budgets)
**Catalogue number:** 28 of 94
**Layer:** Market & Commercial
**Version:** 1.0.0
**Status:** ACTIVE

---

## 1. Definition

The Carbon Catalogue defines **carbon accounting structures**: the emission factors, carbon-intensity series, avoided-emissions computations, credits, budgets, scope classifications and reporting rules through which every energy quantity in the framework acquires a defensible CO₂e value. It is the single authority for the `EIG-CO2-EF-*` factor records already referenced by the energy carrier catalogue (19 defaultEmissionFactorRef) and the only place where avoided-emissions methodology is defined — preventing the classic failure mode of every dashboard computing "tonnes saved" differently.

The catalogue follows the same provenance discipline as quantities and prices: every CO₂e figure must be reproducible from (quantity, factor record + version, method record + version), and every figure carries a data class (REAL, CALIBRATED, SYNTHETIC, COMPUTED) consistent with the platform-wide provenance standard.

## 2. Scope

**In scope:** emission factor registry (static and dynamic, all carriers per 19); grid carbon-intensity time series; location-based vs market-based accounting; avoided/saved emissions methods (counterfactual-based, linked to BL-* baselines in 27 and RDs in 52); marginal vs average emission factors for optimisation; carbon credits and guarantees of origin (GoO/REGO-equivalent) as records affecting market-based accounting; carbon budgets per organisation/building/project; GHG Protocol scope classification; reporting rules (CSRD/ESRS E1, SECR-equivalent, SEAI public-sector M&R, ISO 14064-1).

**Out of scope:** monetary settlement of credits (27 lines reference EIG-CO2-CR-* records); embodied carbon of materials (Material Passport scope — only an interface hook here); non-GHG environmental indicators.

## 3. Classification Structure

Record families:

| Family | Prefix | Description |
|---|---|---|
| EmissionFactor | EIG-CO2-EF | Factor converting an energy/fuel quantity to CO₂e |
| CarbonIntensitySeries | EIG-CO2-CI | Time-resolved intensity (gCO₂e/kWh) per carrier/zone |
| AvoidedEmissionsMethod | EIG-CO2-AVM | Approved counterfactual computation method |
| AvoidedEmissionsRecord | EIG-CO2-AV | A computed avoided-emissions result |
| CarbonCredit | EIG-CO2-CR | Credit/certificate instrument (GoO, voluntary credit) |
| CarbonBudget | EIG-CO2-BG | Cap with trajectory for an accounting entity |
| ReportingRule | EIG-CO2-RR | Mapping from records to a reporting framework |

Factor axes: `factorClass` (grid_average, grid_marginal, grid_dynamic, fuel_static, district_network, supplier_residual_mix, zero_rated); `accountingBasis` (location_based, market_based); `gwpBasis` (AR5_100, AR6_100); `scopeAtPointOfUse` (scope1, scope2, scope3_cat3).

## 4. Hierarchy

```
ReportingFramework (CSRD/ESRS, ISO 14064-1, SEAI M&R, GHG Protocol)
└── ReportingRule (EIG-CO2-RR-*)
Carrier (19, EIG-ECR-*)
└── EmissionFactor (EIG-CO2-EF-*)        ← versioned vintages, never overwritten
    └── CarbonIntensitySeries (EIG-CO2-CI-*)   ← dynamic factors as time series
AccountingEntity (org 15 / building 02 / project 91 / community 86)
└── CarbonBudget (EIG-CO2-BG-*)
    └── AvoidedEmissionsRecord (EIG-CO2-AV-*) via AvoidedEmissionsMethod (EIG-CO2-AVM-*)
CarbonCredit (EIG-CO2-CR-*) → retires against market-based accounting of an entity-period
```

## 5. Field Groups & Fields

### 5.1 EmissionFactor (EIG-CO2-EF-*)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | factorID | string | — | `EIG-CO2-EF-[A-Z0-9-]+` | M | Factor identifier (e.g. EIG-CO2-EF-IE-GRID) |
| 2 | carrierRef | string | — | EIG-ECR-* (19) | M | Carrier the factor applies to |
| 3 | factorClass | string | — | §3 vocab | M | Average/marginal/dynamic/static… |
| 4 | accountingBasis | string | — | location_based, market_based | M | GHG Protocol Scope 2 dual reporting |
| 5 | value | decimal | gCO₂e/kWh (energy) or kgCO₂e/unit (fuel) | >0; 0 only for zero_rated | M | Factor value; energy basis MUST state hhv/ncv consistent with 19 basis declaration |
| 6 | energyBasis | string | — | ncv, hhv, delivered_electricity | M | Basis alignment with 19 (A7 single energy basis) |
| 7 | gwpBasis | string | — | AR5_100, AR6_100 | M | GWP horizon/assessment report |
| 8 | gasBreakdown | object | — | {co2, ch4_co2e, n2o_co2e} | O | Constituent gases where source provides them |
| 9 | vintageYear | integer | — | ≥2015 | M | Data year the factor represents |
| 10 | validFrom / validTo | date | — | ISO 8601; non-overlapping per (carrier, class, basis, zone) | M | Application window |
| 11 | zoneRef | string | — | EIG-GEO-* (08) or EIG-NET-NZ-* (13); default IE national | C | Spatial scope (island zones may carry distinct factors) |
| 12 | sourceRef | string | — | SEAI, EPA Ireland, EirGrid, supplier disclosure, DEFRA — citation + URL/document | M | Authoritative source (25-source provenance discipline) |
| 13 | dataClass | string | — | REAL, CALIBRATED, COMPUTED | M | Provenance class; SYNTHETIC factors forbidden in production accounting |
| 14 | uncertaintyPct | decimal | % | ≥0 | O | Source-stated uncertainty |

### 5.2 CarbonIntensitySeries (EIG-CO2-CI-*)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | seriesID | string | — | `EIG-CO2-CI-[A-Z0-9-]+` | M | Series identifier |
| 2 | factorRef | string | — | EIG-CO2-EF-* with factorClass=grid_dynamic/grid_marginal | M | Parent factor record |
| 3 | resolutionMin | integer | min | 5, 15, 30, 60 | M | Time resolution |
| 4 | seriesKind | string | — | measured, forecast, backcast | M | Forecast series feed carbon-aware optimisation (60) |
| 5 | datasetRef | string | — | EIG-DSP-* (43) | M | Storage location of the series |
| 6 | methodNote | string | — | e.g. EirGrid fuel-mix method | M | Computation provenance |

### 5.3 AvoidedEmissionsMethod (EIG-CO2-AVM-*) & Record (EIG-CO2-AV-*)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | methodID | string | — | `EIG-CO2-AVM-[0-9]{2}` | M | Method identifier |
| 2 | counterfactualBasis | string | — | baseline_bl_ref (27 BL-*), rd_counterfactual (52), archetype_reference (07), pre_retrofit_metered | M | Where the counterfactual quantity comes from |
| 3 | factorSelection | string | — | average_at_time, marginal_at_time, static_annual | M | Which factor multiplies the avoided quantity; marginal_at_time REQUIRED for dispatch/flexibility claims |
| 4 | doubleCountingRule | string | — | statement of exclusivity vs credits/GoOs | M | Prevents claiming both avoided emissions and selling the attribute |
| 5 | approvedByRef | string | — | org (15) or internal governance board | M | Approval provenance |
| — | — | — | — | — | — | **Record fields:** |
| 6 | avoidedID | string | — | `EIG-CO2-AV-[0-9]{8}` | M | Result identifier |
| 7 | methodRef | string | — | EIG-CO2-AVM-* + version | M | Method used |
| 8 | entityRef | string | — | building/asset/org/project/community | M | Claiming entity |
| 9 | period | object | — | {start, end} | M | Claim period |
| 10 | counterfactualQty / actualQty / avoidedQty | decimal | kWh | avoided = counterfactual − actual (signed) | M | Quantity triple |
| 11 | factorApplied | object | — | {factorRef, version, value series ref} | M | Reproducibility |
| 12 | avoidedCO2e | decimal | kgCO₂e | computed | M | Result |
| 13 | dataClass | string | — | COMPUTED (always) | M | Avoided emissions are by nature computed; inputs' classes recorded |

### 5.4 CarbonCredit (EIG-CO2-CR-*)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | creditID | string | — | `EIG-CO2-CR-[0-9]{8}` | M | Instrument identifier |
| 2 | instrumentKind | string | — | goo (guarantee of origin), voluntary_credit, white_certificate | M | Instrument class |
| 3 | registryRef | string | — | external registry + serial (externalRef) | M | Issuing registry; single source of retirement truth |
| 4 | quantity | decimal | MWh (goo) or tCO₂e (credit) | >0 | M | Instrument size |
| 5 | vintage | integer | — | year | M | Production/abatement year |
| 6 | status | string | — | issued, held, transferred, retired, cancelled | M | Lifecycle; retired is terminal and binds to one entity-period |
| 7 | retiredForRef | string | — | accounting entity + period | C | Mandatory when status=retired |
| 8 | settlementLineRefs | array | — | EIG-SET-* (27) | O | Monetary settlement of the instrument |

### 5.5 CarbonBudget (EIG-CO2-BG-*)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | budgetID | string | — | `EIG-CO2-BG-[0-9]{5}` | M | Budget identifier |
| 2 | entityRef | string | — | org (15), building (02), project (91), community (86), district (87) | M | Accounting entity |
| 3 | scopeCoverage | array | — | scope1, scope2_location, scope2_market, scope3_cat3 | M | What counts against the budget |
| 4 | periodStart / periodEnd | date | — | ISO 8601 | M | Budget horizon |
| 5 | capCO2e | decimal | tCO₂e | >0 | M | Cap |
| 6 | trajectory | array | — | [{period, allowanceCO2e}] | M | Interim allowances (e.g. public-sector 51% by 2030 pathway) |
| 7 | basisRef | string | — | policy/regulation (92/93) or voluntary commitment | M | Why this budget exists |
| 8 | breachAction | string | — | workflow ref (64) | M | What happens on projected breach |

### 5.6 ReportingRule (EIG-CO2-RR-*)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | ruleID | string | — | `EIG-CO2-RR-[0-9]{3}` | M | Rule identifier |
| 2 | framework | string | — | CSRD_ESRS_E1, ISO_14064_1, GHG_PROTOCOL, SEAI_MR, SECR_EQ, INTERNAL | M | Target framework |
| 3 | scopeMapping | object | — | framework category ← {carriers, factor classes, basis} | M | Deterministic mapping of records to disclosure lines |
| 4 | dualReporting | boolean | — | true/false | M | Scope 2 dual (location + market) required |
| 5 | factorPolicy | string | — | which vintages/classes are mandated by the framework | M | E.g. SEAI M&R mandates SEAI annual factors |
| 6 | reportingPeriodicity | string | — | annual, quarterly, monthly | M | Cadence; feeds 77 report generation |

## 6. Controlled Vocabularies

- `factorClass`: grid_average, grid_marginal, grid_dynamic, fuel_static, district_network, supplier_residual_mix, zero_rated.
- `accountingBasis`: location_based, market_based (GHG Protocol Scope 2 guidance; both maintained in parallel where dualReporting=true).
- `scopeAtPointOfUse`: scope1 (on-site combustion: GA, BM, FU per 19), scope2 (imported EL, HT, CL, ST), scope3_cat3 (upstream fuel/energy, WTT).
- `gwpBasis`: AR5_100, AR6_100 (framework-dependent; never mixed within one report).
- Credit lifecycle: `issued → held → transferred* → retired | cancelled` (retired/cancelled terminal; one retirement per instrument).
- `dataClass`: REAL, CALIBRATED, SYNTHETIC, COMPUTED — platform provenance standard; SYNTHETIC permitted only in sandbox/scenario accounting, never in disclosure (V9).

## 7. Applicability Rules

| # | Rule |
|---|---|
| A1 | Every carrier in 19 MUST have ≥1 active EIG-CO2-EF record per accountingBasis it is reportable under; 19's defaultEmissionFactorRef resolves here. |
| A2 | Factor vintages are immutable: a new year's factor is a new version with its own validity window; recomputation of past periods uses the factor valid for that period unless a ReportingRule mandates restatement (logged). |
| A3 | Optimisation (60) minimising carbon MUST use marginal or dynamic factors (factorSelection=marginal_at_time/average_at_time series); annual static factors are forbidden in dispatch decisions — they cannot see time-of-use carbon differences. |
| A4 | Avoided-emissions claims require an approved EIG-CO2-AVM method; ad-hoc "savings" computations are forbidden in any dashboard/report (single avoided-emissions truth, mirroring 21 V11 pricing discipline). |
| A5 | Double counting prohibited: energy whose attributes are sold (GoO transferred) reverts to residual-mix factors in the seller's market-based accounting; avoided-emissions claims and credit sales over the same quantity are mutually exclusive (doubleCountingRule enforced). |
| A6 | Scope classification follows point-of-use carrier semantics from 19 (scopeAtPointOfUse); a building's heat-pump electricity is scope2, its gas boiler scope1 — classification is data-driven, never per-report judgement. |
| A7 | Carbon budgets bind decision layers: projected breach (forecast intensity × planned consumption > trajectory allowance) triggers breachAction workflow; budgets are constraints (59) in optimisation, not advisory. |
| A8 | All reported figures carry full reproducibility tuples (quantity ref, factor ref+version, method ref+version) and dataClass; figures missing provenance are blocked from reports (77). |

## 8. Validation Rules

| # | Rule | Severity |
|---|---|---|
| V1 | IDs unique per family patterns | error |
| V2 | Factor validity windows non-overlapping per (carrier, factorClass, accountingBasis, zone) | error |
| V3 | energyBasis consistent with carrier basis declaration (19 A7) | error |
| V4 | gwpBasis uniform within any single report/budget computation | error |
| V5 | Avoided records: avoidedQty = counterfactualQty − actualQty; avoidedCO2e reproducible from factorApplied | error |
| V6 | Counterfactual basis resolves: BL-* exists in 27, RD refs exist in 52, archetype refs in 07 | error |
| V7 | Credit retirement: status=retired ⇒ retiredForRef present; one retirement per registry serial (registry reconciliation) | error |
| V8 | Budget trajectory allowances sum ≤ capCO2e; periods tile the horizon | error |
| V9 | dataClass=SYNTHETIC never appears in disclosure-bound records (reports under any RR except INTERNAL sandbox) | error |
| V10 | Dual-reporting rules: where dualReporting=true, both location and market figures present for scope2 | error |
| V11 | Marginal/dynamic factor series cover the full period of any dispatch-decision audit (no gaps at decision timestamps) | error |
| V12 | SEAI M&R reporting uses SEAI-mandated factor vintages (factorPolicy enforcement) | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 19 energy_carrier | Factor↔carrier binding; basis alignment; emissionFactorClass hooks fulfilled here |
| 27 settlement | Credit monetary lines; BL-* baselines reused as counterfactual bases |
| 52/56 RD | RD counterfactuals admissible in avoided-emissions methods |
| 07 archetype | Archetype reference counterfactuals for retrofit claims |
| 09 weather_climate | Weather normalisation in counterfactuals |
| 57/58/59 KPI/objective/constraint | Carbon KPIs, carbon-minimising objectives (A3 factor discipline), budget constraints |
| 60 decision_intelligence | Carbon-aware dispatch consumes CI forecast series |
| 77 report | Reporting rules drive disclosure generation |
| 92/93/94 policy/regulation/compliance | Budget bases; CSRD/SEAI obligations |
| 02/15/86/87/91 | Accounting entities |
| 43 dataset | CI series storage; input snapshots |

## 10. Benchmarking Requirements

Carbon benchmarks per entity cohort: kgCO₂e/m²/yr (buildings, banded A–G consistent with 06 conventions), gCO₂e/kWh delivered (networks/communities), avoided-emissions per € invested (projects). Min cohort n=10; immutable percentile releases; location-based figures used for cross-entity comparison (market-based reflects purchasing, not physics).

## 11. Dataset Requirements

CI series stored as datasets (43) at declared resolution with seriesKind separation (measured vs forecast never mixed in one dataset); factor registry exported as versioned reference dataset; disclosure computations freeze input snapshots; retention ≥ 10 years (CSRD audit horizons); all timestamps timezone-explicit.

## 12. Feature Requirements

Features: instantaneous/forecast grid CI (gCO₂e/kWh) at 30-min resolution, carbon cost of next-kWh (marginal), cumulative budget consumption %, carbon-optimal window flags for load shifting (joins 21 ToU bands for joint cost-carbon optimisation). Forecast CI features pin the forecast series version used.

## 13. Model Requirements

CI forecast models (46) validated against measured series with horizon-specific metrics; avoided-emissions regression counterfactuals pin model versions per method version (mirrors 27 BL-* discipline); no model may output disclosure figures directly — models feed quantities into the deterministic factor/method pipeline (A8).

## 14. KPI Requirements

KPIs (57): absolute scope1+2 tCO₂e (dual-reported), carbon intensity per m²/per kWh/per occupant, budget attainment %, avoided tCO₂e by measure class (retrofit codes 05, flexibility 52), % consumption in lowest-carbon-quartile periods (shifting effectiveness). Every carbon KPI declares accountingBasis and factor policy in its definition — no basis-ambiguous KPIs.

## 15. Response Derivative Requirements

RDs enable defensible flexibility carbon claims: avoided emissions from a dispatch event = RD-derived counterfactual consumption × marginal CI at event time minus actual × same (method EIG-CO2-AVM with counterfactualBasis=rd_counterfactual, factorSelection=marginal_at_time). RD confidence/recency gates (22 §15) apply to carbon claims exactly as to delivery settlement — a stale RD cannot back a carbon claim either.

## 16. Decision Requirements

Decision intelligence consumes: CI forecasts for carbon-aware scheduling (24), budget headroom as hard constraints (A7), marginal factors for dispatch trade-offs (cost vs carbon Pareto, 58 multi-objective). Credit purchase/retirement decisions require T3+ authority (16) and double-counting check (A5). Reporting restatements require governance approval with logged justification (A2).

## 17. Ontology Mapping

Classes: `eig:EmissionFactor`, `eig:CarbonIntensitySeries`, `eig:AvoidedEmissionsMethod`, `eig:AvoidedEmissionsRecord`, `eig:CarbonCredit`, `eig:CarbonBudget`, `eig:ReportingRule` — all ⊑ `eig:CarbonAccountingEntity`. Object properties: `eig:appliesToCarrier` (→ eig:EnergyCarrier), `eig:usesCounterfactual` (→ eig:BaselineMethodology ∪ eig:ResponseDerivative), `eig:retiredFor` (→ eig:AccountingEntity), `eig:constrains` (budget → entity), `eig:mandatedBy` (→ eig:Regulation). Datatype: value, gwpBasis, accountingBasis, vintageYear, capCO2e, dataClass. Alignments at design-intent level: GHG Protocol conceptual model, ESRS E1 datapoint structure, QUDT units for gCO₂e/kWh.

## 18. Knowledge Graph Mapping

Nodes: `(:EmissionFactor)`, `(:CISeries)`, `(:AvoidedRecord)`, `(:CarbonCredit)`, `(:CarbonBudget)`, `(:ReportingRule)`. Edges: `(:EmissionFactor)-[:FOR_CARRIER]->(:Carrier)`, `(:AvoidedRecord)-[:USES_METHOD]->(:AvoidedMethod)-[:COUNTERFACTUAL_FROM]->(:BaselineMethod|:ResponseDerivative)`, `(:CarbonCredit)-[:RETIRED_FOR]->(:Organisation|:Building)`, `(:CarbonBudget)-[:CONSTRAINS]->(:Entity)`, `(:Entity)-[:EMITTED {tCO2e, period, basis}]->(:Carrier)`. Carbon-flow queries traverse EMITTED edges with basis filters; audit queries reconstruct reproducibility tuples.

## 19. Digital Twin Mapping

Twins compute live carbon state alongside energy state (same factor pipeline, COMPUTED class); NEST sandbox scenarios may use SYNTHETIC factors for what-if grids (e.g. 2030 intensity pathways) clearly partitioned from disclosure data (V9); retrofit twins (07/05) run pre/post counterfactuals via approved AVM methods so simulated savings use the same maths as reported savings.

## 20. Governance

Owner: Sustainability & Reporting Lead. Steward: data engineering (factor registry, CI pipelines). Reviewer: external-audit liaison (disclosure-bound rules). Approver: T3+ (factor vintage adoption, AVM method approval, restatements T4). Duties per 16 A6. Factor source changes require sourceRef evidence; SEAI/EPA/EirGrid annual updates ingested on publication with vintage versioning (A2).

## 21. Versioning

Factors: vintage-versioned, immutable, non-overlapping windows (V2). Methods (AVM) and ReportingRules: semver; results pin versions. Credits/budgets: stateful records with immutable event history. Catalogue semver: MAJOR for scope-model or family changes; MINOR new factor classes/frameworks; PATCH editorial. ChangeLog mandatory.

## 22. Example Records

```yaml
factorID: EIG-CO2-EF-IE-GRID
carrierRef: EIG-ECR-EL | factorClass: grid_average | accountingBasis: location_based
value: 254.3 gCO2e/kWh | energyBasis: delivered_electricity | gwpBasis: AR6_100
vintageYear: 2025 | validFrom: 2026-01-01 | validTo: 2026-12-31
zoneRef: IE national | sourceRef: "SEAI Conversion Factors 2026 (publication + URL)"
dataClass: REAL | uncertaintyPct: 3.0
```

```yaml
seriesID: EIG-CO2-CI-IE-GRID-30M
factorRef: EIG-CO2-EF-IE-GRID-DYN (grid_dynamic) | resolutionMin: 30
seriesKind: measured | datasetRef: EIG-DSP-CI-IE-2026 | methodNote: EirGrid fuel-mix method
```

```yaml
avoidedID: EIG-CO2-AV-00038112
methodRef: EIG-CO2-AVM-03 v1.1 (rd_counterfactual, marginal_at_time, double-counting: exclusive vs GoO)
entityRef: EIG-BLD-000877 (IE_B0014, Inis Mór) | period: {2026-06-12T17:00, 2026-06-12T18:00}
counterfactualQty: 84.6 kWh (RD profile, 52) | actualQty: 41.2 kWh | avoidedQty: 43.4 kWh
factorApplied: {EIG-CO2-EF-IE-GRID-MARG v2026, series EIG-CO2-CI-IE-GRID-MARG-30M, mean 412 gCO2e/kWh}
avoidedCO2e: 17.9 kgCO2e | dataClass: COMPUTED
```

```yaml
budgetID: EIG-CO2-BG-00007
entityRef: EIG-ORG-0061 (Cork City Council estate, ESCO scope)
scopeCoverage: [scope1, scope2_location] | periodStart: 2026-01-01 | periodEnd: 2030-12-31
capCO2e: 14200 tCO2e | trajectory: [{2026, 3400}, {2027, 3100}, {2028, 2800}, {2029, 2550}, {2030, 2350}]
basisRef: Public Sector Climate Action Mandate (51% by 2030) → EIG-POL-* (92)
breachAction: EIG-WF-CARBON-ESC-01 (64)
```

## 23. Completion Criteria

Complete when: every carrier in 19 resolves active factors for both accounting bases (A1); SEAI/EPA/EirGrid current vintages loaded with sources; CI measured + forecast series live at 30-min resolution; ≥1 approved AVM method per claim class (retrofit, flexibility, behavioural); zero ad-hoc savings computations remain in dashboards (A4 sweep); budgets registered for all mandate-bound entities; CSRD/SEAI reporting rules executable end-to-end with reproducibility tuples on every figure; Phase 14 confirms 19 factor hooks, 27 credit settlement links and 57 carbon KPI definitions all resolve into this catalogue.
