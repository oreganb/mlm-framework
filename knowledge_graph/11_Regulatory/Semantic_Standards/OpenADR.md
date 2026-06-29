---
type: regulatory_node
category: Semantic_Standard
id: OpenADR
full_title: Open Automated Demand Response (OpenADR 2.0b)
citation: OpenADR Alliance
status: active
version: 2.0b
tags: [mlm, regulatory, semantic-standard, openadr, demand-response, ven, vtn, signals]
aliases: [OpenADR, OpenADR 2.0, OpenADR 2.0b, Automated Demand Response]
---

# OpenADR 2.0b — Open Automated Demand Response

## Overview
OpenADR is the protocol for automated demand response signal exchange between Virtual Top Nodes (VTN — utility/aggregator) and Virtual End Nodes (VEN — buildings/devices). FLEXUS implements VEN functionality for ODEON buildings.

## OpenADR Signal Types & MLM Mapping

| OpenADR Signal | Description | MLM Model |
|---|---|---|
| SIMPLE (0-3) | 4-level simple signal | [[02_Models/C_Models/C01]] Flex↑/↓ level selection |
| PRICE | Electricity price signal | [[02_Models/M_Models/M01]] → [[02_Models/C_Models/C01]] |
| LOAD_DISPATCH | Explicit load target | [[02_Models/C_Models/C01]] P_HP, P_bat dispatch |
| LOAD_CONTROL | Device on/off | [[02_Models/O_Models/O10]] smart appliance control |
| ELECTRICITY_PRICE | Current and forecast prices | [[02_Models/M_Models/M05]] TOU; [[02_Models/M_Models/M01]] |
| EMERGENCY | Grid emergency event | [[02_Models/C_Models/C10]] governance block |

## FLEXUS as OpenADR VEN
FLEXUS acts as VEN, receiving:
- Price signals → [[02_Models/M_Models/M01]] (updates price_DA)
- Load control signals → [[02_Models/C_Orders/C01]] (updates dispatch objective)
- Emergency signals → [[02_Models/C_Models/C10]] (triggers BLOCK governance)

## OpenADR Event Payload → MLM Fields
```json
{
  "oadrEvent": {
    "eventDescriptor": {
      "eventID": "flex-event-001",
      "eventStatus": "active",
      "marketContext": "urn:oadr:marketContext:ire:iem"
    },
    "eiActivePeriod": {
      "dtstart": "2026-01-15T17:00:00Z",
      "duration": "PT2H"
    },
    "eiEventSignals": {
      "eventSignal": {
        "signalType": "LOAD_DISPATCH",
        "currentValue": { "payloadFloat": { "value": -5.0 } }
      }
    }
  }
}
```
Maps to: C01 P_shed = 5.0 kW for 2h starting 17:00

## Related Standards
- [[11_Regulatory/Semantic_Standards/SAREF]] — Semantic layer over OpenADR
- [[11_Regulatory/Market_Frameworks/I-SEM]] — I-SEM DR uses OpenADR-compatible signals
