---
type: application_node
ontology: MLM
status: active
tags: [mlm, application, arc, ml-pipeline]
aliases: [ARC Tool, ARC ML Tool]
---

# ARC Tool — Automatic ML Pipeline

## Overview
ARC (Automatic Response Configuration) Tool is the data-agnostic ML pipeline for automatic target variable identification, learnability-based candidate ranking, and leakage-aware scoring. Developed by EIG.

## MLM Role
- Selects and ranks target variables across all MLM series
- Trains ML models for L-Series (load forecasting), O-Series (occupancy), B-Series (SoC prediction)
- Provides leakage-aware feature selection for all supervised ML models

## Key Functions
- `ml2_target_selection_probe()` — automatic target variable selection
- Learnability scoring with penalty magnitudes
- Schema-agnostic pipeline across DS-SMART-METER, DS-SENSOR, DS-BATTERY

## Related
- [[D-Series]] — Data quality preprocessing before ARC
- [[B01]] — ARC trains LSTM SoC correction model
- [[L10]] — ARC trains load forecasting LSTM
- [[06_Weighting/Weight Registry]] — ARC-calibrated weights (W-B01-LSTMWIN)
