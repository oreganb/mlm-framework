---
type: dataset_node
ontology: MLM
dataset_id: DS-ASTRONOMY
dataset_family: Foundational
status: active
tags: [mlm, dataset, foundational]
aliases: [DS-ASTRONOMY, Solar Astronomical Ephemeris]
---

# DS-ASTRONOMY — Solar Astronomical Ephemeris

## Description
Solar geometry tables: declination, hour angle, zenith angle, azimuth. Deterministic astronomical calculations. Site-specific for Aran Islands (53.1°N, 9.7°W).

## Key Fields
`solar_zenith, solar_azimuth, solar_declination, hour_angle, sunrise_utc, sunset_utc`

## Resolution
1-minute resolution, pre-computed

## Models That Use This Dataset
- [[W08]]

## Generation Order
**Foundational** — generated/acquired in Phase 1

## Relationships
- `draws_from` by: [[W08]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-ASTRONOMY, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
