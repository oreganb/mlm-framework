---
type: catalogue
ontology: EIG-MLM
catalogue_no: 9
catalogue_id: EIG-CAT-009
entity: weather_climate
band: physical
status: active
tags: [eig-mlm, catalogue, band:physical, entity:weather_climate]
aliases: ["EIG-CAT-009", "Catalogue 09", "weather_climate"]
---

# CAT-09 — Weather Climate

> **EIG-CAT-009** · band: **Physical & Spatial Stack** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Weather & Climate Catalogue

Catalogue ID prefix: **EIG-WX** · Catalogue number: 09 · Version 1.0 · Status: Active

---

## 1. Definition

The Weather & Climate Catalogue defines every weather and climate entity used by the EIG MLM framework: weather stations, forecast providers, weather variables, climate classifications and the climatic profiles of climate zones. It is the single authority for what weather data exists, where it comes from, how it is named, and how it may be used in forecasting, simulation, normalisation and Response Derivative conditioning. Climate zone *identity* lives in the Geographic Catalogue (EIG-GEO-CZ-*); their *climatic profiles and data bindings* are defined here.

## 2. Scope

Applies to: energy forecasting, building physics simulation, degree-day normalisation, demand/generation modelling, RD weather-conditioning, synthetic weather generation, flexibility event planning, comfort modelling, and digital twin boundary conditions. Territory: Ireland (full station register) + any project-specific extension.

## 3. Classification Structure

Four entity classes:

| Class code | Class | Definition |
|---|---|---|
| WXC-ST | Weather Station | Physical or virtual point source of observed weather data |
| WXC-FP | Forecast Provider | Organisation/service issuing weather forecasts |
| WXC-VAR | Weather Variable | A named, unit-fixed measurable atmospheric quantity |
| WXC-CC | Climate Classification | A scheme assigning climates to classes (Köppen, CIBSE, TM59 overheating, etc.) |

Station subtypes: synoptic (hourly, full suite), climatological (daily), rainfall, airport (METAR), buoy/marine, mesonet/private, building_local (on-site sensor cluster elevated to station status), virtual (gridded reanalysis point, e.g. ERA5 cell), satellite_derived.

Forecast provider subtypes: nwp_global (ECMWF, GFS), nwp_regional (HARMONIE-AROME via Met Éireann), commercial_api, ensemble, nowcast, solar_specialist, wind_specialist, internal_ml (EIG in-house forecast models — these are *providers* here and *models* in catalogue 44).

## 4. Hierarchy

```
ClimateClassification (WXC-CC)
 └── ClimateClass (e.g. Köppen Cfb)
      └── ClimateZone profile (binds EIG-GEO-CZ-* to classes + design data)
WeatherStation (WXC-ST) ──locatedIn──▶ EIG-GEO entity
 └── StationInstrument (per-variable sensing record)
ForecastProvider (WXC-FP)
 └── ForecastProduct (variable set × horizon × resolution × update cycle)
WeatherVariable (WXC-VAR)   [flat register, no hierarchy]
```

## 5. Field Groups & Fields

### 5.1 Weather Station (EIG-WX-ST-NNNN)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | stationID | string | — | EIG-WX-ST-NNNN | M | Unique station ID |
| 2 | stationName | string | — | UTF-8 | M | Name (e.g. Cork Airport) |
| 3 | stationSubtype | enum | — | §3 station subtypes | M | Station class |
| 4 | operator | string | — | EIG-ORG ID | M | Operating organisation (Met Éireann, EIG, …) |
| 5 | wmoID / icaoID | string | — | WMO 5-digit / ICAO 4-char | O | International identifiers |
| 6 | geoID | string | — | EIG-GEO-* (GL-03…GL-06) | M | Located-in geographic entity |
| 7 | lat / lon | decimal(9,6) | deg | WGS84 | M | Coordinates |
| 8 | elevationM | decimal(6,1) | m | −10…1100 (IE) | M | Station elevation |
| 9 | exposureClass | enum | — | E1_sheltered, E2_normal, E3_exposed, E4_severe | M | Wind/rain exposure |
| 10 | variablesObserved | array | — | EIG-WX-VAR IDs | M | Observed variable set |
| 11 | temporalResolution | enum | — | 1min, 10min, 1h, 3h, daily | M | Native observation cadence |
| 12 | recordStartDate / recordEndDate | date | — | ISO 8601; end null if active | M/O | Period of record |
| 13 | dataSourceID | string | — | EIG-DS-* | M | Ingestion source (forward ref → data_source 37) |
| 14 | qcLevel | enum | — | raw, provider_qc, eig_qc_l1, eig_qc_l2 | M | Quality-control level of feed |
| 15 | servesDegreeDayRegion | enum | — | DD-IE-N/E/S/W/MID | C (synoptic) | DD region this station anchors |
| 16 | status | enum | — | active, dormant, closed, planned | M | Lifecycle |
| 17 | version/owner/createdDate/modifiedDate/source | — | — | as Geographic §5.5 | M | Audit block |

