# Sample Entity Tables

These are the 10 distributable entity Parquet files from the
EIG MLM synthetic dataset (generator v0.4.0, seed 20210101).

They contain the building population, assets, occupancy profiles,
fabric parameters, BER ratings, spaces, devices, and sensors —
everything except the timeseries (too large for GitHub).

## Files

| File | Rows | Size | Data class | Catalogue |
|------|------|------|-----------|-----------|
| `buildings.parquet` | 5,000 | 0.3 MB | CALIBRATED | 02 |
| `building_classification.parquet` | 5,000 | 0.1 MB | COMPUTED | 01 |
| `building_fabric.parquet` | 5,000 | 0.3 MB | CALIBRATED | 05 |
| `ber_ratings.parquet` | 5,000 | 0.1 MB | CALIBRATED | 06 |
| `occupancy.parquet` | 5,000 | 3.2 MB | CALIBRATED | 03 |
| `asset_classification.parquet` | 109,008 | 1.4 MB | COMPUTED | 10 |
| `assets.parquet` | 109,008 | 1.2 MB | CALIBRATED | 11 |
| `devices.parquet` | 109,008 | 1.7 MB | SYNTHETIC | 36 |
| `sensors.parquet` | 304,479 | 3.7 MB | SYNTHETIC | 36 |
| `spaces.parquet` | 85,329 | 1.5 MB | SYNTHETIC | 04 |

Column schemas for each file are in `../schemas/`.

## Quick load

```python
import pandas as pd

b = pd.read_parquet("buildings.parquet")
print(b.columns.tolist())
print(b.head())
```
