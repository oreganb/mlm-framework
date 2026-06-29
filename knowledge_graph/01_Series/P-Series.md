---
type: series_index
ontology: MLM
series_id: P
series_title: Trading, Revenue & KPIs
layer: rl
status: active
tags: [mlm, series:P, layer:rl, trading, revenue, kpi]
aliases: [P-Series, Trading Series]
sub_model_count: 8
id_range: P01–P08
---

# P-Series — Trading, Revenue & KPIs

## Master Formula
$$R_{total} = \sum_t \left[ E_{sell}(t) \cdot P_{sell}(t) - E_{buy}(t) \cdot P_{buy}(t) \right]$$
Revenue from energy trading using prices from M-Series forecasts.

## Sub-Model Registry

| ID | Title | Key Output |
|---|---|---|
| [[P01]] | P2P Energy Trading Model | R_p2p, E_traded |
| [[P02]] | Intraday Trading Model | R_intraday |
| [[P03]] | Flexibility Market Bidding | bid_volume, bid_price |
| [[P04]] | Self-Consumption KPI | SC_ratio (0–1) |
| [[P05]] | Bill Savings KPI | savings_€ per period |
| [[P06]] | PED Performance KPI | PED_balance_kWh |
| [[P07]] | Response Derivative KPI | RD_score per asset |
| [[P08]] | Portfolio Aggregation | agg_flex_kW, agg_bid |

## Outputs → Consumed By
- R_total → [[K-Series]] (economic sustainability metric)
- SC_ratio → Platform KPI dashboard
- bid_volume/price → Market operator submission