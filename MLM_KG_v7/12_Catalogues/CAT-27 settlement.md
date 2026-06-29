---
type: catalogue
ontology: EIG-MLM
catalogue_no: 27
catalogue_id: EIG-CAT-027
entity: settlement
band: markets
status: active
tags: [eig-mlm, catalogue, band:markets, entity:settlement]
aliases: ["EIG-CAT-027", "Catalogue 27", "settlement"]
---

# CAT-27 — Settlement

> **EIG-CAT-027** · band: **Markets, Tariffs & Settlement** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID prefix:** `EIG-SET` (settlement lines) · `BL-` (baseline methodology registry, owned here)
**Catalogue number:** 27 of 94
**Layer:** Market & Commercial
**Version:** 1.0.0
**Status:** ACTIVE

---

## 1. Definition

The Settlement Catalogue defines **financial settlement and reconciliation**: the structures that convert trades (25), contracts (23), tariffs (21) and metered reality (38) into money owed and money paid, with a complete and auditable trail. It owns three record families: the **SettlementRun** (an execution of settlement logic over a period for a market or billing context), the **SettlementLine** (the atomic money statement: who pays whom how much for what, per settlement period), and the **Baseline Methodology Registry (`BL-*`)** — the authoritative home of counterfactual computation methods referenced by markets (20 V11), trades (25), and ESCO M&V plans (23).

Settlement is the layer where the single-pricing-truth rule (21 V11) is cashed out: the price computed at settlement MUST be produced by the same pricing library hash used in optimisation, applied to metered quantities.

## 2. Scope

**In scope:** market settlement (energy, flexibility, ancillary); supplier billing settlement; community/P2P allocation settlement; ESCO guarantee settlement; penalty and adjustment lines; reconciliation states; baseline methodologies; metered-vs-contracted-vs-delivered quantity semantics; settlement calendars; dispute-driven corrections; audit evidence chains.

**Out of scope:** trade formation (25), price-plan definitions (21), payment-rail execution (banking systems — settlement ends at the approved payment instruction with externalRef), carbon credit settlement quantities (28 defines factors; monetary settlement of credits is a SettlementLine here).

## 3. Classification Structure

`settlementClass` (on runs and lines):

| Code | Class | Description |
|---|---|---|
| SET-MKT | market_settlement | Wholesale/balancing/capacity market settlement per operator rules |
| SET-FLX | flexibility_settlement | LFM/DSO flexibility delivery settlement (baseline-based) |
| SET-SUP | supply_billing | Retail supply billing under tariff plans (21) |
| SET-CMU | community_allocation | Community energy sharing/allocation settlement |
| SET-P2P | p2p_settlement | Peer-to-peer trade settlement |
| SET-ESC | esco_guarantee | ESCO guaranteed-savings settlement per M&V plan (23) |
| SET-ADJ | adjustment_run | Correction/true-up run referencing a prior run |
| SET-SBX | sandbox | NEST sandbox settlement (computed, never paid; 20 A2 firewall) |

Line `lineType`: energy_charge, energy_credit, flex_availability, flex_utilisation, capacity, penalty, bonus, network_charge_passthrough, levy_passthrough, adjustment, dispute_correction, community_allocation, guarantee_share, tax.

## 4. Hierarchy

```
SettlementCalendar (per market/billing context)
└── SettlementRun (EIG-SET-RUN-*)            ← executes over [periodStart, periodEnd]
    ├── QuantityStatement(s) (embedded per counterparty × period × product)
    │     metered / contracted / delivered / baseline quantities
    └── SettlementLine (EIG-SET-*)           ← atomic money statement
          └── PaymentInstruction (terminal, externalRef to payment rail)
BaselineMethodology (BL-*)                    ← registry owned by this catalogue
```

Runs progress `scheduled → computing → provisional → final → closed`, with `superseded` when an SET-ADJ run replaces results. Lines inherit run state and add reconciliation state (§6).

## 5. Field Groups & Fields

