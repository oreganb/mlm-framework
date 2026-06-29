---
type: equipment_node
ontology: MLM
equipment_id: EQ-PYRANOMETER
category: Sensors
status: active
tags: [mlm, equipment, sensors]
aliases: [EQ-PYRANOMETER, Solar Irradiance Pyranometer]
---

# EQ-PYRANOMETER — Solar Irradiance Pyranometer

## Description
Silicon photodiode pyranometer measuring global horizontal irradiance (GHI) on-site. Used by W17 for microclimate bias correction of NWP irradiance forecasts.

## Dataset Fields Populated
`DS-LOCAL-SENSOR.pyranometer_reading`

## Models That Use This Equipment
- [[W01]]
- [[W17]]

## Relationships
- `feeds_into` dataset(s) → feeds model(s)
- `validated_by` [[D-Series]] (sensor fault detection)

## ODEON Deployment Note
This equipment is deployed in ODEON Aran Islands buildings. Refer to ODEON hardware planning documentation for per-building deployment schedule.
