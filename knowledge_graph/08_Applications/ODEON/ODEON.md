---
type: application_node
ontology: MLM
application_id: ODEON
project: O-CEI
deployment: Aran Islands, Ireland
status: active
tags: [mlm, application, odeon, o-cei, aran-islands, edge, jetson-nano]
aliases: [ODEON, O-CEI Pilot 1, Aran Islands Deployment]
---

# ODEON — Aran Islands O-CEI Pilot Deployment

## Overview
ODEON is the primary real-world deployment context for the MLM framework. It covers 70+ residential and commercial buildings on the Aran Islands (Inis Mór, Inis Meáin, Inis Oírr), operated as an Optimised Community Energy Island (O-CEI).

## MLM Deployment Architecture

```
[Jetson Nano — Far Edge]      [FLEXUS Cloud]
  Per Tier 1/2/3 building       Aggregation layer
  - T-Series (T01 RC model)     - F01 Federated averaging
  - B01 SoC tracking            - C07 Multi-agent coordinator
  - W01-W02 local weather       - M-Series market integration
  - D01-D04 local QA            - P-Series trading
  - C01 local dispatch          - K-Series carbon reporting
        │
        └──→ [FLEXUS Edge Gateway]
               Community-level aggregation
               G01 Feeder power flow
               C09 Islanding controller
```

## Building Tiers
| Tier | Role | Jetson Nano | Key MLM Models |
|---|---|---|---|
| Tier 1 | Full DER + flexibility | Required | T01, B01, R01, C01, O01 |
| Tier 2 | Monitoring + limited control | Required | T01, D01-D05, L01 |
| Tier 3 | Monitoring only | Optional | D01, L01 |
| CFOAT | Community hub | Edge gateway | All series aggregation |

## Hardware → Dataset Mapping
| Hardware | Dataset Fed | MLM Models |
|---|---|---|
| Milesight AM319 | DS-SENSOR (T, CO₂, RH) | T01-I01, W06, D01 |
| Enginko sensors | DS-SENSOR (various) | W02, T01 |
| HotDrop meters | DS-SMART-METER | L01, D05 |
| Quandify CubicMeter | DS-WATER (DHW proxy) | O04 |
| Browan Tabs | DS-OCCUPANCY (PIR) | O01 |
| Jetson Nano | Local inference | T01, B01, C01 |
| NIBE MODBUS 40 (IE_B0009) | DS-HVAC | T03, T02 |
| Mitsubishi DHW thermistor (IE_B0016) | DS-SENSOR.dhw_temp | O04, T08 |
| Daikin Madoka/BACnet (IE_B0012) | DS-HVAC | T03 |

## Site-Specific Parameters
- **Climate:** Atlantic maritime, SW prevailing wind, high cloud cover (OKTAS 4–6 typical)
- **Grid:** Islanded or weakly connected — G07/G08 critical for islanding detection
- **Solar:** Lower irradiance than mainland (W01: bias correction W17 required)
- **Community:** ~70 buildings, mix residential + community buildings
- **Connectivity:** Intermittent — F-Series federated model must tolerate offline periods

## Key MLM Constraints for ODEON Code
1. **W17 microclimate correction is mandatory** — Aran Islands has significant marine microclimate bias vs. NWP forecasts
2. **G07/G08 islanding models must run continuously** — grid can island without warning
3. **F01 must tolerate async updates** — connectivity gaps mean federated rounds are irregular
4. **B01 SoC_min = 0.15** for ODEON (stricter than default 0.10) — grid stability buffer
5. **T01 Δt = 900s** — fixed at 15-minute smart meter resolution
6. **Heating type distribution differs from national average** — Aran Islands: higher oil boiler % (isolation)

## Related Nodes
- [[MLM Knowledge Graph]] — Root
- [[W-Series]] — Weather with marine context (W09)
- [[G-Series]] — Grid islanding (G07, G08, G09)
- [[F-Series]] — Federated learning (offline tolerance)
- [[C01]] — Local dispatch with island-aware constraints
- [[07_Relationships/Cross-Series Dependencies]] — Full data flow
