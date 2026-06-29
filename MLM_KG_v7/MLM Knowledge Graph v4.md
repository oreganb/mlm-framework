---
type: kg_root
ontology: MLM
version: 4.0
project: O-CEI / FLEXUS / ODEON
status: active
created: 2026-05-13
tags: [mlm, knowledge-graph, flexgpt, o-cei, energy-informatics, response-derivative]
aliases: [MLM KG v4, Multi-Layered Modelling Knowledge Graph]
---

# MLM Knowledge Graph v4

The canonical semantic reference for the FLEXGPT Multi-Layered Modelling (MLM) framework. Developed by the Energy Informatics Group (EIG), Tyndall National Institute / IERC, University College Cork.

**v4 combines:**
- v2 deep formula/weight/input/output specifications for all 139 models
- v3 asset layer, formula families, RD canonical fields, machine-readable exports
- Corrected model ID-to-name mappings throughout
- Full RD Map with per-series contributions and governance logic
- Enhanced semantic role and validation sections on key models

## Architecture
[[00_Core/Architecture/MLM Architecture v3]] — Four-layer cognitive stack with RD responsibility

## Primary Navigation

| Section | Purpose |
|---|---|
| [[00_Model_Index]] | All 139 sub-models with path links |
| [[01_Series/W-Series]] through [[01_Series/F-Series]] | Series-level indices |
| [[02_Models/]] | Individual model nodes (formula, weights, inputs, outputs) |
| [[03_Datasets/]] | Dataset family and specific dataset schemas |
| [[04_Assets/Asset Index]] | 24 physical asset nodes (sensors, DERs, actuators) |
| [[05_Formulas/]] | Per-series formula family nodes |
| [[06_Weighting/Weight Registry]] | All formula coefficients with calibration sources |
| [[07_Relationships/Cross-Series Dependencies]] | Full model-to-model dependency graph |
| [[08_Response_Derivative/Response Derivative Map v4]] | RD theory, lifecycle, governance, per-series contributions |
| [[09_Applications/ODEON]] | Aran Islands O-CEI pilot deployment |
| [[09_Applications/FLEXUS]] | Platform execution layer |
| [[09_Applications/ARC Tool]] | Automatic ML pipeline |
| [[10_Source_Traceability/Source Files Used]] | Source document registry |
| [[99_Exports/]] | mlm_kg_v4.json, .ttl, _nodes.csv, _edges.csv |

## Series Registry

| Series | Title | Layer | Models |
|---|---|---|---|
| [[01_Series/W-Series\|W]] | Weather & Exogenous | Stochastic | W01–W20 |
| [[01_Series/O-Series\|O]] | Occupancy, Behaviour & DHW | Stochastic | O01–O15 |
| [[01_Series/M-Series\|M]] | Market, Tariff & Carbon Pricing | Stochastic | M01–M12 |
| [[01_Series/L-Series\|L]] | Load & End-Use Decomposition | Deterministic | L01–L13 |
| [[01_Series/R-Series\|R]] | Renewables & DER | Deterministic | R01–R10 |
| [[01_Series/T-Series\|T]] | Thermal Envelope & HVAC/Plant | Deterministic | T01–T20 |
| [[01_Series/B-Series\|B]] | Battery, EV & Storage | AI/ML | B01–B10 |
| [[01_Series/G-Series\|G]] | Grid, Microgrid & Protection | Deterministic | G01–G12 |
| [[01_Series/C-Series\|C]] | Optimisation & Control | RL/Q-Learning | C01–C10 |
| [[01_Series/D-Series\|D]] | Data Quality & Imputation | AI/ML | D01–D08 |
| [[01_Series/P-Series\|P]] | Trading, Revenue & KPIs | RL/Q-Learning | P01–P08 |
| [[01_Series/K-Series\|K]] | Carbon, Compliance & Policy | RL/Q-Learning | K01–K05 |
| [[01_Series/F-Series\|F]] | Meta-Learning, Transfer & Privacy | AI/ML | F01–F06 |

## Response Derivative

