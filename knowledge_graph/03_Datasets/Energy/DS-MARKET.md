---
type: dataset_node
ontology: MLM
dataset_id: DS-MARKET
dataset_family: Energy
status: active
tags: [mlm, dataset, energy]
aliases: [DS-MARKET, Energy Market Data]
---

# DS-MARKET — Energy Market Data

## Description
Irish electricity market data: SEMO/I-SEM day-ahead prices, intraday prices, system marginal price, demand response payment rates, renewable generation dispatched. Historical and forecast.

## Key Fields
`timestamp, price_da_t1, price_intraday, smp, renewable_generation_mw, system_load_mw, dr_payment_rate, intraday_volatility`

## Resolution
30-minute (market) / 15-minute (forecast)

## Models That Use This Dataset
- [[M01]]
- [[M02]]
- [[M03]]
- [[M06]]
- [[M10]]
- [[M12]]

## Generation Order
**Energy** — generated/acquired in Phase 3

## Relationships
- `draws_from` by: [[M01]], [[M02]], [[M03]], [[M06]], [[M10]], [[M12]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-MARKET, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
