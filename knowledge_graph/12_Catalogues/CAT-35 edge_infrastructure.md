---
type: catalogue
ontology: EIG-MLM
catalogue_no: 35
catalogue_id: EIG-CAT-035
entity: edge_infrastructure
band: flexibility
status: active
tags: [eig-mlm, catalogue, band:flexibility, entity:edge_infrastructure]
aliases: ["EIG-CAT-035", "Catalogue 35", "edge_infrastructure"]
---

# CAT-35 — Edge Infrastructure

> **EIG-CAT-035** · band: **Flexibility & Platform** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-02 building]] · [[CAT-11 asset]] · [[CAT-16 role]] · [[CAT-31 platform]] · [[CAT-32 digital_twin]] · [[CAT-34 communication]]
**Used by (downstream):** [[CAT-36 device]] · [[CAT-44 model]] · [[CAT-50 training]] · [[CAT-60 decision_intelligence]] · [[CAT-63 event]] · [[CAT-73 application]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-035`
**Entity prefixes:** `EIG-EDGE-NODE-` (nodes), `EIG-EDGE-CLS-` (node classes), `EIG-EDGE-DEP-` (model deployments), `EIG-EDGE-SITE-` (edge sites)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Edge Engineering Lead
**Depends on (upstream):** 02 building, 11 asset, 16 role, 31 platform, 32 digital_twin, 34 communication
**Used by (downstream):** 36 device, 44 model, 50 training, 60–63 decision/control, 73 application

---

## 1. Definition

The **Edge Infrastructure Catalogue** is the register of compute resources deployed between field devices and central platforms: controllers, gateways, edge servers, and TinyML nodes. It defines node classes (hardware/runtime capability tiers), node instances (physical deployments with location, connectivity, and security posture), edge sites (the per-building or per-cluster grouping), and model deployments (which MLM models run where, under what latency and degraded-mode contract).

The catalogue exists to make **distributed MLM deployment** deterministic: any model placement decision must be expressible as "model X (44), version v, runs on node class ≥ C at site S, with latency budget L and fallback behaviour F" — and verifiable against this register.

## 2. Scope

**In scope:** node class taxonomy with compute capability envelopes; node instances incl. hardware, OS/runtime, JetPack/firmware baselines, power/thermal constraints; edge sites and cluster topology; local model deployment records; degraded-mode/offline autonomy contracts; remote management, attestation, and update machinery; capacity planning fields.

**Out of scope:** sensors/actuators/meters themselves (36), communication channel definitions (34 — referenced, not redefined), model internals (44), central platform infrastructure (31).

## 3. Classification Structure

### 3.1 Node classes (`EIG-EDGE-CLS-*`)

| Code | Class | Indicative Hardware | Compute Envelope | Role |
|---|---|---|---|---|
| CLS-T (TinyML node) | Microcontroller-class | Cortex-M4/M7, ESP32-S3 | ≤1 MB RAM-class models, int8 TFLite-Micro | On-sensor inference, wake/anomaly triggers |
| CLS-C (Controller) | Embedded Linux controller | Cortex-A53/A72 SBC, industrial PLC+ | Small models, rule engines | Asset-local control loops |
| CLS-G (Gateway) | Protocol/cluster gateway | RPi CM4-class, industrial gateway | Bridging, buffering, light inference | Field↔edge aggregation, LoRaWAN concentrator |
| CLS-E1 (Edge AI node, gen-1) | Jetson Nano 4 GB (original), Seeed Studio carrier board, JetPack 4.6.x, USB-C 5 V/3 A | 128-core Maxwell GPU, 4 GB shared, ~472 GFLOPS FP16 | Quantised NN inference, per-building twins | **Current ODEON/O-CEI prototype fleet** |
| CLS-E2 (Edge AI node, gen-2) | Jetson Orin Nano class | ≥20 TOPS INT8, 8 GB | Multi-model serving, on-edge retraining (light) | **Production-fleet candidate** |
| CLS-S (Edge server) | x86/ARM server on-site | ≥8 cores, ≥32 GB, optional dGPU | Site-level twin stacks, container orchestration | Campus/community hub |

### 3.2 Edge site types (`siteType`)

| Code | Type | Description |
|---|---|---|
| SITE-BLD | Building edge site | Nodes serving one building (02) |
| SITE-CLU | Cluster edge site | One gateway/AI node serving several nearby buildings (Aran cluster-gateway topology) |
| SITE-AST | Asset edge site | Node dedicated to one major asset (11) |
| SITE-NET | Network edge site | Node at network asset (substation, 13) |

### 3.3 Deployment latency tiers (reuse 34 §3.3)

LAT-RT / LAT-FAST / LAT-STD / LAT-BATCH — a model deployment declares the tier its inference loop must meet locally.

## 4. Hierarchy

```
Edge site (EIG-EDGE-SITE-*)  ── maps to building(s)/cluster/asset
└── Node (EIG-EDGE-NODE-*)   ── instance of a node class (EIG-EDGE-CLS-*)
    ├── Runtime stack (OS, container runtime, ML runtime)
    ├── Channel bindings (→34 channels; node is endpoint)
    ├── Device bindings (→36 devices served)
    └── Model deployments (EIG-EDGE-DEP-*) ── model version × node × contract
```

Cluster topology: a SITE-CLU site has one primary gateway/AI node plus member-building device sets; per ODEON decision, LoRaWAN concentrates at cluster gateways rather than per building.

## 5. Field Groups & Fields

### 5.1 Node Class (`EIG-EDGE-CLS-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | classID | string | — | `EIG-EDGE-CLS-[A-Z0-9]+` | M | Node class ID |
| 2 | classCode | enum | — | §3.1 | M | Class code |
| 3 | cpuSpec | object | — | arch, cores, GHz | M | CPU envelope |
| 4 | acceleratorSpec | object | — | gpu/npu type, TOPS/GFLOPS, precision (fp16/int8) | C | Required for CLS-E1/E2/S with AI role |
| 5 | memoryMB | int | MB | >0 | M | RAM (note shared CPU/GPU memory where applicable, e.g. Jetson) |
| 6 | storageGB | int | GB | >0 | M | Local storage |
| 7 | supportedRuntimes | array<enum> | — | tflite_micro / tflite / onnxruntime / tensorrt / pytorch / native_rule | M | ML runtimes certified for class |
| 8 | containerSupport | enum | — | none / docker / k3s | M | Orchestration capability |
| 9 | powerEnvelope | object | W | typ/max, supply spec (e.g. USB-C 5 V/3 A for CLS-E1) | M | Power constraints |
| 10 | thermalEnvelope | object | °C | operating range, throttle point, fan/passive | M | Thermal constraints |
| 11 | osBaseline | string | — | pinned (e.g. "JetPack 4.6.x / L4T 32.7" for CLS-E1) | M | Certified OS/BSP baseline |
| 12 | securityCapabilities | object | — | secure boot, TPM/SE, disk encryption | M | Hardware security features |
| 13 | eolDate | date | — | ISO 8601 | O | End-of-life for class planning |

### 5.2 Node Instance (`EIG-EDGE-NODE-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 14 | nodeID | string | — | `EIG-EDGE-NODE-\d{4}` | M | Unique node ID |
| 15 | classRef | ref | — | →classID | M | Node class |
| 16 | siteRef | ref | — | →EIG-EDGE-SITE-* | M | Edge site |
| 17 | hardwareSerial | string | — | manufacturer serial | M | Physical identity |
| 18 | carrierBoard | string | — | e.g. "Seeed Studio A203" | C | Required for SBC/SoM classes |
| 19 | osVersion | string | — | within class baseline | M | Installed OS/BSP (drift vs field 11 → V4) |
| 20 | runtimeStack | array<object> | — | runtime + version | M | Installed ML/container runtimes |
| 21 | channelBindings | array<ref> | — | →34 EIG-COM-CHN-* | M | Channels node terminates |
| 22 | deviceBindings | array<ref> | — | →36 EIG-DEV-* | M | Devices served |
| 23 | buildingRefs | array<ref> | — | →02 buildings | M | Buildings served (≥1; >1 for cluster nodes) |
| 24 | powerSource | object | — | mains / UPS minutes / PoE | M | Power provision; UPS runtime if control role |
| 25 | physicalSecurity | enum | — | locked_enclosure / restricted_room / open | M | Physical posture |
| 26 | securityPosture | object | — | secure boot on/off, disk enc, attestation method, last patch date | M | Cyber posture; pairs with METIS-128 lifecycle (Scan→Detect→Report→Quarantine→Repair→Verify→Rejoin) |
| 27 | mgmtPlane | object | — | fleet manager ref, remote access method (no inbound SSH from WAN; reverse tunnel/agent only) | M | Remote management |
| 28 | capacityBudget | object | — | CPU %, GPU %, MB reserved per deployment | M | Headroom accounting (Σ deployments ≤ budget, V6) |
| 29 | environment | enum | — | live / sandbox | M | Sandbox nodes serve NEST only (A5) |
| 30 | commissioningRef | ref | — | →deployment guide / Quick Reference Card | M | Commissioning procedure followed |
| 31 | status | enum | — | planned / commissioning / active / degraded / quarantined / retired | M | Lifecycle; quarantined per METIS-128 |

### 5.3 Edge Site (`EIG-EDGE-SITE-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 32 | siteID | string | — | `EIG-EDGE-SITE-\d{4}` | M | Site ID |
| 33 | siteType | enum | — | §3.2 | M | Site type |
| 34 | memberBuildings | array<ref> | — | →02 | M | Buildings in scope |
| 35 | primaryNodeRef | ref | — | →nodeID | M | Primary node |
| 36 | uplinkSummary | object | — | primary/failover paths (from 34 channels) | M | Connectivity summary (e.g. ubiquiti_ptp + starlink failover) |
| 37 | localNetworkTopology | object | — | LAN/LoRaWAN/fieldbus layout | M | Site network description |
| 38 | offlineAutonomyTarget | duration | — | ISO 8601 (e.g. PT72H) | M | How long site must operate disconnected |

### 5.4 Model Deployment (`EIG-EDGE-DEP-*`)

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 39 | deploymentID | string | — | `EIG-EDGE-DEP-\d{5}` | M | Deployment ID |
| 40 | modelRef | ref | — | →44 EIG-MDL-* @ exact version | M | Deployed model instance |
| 41 | nodeRef | ref | — | →nodeID | M | Target node |
| 42 | artefactFormat | enum | — | tflite_int8 / tflite_fp16 / onnx / tensorrt_engine / rule_pack | M | Deployed artefact form |
| 43 | artefactHash | string | — | SHA-256 | M | Integrity pin; verified at load (A6) |
| 44 | latencyTier | enum | — | LAT-RT / LAT-FAST / LAT-STD / LAT-BATCH | M | Local inference contract |
| 45 | measuredLatencyP99Ms | decimal | ms | ≥0 | M | Benchmarked on this node class (V7) |
| 46 | resourceClaim | object | — | CPU %, GPU %, MB | M | Against node capacityBudget |
| 47 | inputBindings | array<ref> | — | →36 devices / 34 schemas | M | Input sources |
| 48 | outputBindings | array<ref> | — | →34 channels/schemas, →60+ decision hooks | M | Where outputs go |
| 49 | degradedModeRole | enum | — | continue_full / continue_reduced / safe_hold / disable | M | Behaviour when uplink lost (31 A7) |
| 50 | localAuthorityScope | object | — | bounded actions + envelope refs (11 §5.6 / 29 comfort floors) | C | Required if model issues control while offline; NEVER exceeds asset/contract envelopes; life-safety excluded |
| 51 | updatePolicy | object | — | staged rollout ring, rollback criteria | M | OTA model update contract |
| 52 | validationGateRef | ref | — | →48 validation evidence | M | Gate passed before activation |
| 53 | status | enum | — | staged / active / rolled_back / retired | M | Deployment lifecycle |

## 6. Controlled Vocabularies

Node classes §3.1; site types §3.2; latency tiers from 34; degradedModeRole {continue_full, continue_reduced, safe_hold, disable}; artefact formats §5.4; status machines §5.2/§5.4. METIS-128 lifecycle states (quarantined etc.) reused for security incidents.

## 7. Applicability Rules

- **A1 — Placement by class capability.** A model deployment is valid only if the node's class supports the artefact runtime (field 7) and the resourceClaim fits the capacityBudget. Placement engines MUST consult this catalogue; out-of-register deployments are non-conformant.
- **A2 — Latency locality.** If a control loop's latency tier is LAT-RT, inference MUST be local (CLS-T/C/E1/E2 on-site); LAT-RT loops may not depend on uplink availability.
- **A3 — Degraded-mode autonomy is bounded.** Offline behaviour follows degradedModeRole. Any offline control (field 50) operates strictly within declared asset flex envelopes (11 §5.6), contract envelopes (23), and hard comfort floors (29 A7); overrides honoured locally (29 A4); life-safety circuits untouchable (04). On reconnection, the node replays an event log; the platform reconciles — edge actions are never silently lost (pairs 31 A7, 34 §5.2 offlineBufferPolicy).
- **A4 — Security lifecycle.** Nodes follow METIS-128: a node failing attestation or showing compromise indicators → status quarantined, control authority revoked instantly, channels isolated, Repair→Verify→Rejoin before reactivation. Quarantine of a node with active FS enrolments triggers availability re-declaration (29).
- **A5 — Sandbox firewall.** environment = sandbox nodes bind only sandbox channels and serve EIG-PLT-NEST; no live control authority, consistent with the framework-wide firewall.
- **A6 — Artefact integrity.** Models load only if artefactHash verifies; unsigned/unhashed artefacts never execute. Update rollouts are staged (ring 0 = lab, ring 1 = pilot cluster, ring 2 = fleet) with automatic rollback on regression criteria.
- **A7 — Fleet baseline discipline.** CLS-E1 (Jetson Nano original) is the **prototype** baseline: JetPack 4.6.x pinned (no JetPack 5+ support on original Nano), Seeed carrier board, USB-C 5 V/3 A power budget — heavy models must fit 4 GB shared memory and Maxwell FP16/INT8. New production fleet additions SHOULD target CLS-E2 (Orin Nano class) per fleet decision; CLS-E1 procurement for production requires waiver.
- **A8 — One primary node per site.** Each edge site has exactly one primaryNodeRef; secondary nodes subordinate. Cluster sites (SITE-CLU) own the LoRaWAN concentrator at the gateway, not per building.

## 8. Validation Rules

- **V1** IDs unique; refs resolve; class/instance pattern conformance.
- **V2** Every node has ≥1 channel binding and ≥1 building ref; cluster nodes have >1 building ref.
- **V3** Runtime stack ⊆ class supportedRuntimes.
- **V4** osVersion within class osBaseline; drift → status degraded + remediation task.
- **V5** securityPosture: patch age ≤90 d (≤30 d for nodes with control authority); attestation evidence ≤30 d.
- **V6** Σ resourceClaims of active deployments ≤ capacityBudget per resource dimension.
- **V7** measuredLatencyP99Ms ≤ tier bound (LAT-RT 1000 ms local loop budget apportioned; benchmark evidence required per node class).
- **V8** Deployments with localAuthorityScope: envelope refs resolve and are current; offline event-log replay tested ≤180 d.
- **V9** degradedModeRole = continue_full only if all inputBindings are local.
- **V10** UPS runtime ≥ graceful-shutdown + safe_hold needs for nodes with control role.
- **V11** Quarantined nodes have zero active control deployments (auto-enforced).
- **V12** commissioningRef present; for CLS-E1 nodes this is the Jetson Nano deployment guide (Quick Reference Card pending as companion deliverable).

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 02 building | Sites/nodes map to buildings; per-building hardware requirements (ODEON table) |
| 11 asset | Offline control bounded by asset envelopes; nodes co-located with assets |
| 13 utility_network | SITE-NET nodes at network assets |
| 29 flexibility_service | Latency chain verification at enrolment; quarantine → availability re-declaration |
| 31 platform | EIG-PLT-EDGEWARE is the edge platform of record; degraded-autonomous principle (31 A7) implemented here |
| 32 digital_twin | Edge-resident twin components; M4 twins' local guardrails |
| 33 digital_twin_template | Templates imply edge runtime needs per model bundle |
| 34 communication | Nodes are channel endpoints; offline buffering; clock sync for RD |
| 36 device | Devices bound to nodes; protocol termination |
| 44/48/50 model layer | Deployed artefacts, validation gates, (light) on-edge training |
| 60–63 decision/control | Local decision hooks; dispatch execution path |
| 76 alert | Node health alerting |

## 10. Benchmarking Requirements

Per node class: inference latency benchmarks per artefact format (published, V7 evidence); fleet KPIs: node availability, mean patch age, quarantine MTTR, deployment rollback rate, offline-autonomy events survived vs target. Cohort n≥10 for cross-class comparisons.

## 11. Dataset Requirements

Node telemetry dataset (PT1M: CPU/GPU/mem/temp/disk, REAL) feeds health models and capacity planning; event logs (offline actions) retained ≥13 months; security logs per NIS2 retention.

## 12. Feature Requirements

Node health features (thermal headroom, capacity utilisation trend, link quality from 34) feed placement optimisation and predictive maintenance of the fleet itself.

## 13. Model Requirements

Edge-deployable model specs (44) declare artefact formats and resource footprints per node class; quantisation/validation pipeline (48/50) must show accuracy delta vs cloud reference within declared tolerance before edge gates pass.

## 14. KPI Requirements

`node_availability_pct`, `inference_latency_p99_ms`, `offline_autonomy_success`, `security_patch_age_days`, `deployment_success_rate`.

## 15. Response Derivative Requirements

Edge nodes timestamp at source with disciplined clocks (34 §15); RD measurement for fast services depends on edge-logged actuation timestamps. Offline-delivered flexibility events carry edge event-log evidence into RD/settlement reconciliation (27 four-quantity model).

## 16. Decision Requirements

Local decision authority is the bounded exception, not the rule: only deployments with localAuthorityScope may act offline, inside envelopes, with full logging and reconciliation. Kill-switch (32 V9) reaches nodes via dedicated channel; local kill is honoured even offline (cached command validity).

## 17. Ontology Mapping

- `eig:EdgeNode`, `eig:EdgeNodeClass`, `eig:EdgeSite`, `eig:ModelDeployment` ⊑ `eig:InfrastructureEntity`
- `eig:deployedOn(Model, Node)`, `eig:serves(Node, Building)`, `eig:hasCapability`
- SSN/SOSA platform alignment: `sosa:Platform` hosts; IEC 62443 zone/conduit modelling for security topology

## 18. Knowledge Graph Mapping

Nodes: `(:EdgeNode)`, `(:EdgeNodeClass)`, `(:EdgeSite)`, `(:ModelDeployment)`. Edges: `[:OF_CLASS]`, `[:AT_SITE]`, `[:SERVES]->(:Building)`, `[:TERMINATES]->(:Channel)`, `[:HOSTS]->(:ModelDeployment)-[:OF_MODEL {version, hash}]->(:Model)`, `[:BOUND_BY]->(:FlexEnvelope)`. Queries: "which enrolled services lose local control if node X is quarantined?" traverses HOSTS→outputBindings→enrolments.

## 19. Digital Twin Mapping

Edge nodes host twin runtime components (state estimators, local forecasters) for low-latency loops; the fleet itself can be twinned (SITE/NODE health twin, DTT-NET pattern) for capacity and resilience planning in NEST.

## 20. Governance

Owner: Edge Engineering Lead; security co-governance with Security Officer (METIS-128 authority). Class certification: lab benchmark suite + security review. Node commissioning: per commissioning guide with sign-off; decommissioning: cryptographic wipe + register update. Waivers (A7 production CLS-E1) approver-tier, ≤12 months.

## 21. Versioning

Classes: SemVer (baseline OS bump = MINOR; capability change = MAJOR). Nodes: configuration change records. Deployments: pinned model versions + hashes; every change is a new deployment record (supersede, never edit — mirrors 27 A6 discipline).

## 22. Example Records

### EIG-EDGE-CLS-E1 @ 1.2.0 — Jetson Nano prototype class

- classCode CLS-E1; cpu ARM A57 ×4 @1.43 GHz; accelerator 128-core Maxwell, ~472 GFLOPS FP16, int8/fp16
- memoryMB 4096 (shared CPU/GPU); storage 64 GB (microSD/NVMe via carrier)
- supportedRuntimes [tflite, onnxruntime, tensorrt]; containerSupport docker
- powerEnvelope: 5–10 W typ / 10 W max mode, USB-C 5 V/3 A; thermal: passive+fan, throttle 97 °C
- osBaseline "JetPack 4.6.x / L4T 32.7" (pinned — no JetPack 5 on original Nano); carrierBoard standard "Seeed Studio A203"
- securityCapabilities: secure boot capable, no TPM (compensating: attestation agent + disk encryption)
- Role: ODEON/O-CEI prototype fleet (Aran Islands)

### EIG-EDGE-CLS-E2 @ 1.0.0 — Orin Nano production class

- classCode CLS-E2; ≥20 TOPS INT8; 8 GB; JetPack 5.x/6.x baseline; containerSupport k3s
- Status: certified; designated production-fleet target per fleet decision (A7)

### EIG-EDGE-SITE-0007 — Inis Mór Cluster A (SITE-CLU)

- memberBuildings: 6 dwellings incl. IE_B0009, IE_B0014 + CFOAT office
- primaryNodeRef EIG-EDGE-NODE-0007; uplinkSummary: ubiquiti_ptp primary, starlink failover (channel EIG-COM-CHN-00041)
- localNetworkTopology: LoRaWAN EU868 concentrator at gateway (cluster-gateway topology), Modbus TCP to heat-pump controllers, site LAN
- offlineAutonomyTarget PT72H

### EIG-EDGE-NODE-0007 — Inis Mór Cluster A primary node

- classRef EIG-EDGE-CLS-E1@1.2.0; carrierBoard Seeed A203; osVersion JetPack 4.6.4
- runtimeStack: docker 20.10, tensorrt 8.2, onnxruntime 1.10
- channelBindings: [EIG-COM-CHN-00041 uplink, EIG-COM-CHN-00112 site LoRaWAN, EIG-COM-CHN-00113 Modbus]
- deviceBindings: 14 devices (36) incl. heat-pump controllers, temp sensors, meters; buildingRefs: 6 + CFOAT
- powerSource: mains + UPS 45 min; physicalSecurity locked_enclosure
- securityPosture: disk enc on, attestation agent, last patch 2026-05-28 (≤30 d ✓ control role)
- capacityBudget: CPU 70%, GPU 80%, 3072 MB allocatable; environment live; status active
- commissioningRef: Jetson Nano ODEON deployment guide v1 (QRC companion pending)

### EIG-EDGE-DEP-00231 — Thermal flex envelope model on Node 0007

- modelRef EIG-MDL-FLEXENV-01@1.4.2; artefactFormat tensorrt_engine; artefactHash sha256:…
- latencyTier LAT-FAST; measuredLatencyP99Ms 240 (class benchmark ✓ V7)
- resourceClaim: CPU 15%, GPU 35%, 900 MB; inputBindings: zone temp sensors + ASHP telemetry (36/34)
- outputBindings: dispatch hook (EIG-COM-SCH-FLEX-DISPATCH-v2 ack path), twin EIG-DT-000877 ingestion
- degradedModeRole continue_reduced; localAuthorityScope: ASHP EIG-AST-004211 envelope (11 §5.6), comfort floor 19 °C hard (29 A7), overrides honoured locally, ≤2 events/week contract cap (EIG-CON-001207) enforced edge-side; event-log replay tested 2026-04-12 (✓ V8)
- updatePolicy: ring 1 (pilot cluster); rollback on delivery-success regression >5 pp
- validationGateRef EIG-VAL-EV-00882; status active

## 23. Completion Criteria

- [x] Controllers, gateways, edge servers, TinyML nodes all classed with compute envelopes
- [x] Compute capacity, latency, connectivity, local models fully fielded
- [x] Distributed MLM deployment machinery: placement rules, artefact integrity, staged rollout, validation gates
- [x] Degraded-mode autonomy bounded by envelopes/comfort floors/overrides with reconciliation (A3)
- [x] METIS-128 security lifecycle integrated (A4); sandbox firewall (A5)
- [x] ODEON facts honoured: Jetson Nano 4 GB JetPack 4.6.x Seeed carrier USB-C 5 V/3 A prototype; Orin Nano production target; cluster-gateway LoRaWAN; ubiquiti/starlink uplinks; QRC pending flag
- [x] Cross-refs: EIG-PLT-EDGEWARE (31), EIG-DT-000877 (32), EIG-CON-001207 (23), EIG-AST-004211 (11)
- [x] Example records: two classes, site, node, deployment
