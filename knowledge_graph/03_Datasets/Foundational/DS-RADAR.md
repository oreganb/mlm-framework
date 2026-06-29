---
type: dataset_node
ontology: MLM
dataset_id: DS-RADAR
dataset_family: Foundational
status: active
tags: [mlm, dataset, foundational]
aliases: [DS-RADAR, Weather Radar Nowcast]
---

# DS-RADAR — Weather Radar Nowcast

## Description
Radar reflectivity images from Met Éireann CAPPI radar. Used by W15 for sub-hourly irradiance nowcasting via cloud motion extrapolation.

## Key Fields
`radar_reflectivity, cloud_motion_vectors, nowcast_irradiance`

## Resolution
5-minute, 1km resolution

## Models That Use This Dataset
- [[W15]]

## Generation Order
**Foundational** — generated/acquired in Phase 1

## Relationships
- `draws_from` by: [[W15]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-RADAR, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
