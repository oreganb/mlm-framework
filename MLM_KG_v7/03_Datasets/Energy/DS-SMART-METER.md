---
type: dataset_node
ontology: MLM
dataset_id: DS-SMART-METER
dataset_family: Energy
status: active
tags: [mlm, dataset, energy]
aliases: [DS-SMART-METER, Smart Meter Data]
---

# DS-SMART-METER — Smart Meter Data

## Description
15-minute smart meter import/export readings at building level. Irish smart meter data (Electricity Ireland). HotDrop submeters in ODEON buildings for higher-resolution sub-circuit metering.

## Key Fields
`building_id, timestamp, net_import_kwh, net_export_kwh, net_import_w, pv_generation_w`

## Resolution
15-minute

## Models That Use This Dataset
- [[L01]]
- [[L02]]
- [[L08]]
- [[L10]]
- [[L12]]
- [[D05]]

## Generation Order
**Energy** — generated/acquired in Phase 3

## Relationships
- `draws_from` by: [[L01]], [[L02]], [[L08]], [[L10]], [[L12]], [[D05]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-SMART-METER, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
