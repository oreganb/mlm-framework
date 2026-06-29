---
type: dataset_node
ontology: MLM
dataset_id: DS-CALENDAR
dataset_family: Foundational
status: active
tags: [mlm, dataset, foundational]
aliases: [DS-CALENDAR, Calendar & Event Database]
---

# DS-CALENDAR — Calendar & Event Database

## Description
Irish public holidays, school terms, local Aran Islands events, sports tournaments, seasonal markers. Used for occupancy priors and load pattern modulation.

## Key Fields
`date, day_type, holiday_flag, event_type, season_code, school_term_flag`

## Resolution
Daily

## Models That Use This Dataset
- [[W13]]
- [[O02]]
- [[O12]]

## Generation Order
**Foundational** — generated/acquired in Phase 1

## Relationships
- `draws_from` by: [[W13]], [[O02]], [[O12]]
- `validated_by` [[D-Series]]

## AI Context
When implementing models that use DS-CALENDAR, always access fields by exact name as listed above. Never infer field names from the dataset title. Check `imputed_flag` before using any reading — imputed data must be treated with reduced confidence.
