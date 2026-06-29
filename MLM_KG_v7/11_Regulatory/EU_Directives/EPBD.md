---
type: regulatory_node
category: EU_Directive
id: EPBD
full_title: Energy Performance of Buildings Directive
citation: Directive (EU) 2024/1275 (Recast)
status: active
in_force: 2024-05-28
transposition_deadline: 2026-05-29
irish_transposition: Building Regulations Part L (pending update)
tags: [mlm, regulatory, eu-directive, epbd, epc, buildings, thermal]
aliases: [EPBD, Energy Performance of Buildings Directive, 2024/1275]
---

# EPBD — Energy Performance of Buildings Directive (Recast 2024)

## Summary
The recast EPBD is the primary EU instrument governing building energy performance. It mandates minimum energy performance standards, zero-emission buildings (ZEB) by 2030 for new buildings, and national renovation plans targeting the worst-performing stock.

## Key Requirements Relevant to MLM

| Article | Requirement | MLM Compliance Point |
|---|---|---|
| Art. 3 | National Building Renovation Plans — worst-performing 16% renovated by 2030 | [[02_Models/K_Models/K02]] EPC compliance flags trigger SEAI retrofit alerts |
| Art. 7 | New buildings: Zero-Emission Buildings (ZEB) standard from 2030 | [[02_Models/T_Models/T19]] EPC classification; target EPC A or better |
| Art. 8 | Technical Building Systems — HVAC efficiency documentation | [[02_Models/T_Models/T02]] COP model; [[02_Models/T_Models/T03]] heat pump model |
| Art. 10 | Smart Readiness Indicator (SRI) — building intelligence rating | [[02_Models/C_Models/C01]] MILP dispatch; [[02_Models/C_Models/C02]] MPC = SRI capability |
| Art. 15 | Renovation passports for deep renovation | [[02_Models/T_Models/T11]] archetype lookup drives renovation priority scoring |
| Art. 22 | EPC content — include primary energy use, renewable share | [[02_Models/K_Models/K01]] avoided carbon; [[02_Models/P_Models/P04]] self-consumption ratio |
| Art. 26 | Smart charging for EVs in buildings | [[02_Models/B_Models/B04]] EV charging; [[02_Models/B_Models/B05]] V2G |

## MLM Models That Demonstrate Compliance

- **[[02_Models/K_Models/K02]]** — EPC Compliance Model: directly checks building against EPBD EPC thresholds (2030: ≥C, 2033: ≥B)
- **[[02_Models/T_Models/T19]]** — EPC Classification: derives EPC label from U-values and HVAC system
- **[[02_Models/T_Models/T11]]** — Building Fabric Archetype: identifies worst-performing buildings (EPC D-G)
- **[[02_Models/K_Models/K05]]** — Policy Alignment: maps each building to applicable EPBD incentive schemes
- **[[02_Models/C_Models/C01]]** — MILP includes comfort constraints (Art. 8 HVAC performance)
- **[[02_Models/B_Models/B04]], [[02_Models/B_Models/B05]]** — Smart EV charging (Art. 26)

## Weight Registry Links
- W-K02-THRESH2030: EPC C minimum by 2030
- W-K02-THRESH2033: EPC B minimum by 2033

## Irish Context
Irish transposition pending under updated Building Regulations Part L. SEAI BER (Building Energy Rating) system is the national EPC mechanism. ~50% of Aran Islands buildings estimated EPC D or below — primary ODEON retrofit targeting context.

## Related Instruments
- [[11_Regulatory/EU_Directives/EED]] — Energy Efficiency Directive (demand reduction)
- [[11_Regulatory/Irish_Policy/SEAI_Building_Standards]] — Irish implementation
- [[11_Regulatory/Technical_Standards/EN_ISO_52000]] — Primary energy calculation methodology
