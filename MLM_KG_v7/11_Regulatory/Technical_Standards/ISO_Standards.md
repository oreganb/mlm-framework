---
type: regulatory_node
category: Technical_Standards
id: ISO_Standards
full_title: ISO Standards Referenced in MLM
citation: International Organization for Standardization
status: active
tags: [mlm, regulatory, technical-standard, iso, thermal-comfort, energy-management, buildings]
aliases: [ISO Standards, ISO 7730, ISO 52000, ISO 50001, ISO 8601]
---

# ISO Standards Referenced in MLM

## ISO 7730 — Thermal Comfort (PMV/PPD)

**Full title:** Ergonomics of the thermal environment — Analytical determination and interpretation of thermal comfort using calculation of the PMV and PPD indices
**Relevance:** PMV/PPD comfort model in T10 is the ISO 7730 Fanger model. Comfort constraints in C01 (β weight) are calibrated against ISO 7730 Category A/B thresholds.

| Category | PMV Range | PPD | MLM Usage |
|---|---|---|---|
| Category A (excellent) | -0.5 to +0.5 | <10% | W-C01-BETA comfort weight target |
| Category B (good) | -0.7 to +0.7 | <15% | Override threshold in O03 |
| Category C (moderate) | -1.0 to +1.0 | <25% | Minimum acceptable — triggers alert |

**MLM Models:**
- [[02_Models/T_Models/T10]] — PMV/PPD model: direct implementation of ISO 7730 Fanger equation
- [[02_Models/O_Models/O03]] — Comfort Feedback: |PMV| > 1.0 triggers override (ISO Category C breach)
- [[02_Models/C_Models/C01]] — β (comfort weight = 0.30): ensures ISO 7730 compliance in optimisation
- W-T01-TMIN = 18°C, W-T01-TMAX = 24°C: calibrated to ISO 7730 / CIBSE Guide A comfort band

## ISO 52000-1 — Energy Performance of Buildings (Primary Energy)

**Full title:** Energy performance of buildings — Overarching EPB assessment
**Relevance:** Defines how primary energy is calculated for EPC. The EPB framework underpins T19 EPC classification and K02 EPBD compliance.

**MLM Models:**
- [[02_Models/T_Models/T19]] — EPC Classification: uses ISO 52000 primary energy factor framework
- [[02_Models/K_Models/K02]] — EPC Compliance: threshold checking based on ISO 52000 output

## ISO 50001 — Energy Management Systems

**Full title:** Energy management systems — Requirements with guidance for use
**Relevance:** ODEON energy monitoring and continuous improvement framework follows ISO 50001 Plan-Do-Check-Act cycle. D08 drift detection and F01 federated retraining embody the 'Check-Act' loop.

**MLM Models:**
- [[02_Models/D_Models/D08]] — Model Drift Detection: continuous monitoring (ISO 50001 'Check')
- [[02_Models/F_Models/F01]] — Federated Averaging: model update on drift (ISO 50001 'Act')
- [[02_Models/P_Models/P05]], [[02_Models/P_Models/P06]] — Energy KPIs: ISO 50001 energy performance indicators

## ISO 8601 — Date and Time Format

**Relevance:** All timestamps in MLM datasets use ISO 8601 format (UTC). DS-WEATHER, DS-SMART-METER, DS-SENSOR schemas all specify `datetime (ISO 8601, UTC)`.

**MLM Datasets:** All `timestamp` fields in [[03_Datasets/DS-SENSOR]], [[03_Datasets/DS-SMART-METER]], [[03_Datasets/DS-WEATHER]]

## Related Standards
- [[11_Regulatory/Technical_Standards/EN_Standards]] (EN implementations of ISO standards)
- CIBSE Guide A (aligned with ISO 7730 for Irish/UK context)