### 5.2 Weather Variable (EIG-WX-VAR-NNN) — closed register

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | variableID | string | — | EIG-WX-VAR-NNN | M | Unique variable ID |
| 2 | variableName | string | — | snake_case canonical name | M | e.g. air_temperature_2m |
| 3 | cfStandardName | string | — | CF conventions name | C | e.g. air_temperature |
| 4 | unit | string | — | UCUM | M | Canonical unit (storage unit) |
| 5 | validMin / validMax | decimal | unit | physical plausibility bounds | M | Range check bounds |
| 6 | temporalStatistic | enum | — | instantaneous, mean, sum, max, min | M | What a value represents |
| 7 | verticalReference | string | — | e.g. 2m_agl, 10m_agl, surface, plane_of_array | M | Measurement height/plane |
| 8 | qcRules | array | — | rule IDs (range, step, persistence, spatial) | M | QC rule binding |
| 9 | derivedFrom | array | — | variableIDs + formula ref | C (derived vars) | Derivation lineage |

### 5.3 Forecast Provider (EIG-WX-FP-NNN) and Forecast Product

Provider: providerID, providerName, subtype, operator(EIG-ORG), apiEndpointRef, licenceClass(open, commercial, internal), slaRef(EIG-CON), status, audit block.

Product (child, EIG-WX-FP-NNN-PNN): productID, variables(EIG-WX-VAR list), horizonHours(1–384), temporalResolution, spatialResolution(point, grid_km), updateCycleHours, ensembleMembers(int, 1=deterministic), latencyMinutes, skillBaselineRef(→ validation 47), formats(grib2, netcdf, json_api, csv).

### 5.4 Climate Classification (EIG-WX-CC-NN) and Climate Zone Profile

Classification: ccID, schemeName(koppen_geiger, cibse_weather_years, tm59_overheating, ashrae_169, eurocode_wind_snow), version, classes(closed list per scheme), authority.

Climate Zone Profile (EIG-WX-CZP-*, 1:1 with EIG-GEO-CZ-*): czpID, geoClimateZoneID(M), koppenClass(Cfb for all IE zones), hdd15_5 / cdd18 (annual mean + P10/P90), designTempHeatingC, designTempCoolingC, designWindSpeedMs (Eurocode), snowLoadZone, drivingRainIndex, solarAnnualKWhM2 (GHI), diffuseFraction, tryFileRef (Test Reference Year dataset ref → EIG-DSP), dsyFileRef (Design Summer Year), futureScenarioRefs (array: {rcp/ssp scenario, epoch, fileRef}).

## 6. Controlled Vocabularies

### 6.1 Canonical weather variable register (closed; extension = minor version)

