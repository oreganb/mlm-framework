---
type: dataset_node
ontology: MLM
dataset_id: DS-OCCUPANCY
dataset_family: Occupancy
status: active
tags: [mlm, dataset, occupancy]
aliases: [DS-OCCUPANCY, Occupancy & Behaviour Dataset]
---

# DS-OCCUPANCY — Occupancy & Behaviour Dataset

## Description
Occupancy time series from PIR/BLE/WiFi sensors, DHW draw events, appliance usage logs, resident survey data (schedule type, income, preferences). Generated with synthetic weighting: 9-5 worker 60%, shift 30%, retiree 10%.

## Key Fields
`pir_reading, ble_device_count, wifi_device_count, dhw_draw_volume_L, energy_awareness, schedule_type, household_size`

## Resolution
15-minute + event-based

## Models That Use This Dataset
- [[O01]]
- [[O02]]
- [[O04]]
- [[O05]]
- [[O11]]

## Generation Order
**Occupancy** — generated/acquired in Phase 2

## Relationships
- `draws_from` by: [[O01]], [[O02]], [[O04]], [[O05]], [[O11]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-OCCUPANCY, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
