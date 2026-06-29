---
type: series_index
ontology: MLM
series_id: W
series_title: Weather & Exogenous
layer: stochastic
status: active
tags: [mlm, series:W, layer:stochastic, weather]
aliases: [W-Series, Weather Series]
sub_model_count: 20
id_range: W01–W20
---

# W-Series — Weather & Exogenous

## Series Overview

The W-Series integrates local weather nowcasts, forecasts, and atmospheric factors into a **unified weather impact score (W_SCORE)** that feeds all other series requiring environmental context.

## Master Formula

```
W_SCORE = Σᵢ wᵢ × Wᵢ
```

Where weights `wᵢ` are dynamically updated using Q-learning based on prediction error feedback from the [[C-Series]].

**LaTeX:**
$$W_{SCORE} = \sum_{i=1}^{n} w_i W_i$$

## Layer Role
- **Layer:** Stochastic
- **Epistemic role:** Captures environmental uncertainty and variability
- **Output type:** Weighted composite score + individual sub-model distributions
- **Temporal resolution:** 5–15 min (real-time), 1h (day-ahead), seasonal

## Sub-Model Registry

| ID | Title | Primary Output | Key Dataset |
|---|---|---|---|
| [[W01]] | Solar Irradiance Model | G_POA (W/m²) | DS-WEATHER |
| [[W02]] | Ambient Temperature Model | T_out (°C) | DS-WEATHER |
| [[W03]] | Wind Speed & Direction Model | v_wind (m/s), θ_wind | DS-WEATHER |
| [[W04]] | Rainfall & Precipitation Model | P_rain (mm/h) | DS-WEATHER |
| [[W05]] | Cloud Cover Model (OKTAS) | OKTAS score (0–8) | DS-WEATHER |
| [[W06]] | Humidity & Dew Point Model | RH (%), T_dew (°C) | DS-WEATHER |
| [[W07]] | Atmospheric Pressure Model | P_atm (hPa) | DS-WEATHER |
| [[W08]] | Solar Geometry Model | θ_zenith, θ_azimuth | DS-ASTRONOMY |
| [[W09]] | Coastal & Marine Conditions | Sea state, salinity | DS-MARINE |
| [[W10]] | Extreme Weather Event Model | Storm flags, exceedance probs | DS-WEATHER |
| [[W11]] | Fog & Visibility Model | Visibility (m), fog_flag | DS-WEATHER |
| [[W12]] | UV Index Model | UV_index | DS-WEATHER |
| [[W13]] | Seasonal Pattern Model | Season code, transition probs | DS-CALENDAR |
| [[W14]] | Weather Ensemble Generator | Ensemble members (N=50) | DS-NWP |
| [[W15]] | Nowcasting Model | T+0 to T+60min forecasts | DS-RADAR |
| [[W16]] | Day-Ahead Forecast Model | T+24h distributions | DS-NWP |
| [[W17]] | Microclimate Correction Model | Site-specific bias corrections | DS-LOCAL-SENSOR |
| [[W18]] | Grid-Stress Weather Indicator | Weather → grid risk index | DS-WEATHER, DS-GRID |
| [[W19]] | Islanding Condition Weather | Weather risk for islanding ops | DS-WEATHER |
| [[W20]] | OKTAS-to-PV Mapping Model | Cloud-adjusted PV availability | DS-WEATHER, DS-PV |

## Series Inputs

| Variable | Symbol | Source | Unit |
|---|---|---|---|
| NWP forecast data | — | DS-NWP | Various |
| Local sensor readings | T, RH, P, v | DS-LOCAL-SENSOR | Various |
| Radar nowcast | — | DS-RADAR | — |
| Historical weather archive | — | DS-WEATHER | Various |
| Astronomy ephemeris | θ_z | DS-ASTRONOMY | degrees |
| Marine conditions (Aran) | — | DS-MARINE | Various |

## Series Outputs → Consumed By

| Output | Symbol | Consumed By |
|---|---|---|
| Global irradiance (plane of array) | G_POA | [[R-Series]] (R01 PV model), [[T-Series]] (T01 solar gain) |
| Ambient temperature | T_out | [[T-Series]] (T01 RC thermal), [[B-Series]] (B01 battery temp) |
| Wind speed | v_wind | [[R-Series]] (R02 wind model) |
| Cloud cover (OKTAS) | OKTAS | [[W20]], [[R-Series]] |
| Weather ensemble | Ω_w | [[C-Series]] (determinization bridge) |
| W_SCORE composite | W_SCORE | [[C-Series]], [[P-Series]] |
| Extreme event flag | E_flag | [[G-Series]] (G09 protection) |

## Dataset Dependencies

- **[[DS-WEATHER]]** — Hourly/sub-hourly historical and forecast data: temperature, humidity, wind, pressure, precipitation
- **[[DS-NWP]]** — Numerical Weather Prediction output (ECMWF or Met Éireann)
- **[[DS-RADAR]]** — Radar-based nowcasting (T+0 to T+60min)
- **[[DS-LOCAL-SENSOR]]** — On-site sensor readings for microclimate correction
- **[[DS-ASTRONOMY]]** — Solar geometry tables (declination, hour angle, zenith)
- **[[DS-MARINE]]** — Marine conditions (relevant for Aran Islands deployment)
- **[[DS-CALENDAR]]** — Day type, season, holiday flags

## Equipment Dependencies
- [[EQ-TEMP-SENSOR]] — Temperature measurement
- [[EQ-PYRANOMETER]] — Solar irradiance measurement
- [[EQ-ANEMOMETER]] — Wind speed/direction

## Related Series
- feeds_into: [[T-Series]], [[R-Series]], [[O-Series]], [[G-Series]], [[C-Series]]
- validated_by: [[D-Series]] (data quality checks on sensor feeds)
- aggregated_by: [[C-Series]] (W_SCORE used in optimisation objective)

## AI Context
W-Series provides the primary environmental context. In code, always pass W-series outputs as a `weather_context` struct with named fields matching the symbol column above. Never hardcode weather values — always draw from the DS-WEATHER dataset pipeline.
