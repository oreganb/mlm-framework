---
type: asset
id: Asset-VOC-PM-sensors
---

# VOC / PM sensors

## Context availability

- Residential: (opt.)
- Commercial: X
- Industrial: X

## Model methods represented in source matrix

- **RC (Thermal)**: Mass-balance IAQ model
- **Kirchhoff (Electrical)**: Ventilation rules; ASHRAE limits
- **COP Eqns**: Sensor outage/events MC
- **NTU–ε (HX)**: Occupancy state transitions
- **LP / Rules**: CO₂/RH probabilistic forecast
- **Monte Carlo**: IAQ/weather ensembles
- **Markov**: Classification (air quality events)
- **ARIMA / Prob TS**: CNN/LSTM anomaly detection (PM spikes)
- **Ensembles**: NN surrogate of IAQ response
- **Sup. ML**: Federated IAQ models across sites
- **DL**: Discrete ventilation steps