### 5.1 SettlementRun

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | runID | string | — | `EIG-SET-RUN-[0-9]{6}` | M | Run identifier |
| 2 | settlementClass | string | — | §3 codes | M | Run class |
| 3 | contextRef | string | — | EIG-MKT-* (20) or billing context (21 plan scope) or EIG-CON-* (23, ESCO) | M | What is being settled |
| 4 | periodStart / periodEnd | datetime | — | ISO 8601, Europe/Dublin | M | Settlement window covered |
| 5 | settlementPeriodMin | integer | min | 5, 15, 30, 60 | M | Granularity; from 20/26 (SEM 30→15 transition flagged) |
| 6 | runType | string | — | initial, indicative, final, adjustment | M | Operator-style run sequence (e.g. SEM M+4/M+13 resettlements map to adjustment) |
| 7 | supersedesRunID | string | — | EIG-SET-RUN-* | C | Mandatory for runType=adjustment |
| 8 | pricingLibraryHash | string | — | SHA-256 | M | Hash of pricing library used; MUST equal optimisation-side hash (21 V11) |
| 9 | inputDatasetRefs | array | — | EIG-DSP-* (43), metering datasets (38) | M | Frozen input snapshot |
| 10 | runState | string | — | scheduled, computing, provisional, final, closed, superseded | M | Lifecycle |
| 11 | operatorStatementRef | string | — | externalRef | C | Operator settlement statement reconciled against (SET-MKT) |
| 12 | sandbox | boolean | — | true iff SET-SBX | M | Sandbox runs never emit PaymentInstructions |

### 5.2 QuantityStatement (embedded, per counterparty × period × product/plan)

The four-quantity model is the semantic core:

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | counterpartyRef | string | — | EIG-ACT-*/EIG-ORG-* | M | Settled party (LEI gate per 15 for settlement parties) |
| 2 | periodTS | datetime | — | period start, grid-aligned (26) | M | Settlement period |
| 3 | productOrPlanRef | string | — | EIG-PRD-* (22) or EIG-TRF-* (21) | M | What was bought/sold |
| 4 | meteredQty | decimal | kWh/kW | ≥0, from 38 telemetry via meter point (MPRN↔CP, 13) | M | What the meter recorded |
| 5 | contractedQty | decimal | kWh/kW | ≥0, from trades (25) / contract obligations (23) | M | What was committed |
| 6 | baselineQty | decimal | kWh/kW | ≥0 | C | Counterfactual per BL-* method; mandatory for SET-FLX/SET-ESC |
| 7 | deliveredQty | decimal | kWh/kW | computed | M | SET-FLX: baseline − metered (delivery direction signed); SET-SUP: metered; SET-MKT: per operator rule |
| 8 | baselineMethodRef | string | — | BL-* | C | Method used; mandatory when baselineQty present |
| 9 | dataQualityFlag | string | — | actual, estimated, substituted, disputed | M | Metering quality (40); estimated data triggers true-up in adjustment runs |
| 10 | rdEvidenceRefs | array | — | EIG-RD-*/EIG-RE-* (52/56) | O | RD counterfactual evidence where BL-* method admits it |

### 5.3 SettlementLine

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | lineID | string | — | `EIG-SET-[0-9]{8}` | M | Line identifier |
| 2 | runID | string | — | EIG-SET-RUN-* | M | Parent run |
| 3 | lineType | string | — | §3 lineType vocab | M | Charge nature |
| 4 | payerRef / payeeRef | string | — | EIG-ACT-*/EIG-ORG-* | M | Direction of money |
| 5 | quantityStatementRef | string | — | embedded statement key | C | Quantity basis (absent for fixed fees/penalties) |
| 6 | priceApplied | decimal | EUR/kWh, EUR/kW, EUR | per 21 resolution contract | M | Unit price or fixed amount; produced by pricingLibraryHash |
| 7 | priceSourceRef | string | — | EIG-TRF-* component, trade price (25), contract clause (23) | M | Single pricing truth provenance |
| 8 | amountNet | decimal | EUR | signed, 2 dp | M | Net amount (payer→payee positive) |
| 9 | taxTreatment | object | — | {vatRatePct, vatAmount, exemptionCode} | M | Tax computation |
| 10 | amountGross | decimal | EUR | amountNet + taxes | M | Gross |
| 11 | penaltyBasisRef | string | — | EIG-CON-* penalty clause (23 A5) or market penalty regime (20) | C | Mandatory for lineType=penalty/bonus; machine-computable basis |
| 12 | adjustmentOfLineID | string | — | EIG-SET-* | C | Mandatory for adjustment/dispute_correction lines |
| 13 | reconciliationState | string | — | §6 states | M | Reconciliation lifecycle |
| 14 | paymentInstructionRef | string | — | externalRef | C | Terminal payment-rail reference; forbidden when run.sandbox=true |
| 15 | evidenceBundleRef | string | — | audit bundle (datasets, calc traces, operator statements) | M | Complete audit chain |

