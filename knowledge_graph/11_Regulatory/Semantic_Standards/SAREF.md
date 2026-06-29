---
type: regulatory_node
category: Semantic_Standard
id: SAREF
full_title: Smart Applications REFerence (SAREF) Ontology
citation: ETSI TS 103 264 and extensions
status: active
version: 4.1.1
tags: [mlm, regulatory, semantic-standard, saref, iot, interoperability, smart-energy]
aliases: [SAREF, SAREF4ENER, SAREF4BLDG, Smart Applications Reference Ontology]
---

# SAREF — Smart Applications REFerence Ontology

## Overview
ETSI's reference ontology for smart home/building IoT devices and services. SAREF classes and properties map directly onto MLM dataset schemas and equipment nodes, enabling semantic interoperability with EU data spaces.

## SAREF Core Classes Mapped to MLM

| SAREF Class | MLM Equivalent | MLM Node |
|---|---|---|
| saref:Device | Equipment node | [[04_Assets/Asset-Temperature-sensors-room-flow-return]] etc. |
| saref:Sensor | Sensor asset | [[04_Assets/Asset-Whole-house-smart-meter]], [[04_Assets/Asset-CO-sensors]] |
| saref:Actuator | Controllable DER | [[04_Assets/Asset-Heat-pump-ASHP-GSHP]], [[04_Assets/Asset-Battery-storage]] |
| saref:Measurement | Sensor reading | DS-SENSOR `value`, `unit`, `timestamp` |
| saref:Property | Measured property | DS-SENSOR `sensor_type` |
| saref:Command | Dispatch signal | C01 output: `P_HP`, `P_bat_chg`, `P_bat_dis` |
| saref:Task | MLM model task | W01 task = `ForecastSolarIrradiance` |
| saref:Service | MLM series | C-Series = `FlexibilityOptimisationService` |

## SAREF4ENER Extension (Smart Energy)

| SAREF4ENER Class | MLM Equivalent |
|---|---|
| s4ener:PowerProfile | C01 dispatch schedule (P_HP[k], P_bat[k] for k in H) |
| s4ener:Slot | Each 15-min timestep in C01 horizon |
| s4ener:AlternativesGroup | C03 determinization scenarios |
| s4ener:EnergyMedium | Electricity, heat (thermal), DHW |

## SAREF4BLDG Extension (Buildings)

| SAREF4BLDG Class | MLM Equivalent |
|---|---|
| s4bldg:Building | DS-BUILDING record |
| s4bldg:BuildingSpace | T01/T12 thermal zone |
| s4bldg:PhysicalObject | Equipment assets |
| s4bldg:HVAC | T02/T03 heat pump models |

## MLM KG ↔ SAREF Alignment
The MLM KG Turtle export (`mlm_kg_v4.ttl`) uses the `mlm:` namespace. A SAREF alignment file can map:
- `mlm:Model` → `saref:Service`
- `mlm:Asset` → `saref:Device`
- `mlm:Dataset` → `saref:Measurement` collection
- `mlm:feeds_into` → `saref:hasOutput` / `saref:hasInput`

## Related Standards
- [[11_Regulatory/Semantic_Standards/OpenADR]] — DR protocol that SAREF wraps
- [[11_Regulatory/Semantic_Standards/Brick_Schema]] — Building topology complement
