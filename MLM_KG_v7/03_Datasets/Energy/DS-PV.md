---
type: dataset_node
ontology: MLM
dataset_id: DS-PV
dataset_family: Energy
status: active
tags: [mlm, dataset, energy]
aliases: [DS-PV, PV Panel Specifications Database]
---

# DS-PV — PV Panel Specifications Database

## Description
Per-building PV panel technical specifications: area, STC efficiency, temperature coefficient, NOCT, tilt, azimuth, install date, rated capacity. Irish residential PV installations, SEAI MSS grant scheme data.

## Key Fields
`building_id, panel_area_m2, efficiency_stc, temp_coeff_beta, noct_temp, azimuth_deg, tilt_deg, install_date, rated_capacity_kwp`

## Resolution
Static, updated on retrofit

## Models That Use This Dataset
- [[R01]]
- [[R03]]
- [[R04]]

## Generation Order
**Energy** — generated/acquired in Phase 3

## Relationships
- `draws_from` by: [[R01]], [[R03]], [[R04]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-PV, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
