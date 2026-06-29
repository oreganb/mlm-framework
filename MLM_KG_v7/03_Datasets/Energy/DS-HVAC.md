---
type: dataset_node
ontology: MLM
dataset_id: DS-HVAC
dataset_family: Energy
status: active
tags: [mlm, dataset, energy]
aliases: [DS-HVAC, HVAC System Specifications]
---

# DS-HVAC — HVAC System Specifications

## Description
Per-building HVAC system specifications: heating type, rated COP, flow rates, setpoint ranges. Includes heat pump field trial data from SEAI. IE_B0009 NIBE MODBUS and IE_B0012 Daikin/BACnet gateway specs for ODEON.

## Key Fields
`building_id, heating_type, rated_cop, flow_rate_m3h, setpoint_min, setpoint_max, manufacturer, model, modbus_address`

## Resolution
Static, updated on retrofit

## Models That Use This Dataset
- [[T02]]
- [[T03]]
- [[T08]]
- [[T14]]
- [[T15]]
- [[L03]]
- [[L06]]

## Generation Order
**Energy** — generated/acquired in Phase 3

## Relationships
- `draws_from` by: [[T02]], [[T03]], [[T08]], [[T14]], [[T15]], [[L03]], [[L06]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-HVAC, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
