---
type: regulatory_node
category: Technical_Standards
id: IEC_Standards
full_title: IEC Standards Referenced in MLM
citation: International Electrotechnical Commission
status: active
tags: [mlm, regulatory, technical-standard, iec, pv, battery, grid, power-quality]
aliases: [IEC Standards, IEC 61215, IEC 62619, IEC 61850, IEC 61970, IEC 61968]
---

# IEC Standards Referenced in MLM

## IEC 61215 — PV Module Performance

**Full title:** Terrestrial photovoltaic (PV) modules — Design qualification and type approval
**Relevance:** Defines STC conditions (1000 W/m², 25°C) and NOCT test conditions (800 W/m², 20°C ambient, 1 m/s wind) used in R01 PV model.

| Parameter | Standard Value | MLM Weight ID | Model |
|---|---|---|---|
| STC irradiance | 1000 W/m² | reference condition | [[02_Models/R_Models/R01]] |
| NOCT irradiance | 800 W/m² | W-R01-GNOCT | [[02_Models/R_Models/R01]] |
| NOCT temperature | 45°C (typical) | W-R01-TNOCT | [[02_Models/R_Models/R01]] |
| Temperature coefficient β | -0.0035/°C (monocrystalline Si) | W-R01-BETA | [[02_Models/R_Models/R01]] |
| Degradation rate | 0.5%/year (linear) | W-R03-DELTA_DEG | [[02_Models/R_Models/R03]] |

## IEC 62619 — Battery Safety Requirements

**Full title:** Secondary cells and batteries — Safety requirements for portable, stationary and motive applications
**Relevance:** Defines safe operating envelope for Li-ion batteries. SoC limits and efficiency values in B01 are IEC 62619 calibrated.

| Parameter | Standard Value | MLM Weight ID | Model |
|---|---|---|---|
| Charge efficiency | ≥0.95 (Li-ion) | W-B01-ETCH | [[02_Models/B_Models/B01]] |
| Discharge efficiency | ≥0.95 (Li-ion) | W-B01-ETDIS | [[02_Models/B_Models/B01]] |
| SoC_max recommended | ≤0.90 (calendar aging) | W-B01-SOCMAX | [[02_Models/B_Models/B01]] |
| SoC_min recommended | ≥0.10 (deep discharge prevention) | W-B01-SOCMIN | [[02_Models/B_Models/B01]] |
| Cycle life (Li-ion) | ~10,000 cycles | W-B02-CYCLE | [[02_Models/B_Models/B02]] |

## IEC 61850 — Grid Communication (Power Utility Automation)

**Full title:** Communication networks and systems for power utility automation
**Relevance:** Communication protocol for G-Series grid protection and islanding models. G07/G08/G09 protection schemes operate within IEC 61850 message timing requirements.

| Model | IEC 61850 Relevance |
|---|---|
| [[02_Models/G_Models/G07]] | GOOSE messages for island detection (<4ms) |
| [[02_Models/G_Models/G09]] | Protection relay communication protocol |
| [[02_Models/C_Models/C09]] | Island controller receives G07 GOOSE triggers |

## IEC 61968/61970/62325 — CIM (Common Information Model)

**Relevance:** Grid network data model used in G-Series. DS-GRID schema is CIM-compatible.

| Model | CIM Relevance |
|---|---|
| [[02_Models/G_Models/G01]] | Feeder topology = CIM TopologicalNode/Line |
| [[02_Models/G_Models/G08]] | Island segments = CIM TopologicalIsland |
| [[03_Datasets/DS-GRID]] | Schema aligned with CIM NetworkModel |

## IEC 61724 — PV System Monitoring

**Full title:** Photovoltaic system performance monitoring
**Relevance:** Defines performance ratio and yield metrics for R01/R03 output validation.

| Parameter | Standard Definition | MLM Usage |
|---|---|---|
| Performance Ratio | AC_output / (G_POA × A × η_STC) | [[02_Models/D_Models/D02]] plausibility check |
| Reference yield | G_POA_total / 1000 | [[02_Models/R_Models/R01]] normalisation |

## Related Standards
- [[11_Regulatory/Technical_Standards/ISO_Standards]]
- [[11_Regulatory/Technical_Standards/EN_Standards]]
- [[11_Regulatory/Semantic_Standards/OpenADR]]
