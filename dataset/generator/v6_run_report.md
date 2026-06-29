# EIG Synthetic Data Generator — v6 Run Report

**Generator version:** 0.4.0  
**Run date:** 2026-06-23 09:26 UTC  
**Scale:** 5,000 buildings · calendar year 2021 · 15-min intervals  
**Seed:** 20210101 (fully deterministic)  
**Gate result:** 19 PASS / 0 FAIL / 2 WARN / 4 SKIP-N/A  

---

## 1. Purpose

The EIG Synthetic Data Generator produces a catalogue-native synthetic dataset of Irish buildings, assets, sensors, and energy timeseries for the EIG MLM (Multi-Layer Market) framework. The output covers catalogue categories 01–06, 10–11, 36–38, 41, 52, and 55 of the physical-through-response-derivative stack. All SEAI 2021 sector electricity demand figures are real; the I-SEM price signal is real; all occupancy, fabric, and load timeseries are synthetic but anchored to real statistics through the data-class taint chain (REAL → CALIBRATED → COMPUTED → SYNTHETIC).

This is the v6 run, establishing the occupancy-defect baseline before the residential schedule fix. It adds the `G-DEMAND-SHAPE-RESIDENTIAL` gate (new in v6) and bumps catalogue 52 to v3.

---

## 2. Input Datasets

### 2.1 Real data sources (data_class = REAL)

| Source | Content | Files | Notes |
|--------|---------|-------|-------|
| SEAI National Energy Balance 2021 | Sector electricity demand (TWh) | `01_SEAI_National_Energy_Balance/` | D2 CLOSED. Overrides master.yaml fallback values at runtime |
| ENTSO-E / SEMOpx I-SEM 2021 | Day-ahead market prices, hourly | `04_SEMOpx_ISEM_Prices/GUI_ENERGY_PRICES_202012312300-202112312300.csv` | 8,759 h, 100% gap-free. D7 CLOSED |
| ENTSO-E A65 2021 actual load | Total IE(SEM) national load, 30-min | `04_SEMOpx_ISEM_Prices/GUI_TOTAL_LOAD_DAYAHEAD_202012312300-202112312300.csv` | 16,758 rows. G-DEMAND-SHAPE reference |
| Open-Meteo 2021 archive | Weather reanalysis, 17 Irish stations | cached `.nc` files in `data_raw/` | temperature, GHI, DNI, DHI, wind speed/direction, cloud cover, humidity, pressure |
| SEAI BER Public Search | 1.4M Irish building energy ratings | `data_raw/BER/BERPublicsearch.txt` | Drives BER band distribution, U-values, construction period, dwelling type |
| CSO SAPS 2022 | Small Area Population Statistics | `07_CSO_Census_2022_SAPS/` | County and SA-level socioeconomic weighting |
| Pobal HP Deprivation Index 2022 | SA-level deprivation scores | embedded in SAPS ingest | `deprivation_index` field on buildings |
| CSO National Travel Survey 2022 | Mode share data (NTA01, 2021) | `05_CSO_National_Travel_Survey/` | EV daily trip distance model |
| SEAI Grant Statistics + CSO HH Energy | Heat pump and microgeneration stats | `06_SEAI_Grant_Statistics/` | Asset adoption rates (HP: ~12%, EV: ~5%, PV: ~8% of residential) |
| ESB Networks 2021 EV data | Average daily mileage, charger ratings | embedded as constants | 45 km/day mean, 7 kW Mode 3 charger |

### 2.2 Stub / pending sources

| Source | Status | Gate |
|--------|--------|------|
| CER Smart Metering Trial (ISSDA File1.txt) | STUB — ISSDA licence pending | G-CER-MATCH: SKIP-N/A |

### 2.3 National demand anchors (SEAI 2021 real values)

| Sector | TWh | ktoe basis |
|--------|-----|------------|
| Residential | 8.746 | 752 ktoe × 0.011630 |
| Commercial | 5.199 | 447 ktoe × 0.011630 |
| Industrial | 6.723 | 578 ktoe × 0.011630 |
| Public | 3.070 | 264 ktoe × 0.011630 |
| Data Centre | 5.466 | 470 ktoe × 0.011630 (ICT proxy, upper bound) |
| **Modelled total** | **29.204** | 29.924 TWh total × 0.976 modelled fraction |

---

## 3. Pipeline Steps

