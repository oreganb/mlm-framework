---
type: regulatory_node
category: Irish_Policy
id: Building_Regulations
full_title: Irish Building Regulations
citation: S.I. No. 497 of 1997 (as amended); Technical Guidance Documents
status: active
tags: [mlm, regulatory, irish-policy, building-regulations, part-l, part-f, thermal, ventilation]
aliases: [Building Regulations, TGD, Part L, Part F, Irish Building Regulations]
---

# Irish Building Regulations

## Parts Relevant to MLM

### Part L — Conservation of Fuel and Energy
**Technical Guidance Document L (2019, with 2023 amendment)**
- Maximum heat loss coefficients (U-values) for walls, roofs, floors, windows
- Minimum air permeability: 5 m³/(h·m²) at 50 Pa for new dwellings
- Nearly Zero-Energy Building (NZEB) standard for new dwellings from 2019
- **MLM Models:**
  - [[02_Models/T_Models/T11]] — Archetype lookup uses Part L U-value table defaults
  - [[02_Models/T_Models/T19]] — EPC Classification: Part L compliance determines EPC rating
  - W-T01-RENV: R_env values calibrated against Part L typical constructions

### Part F — Means of Ventilation
**Technical Guidance Document F (2019)**
- Minimum ventilation rates: 0.3 ACH background + 6 l/s/person extract for habitable rooms
- Mechanical ventilation heat recovery (MVHR) credits for NZEB
- **MLM Models:**
  - [[02_Models/T_Models/T05]] — Infiltration & Ventilation: ACH minimum from Part F (0.3/h)
  - W-T05-ACH_MIN: 0.3/h from Part F Building Regulations
  - [[02_Models/O_Models/O09]] — Natural Ventilation: Part F sets maximum uncontrolled ACH

### Part L Key Parameters in MLM Weight Registry
| Parameter | Value | Source | MLM Weight ID |
|---|---|---|---|
| Max U-value wall (new) | 0.18 W/m²K | TGD-L Table 1 | W-T01-RENV (derived) |
| Max U-value roof (new) | 0.16 W/m²K | TGD-L Table 1 | W-T01-RENV (derived) |
| Min ACH (background) | 0.3/h | TGD-F | W-T05-ACH_MIN |
| NZEB primary energy | ≤50 kWh/m²/yr | TGD-L | T19 EPC calculation |

## Related Instruments
- [[11_Regulatory/EU_Directives/EPBD]] — EU framework that Part L implements
- [[11_Regulatory/Irish_Policy/SEAI_Grants]] — Retrofit grants linked to Part L compliance
