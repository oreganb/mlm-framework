---
type: regulatory_node
category: Market_Framework
id: I-SEM
full_title: Integrated Single Electricity Market (Ireland / Northern Ireland)
citation: SEM Committee; CRU; SONI; EirGrid
status: active
in_operation: 2018-10-01
tags: [mlm, regulatory, market, i-sem, semo, day-ahead, intraday, capacity, balancing]
aliases: [I-SEM, SEM, SEMO, Irish Electricity Market, Single Electricity Market]
---

# I-SEM — Integrated Single Electricity Market

## Overview
The Irish and Northern Irish electricity market. Consists of Day-Ahead Market (DAM), Intraday Market (IDM), Balancing Market (BM), and Capacity Market (RM-T). MLM M-Series models are calibrated on I-SEM data.

## Market Components & MLM Alignment

### Day-Ahead Market (DAM)
- Auction clears at 12:00 for D+1 delivery
- Price = System Marginal Price (SMP)
- **MLM Model:** [[02_Models/M_Models/M01]] — Day-Ahead Price Forecast
- **Calibration:** α=0.72, β=0.31 fitted on SEMO DAM price data 2023
- **Horizon:** W-C01-HORIZON-DA = 96 steps (24h)

### Intraday Market (IDM)
- Continuous trading, 15-min products
- **MLM Model:** [[02_Models/M_Models/M02]] — Intraday Price Model; [[02_Models/P_Models/P02]] — Intraday Trading
- **Weight:** W-M02-SIGMA = 0.008 €/kWh (Irish IDM volatility)

### Balancing Market
- Real-time energy balancing, imbalance settlement
- **MLM Model:** [[02_Models/M_Models/M03]] — System Marginal Price Model

### Capacity Market (RM-T)
- T-1 and T-4 capacity auctions
- Demand-side units (DSUs) can participate
- **MLM Model:** [[02_Models/P_Models/P03]] — Flexibility Market Bidding (DSU capacity participation)

### Demand Side Unit (DSU) Registration
- Minimum DSU size: 1 MW (directly) or via aggregation
- **MLM Model:** [[02_Models/P_Models/P08]] — Portfolio Aggregation (community aggregation to DSU threshold)
- ODEON community needs ~1,000 buildings at 1kW average Flex↑ to reach DSU threshold unaided

## Flexibility Market (DS3)
- See [[11_Regulatory/Market_Frameworks/DS3]] — System Services programme
- **MLM Models:** [[02_Models/P_Models/P03]], [[02_Models/C_Models/C01]] Flex↑/↓ products

## SEMO Data Used in MLM
| Dataset | MLM Weight | Used By |
|---|---|---|
| DAM price time series 2023 | W-M01-AR=0.72, W-M01-MA=0.31 | [[02_Models/M_Models/M01]] |
| IDM volatility 2023 | W-M02-SIGMA=0.008 €/kWh | [[02_Models/M_Models/M02]] |
| SMP historical | M03 merit order fit | [[02_Models/M_Models/M03]] |

## Related Instruments
- [[11_Regulatory/Market_Frameworks/DS3]] — System services
- [[11_Regulatory/Irish_Policy/CRU_Framework]] — CRU as market regulator
- [[11_Regulatory/EU_Regulations/Electricity_Market_Regulation]] — EU framework
