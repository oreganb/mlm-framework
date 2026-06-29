---
type: dataset_node
ontology: MLM
dataset_id: DS-NWP
dataset_family: Foundational
status: active
tags: [mlm, dataset, foundational]
aliases: [DS-NWP, Numerical Weather Prediction]
---

# DS-NWP — Numerical Weather Prediction

## Description
ECMWF ERA5 and Met Éireann NWP model output. Provides gridded forecast data for temperature, irradiance, wind, humidity at hourly resolution.

## Key Fields
`irradiance_dni, irradiance_dhi, temperature_forecast, wind_speed_forecast, humidity_forecast, irr_sigma, cloud_cover_pct`

## Resolution
Hourly, 9km grid (ECMWF), 2.5km (Met Éireann)

## Models That Use This Dataset
- [[W01]]
- [[W02]]
- [[W03]]
- [[W14]]
- [[W16]]

## Generation Order
**Foundational** — generated/acquired in Phase 1

## Relationships
- `draws_from` by: [[W01]], [[W02]], [[W03]], [[W14]], [[W16]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-NWP, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