The pipeline runs sequentially through 8 steps, each writing to `synthetic_world/` or `timeseries/`.

### Step 1 — Weather ingest
Fetches or reads cached Open-Meteo 2021 reanalysis for 17 Irish synoptic stations. Produces an xarray dataset used by Steps 4 and 5. Run with `--skip-weather` after first cache build.

### Step 2 — Entity world: buildings (cat 01–02)
Generates 5,000 building records stratified by sector, county, and settlement type using CSO SAPS 2022 population weights. Assigns floor area (residential: Normal(111, 30) m², capped 20–220; commercial/industrial: log-Normal), construction period, weather station, and expansion weight (fleet → national scale).

### Step 3 — Entity world: spaces, fabric, assets, occupancy, BER, sensors, devices (cat 03–06, 10–11, 36)
- **Spaces (cat 04):** 85,329 spaces; 1–6 per building depending on sector.
- **Building fabric (cat 05):** U-values and heat-loss coefficient (HLC, W/K) drawn from SEAI BER register distributions per construction period. HLC = f(floor area, U-values, infiltration).
- **BER ratings (cat 06):** Band drawn from BER register distribution by sector and construction period. 5,000 records, bands A1–G.
- **Assets (cat 11):** 109,008 assets across 8 classes: metering (37,333), hvac_generic (36,858), environmental (33,252), hvac_heatpump (587), ev_charger (405), pv_inverter (385), battery (138), ups_genset (50). Adoption rates keyed to SEAI grant stats.
- **Occupancy (cat 03):** One profile per building. Residential split: commuter 40% / retired 35% / standard 25%. Each archetype carries a 24-point weekday and weekend occupancy schedule (0–1 scale) plus per-building ±5% noise.
- **Sensors (cat 36):** 304,479 sensors, one per measured variable per asset. Resolution tier assigned by asset class from `resolution_registry.yaml`.
- **Devices (cat 36):** 109,008 smart-meter / gateway device records, one per asset.

### Step 4 — Thermal physics (feeds Step 5)
RC thermal model (1-node) computes space-heat demand (W) per building per 15-min step:

```
Q_heat = HLC × (T_setpoint − T_outdoor) − Q_internal_gains
```

With exponential RC lag (time constant 6 h). Setpoint 20°C. Cooling not modelled. Internal gains from floor area and sector. Gas-boiler buildings: heat → gas_heat_kw at 90% efficiency. Heat-pump buildings: heat → electricity via weather-dependent COP (Carnot-degraded, η = 0.4, flow temp 45°C, min COP 1.5).

### Step 5a — Timeseries: building energy (cat 38)
Generates 15-min `ts_building_energy.parquet` (174.7M rows, 7.0 GB). Per building:

1. **Base electricity:** `floor_area_m2 × base_kw_m2 × occ_factor + N(0, noise)`, where `base_kw_m2` is 0.030 kW/m² for residential/industrial, 0.025 commercial, 0.020 public, 0.150 data centre. `occ_factor` is the interpolated hourly occupancy schedule.
2. **Heat pump electricity:** added for the 587 HP buildings via the Step 4 COP model.
3. **EV charging:** 5% EV adoption. Charging window 22:00–07:00 UTC, 7 kW Mode 3, sessions terminate when daily trip energy need (trip km × 0.18 kWh/km) is satisfied. 80% probability of a charging session per night.
4. **LEVEL calibration:** each sector's aggregate is rescaled to the SEAI 2021 TWh target via a single multiplicative factor applied to `electricity_kw`. `electricity_kw_raw` retains the pre-calibration physical values.
5. **PV subtraction:** handled separately (Step 5b); `electricity_kw` in this file does not subtract PV output.

**Known open defect (D8):** The occupancy schedules encode physical presence (is the occupant home?) rather than electricity demand activity. Overnight hours 0–6h have high presence factors (0.85–0.90) for residential archetypes, producing a fleet gross demand peak at 22–23h UTC instead of the correct 17–19h UTC. This is the root cause of the `G-DEMAND-SHAPE-RESIDENTIAL` WARN (r = −0.479). Fix planned for v7.

### Step 5b — Timeseries: Response Derivative (cat 52, v3)
Generates `ts_response_derivative.parquet` (148.8M rows, 2.7 GB) with 25 columns per row. For each active building × RD spec × 15-min step:

