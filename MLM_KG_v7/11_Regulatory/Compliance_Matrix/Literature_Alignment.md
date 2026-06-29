---
type: literature_alignment
ontology: MLM
status: active
tags: [mlm, literature, calibration, citations, peer-review]
aliases: [Literature Alignment, MLM Citation Map, Calibration Sources]
---

# Literature Alignment — MLM Weight Calibration Sources

Maps every formula coefficient in the Weight Registry to its peer-reviewed or authoritative source. Designed for academic paper submission, peer review response, and project deliverable citations.

## Physical / Engineering Coefficients

| Weight ID | Value | Parameter | Source | Full Citation |
|---|---|---|---|---|
| W-R01-BETA | -0.0035 /°C | PV temperature coefficient | IEC 61215 + literature | Standard monocrystalline Si; Marion (2008) Prog. Photovolt. |
| W-R01-TNOCT | 45 °C | Nominal Operating Cell Temp | IEC 61215 | IEC 61215-2:2021 Test conditions |
| W-R01-ETA | 0.20 | STC panel efficiency | IEA PVPS | IEA PVPS Task 1, 2023 Snapshot |
| W-R01-ETAINV | 0.96 | Inverter efficiency | Manufacturer data | Fronius/SMA datasheet consensus |
| W-W01-KCLOUD | 0.11 | Cloud attenuation | Perez et al. 1990 | Perez R. et al. (1990) Solar Energy 44(5) |
| W-B01-ETCH | 0.95 | Charge efficiency | IEC 62619 | IEC 62619:2022 §6.3 |
| W-B01-SOCMIN | 0.10 | Min SoC | Battery manufacturer spec | Degradation studies: Barré et al. (2013) J. Power Sources |
| W-B02-ARRH | 6000 K | Arrhenius E_a/R | Bernardi et al. 2002 | Bernardi et al. (2002) J. Electrochem. Soc. 149(5) |
| W-O08-QOCC | 80 W/person | Occupant heat gain | CIBSE Guide A | CIBSE (2021) Guide A: Environmental Design, 8th Ed. Table 6.3 |
| W-T01-TMIN | 18.0 °C | Comfort lower bound | ISO 7730 + CIBSE | ISO 7730:2005; CIBSE (2021) Guide A Table 1.2 |
| W-T01-TMAX | 24.0 °C | Comfort upper bound | ISO 7730 + CIBSE | ISO 7730:2005 Category A |
| W-T02-COP-BASE | 3.2 | Base COP at 7°C | SEAI field trials | SEAI (2022) Heat Pump Field Trial Report |
| W-G07-ROCOF | 1.0 Hz/s | Islanding ROCOF threshold | Irish grid code | EirGrid Grid Code Version 7.0 (2023) §CC.8 |
| W-G04-VMIN | 0.94 pu | Min voltage (EN 50160) | EN 50160 | EN 50160:2010+A2:2019 §4.2.2 |

## Irish / Market Empirical Coefficients

| Weight ID | Value | Parameter | Source | Full Citation |
|---|---|---|---|---|
| W-K01-EFMARG | 0.295 kgCO₂/kWh | Marginal emission factor | SEAI 2024 | SEAI (2024) Energy Statistics 2023, Table A3 |
| W-K01-EFAVG | 0.218 kgCO₂/kWh | Average emission factor | SEAI 2024 | SEAI (2024) Energy Statistics 2023, Table A3 |
| W-M01-AR | 0.72 | ARIMA AR coefficient | SEMO data | Calibrated on SEMO DAM prices Jan-Dec 2023 |
| W-M01-MA | 0.31 | ARIMA MA coefficient | SEMO data | Calibrated on SEMO DAM prices Jan-Dec 2023 |
| W-M04-RED | 0.18 €/kWh | DUoS Red band rate | CRU/ESBN | ESB Networks DUoS Tariff Schedule 2024 |
| W-M11-FIT | 0.135 €/kWh | FIT rate (MSS) | SEAI | SEAI Microgeneration Support Scheme (2024) |
| W-T01-RENV | 0.15 K/W | Thermal resistance (uninsulated) | SEAI building stock | SEAI (2023) National Heat Study Technical Report |
| W-T02-COP-BETA | -0.08 /°C | COP temperature sensitivity | SEAI field trials | SEAI (2022) Heat Pump Field Trial, regression fit |
| W-W03-K | 2.1 | Weibull shape (Aran Islands wind) | Met Éireann | Met Éireann Wind Atlas (2023), Aran Islands station |
| W-W03-LAMBDA | 7.8 m/s | Weibull scale (Aran Islands) | Met Éireann | Met Éireann Wind Atlas (2023), mean wind speed |

## Policy-Derived Coefficients

| Weight ID | Value | Parameter | Policy Source | Instrument |
|---|---|---|---|---|
| W-C01-ALPHA | 0.50 | Energy cost weight | EIG policy calibration | C01 objective weights (primary = energy cost in Irish context) |
| W-C01-BETA | 0.30 | Comfort weight | CIBSE + SEAI regulatory floor | ISO 7730 Category A maintained |
| W-C01-GAMMA | 0.20 | Carbon weight | EU ETS + CAP 2024 | Carbon price signal weight calibrated to Irish carbon tax trajectory |
| W-C03-EPSILON | 0.10 | Chance-constraint ε (90% reliability) | FT stochastic bridge spec | Engineering reliability standard for energy dispatch |
| W-K02-THRESH2030 | EPC C | EPBD 2030 threshold | EPBD Art. 3 | Directive (EU) 2024/1275 Article 3 |
| W-M08-EFBASE | 0.295 kgCO₂/kWh | Carbon EF base | SEAI 2024 | SEAI Energy Statistics 2023 |

## Response Derivative — Novel Contribution

The Response Derivative concept has no direct literature precedent in exactly the formulation used in MLM. The closest aligned literature:

| Concept | Related Literature | MLM Model |
|---|---|---|
| LP shadow prices as marginal cost | Samuelson (1952) Int. Economic Review; Standard LP duality | [[02_Models/C_Models/C08]] λ*_i = ∂obj/∂x_i |
| Demand response sensitivity | Albadi & El-Saadany (2008) Electric Power Systems Research 78(11) | [[02_Models/C_Models/C08]] applied to Flex↑/↓ |
| Conditional responsiveness | O'Regan B. (2026) IEECB&SC'26 — original contribution | [[08_Response_Derivative/Response Derivative Map v4]] |
| Flexibility characterisation | Ulbig & Andersson (2015) Applied Energy 151 | [[02_Models/P_Models/P07]] RD portfolio KPI |
| Multi-layer energy modelling | Henze et al. (2004) J. Solar Energy Engineering | [[00_Core/Architecture/MLM Architecture v3]] |

## ODEON/O-CEI Pilot Validation Context

When using MLM for academic publication, the standard citation chain for empirical values is:
1. Instrument/standard (IEC, ISO, EN, SEAI) → specific table/clause
2. MLM Weight Registry entry → exact numeric value
3. Model node (W01, T01, etc.) → how the value is used in the formula
4. Application (ODEON) → deployment context where the value has been validated

This three-level citation chain is robust for peer review and Horizon Europe reporting.
