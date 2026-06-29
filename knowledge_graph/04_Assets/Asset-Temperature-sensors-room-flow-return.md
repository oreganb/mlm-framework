---
type: asset
id: Asset-Temperature-sensors-room-flow-return
---

# Temperature sensors (room/flow/return)

## Context availability

- Residential: X
- Commercial: X
- Industrial: X

## Model methods represented in source matrix

- **RC (Thermal)**: RC thermal coupling; zone energy balance
- **Kirchhoff (Electrical)**: Comfort bands; deadband & hysteresis checks
- **COP Eqns**: Sensor failure/drift scenarios
- **NTU–ε (HX)**: Occupancy→temp transitions
- **LP / Rules**: Seasonal drift ARIMA/SARIMA
- **Monte Carlo**: Weather ensemble propagation
- **Markov**: Regression-based calibration
- **ARIMA / Prob TS**: LSTM drift correction/denoising
- **Ensembles**: NN surrogate for calibration curve
- **Sup. ML**: Cross-building calibration (FedAvg)
- **DL**: Setpoint discretization policy