RD is the cross-cutting concept connecting all layers. Estimated in stochastic+AI layers, bounded by deterministic constraints, consumed by RL layer as primary decision primitive.

→ [[08_Response_Derivative/Response Derivative Map v4]]
→ [[02_Models/C_Models/C08]] — Response Derivative Engine

## Machine-Readable Exports
- [[99_Exports/mlm_kg_v4.json]] — 233 nodes, 1076 edges
- [[99_Exports/mlm_kg_v4_nodes.csv]] — Node registry
- [[99_Exports/mlm_kg_v4_edges.csv]] — Edge registry (feeds_into, requires_dataset, uses_asset, etc.)
- [[99_Exports/mlm_kg_v4.ttl]] — RDF/Turtle for ontology tools

## Cursor AI Alignment Rules
1. Every function parameter → named input variable in a model node
2. Every coefficient → registered in [[06_Weighting/Weight Registry]]
3. Dataset field names → match schema in [[03_Datasets/]] nodes
4. Cross-series calls → follow [[07_Relationships/Cross-Series Dependencies]]
5. All outputs carry `confidence` and `imputed_flag` fields
6. Never pass anonymous arrays between MLM models — use named structs
7. C10 gates every actuator signal — no model bypasses governance

---

## Regulatory & Standards Layer (v4 addition)

| Section | Contents |
|---|---|
| [[11_Regulatory/EU_Directives/EPBD]] | EPBD 2024/1275 — buildings, EPC, ZEB, SRI, EV charging |
| [[11_Regulatory/EU_Directives/EED]] | EED 2023/1791 — efficiency targets, demand response, smart metering |
| [[11_Regulatory/EU_Directives/RED]] | RED III 2023/2413 — renewables, RECs, prosumers, heating |
| [[11_Regulatory/EU_Directives/GDPR]] | GDPR 2016/679 — privacy, federated learning architecture |
| [[11_Regulatory/EU_Regulations/Data_Act]] | Data Act 2023/2854 — IoT data rights, interoperability |
| [[11_Regulatory/EU_Regulations/Electricity_Market_Regulation]] | EMR 2024/1747 — DR, aggregation, energy sharing |
| [[11_Regulatory/Irish_Policy/Climate_Action_Plan]] | CAP 2024 — sectoral targets, 80% RES-E, heat pumps |
| [[11_Regulatory/Irish_Policy/SEAI_Grants]] | MSS FIT, HP grants, Warmer Homes, EEOS |
| [[11_Regulatory/Irish_Policy/Building_Regulations]] | Part L/F — U-values, ACH, NZEB |
| [[11_Regulatory/Irish_Policy/CRU_Framework]] | DUoS tariffs, grid code, smart metering, export limits |
| [[11_Regulatory/Technical_Standards/IEC_Standards]] | IEC 61215 (PV), IEC 62619 (battery), IEC 61850 (grid comms) |
| [[11_Regulatory/Technical_Standards/ISO_Standards]] | ISO 7730 (comfort), ISO 50001 (energy mgmt), ISO 8601 |
| [[11_Regulatory/Technical_Standards/EN_Standards]] | EN 50160 (voltage), EN 50549 (generator grid connection) |
| [[11_Regulatory/Market_Frameworks/I-SEM]] | Irish electricity market — DAM, IDM, capacity, DSU |
| [[11_Regulatory/Market_Frameworks/DS3]] | EirGrid system services — FFR, POR, SOR, SNSP |
| [[11_Regulatory/Semantic_Standards/OpenADR]] | OpenADR 2.0b — DR signal protocol |
| [[11_Regulatory/Semantic_Standards/SAREF]] | SAREF 4.1.1 — IoT interoperability ontology |
| [[11_Regulatory/Semantic_Standards/Brick_Schema]] | Brick 1.3 — building metadata ontology |
| [[11_Regulatory/Compliance_Matrix/Compliance_Matrix]] | Article-by-article compliance evidence table |
| [[11_Regulatory/Compliance_Matrix/Literature_Alignment]] | Weight calibration citation map for peer review |
| [[11_Regulatory/Compliance_Matrix/Regulatory_Index]] | Complete instrument registry with model links |
