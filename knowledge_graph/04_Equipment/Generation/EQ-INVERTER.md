---
type: equipment_node
ontology: MLM
equipment_id: EQ-INVERTER
category: Generation
status: active
tags: [mlm, equipment, generation]
aliases: [EQ-INVERTER, PV String Inverter]
---

# EQ-INVERTER — PV String Inverter

## Description
Grid-tied string inverter converting DC PV output to AC. Brands: SMA, Fronius, SolarEdge. Efficiency curve in DS-INVERTER. Smart inverters support reactive power injection.

## Dataset Fields Populated
`DS-INVERTER.efficiency_curve, DS-INVERTER.rated_capacity_w`

## Models That Use This Equipment
- [[R01]]
- [[R05]]
- [[G05]]

## Relationships
- `feeds_into` dataset(s) → feeds model(s)
- `validated_by` [[D-Series]] (sensor fault detection)

## ODEON Deployment Note
This equipment is deployed in ODEON Aran Islands buildings. Refer to ODEON hardware planning documentation for per-building deployment schedule.
