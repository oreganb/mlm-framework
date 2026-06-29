---
type: dataset_node
ontology: MLM
dataset_id: DS-TARIFF
dataset_family: Advanced
status: active
tags: [mlm, dataset, advanced]
aliases: [DS-TARIFF, Energy Tariff Database]
---

# DS-TARIFF — Energy Tariff Database

## Description
Irish energy tariff structures: ESB Networks DUoS charges (red/amber/green bands), retail TOU tariffs, feed-in tariff (MSS rate 0.135 €/kWh), network access charges.

## Key Fields
`tariff_id, tariff_type, effective_date, rate_peak, rate_offpeak, duos_red, duos_amber, duos_green, fit_rate, annual_cap_kwh`

## Resolution
Updated quarterly or on regulatory change

## Models That Use This Dataset
- [[M04]]
- [[M05]]
- [[M11]]
- [[K05]]

## Generation Order
**Advanced** — generated/acquired in Phase 4

## Relationships
- `draws_from` by: [[M04]], [[M05]], [[M11]], [[K05]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-TARIFF, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