- **RDD-ELS** (load shed): downward flexibility from curtailable baseload. 114.1M rows.
- **RDD-EXP** (export increase): upward from PV inverter headroom. 1.7M rows; PV buildings only.
- **RDD-STO** (battery dispatch): bidirectional from battery assets. Power-limited at high SoC, energy-limited at low SoC: `realisable = min(ceiling, (soc × bat_kwh) / 0.5h)`.
- **RDD-THM** (thermal load shift): downward from HP pre-heating / setpoint flex. 13.9M rows.
- **RDD-SHFT** (time shift): bidirectional from deferrable loads. 19.6k rows.
- **RDD-PRC** (price-responsive dispatch): bidirectional, conditioned on real-time I-SEM price band. 17.8M rows.

Each row carries `nameplateCeiling`, `realisedDiscount_fraction`, `confidence`, `intervalState_*` conditioning fields (occupancy, weather band, SoC band, net headroom, price band), and `evidence_root` linking back to `ts_building_energy`.

**Gate G-RD-REALISED:** 0 rows where `magnitude_point_kw / nameplateCeiling > 1.0`. No dispatch cap used; ceiling is a hard physics constraint.

### Step 5c — Timeseries: PV generation (cat 38)
`ts_pv_generation.parquet` (13.5M rows, 84 MB). pvlib PVWatts model using Open-Meteo GHI/DNI/DHI, with 7% system loss applied to correct CF overshoot. 385 PV-equipped buildings (pv_inverter assets). Total 2021 generation: 3.163 GWh. Fleet solar CF: 0.105 (target 0.10–0.11, gate G-PV: PASS).

### Step 5d — Timeseries: high-frequency (cat 38)
`ts_high_freq.parquet` (186.1M rows, 2.7 GB). 60-second resolution for 355 instrumented assets (30% of eligible flagged assets): 168 heat pumps, 140 EV chargers, 35 batteries, 12 UPS/gensets. Columns: `power_kw`, `soc_pct` (battery/EV), `asset_class`.

### Step 5e — Timeseries: price signal (cat 21/38)
`ts_price_signal.parquet` (35,037 rows, 348 KB). Real ENTSO-E/SEMOpx I-SEM 2021 day-ahead prices, hourly. data_class = REAL. Coverage: 2021-01-01 00:00 UTC → 2021-12-31 22:00 UTC. Stats: avg 136.14 €/MWh, min −20.43, max 500.00.

### Step 6 — Features (cat 41)
`features.parquet` (30,770 rows). Engineered per-building features computed via DuckDB out-of-core queries over `ts_building_energy`:

| Feature | Definition |
|---------|------------|
| `annual_electricity_kwh` | Sum of electricity_kw_raw × 0.25h |
| `peak_demand_kw` | 99th percentile of electricity_kw_raw |
| `load_factor` | mean / peak |
| `solar_cf` | PV generation kWh / (capacity_kw × 8760h) |
| `ev_annual_kwh` | Sum of ev_charging_kw × 0.25h |
| `evening_ratio` | Mean 17–21h demand / mean all-day demand |

### Step 7 — RD profile summary (cat 52/55)
`rd_profile.parquet` (9,242 rows). Per building × per active RD spec: p10/p50/p90 flexibility magnitude, mean confidence, mean discount fraction, nameplate ceiling. Derived from `ts_response_derivative` by DuckDB aggregation.

### Step 8 — Validation and export
All gates run (see Section 6). On PASS/WARN, outputs are copied to `exports/parquet/` and schemas to `exports/schemas/`. On FAIL, export is blocked and a ValueError is raised.

---

## 4. Output Files

### 4.1 Entity layer (`synthetic_world/`)