### 5.4 Baseline Methodology Registry (BL-*)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | baselineID | string | — | `BL-[A-Z0-9-]+` | M | Method identifier (e.g. BL-LFM-01) |
| 2 | methodClass | string | — | high_x_of_y, regression, rd_counterfactual, control_group, mv_ipmvp_a/b/c/d, nominated | M | Computation family |
| 3 | parameters | object | — | method-specific (e.g. {x: 5, y: 10, sameDayType: true, adjustment: weather}) | M | Full parameterisation — deterministic given inputs |
| 4 | inputRequirements | array | — | dataset classes (38/43), weather vars (EIG-WX-VAR-*, 09) | M | What data the method consumes |
| 5 | rdAdmissible | boolean | — | true/false | M | Whether RD-derived counterfactuals (52) are admissible evidence |
| 6 | applicableClasses | array | — | SET-FLX, SET-ESC, … | M | Where usable |
| 7 | approvedByRef | string | — | market operator / regulator org (15) | M | Approval provenance |
| 8 | version | string | — | semver | M | Method version; results record method version used |

## 6. Controlled Vocabularies

- `reconciliationState` machine: `computed → matched → provisionally_settled → paid → reconciled`, with branches `mismatch` (from matched; quantity/price diff vs counterparty or operator statement), `disputed` (from any pre-paid state; links dispute record 25), `corrected` (terminal for superseded lines; successor line linked via adjustmentOfLineID), `written_off` (terminal, requires authority tier T3+, 16).
- Mismatch tolerance: quantity diff ≤ 0.5% or ≤ 1 kWh per period auto-matches; beyond tolerance → mismatch and reconciliation workflow (64).
- `dataQualityFlag`: actual, estimated, substituted, disputed (aligned to 40 quality codes).
- `runType`: initial, indicative, final, adjustment.
- Delivery grading bridge: deliveredQty/contractedQty maps to product delivery thresholds (22, e.g. full ≥95% / partial 70–95% / fail <70%) which select utilisation payment vs penalty lines.

## 7. Applicability Rules

| # | Rule |
|---|---|
| A1 | Settlement settles only trades from the 25 ledger and obligations from active 23 contracts; no trade-like or contract-like records may originate here (25 completion criterion mirrored). |
| A2 | Single pricing truth: every line's priceApplied is produced by the run's pricingLibraryHash from priceSourceRef inputs; hash must equal the optimisation-side library hash (21 V11). Divergence is a release-blocking defect. |
| A3 | SET-SBX runs compute everything but emit no PaymentInstructions and feed no production features (20 A2 firewall). |
| A4 | Baseline-settled classes (SET-FLX, SET-ESC) require baselineMethodRef on every QuantityStatement; the method must be approved (approvedByRef) and listed by the market (20 baselineMethodology) or contract M&V plan (23 mvPlanRef). |
| A5 | Penalty lines are computable solely from penaltyBasisRef formula + quantity statements (23 A5/V7); discretionary penalties are forbidden — operator-imposed penalties enter as externalRef pass-throughs. |
| A6 | Adjustment runs never edit prior lines; they supersede whole runs (supersedesRunID) and issue delta lines (adjustmentOfLineID), preserving the immutable audit chain. |
| A7 | Metered quantities trace to meter points reconciled with building/network identity (MPRN↔CP, 13 V10); unreconciled meters block final runs for affected counterparties. |
| A8 | Settlement parties that are organisations MUST satisfy the LEI gate (15); persons settle via supplier/community intermediary contexts, never directly into SET-MKT. |

## 8. Validation Rules

