---
type: equipment_node
ontology: MLM
equipment_id: EQ-ANEMOMETER
category: Sensors
status: active
tags: [mlm, equipment, sensors]
aliases: [EQ-ANEMOMETER, Wind Anemometer]
---

# EQ-ANEMOMETER — Wind Anemometer

## Description
Cup anemometer and wind vane measuring wind speed and direction at site level. Feeds W03 microclimate wind correction.

## Dataset Fields Populated
`DS-LOCAL-SENSOR.wind_speed_local`

## Models That Use This Equipment
- [[W03]]
- [[W17]]

## Relationships
- `feeds_into` dataset(s) → feeds model(s)
- `validated_by` [[D-Series]] (sensor fault detection)

## ODEON Deployment Note
This equipment is deployed in ODEON Aran Islands buildings. Refer to ODEON hardware planning documentation for per-building deployment schedule.
