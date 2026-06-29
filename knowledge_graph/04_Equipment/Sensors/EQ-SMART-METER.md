---
type: equipment_node
ontology: MLM
equipment_id: EQ-SMART-METER
category: Sensors
status: active
tags: [mlm, equipment, sensors]
aliases: [EQ-SMART-METER, Smart Electricity Meter]
---

# EQ-SMART-METER — Smart Electricity Meter

## Description
Irish smart meter (CER specification) providing 15-minute import/export data. HotDrop sub-meters in ODEON for circuit-level metering (2A clamp meters, wireless).

## Dataset Fields Populated
`DS-SMART-METER.net_import_kwh, DS-SMART-METER.net_export_kwh`

## Models That Use This Equipment
- [[L01]]
- [[L02]]
- [[D05]]

## Relationships
- `feeds_into` dataset(s) → feeds model(s)
- `validated_by` [[D-Series]] (sensor fault detection)

## ODEON Deployment Note
This equipment is deployed in ODEON Aran Islands buildings. Refer to ODEON hardware planning documentation for per-building deployment schedule.
