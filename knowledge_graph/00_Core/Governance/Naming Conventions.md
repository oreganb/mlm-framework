---
type: kg_governance
ontology: MLM
status: active
tags: [mlm, governance, naming, conventions]
aliases: [Naming Conventions, MLM ID Scheme]
---

# Naming Conventions

## Node ID Scheme

All nodes use the pattern: `[SERIES][NUMBER]` (e.g., W01, T07, B03)

| Field | Format | Example |
|---|---|---|
| Series ID | Single letter, uppercase | `W`, `T`, `B` |
| Model number | Two-digit zero-padded | `01`, `07`, `13` |
| Full model ID | Series + number | `W01`, `T07` |
| Dataset ID | `DS-` + code | `DS-WEATHER`, `DS-BUILDING` |
| Equipment ID | `EQ-` + abbreviation | `EQ-HP`, `EQ-PV` |
| Formula ID | `F-` + series + number | `F-T01`, `F-R02` |
| Weight ID | `W-` + formula ID + param | `W-T01-RC`, `W-R02-eta` |

---

## Input Variable Notation

All model inputs use the following field schema:

```yaml
inputs:
  - id: [VAR_ID]           # Unique variable identifier
    symbol: [LaTeX symbol] # Mathematical symbol used in formula
    description: [text]    # Plain English description
    unit: [unit string]    # SI or domain unit
    source_model: [ID]     # Which model produces this (if from another model)
    source_dataset: [DS-X] # Which dataset this is drawn from (if raw)
    source_field: [name]   # Exact field name in the dataset schema
    weight: [float]        # Coefficient applied in formula (null if not weighted)
    weight_type: [type]    # additive | multiplicative | exponent | constraint
    weight_justification:  # Why this weight; calibration source
    range: [min, max]      # Valid data range
    resolution: [string]   # Temporal resolution if time-series
```

---

## Output Variable Notation

```yaml
outputs:
  - id: [VAR_ID]
    symbol: [LaTeX symbol]
    description: [text]
    unit: [unit string]
    consumed_by: [list of model IDs that take this as input]
    uncertainty_band: [p10, p50, p90] or null
    temporal_horizon: [60min | 3h | 24h | seasonal]
```

---

## Formula Notation

Formulas are written in three forms in every model node:

1. **LaTeX** — for mathematical precision
2. **Python pseudocode** — for implementation alignment
3. **Plain English** — for non-technical readers and reviewers

Weight annotations appear inline:
- `(0.35)×X` means coefficient 0.35 applied to X
- `[w=0.35, calibrated: SEAI 2024]` is the inline annotation
- All coefficients link to [[06_Weighting/Weight Registry]]

---

## Relationship Types

| Relationship | Meaning | Direction |
|---|---|---|
| `feeds_into` | Output of A is input to B | A → B |
| `bounded_by` | A's output is constrained by B | A ↔ B |
| `calibrated_by` | A's weights use data from B | A ← B |
| `validated_by` | A's outputs are checked by B | A → B |
| `aggregated_by` | Multiple A instances combined into B | A → B |
| `refines` | B corrects or improves A | B → A |
| `consumes_RD` | Uses Response Derivative from A | B ← A |
| `draws_from` | Model reads from dataset | M → DS |

---

## Tag Taxonomy

```
mlm                    — All MLM KG nodes
series:[letter]        — Belongs to a specific series (e.g., series:W)
layer:deterministic    — Belongs to deterministic layer
layer:stochastic       — Belongs to stochastic layer
layer:ai-ml            — Belongs to AI/ML layer
layer:rl               — Belongs to RL/Q-learning layer
model                  — A sub-model node (W01, T07 etc.)
series-index           — A series-level index node
dataset                — A dataset node
equipment              — An equipment/asset node
formula                — A formula node
weight                 — A weight/coefficient node
application            — An application deployment node
```

---

## Status Values

| Status | Meaning |
|---|---|
| `active` | Fully specified, ready for implementation |
| `draft` | Partially specified, weights TBD |
| `placeholder` | Structure only, content needed |
| `deprecated` | Superseded by another node |
