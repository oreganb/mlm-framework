---
type: regulatory_node
category: EU_Directive
id: EED
full_title: Energy Efficiency Directive (Recast)
citation: Directive (EU) 2023/1791
status: active
in_force: 2023-10-10
transposition_deadline: 2025-10-11
tags: [mlm, regulatory, eu-directive, eed, energy-efficiency, demand-response]
aliases: [EED, Energy Efficiency Directive, 2023/1791, EED 2023]
---

# EED — Energy Efficiency Directive (Recast 2023)

## Summary
Sets binding EU-level energy efficiency targets (11.7% reduction vs 2020 projections by 2030). Key for MLM because it mandates demand response access for all consumers, smart metering, and energy savings obligations.

## Key Articles Relevant to MLM

| Article | Requirement | MLM Compliance Point |
|---|---|---|
| Art. 11 | Energy savings obligation: Member States achieve 1.5% annual savings | [[02_Models/K_Models/K01]] avoided carbon; [[02_Models/P_Models/P05]] bill savings KPI |
| Art. 18 | Demand response: consumers must be able to participate | [[02_Models/C_Models/C01]] Flex↑/↓ dispatch; [[02_Models/P_Models/P03]] flexibility market bidding |
| Art. 19 | Smart metering — data access rights for consumers | [[03_Datasets/DS-SMART-METER]] smart meter data pipeline |
| Art. 22 | Energy audits for large enterprises | [[02_Models/L_Models/L01]] net load; [[02_Models/K_Models/K01]] carbon accounting |
| Art. 23 | Building renovation — public sector leading by example | [[02_Models/T_Models/T18]] setpoint optimisation |
| Art. 30 | National Energy Efficiency Fund — financing for retrofits | [[02_Models/K_Models/K05]] incentive eligibility |

## MLM Models That Demonstrate Compliance

- **[[02_Models/C_Models/C01]]** — MILP produces Flex↑/↓ enabling demand response participation (Art. 18)
- **[[02_Models/P_Models/P03]]** — Flexibility Market Bidding: direct market participation mechanism
- **[[02_Models/P_Models/P08]]** — Portfolio Aggregation: enables small consumers to participate collectively
- **[[02_Models/O_Models/O10]]** — Smart Appliance Response: price-responsive load shifting
- **[[02_Models/O_Models/O11]]** — Behavioural Flexibility Willingness: consumer participation model
- **[[02_Models/K_Models/K01]]** — Avoided Carbon: energy savings quantification for Art. 11 reporting
- **[[03_Datasets/DS-SMART-METER]]** — Smart meter data access underpins all consumer-facing EED rights

## Relationship to ODEON/O-CEI
O-CEI community on Aran Islands demonstrates collective demand response (P08 portfolio aggregation) for small consumers who individually cannot meet market minimum lot sizes — directly addresses EED Art. 18 access requirement for vulnerable/island communities.

## Related Instruments
- [[11_Regulatory/EU_Directives/EPBD]] — Building performance (supply-side complement)
- [[11_Regulatory/Market_Frameworks/I-SEM]] — Irish market implementing EED demand response
- [[11_Regulatory/Irish_Policy/Climate_Action_Plan]] — Irish transposition context
