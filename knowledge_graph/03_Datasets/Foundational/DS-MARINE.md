---
type: dataset_node
ontology: MLM
dataset_id: DS-MARINE
dataset_family: Foundational
status: active
tags: [mlm, dataset, foundational]
aliases: [DS-MARINE, Marine & Coastal Conditions]
---

# DS-MARINE — Marine & Coastal Conditions

## Description
Marine conditions for Aran Islands coastal context: sea surface temperature, significant wave height, salinity, tidal state. From Met Éireann Marine Division and Copernicus Marine Service.

## Key Fields
`sea_surface_temp, significant_wave_height, salinity, tidal_height`

## Resolution
Hourly

## Models That Use This Dataset
- [[W09]]

## Generation Order
**Foundational** — generated/acquired in Phase 1

## Relationships
- `draws_from` by: [[W09]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-MARINE, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