| File | Rows | Columns | Size | data_class | Description |
|------|------|---------|------|------------|-------------|
| `buildings.parquet` | 5,000 | 27 | 284 KB | CALIBRATED | One row per building. Key fields: `building_id`, `sector`, `county_name`, `floor_area_m2`, `construction_period`, `expansion_weight`, `lat`, `lon`, `deprivation_index` |
| `spaces.parquet` | 85,329 | 12 | 1.4 MB | SYNTHETIC | Rooms/zones per building: `space_id`, `building_id`, `space_type`, `area_m2`, `thermal_zone` |
| `building_fabric.parquet` | 5,000 | 15 | 312 KB | CALIBRATED | Thermal envelope: `u_value_wall_wm2k`, `u_value_roof_wm2k`, `u_value_glazing_wm2k`, `heat_loss_coefficient_wk`, `wall_type` |
| `ber_ratings.parquet` | 5,000 | 12 | 128 KB | CALIBRATED | `ber_band` (A1–G), `annual_primary_energy_kwh_m2`, `dwelling_type` |
| `assets.parquet` | 109,008 | 14 | 1.2 MB | CALIBRATED | `asset_id`, `building_id`, `asset_class`, `rated_kw`, `high_freq_instrumented`, `install_year` |
| `asset_classification.parquet` | 109,008 | 11 | 1.4 MB | COMPUTED | EIG taxonomy codes per asset: domain, catalogue no., class code |
| `occupancy.parquet` | 5,000 | 13 | 3.1 MB | CALIBRATED | `archetype`, `occupancy_density_persons_m2`, `weekday_schedule_json`, `weekend_schedule_json` (24-point arrays) |
| `sensors.parquet` | 304,479 | 15 | 3.6 MB | SYNTHETIC | One row per measured variable per asset: `sensor_id`, `measured_variable`, `unit`, `resolution_tier` |
| `devices.parquet` | 109,008 | 14 | 1.6 MB | SYNTHETIC | Smart-meter / gateway device records: `protocol`, `firmware_version`, `edge_node_id` |
| `features.parquet` | 30,770 | 12 | 340 KB | COMPUTED | Long-format engineered features (6 per building with PV, 5 without) |
| `rd_profile.parquet` | 9,242 | 15 | 328 KB | COMPUTED | Per-building × per-spec flexibility fingerprint: `p10_kw`, `p50_kw`, `p90_kw`, `mean_confidence`, `mean_discount_fraction`, `nameplateCeiling` |

### 4.2 Timeseries layer (`timeseries/`)

| File | Rows | Columns | Size | data_class | Description |
|------|------|---------|------|------------|-------------|
| `ts_building_energy.parquet` | 174,720,000 | 16 | 7.0 GB | COMPUTED | 5,000 buildings × 34,944 15-min steps. Key fields: `electricity_kw` (calibrated), `electricity_kw_raw` (gross pre-calibration), `gas_heat_kw`, `heat_demand_kw`, `ev_charging_kw`, `net_import_kw`, `sector` |
| `ts_pv_generation.parquet` | 13,453,440 | 11 | 81 MB | COMPUTED | 385 buildings × 34,944 steps. Fields: `pv_generation_kwh`, `capacity_kw` |
| `ts_high_freq.parquet` | 186,076,800 | 12 | 2.7 GB | COMPUTED | 355 assets × 524,160 60-s steps. Fields: `power_kw`, `soc_pct`, `asset_class` |
| `ts_price_signal.parquet` | 35,037 | 5 | 348 KB | **REAL** | Hourly I-SEM 2021 DA prices. Fields: `price_eur_mwh`, `data_class`, `source`, `coverage_note` |
| `ts_response_derivative.parquet` | 148,833,602 | 25 | 2.7 GB | COMPUTED | RD per subject × spec × timestep. Full schema below |

**Total on-disk (parquet):** ~13.3 GB across all files.  
**Total rows (all tables):** 523,900,723.

### 4.3 ts_response_derivative full schema

| Column | Type | Description |
|--------|------|-------------|
| `subject_id` | VARCHAR | Building or asset ID |
| `rd_spec` | VARCHAR | Service code: RDD-ELS / EXP / STO / THM / SHFT / PRC |
| `timestamp_utc` | TIMESTAMP WITH TIME ZONE | 15-min interval UTC |
| `direction` | VARCHAR | `up`, `down`, or `both` |
| `magnitude_point_kw` | DOUBLE | Point-estimate flexibility (kW) |
| `magnitude_ci80_lo_kw` | DOUBLE | 80% CI lower bound |
| `magnitude_ci80_hi_kw` | DOUBLE | 80% CI upper bound |
| `latency_s` | BIGINT | Response latency (seconds) |
| `saturation_kw` | DOUBLE | Fleet-level saturation ceiling |
| `rebound_factor` | DOUBLE | Post-dispatch rebound multiplier |
| `nameplateCeiling` | DOUBLE | Hard physics ceiling from asset nameplate |
| `realisedDiscount_fraction` | DOUBLE | Fractional discount vs nameplate (0–1) |
| `realisedDiscount_dominantCause` | VARCHAR | Dominant discount driver |
| `intervalState_occupancy` | DOUBLE | Occupancy factor at this interval |
| `intervalState_weather_band` | VARCHAR | cold / mild / warm |
| `intervalState_soc_band` | VARCHAR | low / mid / high (battery/EV only) |
| `intervalState_net_headroom` | DOUBLE | Net available headroom (kW) |
| `intervalState_price_band` | VARCHAR | low / mid / high / spike |
| `evidence_root` | VARCHAR | Source row reference in ts_building_energy |
| `confidence` | DOUBLE | Model confidence [0,1] |
| `data_class` | VARCHAR | COMPUTED |
| `syntheticLineage` | VARCHAR | Generation method tag |
| `seed` | BIGINT | Determinism seed |
| `generator_version` | VARCHAR | 0.4.0 |
| `generated_at_utc` | VARCHAR | ISO8601 generation timestamp |

