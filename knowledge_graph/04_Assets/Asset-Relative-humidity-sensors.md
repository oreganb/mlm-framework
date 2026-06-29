---
type: asset
id: Asset-Relative-humidity-sensors
---

# Relative humidity sensors

## Context availability

- Residential: X
- Commercial: X
- Industrial: X

## Model methods represented in source matrix

- **RC (Thermal)**: Psychrometric RC; moist air model
- **Kirchhoff (Electrical)**: Comfort bands; deadband & hysteresis checks
- **COP Eqns**: Sensor failure/drift scenarios
- **NTU–ε (HX)**: Occupancy→temp transitions
- **LP / Rules**: Seasonal drift ARIMA/SARIMA
- **Monte Carlo**: Weather ensemble propagation
- **Markov**: Regression-based calibration
- **ARIMA / Prob TS**: LSTM humidity/temperature co-modelling
- **Ensembles**: NN surrogate for calibration curve
- **Sup. ML**: Cross-building calibration (FedAvg)
- **DL**: Setpoint discretization policy
