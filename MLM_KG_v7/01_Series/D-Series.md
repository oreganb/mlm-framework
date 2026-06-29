---
type: series_index
ontology: MLM
series_id: D
series_title: Data Quality & Imputation
layer: ai-ml
status: active
tags: [mlm, series:D, layer:ai-ml, data-quality, imputation]
aliases: [D-Series, Data Quality Series]
sub_model_count: 8
id_range: D01–D08
---

# D-Series — Data Quality & Imputation

## Master Formula
$$x_t = f(x_{t-1}, x_{t-2}, \ldots) + \epsilon$$
Missing or noisy sensor data imputed via regression, KNN, or Expectation-Maximization.

## Sub-Model Registry

| ID | Title | Key Output |
|---|---|---|
| [[D01]] | Weather Data QA Gate | quality_flag, x_clean |
| [[D02]] | PV Data Validator | pv_plausibility_flag |
| [[D03]] | Battery Data Validator | bat_consistency_flag |
| [[D04]] | Sensor Fault Detection | fault_type, sensor_id |
| [[D05]] | Smart Meter QA | meter_quality_flag |
| [[D06]] | Missing Data Imputation | x_imputed, imputed_flag |
| [[D07]] | Outlier Detection | outlier_flag, outlier_score |
| [[D08]] | Model Drift Detection | drift_flag, retrain_trigger |

## Note
D-Series executes **first** in every timestep cycle. All other series receive D-Series validated data. A `quality_flag = FAIL` on any critical input must halt the pipeline and trigger [[D06]] imputation or alert.