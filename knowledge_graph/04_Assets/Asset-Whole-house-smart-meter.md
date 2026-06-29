---
type: asset
id: Asset-Whole-house-smart-meter
---

# Whole-house smart meter

## Context availability

- Residential: X
- Commercial: –
- Industrial: –

## Model methods represented in source matrix

- **RC (Thermal)**: Whole-building power balance
- **Kirchhoff (Electrical)**: Tariff-aware alerts; baseload rules
- **COP Eqns**: Load uncertainty MC
- **NTU–ε (HX)**: Appliance state transitions (latent)
- **LP / Rules**: Demand TS with intervals
- **Monte Carlo**: Tariff/weather ensembles
- **Markov**: Baseline regression
- **ARIMA / Prob TS**: Seq2seq demand forecast
- **Ensembles**: Proxy for missing sub-meters
- **Sup. ML**: Federated baseline models
- **DL**: Discrete DR actions