| variableID | variableName | Unit | Statistic | Notes |
|---|---|---|---|---|
| EIG-WX-VAR-001 | air_temperature_2m | Cel | instantaneous | −30…40 (IE bounds) |
| EIG-WX-VAR-002 | dew_point_temperature_2m | Cel | instantaneous | ≤ VAR-001 |
| EIG-WX-VAR-003 | relative_humidity_2m | % | instantaneous | 0–100 |
| EIG-WX-VAR-004 | wind_speed_10m | m/s | mean(10min) | 0–60 |
| EIG-WX-VAR-005 | wind_direction_10m | deg | mean(10min) | 0–360 |
| EIG-WX-VAR-006 | wind_gust_10m | m/s | max | ≥ VAR-004 |
| EIG-WX-VAR-007 | precipitation_amount | mm | sum | per interval |
| EIG-WX-VAR-008 | atmospheric_pressure_msl | hPa | instantaneous | 920–1060 |
| EIG-WX-VAR-009 | global_horizontal_irradiance | W/m2 | mean | 0–1200 |
| EIG-WX-VAR-010 | diffuse_horizontal_irradiance | W/m2 | mean | ≤ VAR-009 |
| EIG-WX-VAR-011 | direct_normal_irradiance | W/m2 | mean | 0–1000 |
| EIG-WX-VAR-012 | sunshine_duration | h | sum | 0–24/day |
| EIG-WX-VAR-013 | cloud_cover_total | okta | instantaneous | 0–8 |
| EIG-WX-VAR-014 | visibility | m | instantaneous | 0–80000 |
| EIG-WX-VAR-015 | soil_temperature_10cm | Cel | instantaneous | ground-source models |
| EIG-WX-VAR-016 | snow_depth | cm | instantaneous | 0–100 |
| EIG-WX-VAR-017 | heating_degree_days_15_5 | K.d | sum | derived from VAR-001 |
| EIG-WX-VAR-018 | cooling_degree_days_18 | K.d | sum | derived |
| EIG-WX-VAR-019 | wet_bulb_temperature | Cel | instantaneous | derived (psychrometric) |
| EIG-WX-VAR-020 | sol_air_temperature | Cel | instantaneous | derived; fabric models |
| EIG-WX-VAR-021 | wind_chill_index | Cel | instantaneous | derived |
| EIG-WX-VAR-022 | plane_of_array_irradiance | W/m2 | mean | derived per surface (tilt/azimuth params) |

### 6.2 QC rule vocabulary
qc_range (validMin/Max), qc_step (max inter-step delta), qc_persistence (max identical run), qc_spatial (neighbour consistency, max z-score), qc_solar_geometry (GHI ≤ clear-sky model × 1.1), qc_internal_consistency (dew point ≤ dry bulb; gust ≥ mean).

### 6.3 Gap-fill method vocabulary
none, linear_interp (≤ 3 h), neighbour_station (regression-adjusted), reanalysis_substitute (ERA5), climatology_fill (flag-only, never for training data).

## 7. Applicability Rules

1. Every located entity consumes weather via its geographic chain: geoID → primaryWeatherStationID; overrides require a justification record and apply per twin, not per model.
2. Building-local stations (subtype building_local) apply only to their own building/site; they never anchor a DD region.
3. Forecast products apply per horizon class: nowcast (0–6 h) → real-time control; short (6–72 h) → scheduling/trading; medium (3–10 d) → maintenance/flex planning; climate scenarios → retrofit/design only, never operations.
4. Derived variables (VAR-017…022) are computed centrally once per QC level; models must not re-derive locally.
5. TRY/DSY files apply to simulation only; observed/forecast series apply to operations; mixing within one model run is prohibited unless the model is a hybrid declared in catalogue 44.

## 8. Validation Rules

| # | Rule |
|---|---|
| V1 | stationID/variableID/providerID unique, pattern-valid |
| V2 | Station geoID resolves; station coordinates within geo boundary (100 m tolerance) |
| V3 | variablesObserved ⊆ variable register; each with an instrument record |
| V4 | Every DD region anchored by exactly one active synoptic station (servesDegreeDayRegion unique per region) |
| V5 | All observed values pass qc_range before storage; failures flagged not dropped |
| V6 | Forecast product horizonHours ≥ temporalResolution; updateCycle ≤ horizon |
| V7 | CZ profile exists for every EIG-GEO-CZ-* record (1:1, enforced both ways at Phase 14) |
| V8 | Derived variable lineage acyclic; formula ref resolves |
| V9 | recordEndDate ≥ recordStartDate; closed stations cannot anchor DD regions |
| V10 | Gap-fill method recorded per filled value; climatology_fill values excluded from model training datasets |

## 9. Relationships to Other Catalogues

