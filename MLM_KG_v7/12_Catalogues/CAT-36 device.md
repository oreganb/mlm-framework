---
type: catalogue
ontology: EIG-MLM
catalogue_no: 36
catalogue_id: EIG-CAT-036
entity: device
band: flexibility
status: active
tags: [eig-mlm, catalogue, band:flexibility, entity:device]
aliases: ["EIG-CAT-036", "Catalogue 36", "device"]
---

# CAT-36 — Device

> **EIG-CAT-036** · band: **Flexibility & Platform** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-02 building]] · [[CAT-04 space]] · [[CAT-10 asset_classification]] · [[CAT-11 asset]] · [[CAT-34 communication]] · [[CAT-35 edge_infrastructure]]
**Used by (downstream):** [[CAT-37 data_source]] · [[CAT-38 data]] · [[CAT-40 data_quality]] · [[CAT-52 response_derivative]] · [[CAT-60 decision_intelligence]] · [[CAT-63 event]] · [[CAT-76 alert]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-036`
**Entity prefixes:** `EIG-DEV-` (device instances), `EIG-DEV-TYP-` (device types)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG IoT & Metering Lead
**Depends on (upstream):** 02 building, 04 space, 10 asset_classification, 11 asset, 34 communication, 35 edge_infrastructure
**Used by (downstream):** 37 data_source, 38 data, 40 data_quality, 52 response_derivative, 60–63 control, 76 alert

---

## 1. Definition

A **Device** is a discrete digital/connected hardware unit that senses, meters, actuates, or interfaces within the EIG MLM framework: sensors, smart meters, sub-meters, actuators, controllers' I/O heads, smart plugs, heat-pump interface units, inverter comms modules, and similar. The catalogue registers two entity families:

- **Device type** (`EIG-DEV-TYP-*`): manufacturer/model-level definition — capabilities, protocols, data outputs, control inputs, firmware lineage, certification.
- **Device instance** (`EIG-DEV-*`): a physical unit — serial, location, bindings, firmware state, security status, calibration state.

Devices are the framework's contact surface with physical reality: every REAL data point originates at a device, and every control action terminates at one. The catalogue therefore anchors provenance (what produced this value) and actuation accountability (what executed this command).

**Boundary with Catalogue 11 (asset):** an asset is the energy-significant plant (heat pump, battery, AHU); a device is the digital instrument attached to or embedded in it (its meter, sensor, control interface). An ASHP is an asset (11); its Modbus interface unit, circuit sub-meter, and zone temperature sensors are devices (36) bound to it. Some units are both (a smart EV charger is asset + integral device); these are registered in both with cross-references, energy fields owned by 11, digital fields owned by 36.

## 2. Scope

**In scope:** device type register (identity, capabilities, protocol support, certified firmware); device instances (placement, bindings, firmware, security, calibration); data output definitions (variables, units, resolution, accuracy class); control input definitions (commands, safety interlocks); lifecycle and security state machines; smart-meter specifics (MPRN linkage via DSO readout path).

**Out of scope:** energy plant characteristics (11), communication channel/protocol definitions (34 — bound, not defined), edge compute (35), data storage semantics (38).

## 3. Classification Structure

Device categories (`deviceCategory`):

| Code | Category | Examples |
|---|---|---|
| DEV-MTR | Meter | Main smart meter (DLMS via DSO), sub-meter, heat meter (EN 1434), gas meter |
| DEV-SEN | Sensor | Temperature, RH, CO₂, occupancy/PIR, lux, pressure, flow, energy CT |
| DEV-ACT | Actuator | Relay, valve actuator, damper actuator, smart plug (switching) |
| DEV-CTL | Control interface | Heat-pump Modbus interface, inverter comms module, EV-charger controller head, thermostat |
| DEV-GWI | Gateway-integrated I/O | I/O modules on gateways/controllers (35 CLS-C/G) |
| DEV-HMI | Human interface | In-home display, override button/panel |

Secondary axes:
- **measurementDomain** (for MTR/SEN): electricity / heat / gas / water / indoor_environment / outdoor_environment / state
- **controlDomain** (for ACT/CTL): switching / modulation / setpoint / mode
- **mountContext**: building (02) / space (04) / asset (11) / network point (13)

## 4. Hierarchy

```
Device type (EIG-DEV-TYP-*)            e.g. "Brand X heat meter EN 1434 class 2"
└── Firmware lineage (versioned, certified set)
└── Device instance (EIG-DEV-*)
    ├── Mount binding (building/space/asset)
    ├── Node binding (→35 serving edge node)
    ├── Channel binding (→34 field channel)
    ├── Data output streams (→37/38)
    └── Control input surface (commands)
```

## 5. Field Groups & Fields

### 5.1 Device Type (`EIG-DEV-TYP-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | typeID | string | — | `EIG-DEV-TYP-\d{4}` | M | Type ID |
| 2 | deviceCategory | enum | — | §3 | M | Category |
| 3 | manufacturer | string | — | ≤120 chars | M | Manufacturer |
| 4 | modelDesignation | string | — | exact model/part no. | M | Model |
| 5 | measurementDomain | enum | — | §3 | C | Required for MTR/SEN |
| 6 | controlDomain | enum | — | §3 | C | Required for ACT/CTL |
| 7 | supportedProtocols | array<ref> | — | →34 protocol families/profiles | M | e.g. [PRT-MOD, PRT-LORA] |
| 8 | dataOutputs | array<object> | — | see §5.3 | C | Output definitions (MTR/SEN/CTL) |
| 9 | controlInputs | array<object> | — | see §5.4 | C | Command definitions (ACT/CTL) |
| 10 | accuracyClass | string | — | e.g. "Class B (MID)", "EN 1434 Class 2", "±0.3 °C" | C | Metrological class; MANDATORY for settlement-relevant meters |
| 11 | certifications | array<enum> | — | CE / MID / EN1434 / RED / UKCA / IEC 62443 component | M | Regulatory certifications |
| 12 | certifiedFirmware | array<string> | — | version list | M | Firmware versions approved for fleet (A2) |
| 13 | powerProfile | object | — | mains / battery (chemistry, expected life) / energy_harvest / PoE | M | Power; battery devices declare expected life months |
| 14 | environmentalRating | string | — | IP rating, operating range | M | e.g. IP65, −20…+55 °C |
| 15 | securityCapabilities | object | — | crypto support, signed firmware, secure element | M | Drives channel security fit (34 §5.5) |
| 16 | eolDate / supportEndDate | date | — | ISO 8601 | O | Planning horizons |
| 17 | status | enum | — | evaluated / certified / restricted / banned | M | Fleet approval status (A1) |

### 5.2 Device Instance (`EIG-DEV-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 18 | deviceID | string | — | `EIG-DEV-\d{6}` | M | Unique device ID |
| 19 | typeRef | ref | — | →typeID | M | Device type |
| 20 | serialNumber | string | — | manufacturer serial | M | Physical identity |
| 21 | mountContext | object | — | →02 building + optional →04 space / →11 asset / →13 CP | M | Where mounted; asset-bound devices reference the asset |
| 22 | nodeRef | ref | — | →35 EIG-EDGE-NODE-* | M | Serving edge node |
| 23 | channelRef | ref | — | →34 EIG-COM-CHN-* (field channel) | M | Field channel binding |
| 24 | protocolProfileRef | ref | — | →34 profile@version | M | Profile in force |
| 25 | addressing | object | — | protocol-specific (Modbus unit ID + register map version, DevEUI, BACnet instance, topic) | M | Network identity |
| 26 | firmwareVersion | string | — | ∈ type certifiedFirmware | M | Installed firmware (V3) |
| 27 | firmwareUpdatePolicy | enum | — | ota_staged / manual_site_visit / dso_managed / frozen_justified | M | Update path; smart meters typically dso_managed |
| 28 | securityStatus | object | — | state ∈ {compliant, patch_due, vulnerable, quarantined}; credentials state; last assessment date | M | METIS-128 aligned (A4) |
| 29 | calibrationState | object | — | last cal date, due date, certificate ref | C | Required where accuracyClass declared; overdue → data quality flag (V6) |
| 30 | installDate / commissionedDate | date | — | ISO 8601 | M | Lifecycle dates |
| 31 | commissioningEvidence | ref | — | checklist/photos ref | M | Per deployment guide (QRC companion for field technicians) |
| 32 | batteryState | object | — | level %, est. replacement date | C | Required for battery devices; low battery → 76 alert |
| 33 | mprnLinkage | object | — | MPRN + readout path (dso_hes / local_p1 / ct_shadow) | C | Required for main electricity meters; reconciles with 02 MPRN and 13 CP (P14 V10) |
| 34 | dataStreamRefs | array<ref> | — | →37 EIG-DS-* | M | Registered data source streams produced |
| 35 | environment | enum | — | live / sandbox | M | Sandbox devices are virtual/emulated only (A5) |
| 36 | status | enum | — | planned / installed / commissioned / active / faulty / quarantined / retired | M | Lifecycle state machine (§6) |

### 5.3 Data Output Definition (object, on type)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 37 | variable | ref | — | →38 variable register / 09 EIG-WX-VAR-* where applicable | M | What is measured |
| 38 | unit | string | — | UCUM | M | Unit |
| 39 | nativeResolution | duration | — | ISO 8601 (e.g. PT1S, PT15M) | M | Fastest native sampling |
| 40 | reportingDefault | duration | — | ISO 8601 | M | Default push/poll interval |
| 41 | range | range | — | min/max | M | Measurement range |
| 42 | accuracy | string | — | per accuracyClass | M | Per-output accuracy |
| 43 | registerCumulative | boolean | — | TRUE/FALSE | M | Cumulative register vs interval value (meters: TRUE; ingestion must difference correctly, 38) |
| 44 | timestampSource | enum | — | device_clock_synced / device_clock_free / gateway_stamped | M | Provenance of time (RD integrity, 34 §15) |

### 5.4 Control Input Definition (object, on type)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 45 | command | string | — | verb (on/off, setpoint, modulate_pct, mode) | M | Command name |
| 46 | parameterSpec | object | — | type, unit, range | M | Command parameters |
| 47 | ackBehaviour | enum | — | confirmed_state_readback / ack_only / fire_and_forget_FORBIDDEN | M | Confirmation contract; fire-and-forget is non-conformant for any registered control (34 A6) |
| 48 | safetyInterlocks | array<string> | — | device-local interlocks | M | e.g. min-off-time, anti-cycling, frost protection — enforced ON DEVICE regardless of commands |
| 49 | lifeSafetyFlag | boolean | — | FALSE fixed for controllable inputs | M | Life-safety functions are never registered as controllable (04/29 A7) |
| 50 | maxCommandRate | object | — | commands/hour | M | Actuation rate limit (asset protection, anti-rebound) |

## 6. Controlled Vocabularies

Categories/domains §3; lifecycle: planned → installed → commissioned → active ↔ faulty; active → quarantined → (repair/verify) → active; any → retired. securityStatus states {compliant, patch_due, vulnerable, quarantined}. timestampSource, ackBehaviour, firmwareUpdatePolicy as fielded. dataClass at origin: device-produced values enter as REAL (or CALIBRATED where a correction model applies at ingestion — declared, never silent).

## 7. Applicability Rules

- **A1 — Certified types only.** Instances may only be created for types with status = certified (or restricted, within restriction scope). banned types cannot be instantiated; existing instances of newly-banned types are quarantined.
- **A2 — Firmware allow-list.** firmwareVersion must be in the type's certifiedFirmware set. Unknown firmware → securityStatus vulnerable → quarantine path.
- **A3 — Every stream is registered.** Each data output actually flowing is a registered data source (37) referenced in dataStreamRefs; orphan streams are quarantined at ingestion (pairs 34 A4 provenance rule).
- **A4 — Security lifecycle.** Devices follow METIS-128 Scan→Detect→Report→Quarantine→Repair→Verify→Rejoin. Quarantined devices: streams flagged (not deleted) with quality marker (40), control surface revoked. Default credentials are a commissioning blocker (V5).
- **A5 — Sandbox firewall.** environment = sandbox devices are emulated/virtual (NEST-side); no physical actuation from sandbox; SYNTHETIC streams only inside sandbox.
- **A6 — Control accountability.** Every executed command is logged with: commandID (idempotency key from 34 schema), issuing principal chain (16 RBAC → 29 service or 60 decision), pre/post state readback. Devices without confirmed_state_readback or ack_only behaviour are not eligible as FS actuation endpoints (29 enrolment check).
- **A7 — Metrology for settlement.** Only devices with declared accuracyClass, valid calibrationState, and MID/EN-class certification (as applicable) may source settlement-relevant quantities (27 metered quantity). Sub-metering for FS delivery measurement inherits the same bar where the product (22) demands it.
- **A8 — Smart-meter readout path.** Main-meter data arrives via the declared mprnLinkage path; ct_shadow (parallel CT measurement) is CALIBRATED, never substitutes REAL DSO settlement data (27 reconciliation distinguishes them).

## 8. Validation Rules

- **V1** IDs unique; patterns valid; refs resolve.
- **V2** Category-conditional fields present (outputs for MTR/SEN, inputs for ACT/CTL, accuracyClass for settlement meters, mprnLinkage for main meters).
- **V3** firmwareVersion ∈ certifiedFirmware (CI + runtime attestation where supported).
- **V4** channelRef's protocol profile ∈ type supportedProtocols; addressing schema matches profile.
- **V5** Commissioning gate: no default credentials, security assessment recorded, commissioningEvidence present — else status cannot reach commissioned.
- **V6** calibrationState not overdue; overdue → outputs carry quality flag (40) and settlement eligibility suspended (A7).
- **V7** Battery devices: batteryState monitored; level < threshold → 76 alert; expected-life accounting at fleet level.
- **V8** Control inputs: lifeSafetyFlag FALSE; safetyInterlocks non-empty; ackBehaviour ≠ fire_and_forget.
- **V9** mprnLinkage MPRN exists in 02 and reconciles to 13 CP (feeds P14 V10 reconciliation).
- **V10** Quarantined devices have zero active control bindings and flagged streams (auto-enforced with 35 V11 analogue).
- **V11** timestampSource = device_clock_free outputs are gateway_stamped at ingestion or excluded from RD computation (52 integrity).

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 02 building | Mount context; MPRN linkage |
| 04 space | Sensor placement context; zone mapping for twins |
| 10/11 asset | Asset-bound devices; dual-registered smart assets; envelope telemetry sources |
| 13 utility_network | CP linkage for network-point devices |
| 27 settlement | Metrology bar for metered quantities (A7/A8) |
| 29 flexibility_service | Actuation endpoint eligibility (A6); enrolment device checks |
| 34 communication | Channel/profile bindings; command idempotency; schemas |
| 35 edge_infrastructure | Serving node; device bindings on nodes |
| 37/38/40 data layer | Streams, variables, quality flags |
| 52 response_derivative | Timestamp integrity; actuation evidence |
| 76 alert | Battery, fault, security alerts |

## 10. Benchmarking Requirements

Fleet KPIs per type: failure rate, mean battery life vs declared, calibration drift at recalibration, command success rate, security incident rate. Type benchmarking informs procurement; cohort n≥10 instances before type statistics published.

## 11. Dataset Requirements

Device registry snapshot (daily); device health/event log (REAL, ≥13 months); command audit log (≥7 years where settlement-relevant, mirroring 27 retention).

## 12. Feature Requirements

Device health features (comms gap rate, battery slope, drift indicators) feed predictive maintenance and data-quality models (40/44).

## 13. Model Requirements

Per-type correction models (sensor bias, CT calibration) are declared CALIBRATED transformations at ingestion (38), versioned, never silent. Anomaly models for fault detection consume health logs.

## 14. KPI Requirements

`device_availability_pct`, `stream_completeness_pct` (feeds 40), `command_success_rate`, `calibration_compliance_pct`, `security_compliance_pct`.

## 15. Response Derivative Requirements

RD computation (52) requires: synced or gateway-stamped timestamps (V11), actuation evidence (command log + state readback) and measurement evidence (output streams) from the same device register — closing the loop from dispatch (34 schema) to physical effect to measured delivery (27/EIG-ABM-MET-015).

## 16. Decision Requirements

Decisions actuate only through registered control inputs respecting safetyInterlocks and maxCommandRate; device-local interlocks are the last line of defence and are never overridable remotely.

## 17. Ontology Mapping

- `eig:Device`, `eig:DeviceType` ⊑ `eig:PhysicalEntity`; SSN/SOSA: `sosa:Sensor`, `sosa:Actuator`, `sosa:Platform` (mount), `sosa:observes` / `sosa:actsOnProperty`
- SAREF: `saref:Device`, `saref:Meter`, `saref:Sensor`, `saref:hasFunction`; SAREF4BLDG placement
- Measurement semantics via QUDT/UCUM units (38/80 alignment)

## 18. Knowledge Graph Mapping

Nodes: `(:Device)`, `(:DeviceType)`. Edges: `[:OF_TYPE]`, `[:MOUNTED_IN]->(:Building|:Space)`, `[:ATTACHED_TO]->(:Asset)`, `[:SERVED_BY]->(:EdgeNode)`, `[:ON_CHANNEL]->(:Channel)`, `[:PRODUCES]->(:DataSource)`, `[:ACCEPTS]->(:Command)`. The dispatch-to-delivery audit path traverses: (:Trade)→(:Dispatch)→(:Command)→(:Device)→(:DataSource)→(:SettlementLine).

## 19. Digital Twin Mapping

Devices are the twin's sense/actuate periphery: twin templates (33) imply device requirements via dataset specs and asset coverage; twin state estimators consume device streams; M4 twin actions terminate at device control inputs inside guardrails.

## 20. Governance

Owner: IoT & Metering Lead; security co-governance per METIS-128 authority. Type certification: lab evaluation + security assessment + pilot (n≥3) before certified. Firmware certification per release. Field procedures: commissioning checklist + Quick Reference Card (single-page command cheat sheet for technicians — pending companion deliverable to the Jetson deployment guide). Decommissioning: credential revocation, register update, stream closure markers.

## 21. Versioning

Types: SemVer (new certified firmware = MINOR; capability/protocol change = MAJOR). Instances: change records (firmware updates, recalibrations, re-mounts) — append-only history. Register snapshots immutable for audit.

## 22. Example Records

### EIG-DEV-TYP-0012 — Heat-pump Modbus interface unit (certified)

- deviceCategory DEV-CTL; controlDomain setpoint+mode; manufacturer "NIBE"; model "Modbus 40"
- supportedProtocols [PRT-MOD (TCP via gateway)]; certifiedFirmware [2.11, 2.13]
- dataOutputs: compressor_power (W, PT1M, gateway_stamped), flow_temp (°C ±0.5, PT1M), status_word
- controlInputs: setpoint_offset (°C, ±3 range, confirmed_state_readback, interlocks: min-off 10 min, anti-cycle 3/h, frost protect), mode (enum, readback)
- lifeSafetyFlag FALSE; certifications [CE]; status certified

### EIG-DEV-001847 — NIBE interface at IE_B0009 (Inis Mór)

- typeRef EIG-DEV-TYP-0012; mountContext: building IE_B0009, asset EIG-AST-004211 (ASHP)
- nodeRef EIG-EDGE-NODE-0007; channelRef EIG-COM-CHN-00113 (Modbus); addressing: unit 3, register map v2.13
- firmwareVersion 2.13 (✓ V3); securityStatus compliant (assessed 2026-05-28); commissioned 2025-11-04, evidence ref ODEON-COMM-0091
- dataStreamRefs: [EIG-DS-04471 hp_power, EIG-DS-04472 flow_temp]; environment live; status active
- Role: actuation endpoint for EIG-FS-DR-01 enrolment (A6 ✓ readback confirmed); evidence chain feeds EIG-TRD-008834-style delivery measurement

### EIG-DEV-TYP-0031 — LoRaWAN indoor climate sensor

- DEV-SEN; measurementDomain indoor_environment; protocols [PRT-LORA (EIG-COM-PRT-LORA-01)]
- outputs: air_temp (°C ±0.3, native PT1M, reporting default PT15M, device_clock_free → gateway_stamped per V11), RH (±3%), battery_v
- powerProfile: battery Li-SOCl₂, expected life 60 months; environmentalRating IP30; certifications [CE, RED]
- certifiedFirmware [1.4.2]; status certified

### EIG-DEV-002203 — Climate sensor, IE_B0014 living zone

- typeRef EIG-DEV-TYP-0031; mountContext: building IE_B0014, space living_zone (04)
- nodeRef EIG-EDGE-NODE-0007 (cluster LoRaWAN); addressing DevEUI 70B3D5…; batteryState 87%, est. replacement 2030-08
- Streams feed twin EIG-DT-000877 (32) comfort compliance and RD thermal measurement

### EIG-DEV-TYP-0007 — MID Class B sub-meter (settlement-eligible)

- DEV-MTR; electricity; Modbus TCP; accuracyClass "Class B (MID)"; certifications [CE, MID]
- outputs: active_energy_import (kWh cumulative, registerCumulative TRUE, PT15M), active_power (W, PT1M)
- calibration interval 60 months; eligible for FS delivery measurement where 22 product demands metered evidence (A7)

## 23. Completion Criteria

- [x] Device identity, type, firmware, connectivity, protocols, data outputs, control inputs, security status — all fielded per job spec
- [x] IoT, metering, sensing, and control supported with category-specific machinery
- [x] Asset/device boundary defined against Catalogue 11
- [x] Metrology and settlement eligibility rules (A7/A8) aligned to 27
- [x] Control accountability and FS actuation eligibility (A6) aligned to 29/34
- [x] METIS-128 security lifecycle and sandbox firewall applied
- [x] MPRN/CP reconciliation hook (V9 → P14 V10)
- [x] Cross-refs resolved: EIG-AST-004211, IE_B0009/IE_B0014, EIG-EDGE-NODE-0007, EIG-FS-DR-01, EIG-DT-000877, QRC pending flag
- [x] Example records: two types + two instances + settlement-meter type
