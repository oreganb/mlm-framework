---
type: asset
id: Asset-Heat-meters-heating-cooling-loops
---

# Heat meters (heating/cooling loops)

## Context availability

- Residential: (opt.)
- Commercial: X
- Industrial: X

## Model methods represented in source matrix

- **RC (Thermal)**: Hydronic RC; pipe/tank nodes
- **Kirchhoff (Electrical)**: HX effectiveness from ΔT, UA
- **COP Eqns**: Flow balance & valve logic
- **NTU–ε (HX)**: Sensor failure; leak scenarios
- **LP / Rules**: Flow state transitions
- **Monte Carlo**: Flow/ΔT time-series
- **Markov**: Weather-driven load ensembles
- **ARIMA / Prob TS**: Heat demand regression by zone
- **Ensembles**: LSTM for hydronic dynamics
- **Sup. ML**: NN surrogate for HX/hydronic
- **DL**: Fleet hydronic model sharing
