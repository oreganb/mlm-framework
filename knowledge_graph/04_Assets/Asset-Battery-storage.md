---
type: asset
id: Asset-Battery-storage
---

# Battery storage

## Context availability

- Residential: X
- Commercial: X
- Industrial: X

## Model methods represented in source matrix

- **RC (Thermal)**: Power balance; constraints
- **Kirchhoff (Electrical)**: SoC/ramp/DoD rules
- **COP Eqns**: Cycle-life/SoH degradation MC
- **NTU–ε (HX)**: SoC transition model
- **LP / Rules**: SoC/power TS forecast
- **Monte Carlo**: Price/weather ensembles
- **Markov**: SoH/SoC regressors
- **ARIMA / Prob TS**: LSTM dispatch prediction
- **Ensembles**: NN fast electrochem proxy
- **Sup. ML**: Cross-site battery learn.
- **DL**: Discrete charge/discharge
