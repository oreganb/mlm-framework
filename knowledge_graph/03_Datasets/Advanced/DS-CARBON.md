---
type: dataset_node
ontology: MLM
dataset_id: DS-CARBON
dataset_family: Advanced
status: active
tags: [mlm, dataset, advanced]
aliases: [DS-CARBON, Carbon Intensity & ETS Data]
---

# DS-CARBON — Carbon Intensity & ETS Data

## Description
EirGrid carbon intensity signal (real-time and forecast), EU ETS allowance prices, SEAI marginal and average emission factors. Published carbon data for Ireland.

## Key Fields
`timestamp, carbon_intensity_gco2_kwh, ef_marginal, ef_average, ets_price_eur_t, ets_free_allocation`

## Resolution
30-minute (EirGrid API)

## Models That Use This Dataset
- [[M08]]
- [[M09]]
- [[K01]]
- [[K03]]

## Generation Order
**Advanced** — generated/acquired in Phase 4

## Relationships
- `draws_from` by: [[M08]], [[M09]], [[K01]], [[K03]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-CARBON, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