| # | Rule | Severity |
|---|---|---|
| V1 | IDs unique, patterns per §5 | error |
| V2 | Every line's runID exists; run contextRef exists in 20/21/23 | error |
| V3 | Σ amounts per run balance: every euro paid equals euro received across payer/payee pairs (zero-sum within run scope excluding tax remittance lines) | error |
| V4 | deliveredQty computation matches declared formula for the settlementClass | error |
| V5 | baselineMethodRef present and approved for SET-FLX/SET-ESC statements (A4) | error |
| V6 | priceApplied reproducible: re-running pricing library on inputs yields identical value (regression-tested per release) | error |
| V7 | penalty/bonus lines carry penaltyBasisRef; recomputation matches amount | error |
| V8 | adjustment lines reference existing prior lines; superseded runs marked | error |
| V9 | paymentInstructionRef absent on sandbox runs (A3) | error |
| V10 | dataQualityFlag=estimated lines flagged for true-up; final runs report estimated-data share | warning |
| V11 | Operator statement reconciliation (SET-MKT): platform-computed vs operator amounts diff ≤ €0.01 per line or escalated to mismatch | error |
| V12 | reconciliationState transitions follow §6 machine only | error |
| V13 | settlementPeriodMin matches market/window declaration (20/26); 30→15 transition runs use the period valid for each date | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 20 market | Context for SET-MKT; baselineMethodology references BL-* here; settlement period source |
| 21 tariff | Price source for SET-SUP; single pricing truth (A2/V6) |
| 22 product | Delivery thresholds select utilisation/penalty lines |
| 23 contract | Obligations, penalty bases (A5), ESCO M&V plans, payment terms |
| 25 trade | Settled quantities/prices; settlementRefs bidirectional |
| 26 trade_window | Run keys (market × window instance × period) |
| 28 carbon | Carbon credit monetary lines reference 28 credit records; carbon intensity does not alter energy settlement amounts |
| 38/40 data/quality | Metered quantities and quality flags |
| 13 utility_network | Meter point reconciliation (A7) |
| 14/15 actor/org | Counterparties; LEI gate |
| 52/56 RD | Admissible counterfactual evidence where BL-* allows |
| 57 KPI | Settlement KPIs (accuracy, timeliness, dispute rate) |
| 64 workflow | Reconciliation and dispute workflows |

## 10. Benchmarking Requirements

Settlement-quality benchmarks per context: auto-match rate, mismatch rate, estimated-data share, mean days computing→final, dispute rate per 1,000 lines, adjustment magnitude as % of initial run. Min cohort n=10 runs; immutable releases per benchmark convention. Counterparty-level statistics published only in banded form (privacy, 14 GDPR classes).

## 11. Dataset Requirements

Runs freeze inputDatasetRefs (immutable snapshots, 43); all lines, statements and evidence bundles retained ≥ 7 years (regulatory audit, aligned 24/25); timestamps timezone-explicit; superseded results retained, never deleted; evidence bundles must allow full recomputation (inputs + library hash + method versions ⇒ identical outputs).

## 12. Feature Requirements

Features derivable: realised price per period, imbalance cost per counterparty, penalty incidence, delivery-grade history (feeds RD confidence weighting 52 and bidding risk models 72), estimated-data incidence per meter. Sandbox settlement features firewalled (A3).

## 13. Model Requirements

Models forecasting settlement outcomes (imbalance cost, penalty risk) train only on final-run data with adjustment runs applied; the pricing library itself is deterministic code, never a learned model; baseline methods of class regression/rd_counterfactual must pin model versions (44) per BL-* version so results are reproducible.

## 14. KPI Requirements

KPIs (57): settlement accuracy (|initial − final| / final ≤ 2%), time-to-final ≤ calendar target, auto-reconciliation rate ≥ 98%, dispute resolution ≤ 30 d (23 terms), penalty € by cause, community allocation fairness indices (SET-CMU). All financial KPIs dimensioned by settlementClass, market, counterparty class.

## 15. Response Derivative Requirements

Where BL-* methods set rdAdmissible=true, RD-derived counterfactuals (52) may serve as baselineQty evidence; the RD must meet the product's confidence/recency gates (22 §15) **evaluated at the delivery period**, and the response event record (56) must link the activation to telemetry. Settlement outcomes feed back: delivery grades update RD observed-performance and the declared-vs-observed gap that triggers asset re-declaration (11, >25% rule).

## 16. Decision Requirements

Settlement states gate commercial decisions: counterparties with reconciliationState=disputed beyond threshold are flagged to market participation reviews (20); penalty incidence informs bid/no-bid and envelope-tightening decisions (60); write-offs require T3+ authority with logged justification (16). True-up scheduling decisions follow estimated-data share triggers (V10).

## 17. Ontology Mapping

Classes: `eig:SettlementRun`, `eig:SettlementLine` ⊑ `eig:FinancialRecord`; `eig:BaselineMethodology` ⊑ `eig:Methodology`; `eig:QuantityStatement`. Object properties: `eig:settlesTrade` (→ eig:Trade), `eig:settlesContract` (→ eig:Contract), `eig:hasPayer`/`eig:hasPayee` (→ eig:Actor ∪ eig:Organisation), `eig:usesBaseline` (→ eig:BaselineMethodology), `eig:supersedesRun`, `eig:pricedBy` (→ eig:TariffComponent ∪ eig:Trade). Datatype: amountNet, amountGross, meteredQty, contractedQty, deliveredQty, baselineQty, reconciliationState, pricingLibraryHash. Alignments: FIBO payments/settlement concepts and USEF settlement phases at design-intent level (consistent with SHIFT ontology practice).

## 18. Knowledge Graph Mapping

