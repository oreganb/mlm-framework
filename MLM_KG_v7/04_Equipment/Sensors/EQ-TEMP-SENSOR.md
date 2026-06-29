---
type: equipment_node
ontology: MLM
equipment_id: EQ-TEMP-SENSOR
category: Sensors
status: active
tags: [mlm, equipment, sensors]
aliases: [EQ-TEMP-SENSOR, Temperature Sensor]
---

# EQ-TEMP-SENSOR — Temperature Sensor

## Description
Milesight AM319 indoor environmental sensor (temperature, CO₂, humidity, occupancy). Primary indoor environment sensor in ODEON buildings.

## Dataset Fields Populated
`DS-SENSOR.indoor_temp, DS-SENSOR.co2_ppm`

## Models That Use This Equipment
- [[T01]]
- [[W02]]
- [[D01]]
- [[O01]]

## Relationships
- `feeds_into` dataset(s) → feeds model(s)
- `validated_by` [[D-Series]] (sensor fault detection)

## ODEON Deployment Note
This equipment is deployed in ODEON Aran Islands buildings. Refer to ODEON hardware planning documentation for per-building deployment schedule.