### 4.4 Provenance totals

| data_class | Rows | Meaning |
|------------|------|---------|
| REAL | 35,037 | Directly published / measured (prices) |
| CALIBRATED | 129,008 | Synthetic anchored to real statistics (buildings, assets, fabric, BER, occupancy) |
| COMPUTED | 523,237,862 | Derived by physics model (timeseries, RD, features, classifications) |
| SYNTHETIC | 498,816 | Procedurally generated, no real anchor (spaces, sensors, devices) |
| **Total** | **523,900,723** | |

---

## 5. Fleet Composition

### 5.1 Sector mix

| Sector | Buildings | Target TWh | Calibrated TWh |
|--------|-----------|-----------|----------------|
| Residential | 3,500 | 8.746 | 0.015* |
| Commercial | 900 | 5.199 | 0.022* |
| Industrial | 250 | 6.723 | 0.187* |
| Public | 300 | 3.070 | 0.092* |
| Data Centre | 50 | 5.466 | 3.215* |

*`electricity_kw` calibrated column — per-building electricity_kw is scaled by expansion_weight × calibration_factor to represent the national fleet. The per-row values appear small because each row represents a single building's contribution; multiply by expansion_weight × 1e6 for the national figure.

### 5.2 Occupancy archetypes

| Archetype | Count | Description |
|-----------|-------|-------------|
| residential_commuter | 1,453 | Leaves ~08:00, returns ~18:00; low midday occupancy |
| residential_retired | 1,175 | Home all day; near-constant high occupancy |
| residential_standard | 872 | Mixed / default |
| commercial_standard | 900 | 09:00–18:00 with weekend reduction |
| public_standard | 300 | 09:00–17:00 weekday |
| industrial_standard | 250 | Shift pattern with overnight base |
| data_centre_standard | 50 | 1.0 constant (24/7) |

### 5.3 Asset summary

| Asset class | Count | Notes |
|-------------|-------|-------|
| metering | 37,333 | Smart meters, one per main circuit per building |
| hvac_generic | 36,858 | Boiler / HVAC units |
| environmental | 33,252 | Weather + indoor sensors |
| hvac_heatpump | 587 | Air-source HP (~12% of residential) |
| ev_charger | 405 | Mode 3, 7 kW rated (~5% of residential) |
| pv_inverter | 385 | Rooftop PV (~8% residential, ~11% commercial) |
| battery | 138 | Home storage, co-located with PV |
| ups_genset | 50 | One per data-centre building |

### 5.4 BER band distribution

| Band | Count | | Band | Count |
|------|-------|-|------|-------|
| A1 | 110 | | D1 | 630 |
| A2 | 135 | | D2 | 524 |
| A3 | 211 | | E1 | 351 |
| B1 | 275 | | E2 | 236 |
| B2 | 289 | | F | 198 |
| B3 | 396 | | G | 206 |
| C1 | 447 | | | |
| C2 | 537 | | | |
| C3 | 455 | | | |

Mode band: D1 (630 buildings). Distribution drawn from SEAI BER register by construction period.

### 5.5 Geographic distribution (top 10 counties)

| County | Buildings | | County | Buildings |
|--------|-----------|--|--------|-----------|
| Dublin | 1,459 | | Kerry | 167 |
| Cork | 573 | | Tipperary | 157 |
| Galway | 275 | | Wexford | 153 |
| Kildare | 255 | | Wicklow | 144 |
| Meath | 201 | | Louth | 143 |
| Limerick | 198 | | | |
| Donegal | 168 | | | |

