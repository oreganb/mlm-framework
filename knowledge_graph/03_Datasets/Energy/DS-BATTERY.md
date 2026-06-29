---
type: dataset_node
ontology: MLM
dataset_id: DS-BATTERY
dataset_family: Energy
status: active
tags: [mlm, dataset, energy]
aliases: [DS-BATTERY, Battery System Specifications]
---

# DS-BATTERY — Battery System Specifications

## Description
Per-building stationary battery specs: chemistry, capacity, charge/discharge efficiency, C-rate, SoC limits, installation date, BMS data. Includes real-time SoC from BMS.

## Key Fields
`battery_id, building_id, chemistry, capacity_wh, charge_efficiency, discharge_efficiency, max_charge_rate, max_discharge_rate, soc_min, soc_max, install_date, cycle_count`

## Resolution
Static specs + real-time SoC/SoH

## Models That Use This Dataset
- [[B01]]
- [[B02]]
- [[B06]]
- [[B07]]
- [[B08]]
- [[B09]]
- [[B10]]

## Generation Order
**Energy** — generated/acquired in Phase 3

## Relationships
- `draws_from` by: [[B01]], [[B02]], [[B06]], [[B07]], [[B08]], [[B09]], [[B10]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-BATTERY, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
