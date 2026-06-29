---
type: dataset_node
ontology: MLM
dataset_id: DS-WEATHER
dataset_family: Foundational
generation_order: 1
status: active
tags: [mlm, dataset, foundational, weather]
aliases: [DS-WEATHER, Weather Dataset]
---

# DS-WEATHER — Weather & Atmospheric Dataset

## Purpose
Primary environmental dataset. Feeds all W-Series models and, through them, the entire MLM stack. Must be generated or acquired before any energy model can run.

## Source Types
- **Historical:** Met Éireann station data (primary for Aran Islands / ODEON)
- **Forecast:** ECMWF ERA5 / Met Éireann NWP
- **On-site:** Local sensors (W17 microclimate correction)

## Schema

```json
{
  "record_id": "string (UUID)",
  "timestamp": "datetime (ISO 8601, UTC)",
  "site_id": "string (building or station ID)",
  "resolution_min": "int (15 or 60)",

  // Temperature
  "temperature_outdoor": "float (°C, range: -15 to 45)",
  "temperature_dewpoint": "float (°C)",
  "temperature_wet_bulb": "float (°C)",

  // Solar
  "irradiance_ghi": "float (W/m², 0–1200)",
  "irradiance_dni": "float (W/m², 0–1000)",
  "irradiance_dhi": "float (W/m², 0–500)",
  "irr_sigma": "float (W/m², NWP ensemble spread)",

  // Wind
  "wind_speed": "float (m/s, 0–50)",
  "wind_direction": "float (degrees, 0–360)",
  "wind_gust": "float (m/s)",

  // Atmosphere
  "pressure_hpa": "float (hPa, 950–1050)",
  "humidity_rh": "float (%, 0–100)",
  "cloud_cover_oktas": "int (0–8)",
  "visibility_m": "float (m, 0–50000)",
  "fog_flag": "boolean",

  // Precipitation
  "precipitation_mm_h": "float (mm/h, 0–100)",
  "rain_flag": "boolean",
  "storm_flag": "boolean",

  // Data quality
  "source": "string (MetEireann|ECMWF|OnSite|Synthetic)",
  "quality_flag": "string (PASS|SUSPECT|FAIL)",
  "imputed_flag": "boolean"
}
```

## Generation Weightings (Synthetic)
When generating synthetic weather for ODEON/Aran Islands context:
- Atlantic maritime climate: mild, wet, windy
- Temperature range: 4–18°C typical annual
- Solar irradiance: 800–1000 kWh/m²/yr (lower than continental Europe)
- Wind: predominant SW, Beaufort 4–6 typical

## Models That Read This Dataset

| Model | Fields Used |
|---|---|
| [[W01]] | irradiance_ghi, irradiance_dni, irradiance_dhi, irr_sigma |
| [[W02]] | temperature_outdoor, temperature_dewpoint |
| [[W03]] | wind_speed, wind_direction, wind_gust |
| [[W04]] | precipitation_mm_h, rain_flag |
| [[W05]] | cloud_cover_oktas |
| [[W06]] | humidity_rh, temperature_dewpoint |
| [[W07]] | pressure_hpa |
| [[W10]] | storm_flag, wind_gust |
| [[W11]] | visibility_m, fog_flag |

## Relationships
- `feeds_into` [[W-Series]] (all models)
- `validated_by` [[D01]] (data quality gate)
- `corrected_by` [[W17]] (microclimate bias correction)
---

# DS-BUILDING — Building Physical Parameters Dataset

```json
{
  "building_id": "string (UUID)",
  "site_id": "string (O-CEI pilot site reference)",

  // Classification
  "building_type": "Detached|Semi-Detached|Terraced|Apartment",
  "construction_year": "int",
  "construction_era": "pre-1980|1980-2000|2001-2010|post-2010",
  "floor_area_m2": "float",
  "volume_m3": "float",
  "zone_count": "int (1–10)",

  // Fabric
  "insulation": "boolean",
  "insulation_type": "Cavity|External|Internal|Uninsulated",
  "u_value_wall": "float (W/m²K)",
  "u_value_roof": "float (W/m²K)",
  "u_value_floor": "float (W/m²K)",
  "u_value_window": "float (W/m²K)",
  "window_area_m2": "float",
  "airtightness_ach": "float (air changes/hour at 50Pa)",

  // Thermal model parameters (derived)
  "thermal_resistance": "float (K/W) — R_env for T01",
  "thermal_capacitance": "float (J/K) — C_zone for T01",
  "thermal_time_constant": "float (hours) — τ = R×C/3600",

  // Orientation & solar
  "orientation": "N|S|E|W|NE|NW|SE|SW",
  "roof_pitch_deg": "float",
  "shading_factor": "float (0–1, 0=no shade)",

  // EPC
  "epc_rating": "A1|A2|A3|B1|B2|B3|C1|C2|C3|D|E|F|G",
  "epc_score": "float (0–100)",
  "ber_number": "string (Irish BER cert number)",

  // Systems installed
  "heating_type": "HeatPump|GasBoiler|OilBoiler|ElectricStorage",
  "secondary_heating": "boolean",
  "has_pv": "boolean",
  "has_battery": "boolean",
  "has_ev_charger": "boolean",
  "has_smart_meter": "boolean",

  // Synthetic population weights (for validation)
  "population_weight": "float (sum to 1 across dataset)"
}
```

## Generation Weightings
| Attribute | Distribution |
|---|---|
| Building type | Detached 40%, Semi-D 30%, Terraced 20%, Apt 10% |
| Construction era | Pre-1980 30%, 1980-2000 40%, 2001-10 20%, Post-2010 10% |
| Insulation | Yes 70%, No 30% |
| EPC | A1-A3 15%, B1-B3 35%, C1-C3 30%, D+ 20% |
| Heating | HP 30%, Gas 40%, Oil 20%, Electric 10% |

## Models That Read This Dataset
| Model | Fields Used |
|---|---|
| [[T01]] | thermal_resistance, thermal_capacitance |
| [[T11]] | All fabric fields (archetype lookup) |
| [[T19]] | epc_rating, u_values |
| [[R01]] | orientation, shading_factor (via DS-PV) |
| [[K02]] | epc_rating, heating_type |
| [[O01]] | zone_count, floor_area_m2 |
