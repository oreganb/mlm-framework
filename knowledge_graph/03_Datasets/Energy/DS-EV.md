---
type: dataset_node
ontology: MLM
dataset_id: DS-EV
dataset_family: Energy
status: active
tags: [mlm, dataset, energy]
aliases: [DS-EV, Electric Vehicle Dataset]
---

# DS-EV — Electric Vehicle Dataset

## Description
EV specifications and trip diary data: charger rating, battery capacity, V2G contract status, trip logs (departure time, arrival time, SoC on return, distance). Per-household EV data.

## Key Fields
`ev_id, building_id, charger_rated_power, ev_battery_kwh, v2g_contract_signed, trip_departure, trip_arrival, soc_on_return, v2g_enabled`

## Resolution
Event-based trip logs + static specs

## Models That Use This Dataset
- [[B03]]
- [[B04]]
- [[B05]]
- [[L07]]

## Generation Order
**Energy** — generated/acquired in Phase 3

## Relationships
- `draws_from` by: [[B03]], [[B04]], [[B05]], [[L07]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-EV, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