Nodes: `(:SettlementRun)`, `(:SettlementLine)`, `(:BaselineMethod)`, `(:QuantityStatement)`. Edges: `(:SettlementLine)-[:IN_RUN]->(:SettlementRun)`, `(:SettlementLine)-[:PAYS {amountNet}]->(:Actor)`, `(:SettlementLine)-[:SETTLES]->(:Trade|:Contract)`, `(:QuantityStatement)-[:USES_BASELINE]->(:BaselineMethod)`, `(:SettlementRun)-[:SUPERSEDES]->(:SettlementRun)`, `(:SettlementLine)-[:ADJUSTS]->(:SettlementLine)`. Money-flow queries traverse PAYS edges; audit queries traverse evidence bundle references.

## 19. Digital Twin Mapping

Twins (32) and NEST sandbox run shadow settlement (SET-SBX) on simulated dispatch to estimate revenue/penalty exposure before committing bids; the shadow settlement uses the identical pricing library hash and BL-* methods so sandbox economics match production rules exactly — only the PaymentInstruction step is absent.

## 20. Governance

Owner: Settlement & Billing Lead. Steward: data engineering (input snapshots, library hashes). Reviewer: finance controller (run sign-off provisional→final). Approver: T3+ commercial authority; write-offs T3+, run supersession T4 (16). Duties registered per 16 A6. BL-* registry changes require approver evidence from market operator/regulator. Pricing library releases require paired-hash regression sign-off (21 V11 discipline).

## 21. Versioning

Runs/lines are immutable events — never versioned, only superseded (A6). BL-* methods use semver; results pin method version. Catalogue structure semver: MAJOR for quantity-model or state-machine changes; MINOR for new lineTypes/classes; PATCH editorial. ChangeLog mandatory.

## 22. Example Records

```yaml
runID: EIG-SET-RUN-002417
settlementClass: SET-FLX | contextRef: EIG-MKT-LFM-01
periodStart: 2026-06-12T00:00 | periodEnd: 2026-06-13T00:00 | settlementPeriodMin: 30
runType: initial | runState: provisional
pricingLibraryHash: sha256:9f2c…e1a7   # equals optimisation-side hash (21 V11)
inputDatasetRefs: [EIG-DSP-LFM-METER-20260612, EIG-DSP-WX-20260612]
sandbox: false
```

```yaml
lineID: EIG-SET-00194412 | runID: EIG-SET-RUN-002417 | lineType: flex_utilisation
payerRef: EIG-ORG-0102-counterparty (DSO western) | payeeRef: EIG-ORG-0044 (Aran co-op aggregation)
quantityStatement:
  counterpartyRef: EIG-ORG-0044 | periodTS: 2026-06-12T17:30 | productOrPlanRef: EIG-PRD-FLX-SHED-01
  meteredQty: 41.2 kWh | contractedQty: 45.0 kWh-equiv (90 kW × 0.5 h)
  baselineQty: 84.6 kWh | baselineMethodRef: BL-LFM-01 | deliveredQty: 43.4 kWh (96.4% ⇒ delivered_full per 22)
  dataQualityFlag: actual | rdEvidenceRefs: [EIG-RE-… activation evidence]
priceApplied: 0.155 EUR/kWh | priceSourceRef: EIG-TRD-008834 (25)
amountNet: 6.73 | taxTreatment: {vatRatePct: 23, vatAmount: 1.55} | amountGross: 8.28
reconciliationState: matched | evidenceBundleRef: EVB-2026-06-12-LFM-0044
```

```yaml
baselineID: BL-LFM-01 | methodClass: rd_counterfactual | version: 1.2.0
parameters: {fallback: high_5_of_10 same-day-type, weatherAdjustment: true, vars: [EIG-WX-VAR-001 dry-bulb]}
inputRequirements: [15-min metering (38), weather station EIG-WX-ST-0021 (09), RD profiles (52)]
rdAdmissible: true | applicableClasses: [SET-FLX]
approvedByRef: EIG-ORG-0102 (CRU sandbox approval, LFM pilot)
```

## 23. Completion Criteria

Complete when: every settling market/billing context has a calendar and run definitions; zero-sum balance (V3) holds on all final runs; pricing-library hash parity verified per release; all SET-FLX/SET-ESC statements resolve approved BL-* methods; operator statement reconciliation live for SEM venues; adjustment chains fully linked with no orphan corrections; sandbox shadow settlement produces byte-identical economics to production logic; Phase 14 confirms 25 settlementRefs and 23 payment terms resolve only into this catalogue.
