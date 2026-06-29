---
type: catalogue
ontology: EIG-MLM
catalogue_no: 37
catalogue_id: EIG-CAT-037
entity: data_source
band: flexibility
status: active
tags: [eig-mlm, catalogue, band:flexibility, entity:data_source]
aliases: ["EIG-CAT-037", "Catalogue 37", "data_source"]
---

# CAT-37 — Data Source

> **EIG-CAT-037** · band: **Flexibility & Platform** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-09 weather_climate]] · [[CAT-13 utility_network]] · [[CAT-15 organisation]] · [[CAT-20 market]] · [[CAT-34 communication]] · [[CAT-36 device]]
**Used by (downstream):** [[CAT-38 data]] · [[CAT-39 metadata]] · [[CAT-40 data_quality]] · [[CAT-42 synthetic_data]] · [[CAT-43 dataset]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-037`
**Entity prefix:** `EIG-DS-` (data source streams), `EIG-DSP-` (data source providers)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Data Management Lead
**Depends on (upstream):** 09 weather_climate, 13 utility_network, 15 organisation, 20 market, 34 communication, 36 device
**Used by (downstream):** 38 data, 39 metadata, 40 data_quality, 42 synthetic_data, 43 dataset

---

## 1. Definition

A **Data Source** is a registered origin of data entering the EIG MLM framework. Two entity families:

- **Provider** (`EIG-DSP-*`): the organisational/system origin — a device fleet, a DSO head-end system, a market operator feed, a public statistics body (SEAI, CSO, EirGrid, ESB Networks, Met Éireann), a partner API, a manual survey process, or a synthetic generator (42).
- **Stream** (`EIG-DS-*`): a specific, individually-traceable flow of data from a provider — one device output, one API endpoint subscription, one file drop series. Streams are the unit of lineage: every value in the framework traces to exactly one stream.

The catalogue makes ingestion **traceable by construction**: no data enters except through a registered stream, with declared access method, update frequency, format, reliability profile, and security posture.

## 2. Scope

**In scope:** provider register with ownership, licensing, and trust class; stream register with access method, schedule, format, dataClass at origin, reliability metrics, security; ingestion contract (what happens on arrival: validation, quarantine, landing); linkage to datasets (43) and quality regimes (40).

**Out of scope:** the data content itself (38), dataset packaging (43), quality rule definitions (40 — bound here, defined there), channel mechanics (34), device internals (36).

## 3. Classification Structure

### 3.1 Provider types (`providerType`)

| Code | Type | Examples |
|---|---|---|
| DSP-DEV | Device fleet | Registered devices (36) via edge (35) |
| DSP-DSO | DSO/TSO systems | ESB Networks HES (smart meter DLMS), EirGrid feeds |
| DSP-MKT | Market operator | SEMO market results, balancing data |
| DSP-MET | Meteorological | Met Éireann stations (09), reanalysis providers |
| DSP-PUB | Public/statistical | SEAI, CSO, EPA, Eurostat |
| DSP-PRT | Partner/commercial | Partner APIs, manufacturer clouds |
| DSP-MAN | Manual process | Surveys, audits, commissioning forms |
| DSP-SYN | Synthetic generator | Registered generators (42) — sandbox/test provenance |
| DSP-DRV | Derived/internal | Internal pipelines producing COMPUTED streams |

### 3.2 Trust classes (`trustClass`)

| Code | Class | Criteria |
|---|---|---|
| TR-A | Authoritative | Legal/settlement-grade origin (DSO HES, market operator, MID meters) |
| TR-B | Operationally trusted | Certified devices, established public bodies |
| TR-C | Conditional | Partner feeds, manufacturer clouds — validated on ingest |
| TR-D | Untrusted/experimental | New feeds under evaluation; never feeds settlement or disclosure |

### 3.3 dataClass at origin

REAL (measured), CALIBRATED (corrected/derived from real with declared method), SYNTHETIC (generated, 42), COMPUTED (model/pipeline output). Streams declare exactly one origin dataClass; it propagates in the provenance envelope (31 A3 / 34 A4).

## 4. Hierarchy

```
Provider (EIG-DSP-*)
└── Stream (EIG-DS-*)
    ├── Access binding (channel/API/file-drop → 34, or device output → 36)
    ├── Ingestion contract (validation, landing, quarantine)
    └── Feeds → dataset(s) (43) / variables (38)
```

## 5. Field Groups & Fields

### 5.1 Provider (`EIG-DSP-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | providerID | string | — | `EIG-DSP-\d{4}` | M | Provider ID |
| 2 | providerType | enum | — | §3.1 | M | Type |
| 3 | providerName | string | — | ≤160 chars | M | Name |
| 4 | ownerOrgRef | ref | — | →15 EIG-ORG-* | M | Owning organisation |
| 5 | trustClass | enum | — | §3.2 | M | Trust classification |
| 6 | licence | object | — | licence type (open / contract / restricted), terms ref, attribution text | M | Usage rights; public sources carry required attribution |
| 7 | contactRef | ref | — | →14/15 contact | M | Operational contact |
| 8 | dataProtectionClass | enum | — | none / personal / special_category | M | GDPR class (03 alignment); drives access rules (43) |
| 9 | sourceSystemDesc | string | — | ≤2000 chars | M | System description |
| 10 | status | enum | — | evaluated / active / suspended / retired | M | Lifecycle |

### 5.2 Stream (`EIG-DS-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 11 | streamID | string | — | `EIG-DS-\d{5}` | M | Stream ID |
| 12 | providerRef | ref | — | →providerID | M | Parent provider |
| 13 | streamName | string | — | ≤160 chars | M | Name |
| 14 | description | string | — | ≤2000 chars | M | What flows |
| 15 | originDataClass | enum | — | REAL / CALIBRATED / SYNTHETIC / COMPUTED | M | dataClass at origin (§3.3) |
| 16 | accessMethod | enum | — | channel_push / api_pull / file_drop / db_replica / manual_entry | M | How data arrives |
| 17 | accessBinding | ref/object | — | →34 channel/API; →36 device output; sftp path; form ref | M | Concrete binding (method-specific) |
| 18 | updateFrequency | object | — | schedule (cron/ISO 8601 recurrence) + expected latency | M | e.g. PT15M push, latency ≤5 min; daily file 06:00 |
| 19 | dataFormat | object | — | format (json/cbor/csv/parquet/xml/mdb), schema ref (→34 EIG-COM-SCH-* or 39 schema record) | M | Wire/file format + schema |
| 20 | unitsDeclaration | enum | — | in_schema / per_field_doc | M | Units declared (UCUM, 38) |
| 21 | spatialScope | ref/object | — | →08 geo refs / building set / national | M | Coverage |
| 22 | temporalCoverage | object | — | start date, ongoing/end | M | History availability |
| 23 | reliabilityProfile | object | — | measured: availability %, on-time %, gap rate, mean latency (rolling 90 d) | M | Observed reliability (auto-maintained) |
| 24 | reliabilityFloor | object | — | thresholds per metric | M | Below floor → stream degraded + alert (76) |
| 25 | securityPosture | object | — | transport security (34 §5.5 ref), credential type, secret rotation, last review | M | Stream security |
| 26 | ingestionContract | object | — | validation profile (→40 rule set), landing zone, quarantine policy, dedup/idempotency rule | M | Arrival processing (A4) |
| 27 | linkedDatasets | array<ref> | — | →43 EIG-DSET-* | M | Datasets this stream feeds (≥1 once active) |
| 28 | variableRefs | array<ref> | — | →38 variable register | M | Variables carried |
| 29 | environment | enum | — | live / sandbox | M | Sandbox streams (incl. all DSP-SYN) NEST-side only |
| 30 | piiFields | array<string> | — | field list | C | Required if provider dataProtectionClass ≠ none; minimisation/pseudonymisation rule per field |
| 31 | status | enum | — | planned / testing / active / degraded / suspended / retired | M | Lifecycle |

## 6. Controlled Vocabularies

Provider types §3.1; trust classes §3.2; dataClass §3.3; accessMethod, status machines as fielded. Schedules: cron or RFC 5545 recurrence, DST-aware where local-time anchored (26 V10 pattern). Formats: closed list, extensions via governance.

## 7. Applicability Rules

- **A1 — No unregistered ingestion.** Every ingestion path in any platform (31) maps to a registered stream. Unregistered arrivals are quarantined, never landed (pairs 34 A4 / 36 A3).
- **A2 — Origin dataClass is immutable.** A stream's originDataClass never changes in flight; transformations producing a different class create a NEW derived stream (DSP-DRV) with lineage to the parent. SYNTHETIC never relabels to REAL/CALIBRATED under any transformation (hyperGrid no-fabrication standard, framework-wide).
- **A3 — Trust gates use.** Settlement-relevant quantities (27) only from TR-A streams; disclosure/reporting (28 V9, 77) only TR-A/TR-B REAL or CALIBRATED; TR-D streams feed evaluation sandboxes only.
- **A4 — Ingestion contract enforced.** Arrival pipeline: schema validation → quality profile (40) → dedup/idempotency → landing with full provenance (streamID, ingest timestamp, source timestamp, dataClass). Failures → quarantine zone with reason codes; quarantine is reviewed, never auto-deleted.
- **A5 — Sandbox firewall.** environment = sandbox streams land only in sandbox storage; all DSP-SYN providers are sandbox-side. One-way live→sandbox copies permitted with dataClass preserved (34 A5 pattern).
- **A6 — Licence and attribution honoured.** Open-data streams carry attribution metadata into derived products (39); restricted streams' linkage to datasets respects licence scope (43 access rules).
- **A7 — Personal data minimised.** piiFields are pseudonymised or dropped at ingestion unless a documented purpose + consent basis exists (03/14 consent register); special_category requires DPIA reference.

## 8. Validation Rules

- **V1** IDs unique; refs resolve; one accessBinding per stream consistent with accessMethod.
- **V2** Active streams have ≥1 linkedDataset and ≥1 variableRef.
- **V3** reliabilityProfile maintained automatically; staleness of profile >7 d → monitoring fault.
- **V4** Streams below reliabilityFloor for >24 h → status degraded + 76 alert; dependent models notified (44 dependency graph).
- **V5** DSP-SYN providers: environment sandbox enforced; generator ref (42) mandatory.
- **V6** TR-A claims require evidence: legal basis or certification ref (e.g. DSO HES contract, MID meter chain via 36 A7).
- **V7** Schema ref resolves and arriving payloads validate (CI on samples + runtime).
- **V8** Secret rotation within policy (34 §5.5 reuse); expired credentials → suspended.
- **V9** GDPR consistency: provider dataProtectionClass = personal/special_category ⇒ piiFields declared on all its streams carrying such data.
- **V10** Update frequency vs observed: if expected PT15M but observed gaps > 4 intervals, gap events logged for 40 completeness scoring.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 09 weather | Met streams bind 09 stations/variables (EIG-WX-VAR-*, EIG-WX-ST-*) |
| 13/20 network & market | DSO/market provider feeds |
| 15 organisation | Provider ownership |
| 34 communication | Channel/API access bindings; schema refs |
| 36 device | Device outputs are streams (dataStreamRefs back-reference) |
| 38 data | Variables carried; landing into data entities |
| 39 metadata | Stream metadata records; attribution propagation |
| 40 data_quality | Validation profiles; completeness scoring inputs |
| 42 synthetic_data | DSP-SYN providers wrap generators |
| 43 dataset | linkedDatasets; access-rule inheritance from licence/GDPR |

## 10. Benchmarking Requirements

Stream reliability league per provider type: availability, on-time delivery, gap rate, schema-failure rate. Provider scorecards quarterly; n≥10 streams for provider-type comparisons.

## 11. Dataset Requirements

The register itself snapshots daily (COMPUTED); quarantine logs retained ≥13 months; ingestion audit ≥7 years for TR-A settlement-feeding streams (27 retention).

## 12. Feature Requirements

Reliability features (gap patterns, latency distributions) feed data-quality models and model-input-availability risk features (44 dependency health).

## 13. Model Requirements

Models (44) declare input streams via dataset bindings; a model's deployment validity depends on its streams' status (V4 notification chain). No model consumes quarantined data.

## 14. KPI Requirements

`stream_availability_pct`, `on_time_delivery_pct`, `schema_validation_pass_pct`, `quarantine_rate`, `mean_ingest_latency_s`.

## 15. Response Derivative Requirements

RD computations consume only REAL/CALIBRATED streams with source-timestamp integrity (36 V11); stream lineage lets RD evidence packs (52) cite exact origins for audit.

## 16. Decision Requirements

Decision pipelines (60+) declare stream dependencies; degraded critical streams trigger declared fallbacks (e.g. forecast persistence mode) rather than silent continuation.

## 17. Ontology Mapping

- `eig:DataProvider`, `eig:DataStream` ⊑ `eig:DataEntity`; PROV-O: `prov:Entity` generation, `prov:wasDerivedFrom` for DSP-DRV lineage; DCAT alignment: `dcat:Distribution` access; SSN/SOSA `sosa:Observation` origin for device streams.

## 18. Knowledge Graph Mapping

Nodes: `(:DataProvider)`, `(:DataStream)`. Edges: `[:PROVIDES]`, `[:VIA_CHANNEL]->(:Channel)`, `[:FROM_DEVICE]->(:Device)`, `[:CARRIES]->(:Variable)`, `[:FEEDS]->(:Dataset)`, `[:DERIVED_FROM]->(:DataStream)` for DSP-DRV. Lineage queries traverse FEEDS/DERIVED_FROM end-to-end (value→stream→provider).

## 19. Digital Twin Mapping

Twin dataset requirements (33 §5.6) resolve to streams at instantiation; twin conformance checks verify stream status active and reliability above floor.

## 20. Governance

Owner: Data Management Lead (Karen Mould role pattern). New providers: trust assessment + licence review + security review. New streams: ingestion-contract test on samples before active. Suspension authority: data steward; TR-A changes require approver tier. Annual licence/attribution audit.

## 21. Versioning

Providers/streams: change records (append-only). Schema changes follow 34 §21 (dual-running ≥90 d). Stream retirement: closure marker, datasets keep historical lineage; IDs never reused.

## 22. Example Records

### EIG-DSP-0003 — ESB Networks HES (smart metering)

- providerType DSP-DSO; ownerOrgRef ESB Networks (15); trustClass TR-A (evidence: DSO data-access agreement)
- licence: contract; dataProtectionClass personal (MPRN-linked consumption); status active

### EIG-DS-00211 — Smart-meter HH consumption, Aran portfolio

- providerRef EIG-DSP-0003; originDataClass REAL; accessMethod file_drop (SFTP daily 06:30, D+1 latency)
- dataFormat: csv per HES spec (schema record 39); variables: active_import_kwh (30-min, transitioning 15-min per 20 settlementPeriod flag)
- spatialScope: MPRN set ↔ 02 buildings (reconciles 13 CP, P14 V10); temporalCoverage 2024-01→ongoing
- reliabilityProfile (90 d): availability 99.4%, on-time 97.8%; floor: ≥98%/≥95%
- ingestionContract: schema check → 40 profile EIG-DQ-R-014 → MPRN dedup → landing; piiFields [mprn → pseudonym map]; trust TR-A → settlement-eligible (27 metered)
- linkedDatasets: [EIG-DSET-ARAN-ELEC-HH]

### EIG-DS-04471 — ASHP power telemetry, IE_B0009

- providerRef EIG-DSP-0010 (ODEON device fleet, DSP-DEV); device EIG-DEV-001847 (36); via EIG-COM-CHN-00041→00113 chain
- originDataClass REAL; PT1M push, latency ≤60 s; format json (EIG-COM-SCH-TS-RESULT-v1)
- Feeds: EIG-DSET-ODEON-ASSET-TELEM; variables hp_power_w; RD-eligible (gateway-stamped timestamps)
- Companion: EIG-DS-04472 flow_temp (same bindings)

### EIG-DS-00890 — Met Éireann Cork Airport observations

- providerRef EIG-DSP-0005 (DSP-MET, TR-B); station EIG-WX-ST-0007 (09); variables ⊂ EIG-WX-VAR-001..022
- api_pull hourly; licence open (attribution "Met Éireann" propagated via 39); originDataClass REAL

### EIG-DS-09102 — SYNTH_GRID_IE national synthetic demand

- providerRef EIG-DSP-0021 (DSP-SYN wrapping generator EIG-SYN-GEN-0004, 42); originDataClass SYNTHETIC; environment sandbox (V5 ✓)
- Feeds NEST scenario datasets only (A5); never relabelled (A2 — hyperGrid no-fabrication standard)

## 23. Completion Criteria

- [x] Source identity, type, owner, access method, update frequency, format, reliability, security, linked datasets — all per job spec
- [x] Traceable ingestion: registered-streams-only rule, ingestion contracts, quarantine machinery (A1/A4)
- [x] dataClass-at-origin immutability incl. no-fabrication standard (A2)
- [x] Trust classes gating settlement/disclosure (A3 → 27/28)
- [x] GDPR minimisation (A7) and licence/attribution propagation (A6)
- [x] Cross-refs resolved: EIG-DS-04471/04472 (minted in 36), MPRN/CP hook, EIG-WX-ST-0007, sandbox synthetic streams
- [x] Example records across DSO, device, met, synthetic providers
