---
type: catalogue
ontology: EIG-MLM
catalogue_no: 31
catalogue_id: EIG-CAT-031
entity: platform
band: flexibility
status: active
tags: [eig-mlm, catalogue, band:flexibility, entity:platform]
aliases: ["EIG-CAT-031", "Catalogue 31", "platform"]
---

# CAT-31 — Platform

> **EIG-CAT-031** · band: **Flexibility & Platform** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID prefix:** `EIG-PLT`
**Catalogue number:** 31 of 94
**Layer:** Flexibility & Platform
**Version:** 1.0.0
**Status:** ACTIVE

---

## 1. Definition

The Platform Catalogue defines **platform entities**: the named software platforms through which the MLM framework is operated, exposed and integrated — FLEXUS and its ecosystem components (EdgeWare, PARA//EL, minutemanSEM, FLEXUS_HIVE), FLEXGPT, digital-twin platforms, transactive/market platforms (NEST sandbox runtime), and the EIG Platform itself. A platform record declares identity, function, users, APIs, services, modules, integrations and governance, so that every other catalogue can reference *where* a capability runs without redefining it.

Platforms are the deployment substrate: models (44) deploy onto platforms, services (74) run on platforms, twins (32) are hosted by platforms, edge nodes (35) federate to platforms.

## 2. Scope

**In scope:** platform identity and classification; functional capability declarations; module/service composition; API surface registry; integration registry (platform↔platform, platform↔external); user/role binding (17/16); environment model (production, staging, sandbox); hosting and deployment facts; platform-level governance and SLAs.

**Out of scope:** individual service contracts (74), API message schemas (34), device/edge details (35/36), application UX (73), infrastructure-as-code specifics (deployment repos — referenced, not duplicated).

## 3. Classification Structure

`platformClass`:

| Code | Class | Description |
|---|---|---|
| PLT-CORE | core_platform | Multi-domain operational platform (EIG Platform, FLEXUS) |
| PLT-EDGE | edge_platform | Far-edge runtime stack (EdgeWare on Jetson/edge fleet, 35) |
| PLT-TWIN | twin_platform | Digital-twin hosting/runtime (32/33) |
| PLT-MKT | transactive_platform | Market/trading/settlement runtime (20/25/27 execution) |
| PLT-AI | ai_platform | Model serving/training/agent runtime (FLEXGPT, MLM stack) |
| PLT-DATA | data_platform | Lakehouse/pipeline platform (DuckDB/Parquet, MariaDB/MongoDB stacks) |
| PLT-SBX | sandbox_platform | NEST sandbox runtime (firewalled per 20 A2) |

`environment`: production, staging, development, sandbox. `hostingModel`: cloud (AWS EC2 eu-west-1 pattern), on_prem, far_edge, hybrid.

## 4. Hierarchy

```
Platform (EIG-PLT-*)
├── Environment (per environment value; isolated config/data)
├── Module (EIG-PLT-*-MOD-*)           ← deployable functional unit
│     └── exposes Services (74) and APIs (§5.4)
├── IntegrationLink (EIG-PLT-INT-*)    ← typed connection to another platform/external system
└── PlatformRelease (versioned deployment state)
```

## 5. Field Groups & Fields

### 5.1 Identity & Classification

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | platformID | string | — | `EIG-PLT-[A-Z0-9]+` | M | Platform identifier |
| 2 | platformName | string | — | ≤120 chars | M | Name (FLEXUS, EdgeWare, FLEXGPT, NEST, EIG Platform, PARA//EL, minutemanSEM, FLEXUS_HIVE) |
| 3 | platformClass | string | — | §3 codes | M | Class |
| 4 | purpose | string | — | ≤500 chars | M | Functional statement |
| 5 | ownerOrgRef | string | — | EIG-ORG-* (15) | M | Owning organisation |
| 6 | status | string | — | concept, development, pilot, production, deprecated, retired | M | Lifecycle |
| 7 | projectProvenanceRefs | array | — | EIG-PRJ-* (91) | M | Funding/project lineage (attribution discipline: e.g. PSR/PULSEse → OPTICS/PULSE, never FLEXUS) |

### 5.2 Function & Capability

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | capabilityDomains | array | — | monitoring, control, forecasting, optimisation, trading, settlement, twin_hosting, model_serving, agent_runtime, data_management, edge_orchestration, reporting | M | What the platform does |
| 2 | catalogueRolesServed | array | — | catalogue numbers + role (e.g. "25: trade ledger of record", "27: settlement runtime") | M | Where this platform is the executing system for catalogue logic |
| 3 | mlmLayersHosted | array | — | deterministic, stochastic, ai_ml, rl_q (four-layer cognitive stack) | C | For PLT-AI: which MLM layers run here |
| 4 | dataClassesHandled | array | — | REAL, CALIBRATED, SYNTHETIC, COMPUTED | M | Provenance classes present; SBX platforms may hold SYNTHETIC, production disclosure paths must exclude it (28 V9 analogue) |

### 5.3 Composition — Modules & Services

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | moduleID | string | — | `EIG-PLT-<PLT>-MOD-[0-9]{2}` | M | Module identifier |
| 2 | moduleName | string | — | ≤120 chars | M | Name |
| 3 | moduleFunction | string | — | ≤300 chars | M | Function |
| 4 | serviceRefs | array | — | EIG-SVC-* (74) | M | Services exposed |
| 5 | deploymentUnit | string | — | container image ref (ECR pattern), edge package, lambda | M | How it ships (Docker/Watchtower/ECR convention) |
| 6 | dependsOnModules | array | — | module IDs (same or other platform) | M | Dependency graph; cycles forbidden (V6) |

### 5.4 API Surface

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | apiID | string | — | `EIG-PLT-<PLT>-API-[0-9]{2}` | M | API identifier |
| 2 | baseURL | string | — | URL pattern (e.g. `https://api.parallel-energy.io/v1`) | M | Endpoint root per environment |
| 3 | style | string | — | REST, GraphQL, MQTT, gRPC, WebSocket | M | Interface style (schemas in 34) |
| 4 | authModel | string | — | oauth2_cc, api_key, mtls, signed_jwt | M | Authentication; binds to user kinds USR-S-* (17) |
| 5 | provenanceEnvelope | boolean | — | true/false | M | Whether responses carry the provenance envelope (data class + source); MUST be true for any data-serving API (hyperGrid standard generalised) |
| 6 | rateLimits | object | — | {rps, burst} | M | Declared limits |
| 7 | schemaRefs | array | — | EIG-COM-SCH-* (34) | M | Message/payload schemas |

### 5.5 Integrations

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | integrationID | string | — | `EIG-PLT-INT-[0-9]{3}` | M | Link identifier |
| 2 | fromPlatformRef / toRef | string | — | EIG-PLT-* or external system (externalRef: EirGrid, SEAI, supplier APIs, operator systems) | M | Endpoints |
| 3 | integrationKind | string | — | data_feed, control_channel, model_exchange, settlement_feed, identity_federation | M | Nature |
| 4 | direction | string | — | inbound, outbound, bidirectional | M | Flow |
| 5 | channelRef | string | — | 34 protocol/channel | M | Transport |
| 6 | dataContract | string | — | dataset/schema refs (43/34) + data classes | M | What crosses, with provenance |
| 7 | sandboxFirewallApplies | boolean | — | true/false | M | TRUE for any PLT-SBX link; sandbox→production data flow forbidden except declared anonymised research extracts (20 A2) |

### 5.6 Users, Hosting, Governance

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | userKindsSupported | array | — | USR-H-*/USR-S-* (17) | M | Account kinds |
| 2 | roleBindings | array | — | EIG-ROL-* (16) usable on this platform | M | RBAC surface; all authority via roles (16 invariant) |
| 3 | hostingModel | string | — | §3 vocab | M | Where it runs |
| 4 | hostingDetail | object | — | {region: eu-west-1, runtime: EC2/Docker, storage: B2/DuckDB/MariaDB/MongoDB, routing: Traefik} | M | Deployment facts (convention refs, not secrets) |
| 5 | environments | array | — | §3 environment values with config isolation statement | M | Environment model |
| 6 | slaTier | object | — | {availabilityPct, rtoMin, rpoMin} | M | Service levels |
| 7 | securityBaseline | array | — | NIS2, IEC 62443 zones/conduits (edge), GDPR, ISO 27001 alignment statements | M | Regulatory alignment (joins 93/94) |

## 6. Controlled Vocabularies

- `platformClass`, `environment`, `hostingModel`, `integrationKind`, API `style`, `authModel`: §3/§5.
- Status lifecycle: `concept → development → pilot → production → deprecated → retired`; deprecated platforms accept no new integrations (V9).
- Naming discipline: UI/brand names vs backend identifiers may differ (hyperGrid vs synth_grid_ie pattern); both recorded, backend identifier authoritative in code.

## 7. Applicability Rules

| # | Rule |
|---|---|
| A1 | Every executing system referenced by another catalogue ("the platform computes/serves/hosts X") must be a registered platform here with the role listed in catalogueRolesServed. |
| A2 | Sandbox platforms (PLT-SBX) are firewalled: integrations to production are inbound-only for definitions/replicas; outbound sandbox results never enter production features, settlement, or disclosure (20 A2, 27 A3, 28 V9). |
| A3 | All data-serving APIs carry the provenance envelope (§5.4); APIs without it may not serve catalogue-governed data. |
| A4 | RBAC invariant (16/17): platforms enforce authority exclusively via role bindings; platform-local privilege systems are forbidden. |
| A5 | Module dependency graphs are acyclic; cross-platform dependencies route through registered integrations, never direct database access. |
| A6 | Project attribution: platform components carry projectProvenanceRefs; outputs are attributed per provenance (e.g. PSR/PULSEse → OPTICS/PULSE), enforced in reports (77). |
| A7 | Edge platforms (PLT-EDGE) operate degraded-autonomous on connectivity loss: local failsafe rules (29 §5.4) and local models (35) continue; reconciliation on reconnect is mandatory and logged. |

## 8. Validation Rules

| # | Rule | Severity |
|---|---|---|
| V1 | platformID unique; names unique per environment | error |
| V2 | catalogueRolesServed: exactly one platform of record per role (e.g. one trade ledger, 25) | error |
| V3 | All serviceRefs/schemaRefs/roleBindings resolve (74/34/16) | error |
| V4 | Sandbox firewall flags set on all PLT-SBX integrations (A2) | error |
| V5 | provenanceEnvelope=true for data-serving APIs (A3) | error |
| V6 | Module dependency graph acyclic (A5) | error |
| V7 | Every production platform declares SLA tier and security baseline | error |
| V8 | userKindsSupported ⊆ kinds defined in 17; USR-S-MODEL bindings one-per-deployment (17 rule) | error |
| V9 | deprecated status ⇒ no new integration records dated after deprecation | error |
| V10 | hostingDetail contains no secrets/credentials (convention refs only) | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 32/33 digital_twin(+template) | Twin hosting (PLT-TWIN); template execution |
| 34 communication | API styles/schemas/channels |
| 35/36 edge/device | Edge platform fleet; device onboarding |
| 44/51 model/model_benchmark | Model deployment targets; serving runtimes |
| 72 agent | Agent runtimes (PLT-AI) |
| 73/74 application/service | Apps and services composed on platforms |
| 25/27 trade/settlement | Ledger/settlement platforms of record (V2) |
| 16/17 role/user | RBAC and account binding |
| 91 project | Provenance/attribution |
| 93/94 regulation/compliance | Security baselines, NIS2/IEC 62443 |

## 10. Benchmarking Requirements

Platform operational benchmarks: availability vs SLA, API latency p95/p99 per endpoint class, deployment frequency, change-failure rate, mean-time-to-restore, edge fleet check-in rate. Min n=10 observation windows; immutable releases; published per environment.

## 11. Dataset Requirements

Platform telemetry (logs, metrics, traces) retained per security baseline (NIS2 incident horizons); release manifests immutable; integration data contracts versioned with the datasets they govern (43); environment isolation verified by data-lineage checks (no production data in development beyond declared anonymised sets).

## 12. Feature Requirements

Operational features for meta-models (capacity planning, anomaly detection on platform health): request rates, queue depths, model-serving latencies, edge connectivity ratios. These features are COMPUTED class and never mix with energy-domain features in domain models.

## 13. Model Requirements

Model deployments (44) declare target platformID + environment; serving platforms enforce model version pinning and rollback; PLT-AI platforms log inference provenance (model version, input snapshot hash) for every catalogue-governed output (auditability mirroring 27 evidence discipline).

## 14. KPI Requirements

KPIs (57): platform availability, API error rate, integration freshness (data_feed lag), sandbox-to-production promotion lead time (NEST→pilot pipeline), security patch latency. Dimensioned by platform, environment, module.

## 15. Response Derivative Requirements

Platforms hosting RD computation (PLT-AI/PLT-DATA) must guarantee reproducibility (pinned versions, input snapshots) so RD confidence values are auditable; activation signal paths (29 A8) declare the platform chain end-to-end (cloud → edge) with latency budgets allocated per hop.

## 16. Decision Requirements

Platform decisions: promotion gates (development→pilot→production) require V-checks green + approver sign-off (T3+); integration approval requires data-contract review; sandbox experiment graduation to pilot follows NEST handshake/integrity model. Incident decisions follow security baseline playbooks (64 workflows).

## 17. Ontology Mapping

Classes: `eig:Platform` ⊑ `eig:System`; `eig:PlatformModule`, `eig:PlatformAPI`, `eig:IntegrationLink`. Object properties: `eig:hostsTwin`, `eig:servesModel`, `eig:exposesService`, `eig:integratesWith`, `eig:ownedBy`, `eig:provenancedBy` (→ eig:Project). Datatype: platformID, platformClass, environment, slaAvailabilityPct, sandboxFirewallApplies. Alignments: design-intent links to DUL (information system), IEC 62443 zone/conduit concepts for edge security modelling.

## 18. Knowledge Graph Mapping

Nodes: `(:Platform)`, `(:Module)`, `(:API)`, `(:Integration)`. Edges: `(:Module)-[:PART_OF]->(:Platform)`, `(:Module)-[:DEPENDS_ON]->(:Module)`, `(:Platform)-[:INTEGRATES_WITH {kind, direction}]->(:Platform|:ExternalSystem)`, `(:Model)-[:DEPLOYED_ON]->(:Platform)`, `(:Twin)-[:HOSTED_BY]->(:Platform)`, `(:Service)-[:RUNS_ON]->(:Module)`. Impact analysis traverses dependency and integration edges; firewall audits filter sandbox links.

## 19. Digital Twin Mapping

PLT-TWIN platforms host twin instances (32) generated from templates (33); the NEST sandbox platform is itself the twin of the market/grid system (100% virtual, parallel to the regulated chain); platform records give twins their execution context (compute, data, latency) so fidelity declarations (32) are grounded.

## 20. Governance

Owner: Platform Engineering Lead. Steward: DevOps engineer (releases, integrations). Reviewer: security officer (baseline conformance). Approver: T3+ for production promotion and new integrations; T4 for new platforms (16). Duties per 16 A6. NEST sandbox governance under its sandbox board with regulator liaison (15 EIG-ORG-0102).

## 21. Versioning

Platforms: release-versioned (PlatformRelease records, immutable manifests). Catalogue records semver: MAJOR for class/role changes, MINOR for module/API additions, PATCH editorial. Deprecation requires migration note and sunset date. ChangeLog mandatory.

## 22. Example Records

```yaml
platformID: EIG-PLT-FLEXUS
platformName: FLEXUS | platformClass: PLT-CORE | status: production
ownerOrgRef: EIG-ORG (Tyndall/IERC EIG) | projectProvenanceRefs: [multiple funded projects per 91]
capabilityDomains: [monitoring, forecasting, optimisation, control]
catalogueRolesServed: ["30: state-of-flexibility runtime", "60: optimisation engine host"]
modules: [MOD-01 ingestion, MOD-02 forecasting, MOD-03 optimisation, MOD-04 dispatch]
apis: [{API-01, https://api…/v1, REST, oauth2_cc, provenanceEnvelope: true}]
hostingDetail: {region: eu-west-1, runtime: EC2/Docker/Watchtower/ECR, routing: Traefik}
slaTier: {availabilityPct: 99.5, rtoMin: 60, rpoMin: 15}
securityBaseline: [NIS2, GDPR, ISO 27001-aligned]
```

```yaml
platformID: EIG-PLT-NEST
platformName: NEST (National Energy Sandbox & Testbed) | platformClass: PLT-SBX | status: pilot
purpose: 100% virtual digital replica of the Irish grid for safe innovation testing, parallel to the regulated chain
catalogueRolesServed: ["20: MKT-SBX venue runtime", "26: TW-SBX windows", "27: SET-SBX shadow settlement"]
dataClassesHandled: [SYNTHETIC, COMPUTED, CALIBRATED]
integrations:
  - {INT-014, from: EIG-PLT-NEST, to: EIG-PLT-FLEXUS, kind: model_exchange, direction: inbound, sandboxFirewallApplies: true}
environments: [sandbox only]
```

```yaml
platformID: EIG-PLT-EDGEWARE
platformName: EdgeWare | platformClass: PLT-EDGE | status: pilot
capabilityDomains: [monitoring, control, edge_orchestration, model_serving]
catalogueRolesServed: ["35: edge node runtime", "29: failsafe execution", "36: device gateway"]
hostingModel: far_edge | hostingDetail: {fleet: Jetson Nano (JetPack 4.6.x, Seeed carrier), comms: LoRaWAN cluster gateways + Ubiquiti PtP/Starlink}
securityBaseline: [IEC 62443 zones/conduits, NIS2]
slaTier: {availabilityPct: 99.0 (degraded-autonomous on disconnect, A7)}
```

## 23. Completion Criteria

Complete when: all named ecosystem platforms (FLEXUS, EdgeWare, PARA//EL, minutemanSEM, FLEXUS_HIVE, FLEXGPT, NEST, EIG Platform) registered with modules, APIs and integrations; exactly one platform of record per catalogue execution role (V2); sandbox firewall verified across all NEST links; RBAC bindings resolve for every platform; attribution provenance complete (A6); Phase 14 confirms every "executing system" reference framework-wide resolves to a platform here.
