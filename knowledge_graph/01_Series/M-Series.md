---
type: series_index
ontology: MLM
series_id: M
series_title: Market, Tariff & Carbon Pricing
layer: stochastic
status: active
tags: [mlm, series:M, layer:stochastic, market, price, carbon]
aliases: [M-Series, Market Series]
sub_model_count: 12
id_range: M01–M12
---

# M-Series — Market, Tariff & Carbon Pricing

## Master Formula
$$P^{DA}_{t+1} = \alpha P^{DA}_t + \beta P^{ID}_t + \gamma X_t + \epsilon_t$$
ARIMA day-ahead price forecast. `α=0.72, β=0.31` (calibrated: SEMO 2023). `X_t` = exogenous variables (carbon price, weather).

## Sub-Model Registry

| ID | Title | Key Output |
|---|---|---|
| [[M01]] | Day-Ahead Price Forecast | price_DA[k] (€/kWh) |
| [[M02]] | Intraday Price Model | price_ID[k] |
| [[M03]] | System Marginal Price Model | SMP[k] |
| [[M04]] | Distribution Tariff Model | tariff_network[k] |
| [[M05]] | Time-of-Use Tariff Model | TOU_rate[k] |
| [[M06]] | Demand Response Incentive Model | DR_payment[k] |
| [[M07]] | P2P Price Mechanism | P2P_clearing_price[k] |
| [[M08]] | Carbon Intensity Model | EF[k] (kgCO₂/kWh) |
| [[M09]] | Carbon Price Model | carbon_price (€/tonne) |
| [[M10]] | Flexibility Market Price | flex_price[k] |
| [[M11]] | Feed-in Tariff Model | FIT_rate[k] |
| [[M12]] | Market Ensemble Generator | price ensembles Ω_m |

## Datasets
- **[[DS-MARKET]]** — SEMO day-ahead prices, intraday, SMP historical
- **[[DS-CARBON]]** — EU ETS, carbon intensity signal (EirGrid API)
- **[[DS-TARIFF]]** — Network tariff structure, DSO charges

## Weights
- `α=0.72` (AR term), `β=0.31` (MA term) — SEMO 2023 calibration
- EF_marginal = 0.295 kgCO₂/kWh (SEAI 2024 Ireland)

## Outputs → Consumed By
- price_DA[k] → [[C01]] (energy cost term), [[P01]] (revenue)
- EF[k] → [[C01]] (carbon cost term), [[K01]] (avoided carbon)
- price ensembles → [[C03]] (determinization bridge)