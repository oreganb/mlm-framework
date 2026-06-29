---
type: regulatory_node
category: EU_Regulation
id: Electricity_Market_Regulation
full_title: Regulation on the Internal Market for Electricity (Recast)
citation: Regulation (EU) 2024/1747
status: active
in_force: 2024-06-17
tags: [mlm, regulatory, eu-regulation, electricity-market, flexibility, demand-response, peer-to-peer]
aliases: [Electricity Market Regulation, EMR, 2024/1747, Internal Electricity Market]
---

# Electricity Market Regulation (Recast 2024)

## Summary
Recast regulation governing the internal electricity market. Key provisions: demand response access for all customers, right to share energy in communities, flexibility service frameworks, and smart meter data access. Direct legislative basis for FLEXUS flexibility platform and O-CEI P2P trading.

## Key Articles Relevant to MLM

| Article | Requirement | MLM Compliance Point |
|---|---|---|
| Art. 17 | Demand response: all customers have right to participate | [[02_Models/C_Models/C01]] Flex↑/↓; [[02_Models/P_Models/P03]] market bidding |
| Art. 19 | Aggregation: aggregators can participate independently of supplier | [[02_Models/P_Models/P08]] portfolio aggregation |
| Art. 21 | Energy sharing: customers in same grid zone can share | [[02_Models/M_Models/M07]] P2P price mechanism; [[02_Models/G_Models/G12]] community balance |
| Art. 22 | Active customer rights: self-generate, store, sell excess | [[02_Models/R_Models/R01]] PV; [[02_Models/B_Models/B01]] storage |
| Art. 40 | Flexibility needs assessment by DSO | [[02_Models/G_Models/G11]] hosting capacity; [[02_Models/P_Models/P03]] flex bids |
| Art. 58 | Smart meter minimum functions | [[03_Datasets/DS-SMART-METER]] 15-min data pipeline |

## MLM Models That Demonstrate Compliance
- **[[02_Models/P_Models/P03]]** — Flexibility Market Bidding: formal DR participation (Art. 17)
- **[[02_Models/P_Models/P08]]** — Portfolio Aggregation: independent aggregation (Art. 19)
- **[[02_Models/M_Models/M07]]** — P2P Price Mechanism: energy sharing within O-CEI zone (Art. 21)
- **[[02_Models/C_Models/C01]]** — MILP produces bankable Flex↑/↓ signals (Art. 40 flexibility product)
- **[[02_Models/G_Models/G11]]** — DER Hosting Capacity: feeds DSO flexibility needs assessment

## Related Instruments
- [[11_Regulatory/Market_Frameworks/I-SEM]] — Irish implementing market
- [[11_Regulatory/EU_Directives/EED]] — Demand response access directive
