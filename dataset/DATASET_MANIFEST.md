# EIG MLM Dataset Manifest

**Generator version:** `0.4.0`  
**Generated:** 2026-06-23 09:26 UTC  
**Scale:** 5,000 buildings · 2021 calendar year · 15-min intervals  
**Seed:** 20210101 (deterministic)  

## Validation summary

Gate result: **19 PASS / 0 FAIL / 2 WARN / 4 SKIP-N/A**

All gates at full FAIL severity. No threshold was lowered to manufacture a pass.

## Real data anchors

- SEAI National Energy Balance 2021 (sector demand TWh — D2 CLOSED)
- ENTSO-E/SEMOpx I-SEM 2021 day-ahead prices, 8,759h gap-free (D7 CLOSED)
- Open-Meteo 2021 archive (weather reanalysis)
- SEAI BER Public Search (1.4M Irish building energy ratings)
- CSO Small Area Population Statistics 2022 (socioeconomic enrichment)
- Pobal HP Deprivation Index 2022 (deprivation enrichment)
- CSO National Travel Survey 2022 (EV trip model)
- ENTSO-E A65 2021 actual total load, IE(SEM) (G-DEMAND-SHAPE reference)

## Table inventory

| Table | Layer | Cat # | Cat name | Rows | Size (MB) | dataClass |
|-------|-------|-------|----------|------|-----------|-----------|
| `asset_classification.parquet` | entity | 10 | asset_classification | 109,008 | 1.4 | COMPUTED:109,008 |
| `assets.parquet` | entity | 11 | asset | 109,008 | 1.2 | CALIBRATED:109,008 |
| `ber_ratings.parquet` | entity | 06 | building_benchmark | 5,000 | 0.1 | CALIBRATED:5,000 |
| `building_classification.parquet` | entity | 01 | building_classification | 5,000 | 0.1 | COMPUTED:5,000 |
| `building_fabric.parquet` | entity | 05 | building_fabric | 5,000 | 0.3 | CALIBRATED:5,000 |
| `buildings.parquet` | entity | 02 | building | 5,000 | 0.3 | CALIBRATED:5,000 |
| `devices.parquet` | entity | 36 | device | 109,008 | 1.7 | SYNTHETIC:109,008 |
| `features.parquet` | entity | 41 | feature | 30,770 | 0.3 | COMPUTED:30,770 |
| `occupancy.parquet` | entity | 03 | occupancy | 5,000 | 3.2 | CALIBRATED:5,000 |
| `rd_profile.parquet` | entity | 52/55 | response_derivative / response_profile | 9,242 | 0.3 | COMPUTED:9,242 |
| `sensors.parquet` | entity | 36 | device | 304,479 | 3.7 | SYNTHETIC:304,479 |
| `spaces.parquet` | entity | 04 | space | 85,329 | 1.5 | SYNTHETIC:85,329 |
| `ts_building_energy.parquet` | timeseries | 38 | data | 174,720,000 | 7486.1 | COMPUTED:174,720,000 |
| `ts_high_freq.parquet` | timeseries | 38 | data | 186,076,800 | 2869.2 | COMPUTED:186,076,800 |
| `ts_price_signal.parquet` | timeseries | 21/38 | tariff / data | 35,037 | 0.3 | REAL:35,037 |
| `ts_pv_generation.parquet` | timeseries | 38 | data | 13,453,440 | 83.9 | COMPUTED:13,453,440 |
| `ts_response_derivative.parquet` | timeseries | 52 | response_derivative | 148,833,602 | 2872.9 | COMPUTED:148,833,602 |

**Total rows:** 523,900,723  
**Total size (parquet):** 13327 MB  
  synthetic_world/: 14 MB  
  timeseries/: 13312 MB  

## Data class breakdown (all tables combined)

| dataClass | Rows | Meaning |
|-----------|------|---------|
| REAL | 35,037 | Measured or directly published data (prices, BER ratings) |
| CALIBRATED | 129,008 | Synthetic data anchored to real statistics |
| COMPUTED | 523,237,862 | Derived by physics model from other data (RD, features) |
| SYNTHETIC | 498,816 | Procedurally generated with no real anchor |

## What this dataset is and is not

**What it is:** A complete 5,000-building, full-calendar-year 2021 synthetic energy dataset covering the physical-through-RD catalogue subset (cats 01–06, 10–11, 36–38, 41, 52, 55) of the EIG MLM framework. All six Response Derivative services are generated (RDD-ELS, RDD-EXP, RDD-STO, RDD-THM, RDD-SHFT, RDD-PRC). Sector demand anchors are real (SEAI 2021). The I-SEM price signal is real (ENTSO-E/SEMOpx 2021, 8,759h, gap-free). BER ratings are drawn from the real SEAI BER database. The G-RD-REALISED gate passes on physics alone — no dispatch cap, 0 strict ceiling breaches.

**What it is not:** It does not cover the abstract/semantic catalogues (P7 model layer, P9 decision, P10 learning, P11 application, P12 semantic) — those are SPEC-ONLY and require live model training, RL deployment, and ontology instantiation beyond a generation run. It does not cover the market-settlement catalogues (23 contract, 25 trade, 27 settlement, 53 response_behaviour, 56 response_event) — those are NEEDS-EXTERNAL, requiring real transactional data from live market participation. CER smart-meter matching is in stub mode (SYNTHETIC-fallback) — ISSDA licence pending; no CER traces are fabricated. G-DEMAND-SHAPE (mixed fleet vs A65 all-sector): r=0.584 — same-year 2021, vintage resolved. G-DEMAND-SHAPE-RESIDENTIAL (residential gross vs A65): r=-0.479 — real residential shape defect: fleet gross demand peaks 22-23h UTC (overnight EV charging + late heating dominant) while national reference peaks 18h UTC (commercial-driven). Open item: investigate overnight EV charging fraction and residential occupancy peak timing. CER licence (ISSDA pending) will provide ground-truth residential reference for this gate.