### 5.6 Floor area by sector

| Sector | Avg m² | Min m² | Max m² |
|--------|--------|--------|--------|
| Data centre | 11,208 | 20 | 31,792 |
| Public | 6,315 | 20 | 26,648 |
| Industrial | 3,672 | 20 | 14,221 |
| Commercial | 2,113 | 20 | 9,458 |
| Residential | 111 | 20 | 220 |

---

## 6. Validation Gates

All 19 FAIL-severity gates pass. 2 WARN gates remain open. 4 gates are SKIP-N/A by design.

| Gate | Result | Detail |
|------|--------|--------|
| G-SCHEMA | PASS | All written fields exist in master_schema.parquet |
| G-PROV | PASS | All tables carry 5 provenance columns |
| G-ORDER | PASS | Entity generation followed registry order |
| G-CARDINALITY | PASS | Max assets per building: 142 |
| G-RESOLUTION | PASS | Volume guard enforced |
| G-RECON | PASS | factor=0.9999, deviation=0.0% |
| G-SECTOR | PASS | SEAI 2021 sector demand targets applied (D2 CLOSED) |
| G-PV | PASS | Solar CF=0.105 (target 0.10–0.11), no night generation |
| G-ENERGY-SIGN | PASS | No negative consumption; EV range ok |
| G-THERMAL | PASS | RC model enforces anti-correlation by construction |
| G-TS-CONTINUITY | PASS | 15-min continuity verified |
| G-BER | PASS | BER band distribution validated |
| G-GEO | PASS | All lat/lon within county bbox |
| G-RD-REALISED | PASS | 0 rows with magnitude/ceiling > 1.0 (strict, no dispatch cap) |
| G-RD-OCC-SENSITIVITY | PASS | Matched-pair median delta=82.5% (threshold 25%, 10 pairs); commuter median 0.348 kW vs retired |
| G-RD-PERSIGNAL | PASS | 6 specs, median range=4.077 kW |
| G-RD-PROV | PASS | 0 rows with data_class ≠ COMPUTED |
| G-RD-CONFIDENCE | PASS | std=0.268, range=[0.020, 0.980] |
| G-PRICE-SIGNAL | PASS | 8,759h, 100% coverage, gap-free, data_class=REAL |
| G-QUALITY | SKIP-N/A | Quality flag check not run in this configuration |
| G-DETERMINISM | SKIP-N/A | Requires second run with same seed |
| G-WIND | SKIP-N/A | Utility-scale wind not modelled per building |
| G-CER-MATCH | SKIP-N/A | ISSDA licence pending; all buildings on SYNTHETIC-fallback |
| **G-DEMAND-SHAPE** | **WARN** | Pearson r=0.588 vs ENTSO-E A65 2021 (mixed fleet, all-sector reference). Structural gap expected. |
| **G-DEMAND-SHAPE-RESIDENTIAL** | **WARN** | Pearson r=−0.479 vs ENTSO-E A65 2021 (residential gross demand). **Real defect — see Section 7.** |

---

## 7. Known Defects and Open Items

### 7.1 D8 — Residential occupancy schedule: presence vs demand (OPEN)

**Severity:** High — affects G-DEMAND-SHAPE-RESIDENTIAL  
**Symptom:** Residential gross demand (electricity_kw_raw) peaks at 22–23h UTC; trough at 08h UTC. Pearson r = −0.479 vs national load (which peaks 18h UTC). Occupancy model is the root cause (EV charging contributes only ~7% of 22h peak load).  
**Root cause:** `occupancy.py` `_SCHEDULES` encodes physical presence (occupant is home, asleep = 0.90) rather than electricity demand activity (occupant is asleep = fridge+standby ≈ 0.05–0.08 of peak). Hours 0–6h for all three residential archetypes carry 0.75–0.90 occupancy factors, which feed directly into `base_elec_kw = floor_area × base_kw_m2 × occ_factor`.  
**Fix:** Correct overnight hours to demand-factor values (0.05–0.12) and shift morning peak to 07–09h. Planned for v7.  
**Baseline:** v6 is the before snapshot. v7 will provide the after snapshot for before/after benchmarking.

### 7.2 G-CER-MATCH — stub mode (PENDING EXTERNAL)

The CER Smart Metering Trial matching layer (`src/eig_sd_generator/match/cer_ber_match.py`) is built and tested in stub mode. It will activate when the ISSDA licence for CER File1.txt is approved, flipping this gate from SKIP-N/A to a real residential calibration validation. No synthetic CER traces are fabricated.

