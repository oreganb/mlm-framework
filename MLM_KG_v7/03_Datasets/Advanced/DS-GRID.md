---
type: dataset_node
ontology: MLM
dataset_id: DS-GRID
dataset_family: Advanced
status: active
tags: [mlm, dataset, advanced]
aliases: [DS-GRID, Grid Network Parameters]
---

# DS-GRID — Grid Network Parameters

## Description
Electrical network parameters for Aran Islands feeder: line impedances, bus voltages, protection settings, feeder topology, hosting capacity limits. From ESB Networks Aran Islands data.

## Key Fields
`feeder_id, bus_id, feeder_resistance, feeder_reactance, rated_current, protection_settings, feeder_topology_json, hosting_capacity_kw, submarine_cable_params`

## Resolution
Static (updated on network change)

## Models That Use This Dataset
- [[G01]]
- [[G02]]
- [[G03]]
- [[G07]]
- [[G08]]
- [[G09]]
- [[G10]]
- [[G11]]

## Generation Order
**Advanced** — generated/acquired in Phase 4

## Relationships
- `draws_from` by: [[G01]], [[G02]], [[G03]], [[G07]], [[G08]], [[G09]], [[G10]], [[G11]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-GRID, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