| Direction | Catalogue | Relationship |
|---|---|---|
| ▶ depends on | geographic (08) | station.geoID; CZ identity |
| ▶ depends on | organisation (15) | operator, provider |
| ▶ depends on | data_source (37) | feed ingestion definitions |
| ◀ consumed by | building (02), fabric (05) | design temps, sol-air, driving rain |
| ◀ consumed by | benchmark (06, 12, 51, 65) | degree-day normalisation |
| ◀ consumed by | forecast (46) | every energy forecast declares weather product inputs |
| ◀ consumed by | feature (41) | weather feature derivations |
| ◀ consumed by | response_derivative (52), sensitivity (54) | weather-conditioned RDs |
| ◀ consumed by | synthetic_data (42) | synthetic weather generation seeds (TRY + stochastic) |
| ◀ consumed by | digital_twin (32) | boundary conditions |

## 10. Benchmarking Requirements

Weather normalisation is mandatory for cross-period or cross-zone benchmark comparison: HDD15.5 for heating metrics, CDD18 for cooling, GHI for solar yield. Each benchmark release (EIG-BBM/ABM) records the weather vintage (station, period, gap-fill summary) used for correction. Forecast providers are themselves benchmarked: per product, per variable, per horizon — MAE/RMSE/CRPS against observation, registered in model_benchmark (51) with the provider as the "model".

## 11. Dataset Requirements

Weather datasets stored long-format: (stationID, variableID, timestampUTC, value, qcFlag, gapFillMethod). Timestamps UTC; local-time conversion only at presentation. Storage: Parquet partitions by stationID/year under `EIG_WX_ROOT/`. Every model training dataset embeds a weather lineage block: stations, variables, period, QC level, gap-fill stats (% filled per variable; reject if > 5% for training).

## 12. Feature Requirements

Registered weather features (FEA-WX-*): lagged temperatures (1h…168h), rolling means (3/24/168 h), HDD/CDD daily, solar position (zenith/azimuth — deterministic, computed not observed), POA irradiance per declared surface, wind power density (½ρv³), psychrometric set (wet bulb, enthalpy), weather forecast features at decision horizons (forecast value + ensemble spread as uncertainty feature), anomaly features (departure from climatology percentile).

## 13. Model Requirements

Every weather-driven model declares: input variables (by variableID), source class (observed/forecast/TRY), station resolution method, acceptable gap-fill methods, and forecast horizon alignment (model horizon ≤ product horizon). Forecast-fed operational models MUST be validated against both perfect-foresight (observed) and realistic (forecast) inputs; the skill gap is recorded in validation (47).

## 14. KPI Requirements

Weather-dependent KPIs declare normalisation basis (none, hdd, cdd, ghi, composite). Comfort KPIs bind to indoor variables but record concurrent outdoor context (VAR-001, 009) for exceedance attribution. Forecast skill KPIs: MAE, RMSE, MBE, CRPS (ensembles), hit rate for event thresholds (e.g. frost, wind > 20 m/s).

## 15. Response Derivative Requirements

RDs MUST be conditioned on outdoor temperature band (≤ 0, 0–5, 5–10, 10–15, 15–20, > 20 °C) and may additionally condition on irradiance band and wind band where the responding asset is weather-coupled (heat pumps: temperature; PV curtailment: irradiance). Weather forecast uncertainty propagates into RD confidence intervals; the ensemble spread feature is a mandatory input to RD reliability scoring for horizons > 6 h.

## 16. Decision Requirements

Decisions using weather forecasts record: providerID, productID, issueTime, horizon at decision, and the forecast values consumed — enabling post-hoc attribution of bad decisions to forecast error vs model error. Weather-triggered events (frost protection, storm curtailment, overheating pre-cooling) bind trigger thresholds to variableIDs with hysteresis bands defined in control_strategy (62).

## 17. Ontology Mapping

