---
type: regulatory_node
category: Market_Framework
id: DS3
full_title: DS3 — Delivering a Secure, Sustainable Electricity System (System Services)
citation: EirGrid; SONI
status: active
tags: [mlm, regulatory, market, ds3, system-services, frequency-response, flexibility, eirgrid]
aliases: [DS3, System Services, EirGrid DS3, Delivering Secure Sustainable Electricity]
---

# DS3 — System Services Programme

## Overview
DS3 is EirGrid's programme for procuring system services (frequency regulation, voltage control, inertia) needed to operate a high-renewable grid at up to 75% non-synchronous penetration (SNSP). Directly relevant to ODEON/FLEXUS as a flexibility provider.

## System Services Products Relevant to MLM

| Product | Description | MLM Compliance |
|---|---|---|
| FFR (Fast Frequency Response) | Response within 2s to frequency event | [[02_Models/G_Models/G03]] frequency model; [[02_Models/C_Models/C09]] islanding controller |
| POR (Primary Operating Reserve) | Response within 10s | [[02_Models/C_Models/C01]] Flex↑ — 10s component |
| SOR (Secondary Operating Reserve) | Response within 90s | [[02_Models/C_Models/C01]] Flex↑ — 90s component |
| TOR (Tertiary Operating Reserve) | Response within 20 minutes | [[02_Models/B_Models/B01]] SoC headroom |
| SIR (Synchronous Inertia Replacement) | Virtual inertia from storage | [[02_Models/G_Models/G03]] H_inertia |
| Voltage Control | Reactive power support | [[02_Models/G_Models/G05]] power factor; [[02_Models/G_Models/G04]] voltage |

## SNSP Limit & MLM Islanding
- Current SNSP limit: 75% (2024), targeting 95%+ by 2030
- At high SNSP: low inertia → fast frequency deviations → ROCOF protection critical
- **MLM Models:** [[02_Models/G_Models/G07]] ROCOF detection; W-G07-ROCOF = 1.0 Hz/s (EirGrid grid code)
- [[02_Models/C_Models/C09]] Fragmented Islanding Controller designed for low-inertia grid operation

## Aran Islands / ODEON Context
- Aran Islands feeder has H_inertia ≈ 1.5s (vs mainland ~5s) — extremely low
- DS3 ROCOF limits are the binding grid code constraint for islanding operations
- G07/G09 parameters calibrated to DS3/EirGrid grid code requirements

## Related Instruments
- [[11_Regulatory/Market_Frameworks/I-SEM]] — Market framework alongside DS3
- [[11_Regulatory/Technical_Standards/EN_Standards]] EN 50160 (voltage quality)
- [[11_Regulatory/Irish_Policy/CRU_Framework]] — CRU regulates DS3 alongside EirGrid
