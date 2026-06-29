---
type: series_index
ontology: MLM
series_id: F
series_title: Meta-Learning, Transfer & Privacy
layer: ai-ml
status: active
tags: [mlm, series:F, layer:ai-ml, federated, transfer, privacy]
aliases: [F-Series, Federated Series]
sub_model_count: 6
id_range: F01–F06
---

# F-Series — Meta-Learning, Transfer & Privacy

## Master Formula
$$\theta_{global} = \frac{1}{N} \sum_{i=1}^{N} \theta_i$$
Federated averaging combines local model weights from edge/far-edge nodes. Protects data privacy.

## Sub-Model Registry

| ID | Title | Key Output |
|---|---|---|
| [[F01]] | Federated Averaging Model | θ_global per model type |
| [[F02]] | Transfer Learning Adapter | θ_adapted for new site |
| [[F03]] | Differential Privacy Layer | ε-DP guarantee |
| [[F04]] | Cross-Site Calibration | bias_correction_factors |
| [[F05]] | Model Versioning & Rollback | model_version, rollback_flag |
| [[F06]] | Data Residency Controller | data_stays_local_flag |

## ODEON Deployment Note
For the Aran Islands, F01 federated averaging runs across all 70+ buildings. Each Jetson Nano holds local model weights (θ_i); the FLEXUS cloud layer aggregates to θ_global and redistributes. **No raw data leaves the island.**