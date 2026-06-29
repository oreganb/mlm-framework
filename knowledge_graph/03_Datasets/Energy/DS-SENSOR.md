---
type: dataset_node
ontology: MLM
dataset_id: DS-SENSOR
dataset_family: Energy
status: active
tags: [mlm, dataset, energy]
aliases: [DS-SENSOR, Building Sensor Archive]
---

# DS-SENSOR — Building Sensor Archive

## Description
Real-time and historical sensor readings from all ODEON building sensors: Milesight AM319 (temperature, CO₂, humidity), Enginko (outdoor env), Browan Tabs (PIR/occupancy), HotDrop meters (power), Quandify CubicMeter (water/DHW).

## Key Fields
`building_id, sensor_id, sensor_type, timestamp, indoor_temp, co2_ppm, humidity_rh, pir_reading, battery_temp, battery_soc_bms, power_w, water_flow_lh`

## Resolution
15-minute or real-time (1-minute)

## Models That Use This Dataset
- [[T01]]
- [[B01]]
- [[B08]]
- [[D01]]
- [[D03]]
- [[D04]]
- [[D05]]
- [[O01]]

## Generation Order
**Energy** — generated/acquired in Phase 3

## Relationships
- `draws_from` by: [[T01]], [[B01]], [[B08]], [[D01]], [[D03]], [[D04]], [[D05]], [[O01]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-SENSOR, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
