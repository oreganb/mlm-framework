---
type: dataset_node
ontology: MLM
dataset_id: DS-LOCAL-SENSOR
dataset_family: Foundational
status: active
tags: [mlm, dataset, foundational]
aliases: [DS-LOCAL-SENSOR, On-Site Local Sensor Archive]
---

# DS-LOCAL-SENSOR — On-Site Local Sensor Archive

## Description
Historical archive of on-site sensor readings at each ODEON building: temperature (indoor/outdoor), CO₂, humidity, PIR, irradiance from pyranometer. Primary source for bias correction (W17) and microclimate calibration.

## Key Fields
`temperature_outdoor_local, temperature_indoor, co2_ppm, humidity_rh, pir_reading, pyranometer_reading`

## Resolution
15-minute

## Models That Use This Dataset
- [[W02]]
- [[W17]]
- [[F04]]

## Generation Order
**Foundational** — generated/acquired in Phase 1

## Relationships
- `draws_from` by: [[W02]], [[W17]], [[F04]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-LOCAL-SENSOR, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
