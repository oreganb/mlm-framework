---
type: regulatory_node
category: Irish_Policy
id: CRU_Framework
full_title: Commission for Regulation of Utilities — Energy Framework
citation: CRU (formerly CER), Ireland
status: active
tags: [mlm, regulatory, irish-policy, cru, grid-code, smart-metering, network-tariffs, demand-response]
aliases: [CRU, Commission for Regulation of Utilities, CER, Irish Grid Code]
---

# CRU — Commission for Regulation of Utilities

## Overview
CRU is the Irish energy regulator. Sets grid codes, network tariff structures (DUoS), smart metering requirements, and demand response frameworks. Directly governs how ODEON/FLEXUS interacts with the ESB Networks and EirGrid.

## Key Frameworks Relevant to MLM

### Distribution Use of System (DUoS) Charges
- Red/Amber/Green time-of-use tariff bands
- **MLM Model:** [[02_Models/M_Models/M04]] — Distribution Network Tariff Model
- **Weights:** W-M04-RED = 0.18 €/kWh, W-M04-AMBER = 0.08 €/kWh, W-M04-GREEN = 0.02 €/kWh

### Smart Metering Programme
- 15-minute interval data standard
- Consumer access to own data (Data Act alignment)
- **MLM Models:** [[03_Datasets/DS-SMART-METER]]; W-T01-DT = 900s (aligned with 15-min meter resolution)

### Grid Code — Technical Requirements
- Voltage limits: V ∈ [0.94, 1.06] pu (EN 50160 aligned)
- ROCOF protection: 1.0 Hz/s threshold
- **MLM Models:** [[02_Models/G_Models/G04]] voltage stability; [[02_Models/G_Models/G07]] islanding detection
- **Weights:** W-G07-ROCOF = 1.0 Hz/s; W-G04-VMIN = 0.94 pu

### Demand Response Code of Practice
- Minimum DR event notice: 1 hour
- Maximum DR duration: 4 hours
- **MLM Models:** [[02_Models/P_Models/P03]] flexibility bidding; [[02_Models/C_Models/C01]] dispatch horizon
- W-C01-HORIZON-RT: 60-min real-time horizon aligned with DR notice requirements

### Export Limiting — Technical Standard (ESBN)
- Single-phase residential: 6 kW export limit
- **MLM Model:** [[02_Models/G_Models/G02]] — Grid Import/Export Limits
- **Weight:** W-G02-EXPORT = 6 kW

## Related Instruments
- [[11_Regulatory/Market_Frameworks/I-SEM]] — Irish electricity market (CRU co-governed with SEM Committee)
- [[11_Regulatory/Technical_Standards/EN_50160]] — Voltage quality standard CRU enforces
- [[11_Regulatory/Market_Frameworks/DS3]] — System services programme
