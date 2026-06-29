---
type: dataset_node
ontology: MLM
dataset_id: DS-INVERTER
dataset_family: Energy
status: active
tags: [mlm, dataset, energy]
aliases: [DS-INVERTER, Inverter Specifications Database]
---

# DS-INVERTER — Inverter Specifications Database

## Description
Inverter efficiency curves, rated capacity, reactive power capability, MPPT range. Per-building inverter specifications from manufacturers (SMA, Fronius, SolarEdge).

## Key Fields
`inverter_id, building_id, rated_capacity_w, efficiency_curve, reactive_capability_kvar, inverter_model`

## Resolution
Static

## Models That Use This Dataset
- [[R01]]
- [[R05]]
- [[G05]]

## Generation Order
**Energy** — generated/acquired in Phase 3

## Relationships
- `draws_from` by: [[R01]], [[R05]], [[G05]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-INVERTER, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
