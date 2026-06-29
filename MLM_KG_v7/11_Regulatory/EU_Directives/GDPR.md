---
type: regulatory_node
category: EU_Regulation
id: GDPR
full_title: General Data Protection Regulation
citation: Regulation (EU) 2016/679
status: active
in_force: 2018-05-25
tags: [mlm, regulatory, eu-regulation, gdpr, privacy, data-protection, federated-learning]
aliases: [GDPR, General Data Protection Regulation, 2016/679]
---

# GDPR — General Data Protection Regulation

## Summary
GDPR governs processing of personal data. Smart meter readings, occupancy data, and behavioural patterns in ODEON are personal data. The federated learning architecture (F-Series) is the primary MLM compliance mechanism — data never leaves the building.

## Key Articles Relevant to MLM

| Article | Requirement | MLM Compliance Point |
|---|---|---|
| Art. 5(1)(c) | Data minimisation — only collect what is necessary | [[02_Models/D_Models/D01]]–[[02_Models/D_Models/D05]] QA gates prevent unnecessary data retention |
| Art. 6 | Lawful basis for processing | O-CEI participant consent + legitimate interest for energy management |
| Art. 13/14 | Transparency — inform data subjects | ODEON participant information sheet references KG model descriptions |
| Art. 25 | Data protection by design and by default | [[02_Models/F_Models/F06]] Data Residency Controller — architectural GDPR compliance |
| Art. 32 | Security of processing | [[02_Models/F_Models/F03]] Differential Privacy Layer; edge processing on Jetson Nano |
| Art. 44 | Transfers to third countries | F01 federated averaging: only weight deltas leave island, not personal data |

## MLM Models That Demonstrate Compliance

- **[[02_Models/F_Models/F06]]** — Data Residency Controller: enforces Art. 25 data protection by design. Raw consumption, occupancy, and sensor data never leaves the building or Aran Islands.
- **[[02_Models/F_Models/F03]]** — Differential Privacy Layer: adds calibrated Gaussian noise (ε_DP=1.0) to model weights before any transmission (Art. 32 security)
- **[[02_Models/F_Models/F01]]** — Federated Averaging: model weights only (not personal data) transmitted to cloud (Art. 44)
- **[[02_Models/D_Models/D01]]–[[02_Models/D_Models/D07]]** — All QA models operate locally on Jetson Nano — no raw data transmitted
- **[[02_Models/O_Models/O13]]** — Household Archetype Classification: anonymised archetype (not individual) used for cross-building inference

## Data Classification in ODEON

| Data Type | Personal? | Location | MLM Model |
|---|---|---|---|
| Smart meter 15-min readings | Yes | Stays on-island | DS-SMART-METER, L01 |
| Indoor temperature | Yes | Stays on-building Jetson | DS-SENSOR, T01 |
| Occupancy PIR | Yes | Stays on-building Jetson | DS-OCCUPANCY, O01 |
| Model weight deltas | No (anonymised) | Transmitted to cloud | F01, F03 |
| Aggregate community stats (≥5 buildings) | No | May be published | G12, P06 |
| EPC rating | Borderline | Stays on-island | T19, K02 |

## Related Instruments
- [[11_Regulatory/EU_Regulations/Data_Act]] — Complementary data sharing rights
- [[02_Models/F_Models/F06]] — Primary GDPR compliance model
- [[02_Models/F_Models/F03]] — Differential privacy implementation
