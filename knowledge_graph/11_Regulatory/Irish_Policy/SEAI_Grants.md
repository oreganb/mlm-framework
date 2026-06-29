---
type: regulatory_node
category: Irish_Policy
id: SEAI_Grants
full_title: SEAI Energy Grants & Support Schemes
citation: Sustainable Energy Authority of Ireland, 2024
status: active
tags: [mlm, regulatory, irish-policy, seai, grants, heat-pump, retrofit, pv, battery]
aliases: [SEAI Grants, SEAI Schemes, MSS, Warmer Homes, EEOS, BEC]
---

# SEAI Energy Grants & Support Schemes

## Overview
SEAI administers Ireland's primary residential and community energy grant schemes. All grant eligibility decisions in ODEON are modelled through K05.

## Schemes Relevant to MLM

### Microgeneration Support Scheme (MSS)
- **FIT rate:** 0.135 €/kWh (2024) for solar PV export
- **Annual cap:** 2,500 kWh at FIT rate
- **MLM Model:** [[02_Models/M_Models/M11]] — Feed-in Tariff Model
- **Weight:** W-M11-FIT = 0.135 €/kWh

### Heat Pump Grant
- **Quantum:** Up to €6,500 for ASHP; up to €6,500 for GSHP
- **Eligibility:** BER B3 or better post-retrofit; home built before 2011
- **MLM Model:** [[02_Models/K_Models/K05]] — Policy Alignment & Incentive Eligibility
- **Weight:** W-K05-SEAI_HP = €6,500

### Solar PV Grant
- **Quantum:** Up to €2,400 (up to 4kWp)
- **MLM Models:** [[02_Models/R_Models/R01]], [[02_Models/K_Models/K05]]

### Warmer Homes Scheme
- **Target:** Low-income households (DPSP/Fuel Allowance recipients)
- **MLM Model:** [[02_Models/O_Models/O14]] — Income-Energy Correlation (energy poverty flag)
- **Connection:** O14 flags income=Low + EPC=D-G → K05 Warmer Homes eligibility

### Better Energy Communities (BEC)
- **Target:** Community energy projects
- **MLM Model:** [[02_Models/P_Models/P06]] — PED Performance (community energy balance)
- **Direct relevance:** ODEON/O-CEI could qualify as BEC project

### EEOS (Energy Efficiency Obligation Scheme)
- **Obligated parties:** Energy suppliers ≥ 1 TWh/yr
- **MLM Model:** [[02_Models/K_Models/K01]] — Avoided Carbon (EEOS savings quantification)

## Calibration Sources
SEAI data underpins multiple MLM weight values:
- W-T01-RENV: Thermal resistance from SEAI building stock analysis 2023
- W-T02-COP-BASE: COP_base = 3.2 from SEAI ASHP field trials 2022
- W-K01-EFMARG: EF_marginal = 0.295 kgCO₂/kWh (SEAI Energy Statistics 2024)
- W-K01-EFAVG: EF_average = 0.218 kgCO₂/kWh (SEAI 2024)

## Related Instruments
- [[11_Regulatory/Irish_Policy/Climate_Action_Plan]] — Policy context for grant schemes
- [[11_Regulatory/EU_Directives/EPBD]] — EPBD requirements that grants implement
- [[02_Models/K_Models/K05]] — Primary policy alignment model
