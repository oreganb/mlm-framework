# MLM SD Generator — Synthetic Data Generator v0.4.0

The MLM Synthetic Data Generator (SD Generator) produces
hyper-realistic synthetic Irish building portfolios for
training, testing, and validating the MLM framework.

## What it generates

- 5,000 buildings (configurable)
- 8,760 hourly records per building (one full year)
- 47 features per record
- 43.8 million data points total at full scale

All outputs are statistically calibrated against six
real Irish national datasets — see the main README for
the source dataset table.

## Installation

```bash
cd generator
pip install -r requirements.txt
```

Python 3.9 or later is required. DuckDB is used for
streaming generation at scale — do not remove it from
requirements.txt or generation above ~500 buildings
will run out of memory.

## Usage

### Small run (development and testing)

```bash
python -m mlm_sd_generator --n_buildings 100 \
    --output_dir ../output/
```

### Full dataset (5,000 buildings)

```bash
python -m mlm_sd_generator --n_buildings 5000 \
    --output_dir ../output/ \
    --stream
```

The `--stream` flag activates DuckDB streaming mode.
Peak RAM with streaming: ~4.2 GB. Without streaming,
5,000 buildings will OOM on standard hardware.

### All options

| Flag | Default | Description |
|------|---------|-------------|
| `--n_buildings` | 100 | Number of buildings to generate |
| `--output_dir` | `./output/` | Output directory |
| `--stream` | False | Enable DuckDB streaming |
| `--seed` | 42 | Random seed for reproducibility |
| `--typology` | all | Filter by typology (residential, commercial, industrial) |
| `--county` | all | Filter by Irish county |
| `--ber_band` | all | Filter by BER band (A1–G) |

## Generation pipeline

The generator runs seven dependency-ordered steps:

| Step | Description | Output |
|------|-------------|--------|
| 1 | Building stock synthesis | Typology, vintage, BER band per building |
| 2 | Weather generation | 8,760-hour records per building per county |
| 3 | Occupancy synthesis | Markov chain schedules (0/1/2 states) |
| 4 | Thermal and HVAC modelling | Indoor temperature, HVAC energy, flex headroom |
| 5 | DER and storage generation | PV generation (Perez model), battery SoC, EV |
| 6 | Market price assignment | I-SEM day-ahead and intraday, carbon intensity |
| 7 | Load and trading calculation | Total load, P2P trading potential, revenue |

Steps follow the MLM inter-series execution order:
`D → W → O → M → L → R → T → B → G → C → P → K → F`

## Output format

Each building is output as a directory:
