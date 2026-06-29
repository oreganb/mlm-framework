---
type: equipment_node
ontology: MLM
equipment_id: EQ-PV
category: Generation
status: active
tags: [mlm, equipment, generation]
aliases: [EQ-PV, PV Panel Array]
---

# EQ-PV — PV Panel Array

## Description
Photovoltaic panels on residential/commercial buildings. Monocrystalline silicon, 300–400Wp per panel. SEAI MSS scheme eligible. Per-building specs in DS-PV.

## Dataset Fields Populated
`DS-PV.panel_area_m2, DS-PV.efficiency_stc`

## Models That Use This Equipment
- [[R01]]
- [[R03]]
- [[R04]]

## Relationships
- `feeds_into` dataset(s) → feeds model(s)
- `validated_by` [[D-Series]] (sensor fault detection)

## ODEON Deployment Note
This equipment is deployed in ODEON Aran Islands buildings. Refer to ODEON hardware planning documentation for per-building deployment schedule.
