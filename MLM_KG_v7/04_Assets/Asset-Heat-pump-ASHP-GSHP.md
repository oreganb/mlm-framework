---
type: asset
id: Asset-Heat-pump-ASHP-GSHP
---

# Heat pump (ASHP/GSHP)

## Context availability

- Residential: X
- Commercial: X
- Industrial: –

## Model methods represented in source matrix

- **RC (Thermal)**: RC building-HVAC coupling
- **Kirchhoff (Electrical)**: COP=f(Tout, Tin, lift)
- **COP Eqns**: Setpoint & staging rules
- **NTU–ε (HX)**: Ambient/load uncertainty
- **LP / Rules**: Mode transitions/defrost
- **Monte Carlo**: Thermal load TS
- **Markov**: Weather ensembles
- **ARIMA / Prob TS**: COP/load regression
- **Ensembles**: LSTM HVAC load
- **Sup. ML**: NN HVAC surrogate
- **DL**: Federated HVAC models
