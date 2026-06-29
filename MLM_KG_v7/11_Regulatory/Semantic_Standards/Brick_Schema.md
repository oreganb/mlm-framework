---
type: regulatory_node
category: Semantic_Standard
id: Brick_Schema
full_title: Brick Schema — Building Metadata Ontology
citation: Fierro et al., BuildSys 2018; brickschema.org
status: active
version: 1.3
tags: [mlm, regulatory, semantic-standard, brick, building-metadata, sensors, hvac, points]
aliases: [Brick Schema, Brick, brickschema, Building Metadata Ontology]
---

# Brick Schema

## Overview
Brick is a metadata schema for describing building systems, sensors, and control points in a machine-readable way. Complements SAREF at the building level. Relevant for ODEON sensor tagging and FLEXUS building model ingestion.

## Brick Classes Mapped to MLM

| Brick Class | MLM Equivalent | MLM Node |
|---|---|---|
| brick:Building | DS-BUILDING record | [[03_Datasets/DS-BUILDING]] |
| brick:Zone | T01/T12 thermal zone | [[02_Models/T_Models/T01]], [[02_Models/T_Models/T12]] |
| brick:Air_Temperature_Sensor | Milesight AM319 temp output | [[04_Assets/Asset-Temperature-sensors-room-flow-return]] → DS-SENSOR.indoor_temp |
| brick:CO2_Sensor | Milesight AM319 CO₂ output | [[04_Assets/Asset-CO-sensors]] → DS-SENSOR.co2_ppm |
| brick:Occupancy_Sensor | Browan Tabs PIR | [[04_Assets/Asset-Window-Door-open-closed-sensors]] → DS-OCCUPANCY.pir_reading |
| brick:HVAC_Zone | T03 heat pump zone | [[02_Models/T_Models/T03]] |
| brick:Heat_Pump | EQ-HP asset | [[04_Assets/Asset-Heat-pump-ASHP-GSHP]] |
| brick:Photovoltaic_Panel | EQ-PV asset | [[04_Assets/Asset-PV-inverter]] |
| brick:Battery | EQ-BATTERY asset | [[04_Assets/Asset-Battery-storage]] |
| brick:Electric_Meter | EQ-SMART-METER | [[04_Assets/Asset-Whole-house-smart-meter]] |

## Brick Points (Sensor/Control) → MLM Fields

| Brick Point | DS-SENSOR Field | MLM Model |
|---|---|---|
| brick:Zone_Air_Temperature_Sensor | `indoor_temp` | [[02_Models/T_Models/T01]] T_in |
| brick:Outside_Air_Temperature_Sensor | `temperature_outdoor_local` | [[02_Models/W_Models/W02]] T_out |
| brick:Solar_Irradiance_Sensor | `pyranometer_reading` | [[02_Models/W_Models/W01]] G_POA bias correction |
| brick:Active_Power_Sensor | `power_w` | [[02_Models/L_Models/L01]] P_net |
| brick:Battery_Energy_Storage_System | Battery asset | [[02_Models/B_Models/B01]] |

## ODEON Sensor Graph
For each ODEON building, a Brick graph could be auto-generated from DS-BUILDING + DS-SENSOR schemas, enabling:
- Automated FLEXUS sensor discovery
- Cross-building sensor queries via SPARQL
- Integration with digital twin platforms

## Related Standards
- [[11_Regulatory/Semantic_Standards/SAREF]] — Complementary IoT ontology
- [[11_Regulatory/Semantic_Standards/OpenADR]] — Control signal layer
