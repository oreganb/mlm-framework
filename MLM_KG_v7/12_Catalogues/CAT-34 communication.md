---
type: catalogue
ontology: EIG-MLM
catalogue_no: 34
catalogue_id: EIG-CAT-034
entity: communication
band: flexibility
status: active
tags: [eig-mlm, catalogue, band:flexibility, entity:communication]
aliases: ["EIG-CAT-034", "Catalogue 34", "communication"]
---

# CAT-34 — Communication

> **EIG-CAT-034** · band: **Flexibility & Platform** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-11 asset]] · [[CAT-13 utility_network]] · [[CAT-16 role]] · [[CAT-29 flexibility_service]] · [[CAT-31 platform]] · [[CAT-32 digital_twin]]
**Used by (downstream):** [[CAT-35 edge_infrastructure]] · [[CAT-36 device]] · [[CAT-37 data_source]] · [[CAT-73 application]] · [[CAT-76 alert]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-034`
**Entity prefixes:** `EIG-COM-PRT-` (protocol profiles), `EIG-COM-CHN-` (channels), `EIG-COM-SCH-` (message schemas), `EIG-COM-API-` (API definitions)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Platform Engineering Lead
**Depends on (upstream):** 11 asset, 13 utility_network, 16 role, 29 flexibility_service, 31 platform, 32 digital_twin
**Used by (downstream):** 35 edge_infrastructure, 36 device, 37 data_source, 73 application, 76 alert

---

## 1. Definition

The **Communication Catalogue** is the authoritative register of communication protocols, channels, message schemas, and APIs used across the EIG MLM framework. It defines *how* bits move between devices, edge nodes, platforms, and external parties: which protocol profile applies, over which channel, carrying which versioned message schema, under which security and latency contract.

Four entity families:
- **Protocol profile** (`EIG-COM-PRT-*`): a constrained, versioned usage profile of a standard protocol (MQTT, BACnet, Modbus, OPC-UA, LoRaWAN, HTTPS, …). Profiles pin versions, QoS, security options, and payload encodings so "MQTT" means one thing framework-wide.
- **Channel** (`EIG-COM-CHN-*`): a concrete communication path instance (broker, fieldbus segment, LoRaWAN network, API endpoint group) binding endpoints, a protocol profile, and an SLA.
- **Message schema** (`EIG-COM-SCH-*`): versioned payload definition with field-level types, units, and provenance fields.
- **API definition** (`EIG-COM-API-*`): versioned REST/streaming API contract exposed by a platform (31), governed by RBAC (16) and the provenance envelope (31 A3).

## 2. Scope

**In scope:** protocol profiles for the ten mandated families (§3); channel registration with endpoints, SLAs, and security posture; message schema registry with versioning and compatibility rules; API registry; latency classes; interoperability and bridging rules; security baseline (TLS, auth, key rotation).

**Out of scope:** physical devices (36), edge compute (35), data semantics beyond payload structure (38/39), network electrical topology (13), application logic (73).

## 3. Classification Structure

### 3.1 Protocol families (`protocolFamily`)

| Code | Family | Standard | Typical Use |
|---|---|---|---|
| PRT-MQTT | MQTT | MQTT 3.1.1 / 5.0 (ISO/IEC 20922) | Telemetry, dispatch signals, edge↔platform |
| PRT-BAC | BACnet | ISO 16484-5 (BACnet/IP, MS/TP) | BMS integration, HVAC points |
| PRT-MOD | Modbus | Modbus TCP / RTU | Meters, inverters, legacy PLCs |
| PRT-OPC | OPC-UA | IEC 62541 | Industrial assets, secure pub/sub |
| PRT-LORA | LoRaWAN | LoRaWAN 1.0.4 (EU868) | Battery sensors, remote/rural telemetry |
| PRT-HTTPS | HTTPS/REST | HTTP/1.1, HTTP/2 + TLS 1.2+ | APIs, webhooks, file transfer |
| PRT-WS | WebSocket/SSE | RFC 6455 / SSE | Dashboards, live streams |
| PRT-AMQP | AMQP | AMQP 0-9-1 / 1.0 | Inter-platform queues |
| PRT-SFTP | SFTP | SSH File Transfer | Batch files (market results, registers) |
| PRT-DLMS | DLMS/COSEM | IEC 62056 | Smart-meter readout (via DSO/HES) |

### 3.2 Channel classes (`channelClass`)

| Code | Class | Description |
|---|---|---|
| CHN-FLD | Field channel | Device↔gateway (Modbus segment, BACnet network, LoRaWAN) |
| CHN-EDG | Edge channel | Gateway/edge↔platform (MQTT over TLS, typical) |
| CHN-PLT | Platform channel | Platform↔platform internal (AMQP, REST, private VPC) |
| CHN-EXT | External channel | Platform↔external party (market operator, DSO, weather provider) |
| CHN-USR | User channel | Platform↔user-facing apps (HTTPS, WS) |

### 3.3 Latency classes (`latencyClass`)

Aligned with 29 A8 activation-latency contract:

| Code | Bound | Drives |
|---|---|---|
| LAT-RT | ≤1 s end-to-end | FS-FRQ frequency services, fast control loops |
| LAT-FAST | ≤60 s | Explicit dispatch (FS-DR explicit), alarms |
| LAT-STD | ≤900 s | Price signals, schedules, routine telemetry |
| LAT-BATCH | ≤24 h | Settlement files, registry syncs, reports |

## 4. Hierarchy

```
Protocol family (PRT-MQTT)
└── Protocol profile (EIG-COM-PRT-MQTT-01, versioned)
    └── Channel (EIG-COM-CHN-00041: Aran edge MQTT over TLS)
        └── Topic/point namespace
            └── Message schema binding (EIG-COM-SCH-FLEX-DISPATCH-v2)
API definitions (EIG-COM-API-*) sit on CHN-USR/CHN-EXT/CHN-PLT channels and bind schemas per endpoint.
```

## 5. Field Groups & Fields

### 5.1 Protocol Profile (`EIG-COM-PRT-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | profileID | string | — | `EIG-COM-PRT-[A-Z]+-\d{2}` | M | Unique profile ID |
| 2 | profileVersion | string | — | SemVer | M | Profile version |
| 3 | protocolFamily | enum | — | §3.1 | M | Base protocol |
| 4 | standardVersion | string | — | pinned (e.g. "MQTT 5.0") | M | Exact standard version permitted |
| 5 | transport | enum | — | tcp / udp / tls / dtls / serial / radio_eu868 | M | Transport binding |
| 6 | securityBaseline | object | — | see §5.5 | M | Mandatory security settings |
| 7 | qosPolicy | object | — | family-specific (e.g. MQTT QoS 1 default, QoS 2 for dispatch) | M | Delivery guarantees |
| 8 | payloadEncodings | array<enum> | — | json / cbor / protobuf / native | M | Permitted encodings |
| 9 | maxPayloadBytes | int | B | >0 | M | Payload ceiling |
| 10 | keepAlive / pollPolicy | object | — | family-specific | M | Liveness/polling rules |
| 11 | bridgingAllowed | array<ref> | — | →other profileIDs | O | Profiles this may bridge to (e.g. BACnet→MQTT) |
| 12 | status | enum | — | draft / active / deprecated | M | Lifecycle |

### 5.2 Channel (`EIG-COM-CHN-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 13 | channelID | string | — | `EIG-COM-CHN-\d{5}` | M | Unique channel ID |
| 14 | channelClass | enum | — | §3.2 | M | Channel class |
| 15 | profileRef | ref | — | →profileID@version | M | Protocol profile in force |
| 16 | endpointA / endpointB | object | — | typed refs: →36 device / →35 edge node / →31 platform / external party (15 org) | M | Channel endpoints (B may be a set for pub/sub) |
| 17 | direction | enum | — | a_to_b / b_to_a / bidirectional | M | Data flow direction |
| 18 | latencyClass | enum | — | §3.3 | M | Contracted latency |
| 19 | availabilityTargetPct | decimal | % | 0–100 | M | SLA availability (e.g. 99.5) |
| 20 | throughputProfile | object | — | msg/s, bytes/s peaks | M | Capacity contract |
| 21 | networkPath | object | — | e.g. fibre / ubiquiti_ptp / starlink / 4g / lorawan_eu868 | M | Physical/logical path; informs degraded-mode planning (35) |
| 22 | failoverPath | object | — | secondary networkPath + switch criteria | C | Required if latencyClass ∈ {LAT-RT, LAT-FAST} |
| 23 | offlineBufferPolicy | object | — | store-and-forward depth, retention | M | Behaviour when disconnected (pairs with 31 A7 edge degraded-autonomous) |
| 24 | environment | enum | — | live / sandbox | M | SBX channels terminate only on EIG-PLT-NEST (firewall) |
| 25 | monitoringRef | ref | — | →76 alert rules | M | Health monitoring binding |
| 26 | status | enum | — | planned / active / degraded / retired | M | Operational status |

### 5.3 Message Schema (`EIG-COM-SCH-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 27 | schemaID | string | — | `EIG-COM-SCH-[A-Z0-9-]+-v\d+` | M | Schema ID with major version in name |
| 28 | schemaVersion | string | — | SemVer (full) | M | Full version |
| 29 | purpose | enum | — | telemetry / dispatch / schedule / market / settlement_file / alert / registry_sync / control_ack / heartbeat | M | Message purpose |
| 30 | encoding | enum | — | json / cbor / protobuf | M | Wire encoding |
| 31 | schemaDefinition | uri/blob | — | JSON Schema 2020-12 / .proto | M | Machine-validatable definition |
| 32 | fieldDictionary | array<object> | — | field, type, unit (UCUM), allowed values, req | M | Every payload field defined — no undocumented fields |
| 33 | provenanceFields | object | — | dataClass, sourceRef, timestamp (RFC 3339 UTC), sequence | M | Mandatory envelope fields on all data-bearing messages (31 A3) |
| 34 | idempotencyKeyField | string | — | field name | C | Required for purpose ∈ {dispatch, control_ack, settlement_file} |
| 35 | compatibilityPolicy | enum | — | backward / forward / full / none | M | Evolution contract (default backward) |
| 36 | exampleMessage | blob | — | valid instance | M | At least one validated example |
| 37 | status | enum | — | draft / active / deprecated | M | Lifecycle |

### 5.4 API Definition (`EIG-COM-API-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 38 | apiID | string | — | `EIG-COM-API-[A-Z0-9-]+` | M | API ID |
| 39 | apiVersion | string | — | SemVer; major in URL path `/v{n}` | M | Version |
| 40 | platformRef | ref | — | →31 EIG-PLT-* | M | Exposing platform (one platform of record per API, 31 V2) |
| 41 | style | enum | — | rest / streaming_ws / streaming_sse / webhook / sftp_drop | M | Interaction style |
| 42 | specDefinition | uri | — | OpenAPI 3.1 / AsyncAPI 2.x | M | Machine-readable contract |
| 43 | authMethod | enum | — | oauth2_cc / oauth2_pkce / mtls / api_key_hmac / ssh_key | M | Authentication; api_key plain FORBIDDEN |
| 44 | rbacBinding | array<ref> | — | →16 permissions `domain:resource:action[:scope]` | M | Permission required per endpoint group (31 A4) |
| 45 | rateLimitPolicy | object | — | req/min per principal | M | Throttling contract |
| 46 | provenanceEnvelope | boolean | — | TRUE for data APIs | M | 31 A3: every data response carries dataClass per value/series |
| 47 | schemaBindings | array<ref> | — | →schemaIDs | M | Schemas served/accepted |
| 48 | deprecationPolicy | object | — | sunset notice ≥180 d | M | Lifecycle commitment |

### 5.5 Security Baseline (object reused by profiles/channels)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 49 | encryptionInTransit | enum | tls12 / tls13 / dtls12 / lorawan_aes128 / opcua_signencrypt | M | Minimum: TLS 1.2; field protocols use native crypto (LoRaWAN AES-128, OPC-UA SignAndEncrypt) |
| 50 | mutualAuth | enum | mtls / client_cert / psk / oauth2 / abp_otaa | M | Both ends authenticate; OTAA preferred over ABP for LoRaWAN |
| 51 | certAuthority | ref | EIG internal CA / approved external | M | PKI root |
| 52 | keyRotationDays | int | ≤365; ≤90 for CHN-EXT | M | Rotation policy |
| 53 | legacyExemption | object | scope + compensating controls + expiry | C | For non-securable field buses (Modbus RTU, BACnet MS/TP): physical segmentation + gateway-terminated security, expiry ≤24 months, reviewed |
| 54 | securityStandardRefs | array | IEC 62443-3-3 SL2 min / NIS2 / GDPR | M | Compliance anchors (94/93) |

## 6. Controlled Vocabularies

All enums in §3 and §5; UCUM for units in field dictionaries; RFC 3339 UTC timestamps everywhere (DST handled at presentation, consistent with 21 V6 / 26 V10); dataClass vocabulary REAL / CALIBRATED / SYNTHETIC / COMPUTED owned framework-wide, reused here in provenanceFields.

## 7. Applicability Rules

- **A1 — No unprofiled protocols.** Every channel binds a registered protocol profile. Raw/ad-hoc protocol use is non-conformant; new needs → new profile via governance.
- **A2 — Schemas are the only payloads.** Every data-bearing message on a registered channel validates against a bound `EIG-COM-SCH-*` version. Unversioned or undocumented payloads fail conformance.
- **A3 — Latency class must support the service.** A channel carrying activation signals for a flexibility service (29) must have latencyClass at or faster than the service's activation contract: FS-FRQ → LAT-RT, explicit dispatch → LAT-FAST, price signals → LAT-STD (29 A8). Enrolment (29) MUST verify the channel chain end-to-end.
- **A4 — Provenance envelope.** All data-bearing messages and all data API responses carry provenanceFields/dataClass (31 A3). Messages lacking provenance are quarantined, not ingested.
- **A5 — Sandbox firewall.** `environment = sandbox` channels terminate only on sandbox platforms (EIG-PLT-NEST); no channel bridges sandbox↔live data planes (20 A2 / 26 A3 / 27 A3 / 31 A2 / 32 A6). Registry-sync messages crossing the boundary are one-way (live→sandbox) and carry dataClass unchanged.
- **A6 — Control messages are idempotent and acknowledged.** purpose ∈ {dispatch, control_ack}: idempotency key mandatory (field 34), QoS exactly-once semantics (MQTT QoS 2 or equivalent), explicit ack with timeout → retry → alarm escalation (76).
- **A7 — Security baseline non-negotiable.** No plaintext transport for CHN-EDG/CHN-PLT/CHN-EXT/CHN-USR. Field-bus legacy exemptions only per field 53 with expiry. Life-safety systems are NEVER write targets over any registered channel (04/29 A7 inheritance).
- **A8 — One schema authority.** A message purpose+domain pair has exactly one active schema lineage; competing schemas for the same purpose are a registry error.

## 8. Validation Rules

- **V1** IDs unique and pattern-valid; refs resolve (CI).
- **V2** Profile standardVersion pinned; channels referencing deprecated profiles flagged for migration.
- **V3** Channel latencyClass ≥ strictest bound of all services routed over it (cross-check 29 enrolments).
- **V4** failoverPath present where required (field 22); failover tested ≤180 d (evidence ref).
- **V5** Every schema has a validating schemaDefinition and ≥1 example that passes validation in CI.
- **V6** Backward-compat check on schema MINOR/PATCH releases: old consumers must parse new messages (automated contract test).
- **V7** API authMethod ≠ plain api_key; rbacBinding non-empty; data APIs have provenanceEnvelope TRUE.
- **V8** keyRotationDays within policy; certs not within 30 d of expiry (monitored, 76).
- **V9** environment consistency: sandbox channels' both endpoints are sandbox-side.
- **V10** Legacy exemptions (field 53) not expired; expired exemptions force channel status = degraded.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 11 asset | Assets' connectivity declared via channel bindings; control authority flows over A6-conformant channels |
| 13 utility_network | External channels to DSO/TSO systems; smart-meter DLMS path |
| 16 role | RBAC bindings on APIs; permission grammar reuse |
| 29 flexibility_service | Latency classes implement 29 A8; enrolment verifies channel chain (A3) |
| 31 platform | Platforms expose APIs; provenance envelope; platform-of-record per API |
| 32 digital_twin | Twin data ingestion channels; M4 control channels require A6 |
| 35 edge_infrastructure | Edge nodes are channel endpoints; offline buffering pairs with edge autonomy |
| 36 device | Devices bind field channels and protocol profiles |
| 37 data_source | Data sources declare access via channels/APIs |
| 76 alert | Channel health monitoring and escalation |
| 93/94 regulation/standard | IEC 62443, NIS2, GDPR anchors |

## 10. Benchmarking Requirements

Channel KPIs: measured p95/p99 latency vs latencyClass bound, availability vs SLA, message loss rate, schema validation failure rate, security posture score. Published per channel class monthly; cohort n≥10 for cross-site comparisons.

## 11. Dataset Requirements

Channel telemetry (health metrics) is itself a dataset (PT1M heartbeats, REAL) feeding 40 quality monitoring and 76 alerts. Message audit logs retained ≥13 months (settlement-relevant channels ≥7 years per 27 retention).

## 12. Feature Requirements

Latency/availability series feed reliability features for edge model-placement decisions (35) and dispatch-risk features (61).

## 13. Model Requirements

Anomaly models on channel telemetry (44) for fault prediction; no model may publish control messages except through an A6-conformant channel with authority checks (16/29).

## 14. KPI Requirements

`channel_availability_pct`, `latency_p99_ms`, `message_loss_rate`, `dispatch_ack_success_rate` — the last feeding flex delivery diagnostics (EIG-ABM-MET-015 root-cause analysis).

## 15. Response Derivative Requirements

RD measurement integrity depends on timestamp fidelity: schemas for telemetry carry source timestamps (device clock) and ingest timestamps; clock sync discipline (NTP, drift ≤1 s for LAT-STD, PTP/GPS for LAT-RT) is part of profile qosPolicy. RD computations (52) MUST use source timestamps.

## 16. Decision Requirements

Dispatch decisions (60/61) emit only via A6 channels; ack timeout policy and fallback (local edge rule, 35) defined per channel. Kill-switch commands (32 V9) use a dedicated LAT-FAST channel with independent failover.

## 17. Ontology Mapping

- `eig:ProtocolProfile`, `eig:Channel`, `eig:MessageSchema`, `eig:API` ⊑ `eig:CommunicationEntity`
- SAREF alignment: `saref:isUsedFor` communication functions; OPC-UA/BACnet semantic mappings via 81 semantic_mapping
- `eig:carriesSchema`, `eig:hasLatencyClass`, `eig:securedBy`

## 18. Knowledge Graph Mapping

Nodes: `(:ProtocolProfile)`, `(:Channel)`, `(:MessageSchema)`, `(:API)`. Edges: `(:Channel)-[:USES_PROFILE {version}]`, `(:Channel)-[:CONNECTS]->(:Device|:EdgeNode|:Platform|:Organisation)`, `(:Channel)-[:CARRIES]->(:MessageSchema)`, `(:API)-[:EXPOSED_BY]->(:Platform)`, `(:API)-[:REQUIRES_PERMISSION]->(:Permission)`. Path queries answer "can dispatch reach asset X within 60 s?" by traversing channel chains and latency classes.

## 19. Digital Twin Mapping

Twins ingest via registered channels only; twin templates (33) imply channel requirements through dataset minResolution. Network twins (DTT-NET) may model the communication overlay itself for resilience studies (sandbox).

## 20. Governance

Owner: Platform Engineering Lead; security co-approval by Security Officer role (16) for profiles, external channels, and exemptions. New schemas: design review + contract tests. Profile changes affecting field devices: coordinated with 36 firmware governance. Quarterly security review of CHN-EXT.

## 21. Versioning

Profiles/schemas/APIs: SemVer. Schema MAJOR bumps embed in schemaID (…-v2 → …-v3); dual-running window ≥90 d with bridge translation where feasible. API MAJOR in URL path; sunset ≥180 d notice. Channels: configuration versioned via change records, not SemVer.

## 22. Example Records

### EIG-COM-PRT-MQTT-01 @ 2.0.0 — Standard edge MQTT profile

- protocolFamily PRT-MQTT; standardVersion "MQTT 5.0"; transport tls (TLS 1.3)
- security: mtls, EIG internal CA, keyRotationDays 180
- qosPolicy: telemetry QoS 1; dispatch/control QoS 2; LWT mandatory for edge clients
- payloadEncodings [json, cbor]; maxPayloadBytes 65,536; keepAlive 60 s
- Topic namespace: `eig/{env}/{siteID}/{deviceID}/{purpose}` — env ∈ {live, sbx} enforces A5 at broker ACL level

### EIG-COM-CHN-00041 — Aran Islands edge uplink (live)

- channelClass CHN-EDG; profileRef EIG-COM-PRT-MQTT-01@2.0.0; direction bidirectional
- endpointA: EIG-EDGE-NODE-0007 (Inis Mór cluster gateway, 35); endpointB: EIG-PLT-FLEXUS broker
- latencyClass LAT-FAST; availabilityTargetPct 99.0; networkPath ubiquiti_ptp primary; failoverPath starlink (switch on 30 s loss); offlineBufferPolicy: store-and-forward 72 h depth at edge
- monitoringRef EIG-ALERT-CHN-STD; status active
- Services routed: EIG-FS-DR-01 explicit dispatch (LAT-FAST ✓ per A3/V3)

### EIG-COM-SCH-FLEX-DISPATCH-v2 @ 2.1.0 — Flexibility dispatch message

- purpose dispatch; encoding json; compatibilityPolicy backward
- fieldDictionary (abridged): dispatchID (string, idempotency key), serviceRef (→29 EIG-FS-*), enrolmentRef, assetRef (→11), action (enum shed/shift/modulate/charge/discharge), magnitudeKW (decimal, kW), startAt / endAt (RFC 3339 UTC), responseDeadline (RFC 3339), priceSignal (decimal EUR/kWh, O), provenance {dataClass, sourceRef, timestamp, sequence}
- idempotencyKeyField: dispatchID; QoS 2 required (A6)
- exampleMessage validated in CI; referenced by 29 activation contract and 31 EIG-PLT-FLEXUS
- v1→v2 change: added responseDeadline + rebound advisory block; backward compatible consumers confirmed

### EIG-COM-API-FLEXUS-DATA @ 3.2.0 — FLEXUS data API

- platformRef EIG-PLT-FLEXUS; style rest; spec OpenAPI 3.1; URL `/v3`
- authMethod oauth2_cc; rbacBinding e.g. `data:timeseries:read:site`; rateLimit 600 req/min
- provenanceEnvelope TRUE — every series response carries per-point dataClass (REAL/CALIBRATED/SYNTHETIC/COMPUTED)
- schemaBindings: [EIG-COM-SCH-TS-QUERY-v1, EIG-COM-SCH-TS-RESULT-v1]
- deprecation: v2 sunset 2026-12-31 (notice issued 2026-05-01, ≥180 d ✓)

### EIG-COM-PRT-LORA-01 @ 1.1.0 — LoRaWAN sensor profile

- standardVersion "LoRaWAN 1.0.4 EU868"; transport radio_eu868; mutualAuth otaa; encryption lorawan_aes128
- qosPolicy: confirmed uplinks for alarms only (duty-cycle respect); SF7–SF12 adaptive; maxPayloadBytes 51 (DR0-safe)
- Typical channels: Aran cluster-gateway LoRaWAN networks (per ODEON topology: cluster gateways rather than per-building)

### EIG-COM-PRT-MOD-01 @ 1.0.0 — Modbus TCP metering profile (with RTU legacy exemption pattern)

- Modbus TCP over site LAN, gateway-terminated TLS uplink; register maps versioned per device type (36)
- RTU variant only under legacyExemption: physically segmented bus, gateway is security boundary, expiry ≤24 months, compensating monitoring

## 23. Completion Criteria

- [x] All ten mandated protocol families profiled (MQTT, BACnet, Modbus, OPC-UA, LoRaWAN, HTTPS + WS/AMQP/SFTP/DLMS)
- [x] Channels, schemas, APIs fully fielded with types/units/required flags
- [x] Latency classes bound to 29 A8 activation contract (A3/V3)
- [x] Security baseline incl. legacy exemption machinery and IEC 62443/NIS2/GDPR anchors
- [x] Provenance envelope and sandbox firewall enforced at message level (A4/A5)
- [x] Cross-refs resolved: EIG-COM-SCH-FLEX-DISPATCH-v2 (cited by 29/31), NEST topic firewall, Aran connectivity (ubiquiti_ptp/starlink, cluster-gateway LoRaWAN)
- [x] Idempotent control messaging defined (A6)
- [x] Example records across profiles, channel, schema, API
