---
type: equipment_node
ontology: MLM
equipment_id: EQ-HP
category: HVAC
status: active
tags: [mlm, equipment, hvac]
aliases: [EQ-HP, Heat Pump]
---

# EQ-HP — Heat Pump

## Description
Air-source heat pump (ASHP) for space heating. Primary flexible thermal load in ODEON. NIBE integration via IE_B0009 MODBUS 40 interface. COP 2.5–4.5 depending on conditions.

## Dataset Fields Populated
`DS-HVAC.rated_cop, DS-HVAC.modbus_address`

## Models That Use This Equipment
- [[T03]]
- [[T02]]
- [[L03]]

## Relationships
- `feeds_into` dataset(s) → feeds model(s)
- `validated_by` [[D-Series]] (sensor fault detection)

## ODEON Deployment Note
This equipment is deployed in ODEON Aran Islands buildings. Refer to ODEON hardware planning documentation for per-building deployment schedule.
