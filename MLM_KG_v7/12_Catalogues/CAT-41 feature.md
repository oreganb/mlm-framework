---
type: catalogue
ontology: EIG-MLM
catalogue_no: 41
catalogue_id: EIG-CAT-041
entity: feature
band: data
status: active
tags: [eig-mlm, catalogue, band:data, entity:feature]
aliases: ["EIG-CAT-041", "Catalogue 41", "feature"]
---

# CAT-41 — Feature

> **EIG-CAT-041** · band: **Data, Features & Datasets** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-09 weather_climate]] · [[CAT-21 tariff]] · [[CAT-28 carbon]] · [[CAT-38 data]] · [[CAT-39 metadata]] · [[CAT-40 data_quality]]
**Used by (downstream):** [[CAT-44 model]] · [[CAT-46 forecast]] · [[CAT-50 training]] · [[CAT-52 response_derivative]] · [[CAT-66 reinforcement_learning]] · [[CAT-70 model_selection]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-041`
**Entity prefixes:** `EIG-FT-` (features), `EIG-FT-SET-` (feature sets)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG AI/ML Lead
**Depends on (upstream):** 09 weather_climate, 21 tariff, 28 carbon, 38 data, 39 metadata, 40 data_quality
**Used by (downstream):** 44 model, 46 forecast, 50 training, 52 response_derivative, 66 reinforcement_learning, 70 model_selection

---

## 1. Definition

The **Feature Catalogue** is the single register of engineered features: named, versioned, formula-defined quantities computed from L2+ data entities (38) for consumption by models (44), forecasts (46), RD computation (52), and learning agents (66/72). A feature (`EIG-FT-*`) binds: identity, a machine-evaluable formula, source data entities and variables, unit (UCUM), output frequency/grain, applicable model families, validation expectations, and lineage. **Feature sets** (`EIG-FT-SET-*`) are versioned, ordered collections forming a model's input contract.

One definition, one ID: the same conceptual feature (e.g. heating degree hours base 15.5 °C) MUST NOT exist under two formulas. Models reference feature IDs, never re-implement formulas inline (A1).

## 2. Scope

**In scope:** feature identity and taxonomy; formula representation; source binding; units/frequency/grain; transformation classes; leakage rules; quality propagation; feature sets as model input contracts; feature stores (online/offline) at the contract level; lineage and reproducibility.

**Out of scope:** raw variables (38 — features are derived); model hyperparameters (48); training pipelines (50 — they *execute* features); feature importance/explanation (49); storage engine choice (31).

## 3. Classification Structure

### 3.1 Feature classes (`featureClass`)

| Code | Class | Description | Examples |
|---|---|---|---|
| FC-TMP | Temporal/calendar | Encodings of time | hour_sin/cos, dow, is_holiday_ie, is_dst_transition (26 V10 awareness) |
| FC-LAG | Lag/window | Past values & rolling statistics | load_lag_48, load_roll_mean_336, load_roll_std_48 |
| FC-WX | Weather-derived | From 09 variables | hdh_15p5, cdh_22, solar_irr_lag_1, wx_forecast_temp_h+24 |
| FC-PHY | Physical/asset | From asset/fabric physics | thermal_mass_proxy_r2c2, cop_expected, envelope_ua (05) |
| FC-OCC | Occupancy/behavioural | From 03/17 signals | occ_prob_hh, vacancy_flag, baseload_ratio |
| FC-MKT | Market/price | From 21/25 | tariff_rate_now, dam_price_h+1, price_spread_peak_offpeak |
| FC-CB | Carbon | From 28 CI series | grid_ci_now, ci_forecast_h+24, low_ci_window_flag |
| FC-FLX | Flexibility/RD | From 30/52 outputs | flex_headroom_kw, rd_conf_last, rebound_ratio_est |
| FC-INT | Interaction/composite | Cross-domain combinations | hdh×occ_prob, price×headroom |

### 3.2 Transformation classes (`transformClass`)

| Code | Transform | Notes |
|---|---|---|
| TX-IDENT | Pass-through resample | grain alignment only |
| TX-AGG | Aggregation | sum/mean/min/max/percentile over window, TS-semantics-aware (38 A4: never average TS-CUM) |
| TX-LAG | Shift | leakage rules §7 A3 apply |
| TX-ROLL | Rolling statistic | window in grain units, min_periods declared |
| TX-ENC | Encoding | cyclical (sin/cos), one-hot, ordinal, target-encoding (training-fold-safe only) |
| TX-NORM | Normalisation | per-entity z-score / min-max; fitted params versioned with feature |
| TX-PHYS | Physical formula | e.g. HDH = Σ max(0, Tbase − Tout)·Δt; constants cited |
| TX-MODEL | Model-derived | output of upstream model (44) used as feature — dependency declared, COMPUTED class |

### 3.3 Serving modes (`servingMode`)

| Code | Mode | Latency | Use |
|---|---|---|---|
| SV-OFF | Offline/batch | minutes–hours | training (50), backtests |
| SV-ON | Online | ≤ LAT-FAST (34) | live inference, dispatch (60), edge (35) |
| SV-BOTH | Both | — | must be point-in-time consistent (V6 parity) |

## 4. Hierarchy

```
Feature set (EIG-FT-SET-*)        ← model input contract, ordered, versioned
  └── Feature (EIG-FT-*)          ← one formula, one unit, one grain
        └── Source bindings       ← →38 EIG-DE-* entities / EIG-VAR-* variables
```

## 5. Field Groups & Fields

### 5.1 Feature (`EIG-FT-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | featureID | string | — | `EIG-FT-\d{4}` | M | Feature ID |
| 2 | name | string | — | snake_case, unique | M | Canonical name (single naming authority with 38 register style) |
| 3 | featureClass | enum | — | §3.1 | M | Class |
| 4 | transformClass | enum | — | §3.2 | M | Transform |
| 5 | formula | string | — | machine-evaluable (SQL/Python expr/pinned function ref + hash) | M | The definition; prose-only forbidden |
| 6 | formulaHash | string | — | SHA-256 of implementation | M | Pinned; change ⇒ version bump (39 MC-LIN) |
| 7 | sourceEntities | array<ref> | — | →38 EIG-DE-* at level ≥ L2 | M | Inputs (L2+ only, A2) |
| 8 | sourceVariables | array<ref> | — | →38 EIG-VAR-* | M | Variables used |
| 9 | unit | string | UCUM | e.g. kW·h, Cel, 1 (dimensionless) | M | Output unit (38 A2) |
| 10 | outputGrain | string | — | e.g. building×PT30M | M | Grain of output |
| 11 | temporalSemantics | enum | — | 38 TS-{INST,AVG,CUM,INT,EVT} | M | Output semantics |
| 12 | horizon | duration | — | ISO 8601; 0 for nowcast features | C | For forecast-aligned features (h+24 etc.) |
| 13 | leakageWindow | duration | — | min lag from target time | M | Anti-leakage declaration (A3) |
| 14 | nullPolicy | enum | — | null_propagate, default_value(flagged) | M | 40 §12: suspect/missing ⇒ NULL, never guessed |
| 15 | qualityFloor | enum | — | 40 fitnessPurpose | M | Min input fitness (default training) |
| 16 | applicableModels | array<ref> | — | →44 model families | M | Where usable |
| 17 | servingMode | enum | — | §3.3 | M | Offline/online/both |
| 18 | dataClassOut | enum | — | COMPUTED (fixed) | M | Features are always COMPUTED (37 A2 lineage visible) |
| 19 | normalisationParams | json | — | versioned with fold provenance | C | For TX-NORM/TX-ENC |
| 20 | validationExpect | json | — | {range, distribution checks, drift bounds} | M | Feature-level QC (V4) |
| 21 | owner / steward | ref | — | →16 roles | M | Duties (16 A6) |
| 22 | version / status | semver/enum | — | DRAFT, ACTIVE, DEPRECATED, RETIRED | M | Lifecycle |

### 5.2 Feature set (`EIG-FT-SET-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | setID | string | `EIG-FT-SET-\d{4}` | M | Set ID |
| 2 | name | string | ≤120 chars | M | e.g. "Residential HH load forecast inputs" |
| 3 | members | array<ref> | →EIG-FT-* @version, ordered | M | Pinned versions (input contract) |
| 4 | targetVariable | ref | →38 EIG-VAR-* | C | For supervised sets |
| 5 | consumingModels | array<ref> | →44 EIG-MDL-* | M | Contract holders |
| 6 | pointInTimeRule | string | as-of join spec | M | Reproducible historical assembly (V6) |
| 7 | setHash | string | SHA-256 over member IDs+versions | M | Contract fingerprint |
| 8 | version / status | semver/enum | — | M | Lifecycle; member change ⇒ MAJOR if removal/semantics, MINOR if additive |

## 6. Controlled Vocabularies
featureClass (§3.1), transformClass (§3.2), servingMode (§3.3), nullPolicy, lifecycle status. Feature names: snake_case, unit-suffixed where ambiguous (…_kwh, …_c), registered uniquely (V1).

## 7. Applicability Rules

- **A1 — No inline features.** Models (44) and pipelines (50) consume EIG-FT refs only; ad-hoc transformations in training code are a validation failure (47 audit).
- **A2 — L2+ inputs only.** Features compute from harmonised entities (38 L2/L3); L0/L1 binding forbidden — unit/calendar harmonisation happens in 38, not here.
- **A3 — Leakage rule.** For any feature feeding a model predicting target at time t with horizon h: all inputs ≤ t − leakageWindow, and leakageWindow ≥ data availability lag of the source stream (37 latency). Target encoding fitted within training folds only.
- **A4 — Quality propagation.** Inputs flagged suspect/missing ⇒ output NULL (40 §12); estimated inputs propagate a feature-level estimated marker exposed to models as a companion flag.
- **A5 — RD feature bar.** Features used in RD computation (52) require qualityFloor=rd and REAL/CALIBRATED-rooted lineage; TX-MODEL features are NOT RD evidence (model-on-model circularity bar).
- **A6 — Online/offline parity.** SV-BOTH features must produce identical values for identical as-of inputs (V6); divergence ⇒ feature frozen.
- **A7 — Sandbox tagging.** Features computed over sandbox entities carry environment=sandbox in metadata (39 A5); mixed-environment feature sets forbidden.
- **A8 — Edge subset.** Features served on edge nodes (35) MUST be computable within node class resources (CLS-E1 baseline) and from locally available streams during degraded mode (35 A3) — declared per feature in serving metadata.

## 8. Validation Rules

- **V1:** name uniqueness across the register; formula duplicates (same hash, different ID) rejected.
- **V2:** unit dimensional consistency: formula output dimension must match declared UCUM unit.
- **V3:** sourceEntities level ≥ L2 (A2).
- **V4:** validationExpect checks run per computation batch: range breach rate >1% ⇒ feature flagged suspect, consuming models notified (44 dependency graph).
- **V5:** leakageWindow ≥ max source availability lag (A3) — checked at activation.
- **V6:** point-in-time backtest parity: offline reconstruction equals logged online values (sampled daily) for SV-BOTH.
- **V7:** TS-semantics legality: TX-AGG mean over TS-CUM forbidden (38 A4).
- **V8:** feature set members pinned to explicit versions; floating "latest" forbidden.
- **V9:** drift monitor: PSI vs training-time distribution > 0.25 ⇒ alert (76) + 70 model_selection review.
- **V10:** deprecation: a feature with ACTIVE consumers cannot be RETIRED; deprecation window ≥ 90 d with migration note.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 38 data | Source entities/variables; TS semantics; naming style authority |
| 39 metadata | EIG-MET-STD-FT profile: lineage (formula hash), provenance (COMPUTED), quality |
| 40 data_quality | qualityFloor gates; null/estimated propagation; drift escalation |
| 44 model | applicableModels; feature sets are model input contracts; dependency notifications |
| 46 forecast | horizon-aligned features (wx/price forecast features) |
| 50 training | executes feature pipelines; fold-safe encodings |
| 52 response_derivative | RD feature bar (A5); headroom/rebound features fed back as FC-FLX |
| 66/72 RL/agent | state-space features; edge-computable subset (A8) |
| 09/21/28 | upstream domains for FC-WX/FC-MKT/FC-CB |

## 10. Benchmarking Requirements
Feature usage census (consumers per feature), computation cost per serving mode, drift incident rate. Unused-for-180-d ACTIVE features flagged for deprecation review.

## 11. Dataset Requirements
Feature tables materialised for release ship as L3 entities (38) inside datasets (43) with the feature-set hash recorded; reproducibility = sources + formulas + params (39 MC-LIN.reproducible=TRUE).

## 12. Feature Requirements
(Self) — register completeness: every model input in 44 resolves to an ACTIVE EIG-FT; no orphan formulas.

## 13. Model Requirements
Models declare input contracts as EIG-FT-SET refs; retraining against a changed set hash ⇒ new model version (44/50). MLM/FLEXGPT sub-models (139) map their inputs onto this register — series-level shared sets encouraged.

## 14. KPI Requirements
KPI formulas (57) that reuse engineered quantities reference EIG-FT IDs to guarantee identical computation between dashboards (75) and models.

## 15. Response Derivative Requirements
RD pipelines (52) consume: FC-FLX headroom features, FC-PHY thermal proxies, FC-TMP/FC-WX baselines — all at qualityFloor=rd, leakage-checked, with REAL/CALIBRATED roots (A5). RD outputs re-enter the register as FC-FLX features (rd_conf_last etc.) marked TX-MODEL (not RD evidence themselves).

## 16. Decision Requirements
Decision intelligence (60) consumes online features (SV-ON) within LAT-FAST; missing online features trigger documented fallbacks (last-known-good ≤ declared staleness bound), never silent defaults.

## 17. Ontology Mapping
Feature ↔ MLS (`mls:Feature`)/MEX vocabularies; formula provenance ↔ PROV-O `prov:Activity`; unit ↔ QUDT/UCUM; alignment owned by 79 (SHIFT/MLM ontology line).

## 18. Knowledge Graph Mapping
Node `Feature`; edges `COMPUTED_FROM`→38 entities, `MEMBER_OF`→set, `FEEDS`→44 models, `GOVERNED_BY`→40 regime. Enables impact queries: "which models break if EIG-VAR-0240 changes?"

## 19. Digital Twin Mapping
Twin templates (33) declare required feature sets per fidelity class; F3 calibrated twins (32) expose physics features (FC-PHY) computed from their calibrated parameters.

## 20. Governance
Owner: EIG AI/ML Lead (Farah Tahir role-equivalent); steward per featureClass domain; formula changes peer-reviewed (16 SoD: author ≠ approver). Breaking changes follow V10 deprecation discipline.

## 21. Versioning
Features semver: PATCH=implementation-only (hash change, same semantics — requires parity proof), MINOR=parameter default change, MAJOR=semantic/formula change. Sets per §5.2. Catalogue 1.0.0.

## 22. Example Records

### EIG-FT-0012 — hdh_15p5 (heating degree hours, base 15.5 °C)
- class FC-WX; transform TX-PHYS; formula `sum(max(0, 15.5 - air_temp_c) * 0.5h)` over PT30M grain; hash sha256:…
- sources: EIG-DE-WX-CORK-HH-L2 (EIG-WX-VAR-001 via 09/38); unit Cel·h; grain station×P1D; semantics TS-INT
- leakageWindow PT2H (Met Éireann availability lag, EIG-DS-00890); qualityFloor training; serving SV-BOTH; edge-computable (A8 ✓)
- applicableModels: load forecast family, R2C2 thermal (EIG-MDL-SPEC-THERM-R2C2)

### EIG-FT-0047 — flex_headroom_kw (live shiftable headroom)
- class FC-FLX; transform TX-MODEL (from EIG-MDL-FLEXENV-01@1.4.2); unit kW; grain asset×PT1M; serving SV-ON; LAT-FAST
- sources: EIG-DE-ODEON-ASSET-TELEM-L2; qualityFloor rd; NOT RD evidence (A5 — TX-MODEL bar); feeds dispatch (60) within EIG-CON-001207 caps
- edge deployment: EIG-EDGE-DEP-00231 computes locally during degraded mode

### EIG-FT-SET-0009 — residential HH load forecast input contract
- members (pinned): EIG-FT-0012@1.2.0, EIG-FT-0003 hour_sin@1.0.0, EIG-FT-0004 hour_cos@1.0.0, EIG-FT-0021 load_lag_48@1.1.0, EIG-FT-0022 load_roll_mean_336@1.0.0, EIG-FT-0033 tariff_rate_now@2.0.0, EIG-FT-0051 is_holiday_ie@1.0.0
- target EIG-VAR-0102 (active_import_kwh); consumers [EIG-MDL-LOADFC-RES-01]; pointInTimeRule as-of ts−PT30M; setHash sha256:…

## 23. Completion Criteria
☑ Feature identity/formula/lineage machine-evaluable ☑ Source binding L2+ with leakage rules ☑ Quality propagation + RD feature bar ☑ Online/offline parity contract ☑ Feature sets as pinned model input contracts ☑ Edge-computability declared ☑ Examples resolve 38/40/44/52 refs.