Classes: WeatherStation, WeatherVariable, WeatherObservation, ForecastProvider, ForecastProduct, ClimateClassification, ClimateZoneProfile.
Object properties: observesVariable, locatedIn (→ geo), anchorsDegreeDayRegion, providesProduct, forecastsVariable, profilesClimateZone (→ EIG-GEO-CZ), derivedFromVariable, qualityControlledBy.
Datatype properties: stationID, variableID, unit, validMin, validMax, horizonHours, updateCycleHours, hdd15_5, designTempHeatingC.
Alignment: SOSA/SSN (sosa:Sensor=station instrument, sosa:ObservableProperty=variable, sosa:Observation), SAREF saref:Measurement, QUDT units, CF standard names as skos:exactMatch.

## 18. Knowledge Graph Mapping

Nodes: WeatherStation, WeatherVariable, ForecastProvider, ForecastProduct, ClimateZoneProfile.
Relationships: OBSERVES (station→variable), LOCATED_IN (station→geo), ANCHORS (station→DDRegion), PROVIDES (provider→product), FORECASTS (product→variable), PROFILES (czp→cz), DERIVED_FROM (variable→variable), FEEDS (product/station→model).
Keys: stationID, variableID, providerID. Time-series values live in the lakehouse, not the KG; KG holds identity + lineage.

## 19. Digital Twin Mapping

Building/Asset/District twins bind a WeatherContext: resolved station, active forecast products per horizon, current QC status. Twin simulation mode binds TRY/DSY; operational mode binds live feeds. Station twins exist for EIG-operated stations (subtype building_local, mesonet) carrying instrument health, calibration dates and uptime.

## 20. Governance

Owner: EIG Data Management Lead. RL/Model stakeholders consulted on variable register changes (feature breakage). Provider/product changes require forecast-skill re-baselining before operational cutover. Variable register is closed-controlled: additions minor, semantic changes major with deprecation aliasing (old name → new ID maintained ≥ 2 major versions).

## 21. Versioning

Catalogue semver. Station metadata changes: patch. New stations/products: minor. Variable semantics, unit changes, CZ profile redefinition: major + cascade notice to feature, forecast, RD catalogues.

## 22. Example Records

```text
stationID: EIG-WX-ST-0007 | stationName: Cork Airport | subtype: synoptic | operator: EIG-ORG-0012 (Met Éireann)
wmoID: 03955 | geoID: EIG-GEO-IE-CTY-0004 | lat: 51.8421 | lon: -8.4858 | elevationM: 155.0
exposureClass: E2_normal | variablesObserved: VAR-001..014 | temporalResolution: 1h
recordStartDate: 1962-01-01 | servesDegreeDayRegion: DD-IE-S | qcLevel: provider_qc | status: active
```

```text
stationID: EIG-WX-ST-0021 | stationName: Mace Head | subtype: synoptic | exposureClass: E4_severe
geoID: EIG-GEO-IE-CTY-0011 | servesDegreeDayRegion: DD-IE-W | status: active
note: anchors EIG-GEO-CZ-IE-ISL profile; primary station for ODEON Inis Mór fleet
```

```text
providerID: EIG-WX-FP-001 | providerName: Met Éireann | subtype: nwp_regional
product: EIG-WX-FP-001-P01 | model: HARMONIE-AROME | variables: [001,003,004,005,007,009]
horizonHours: 54 | temporalResolution: 1h | spatialResolution: 2.0 km grid | updateCycleHours: 6
ensembleMembers: 1 | latencyMinutes: 90 | formats: [grib2, json_api]
```

```text
czpID: EIG-WX-CZP-ISL | geoClimateZoneID: EIG-GEO-CZ-IE-ISL | koppenClass: Cfb
hdd15_5: {mean: 1980, p10: 1820, p90: 2150} | designTempHeatingC: -1 | designWindSpeedMs: 31
drivingRainIndex: 92.4 | solarAnnualKWhM2: 1010 | tryFileRef: EIG-DSP-TRY-ISL-2020
futureScenarioRefs: [{scenario: SSP2-4.5, epoch: 2050, fileRef: EIG-DSP-FWX-ISL-245-2050}]
```

## 23. Completion Criteria

Complete when: all Met Éireann synoptic + relevant climatological stations registered; variable register frozen at v1 (VAR-001…022); every DD region anchored; CZ profiles 1:1 with geographic CZ register; all forecast products in operational use registered with skill baselines; every weather-consuming model in catalogue 44 resolves its inputs here with zero unregistered variables.
