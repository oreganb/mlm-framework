---
type: asset
id: Asset-CO-sensors
---

# CO₂ sensors

## Context availability

- Residential: X
- Commercial: X
- Industrial: –

## Model methods represented in source matrix

- **RC (Thermal)**: Mass-balance IAQ model
- **Kirchhoff (Electrical)**: Ventilation rules; ASHRAE limits
- **COP Eqns**: Sensor outage/events MC
- **NTU–ε (HX)**: Occupancy state transitions
- **LP / Rules**: CO₂/RH probabilistic forecast
- **Monte Carlo**: IAQ/weather ensembles
- **Markov**: Occupancy/IAQ regression/classification
- **ARIMA / Prob TS**: LSTM/AE for IAQ patterns/anomalies
- **Ensembles**: NN surrogate of IAQ response
- **Sup. ML**: Federated IAQ models across sites
- **DL**: Discrete ventilation steps
