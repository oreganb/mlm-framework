---
type: dataset_node
ontology: MLM
dataset_id: DS-ENERGY
dataset_family: Energy
status: active
tags: [mlm, dataset, energy]
aliases: [DS-ENERGY, Building Energy Dataset]
---

# DS-ENERGY — Building Energy Dataset

## Description
Energy consumption time series per end-use category: HVAC, DHW, appliances, lighting. Derived from sub-metering and NILM. Feeds L-Series load decomposition models.

## Key Fields
`hvac_kwh, dhw_kwh, appliances_kwh, lighting_kwh, total_kwh`

## Resolution
15-minute

## Models That Use This Dataset
- [[L04]]
- [[L05]]
- [[L06]]

## Generation Order
**Energy** — generated/acquired in Phase 3

## Relationships
- `draws_from` by: [[L04]], [[L05]], [[L06]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-ENERGY, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