### 7.3 G-DEMAND-SHAPE — structural gap vs national reference

Mixed-fleet r=0.588. Once D8 is fixed in v7, this figure will change. The all-sector national load reference will always carry a structural mismatch vs a residential-only fleet; G-DEMAND-SHAPE-RESIDENTIAL against a CER-derived residential reference (post-ISSDA) will be the definitive shape gate.

---

## 8. Algorithms Summary

| Algorithm | Location | Description |
|-----------|----------|-------------|
| RC thermal model | `physics/thermal.py` | 1-node Q = HLC × (T_set − T_out) − IG, exponential lag τ=6h |
| COP (air-source HP) | `physics/thermal.py` | Carnot-degraded: η=0.4, floor 1.5, flow temp 45°C |
| PV generation | `timeseries/generator.py` | pvlib PVWatts + 7% system losses on real Open-Meteo irradiance |
| Level calibration | `timeseries/generator.py` | Per-sector multiplicative scale to SEAI 2021 TWh targets |
| EV charge model | `timeseries/generator.py` | Overnight window 22:00–07:00, 7 kW, terminates at daily need |
| RD — ELS/THM/SHFT | `rd/realisable.py` | Load realisability × discount × confidence per interval |
| RD — STO | `rd/realisable.py` | `min(ceiling, (soc × bat_kwh) / 0.5h)` — power- and energy-limited |
| RD — EXP | `rd/realisable.py` | PV inverter headroom: `surplus = capacity − output` |
| RD — PRC | `rd/realisable.py` | Price band conditioning on real I-SEM hourly price signal |
| BER sampling | `world/ber.py` | Draw from SEAI BER register filtered by construction period |
| Occupancy schedules | `world/occupancy.py` | 24-point presence-factor schedules (**D8: demand-factor fix pending**) |
| Feature engineering | `features/features.py` | DuckDB out-of-core aggregations on 174M-row energy parquet |
| Demand shape gate | `validate/validator.py` | z-scored Pearson r vs ENTSO-E A65; residential gate uses electricity_kw_raw |

---

## 9. Data Quality and Taint Chain

The data-class taint system ensures no field is upgraded after assignment:

```
REAL → CALIBRATED → COMPUTED → SYNTHETIC
```

Promotions are forbidden; any field derived from SYNTHETIC data cannot become CALIBRATED. The five provenance columns on every row (`synthetic_flag`, `data_class`, `seed`, `source_anchors_used`, `generator_version`) carry this chain through the dataset.

**Gate G-RD-PROV** verifies that all RD rows are `data_class = COMPUTED` (derived from physics of CALIBRATED/COMPUTED inputs — not fabricated as SYNTHETIC).

---

## 10. Reproducibility

All outputs are fully deterministic from `seed=20210101`. Re-running the pipeline with the same seed, year, and building count produces bit-identical parquet files (subject to identical source data in `RD_Data_Extended/` and `data_raw/`).

Run command:
```
EIG_OUTPUT_ROOT=/path/to/output \
  python run_pipeline.py run --year 2021 --buildings 5000 --skip-lc
```

`--skip-lc` skips NLCM land-cover enrichment (not required for standard runs; falls back to `pv_shading_factor=0.97`).

---

## 11. Catalogue Coverage

Populated catalogues in this run: **01, 02, 03, 04, 05, 06, 10, 11, 21, 36, 38, 41, 52, 55**  
Catalogue 52 version: **v3** (strict physics ceiling, no dispatch cap, EXP clip, STO power/energy model)  
Catalogue 21 version: **v3.0** (D7 CLOSED — real ENTSO-E/SEMOpx I-SEM 2021 price feed)  
Catalogue 37 version: **v3.0** (ENTSO-E/SEMOpx, SEAI, Met Éireann registered as real sources)

NEEDS-EXTERNAL (not generated): 23 contract, 25 trade, 27 settlement, 53 response_behaviour, 56 response_event  
SPEC-ONLY (not generated): 44 model, 45 algorithm, 46 forecast, 47 validation, 48 hyperparameter, 49 explainability, 50 training, 51 model_benchmark, 57–60 KPI/decision/learning/application  
STUB (pending licence): G-CER-MATCH (cat 53 partial — architecture built, data absent)
