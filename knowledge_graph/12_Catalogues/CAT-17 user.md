---
type: catalogue
ontology: EIG-MLM
catalogue_no: 17
catalogue_id: EIG-CAT-017
entity: user
band: actors
status: active
tags: [eig-mlm, catalogue, band:actors, entity:user]
aliases: ["EIG-CAT-017", "Catalogue 17", "user"]
---

# CAT-17 — User

> **EIG-CAT-017** · band: **Actors & Organisations** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM User Catalogue

## 1. Definition

The User Catalogue is the authoritative register of all system users of the EIG MLM platform: every human or service identity that authenticates to any platform component (web, mobile, API, edge management plane). A user is an authenticated digital identity. It is distinct from an actor (14 — a participating party in the energy ecosystem) and from a role (16 — a bundle of authority): a user IS an identity, REPRESENTS zero or more actors, and HOLDS roles via assignment records stored here.

This catalogue defines user identity, authentication, authorisation (role assignments), organisational affiliation, assigned resources (assets, buildings, dashboards), session and audit requirements, and GDPR lifecycle. It is the enforcement point of the RBAC invariant declared in catalogue 16: permissions are resolved ONLY through the role assignments recorded here.

## 2. Scope

In scope:

- Human users: platform staff, org users (energy managers, technicians, traders), residents/occupant users, regulator read-only users, research users.
- Service accounts: API clients, model execution identities, edge-device management identities, integration connectors.
- Federated identities (SSO via org IdP) and local identities.
- Full lifecycle: invitation → provisioning → active use → suspension → deprovisioning → erasure/retention.

Out of scope:

- Actor identity and participation (14). A householder may exist as an actor without ever being a user; a user may represent several actors (e.g. an aggregation manager acting for a pool).
- Role definitions and permission semantics (16).
- Device identity for telemetry (36 device catalogue handles device credentials; edge MANAGEMENT logins are users here).
- Stakeholder records (18).

## 3. Classification Structure

User kind (`userKind`) — closed vocabulary:

| Code | Kind | Definition |
|---|---|---|
| USR-H-STAFF | human_platform_staff | EIG platform/operations staff |
| USR-H-ORG | human_org_user | Employee/agent of a registered organisation |
| USR-H-RES | human_resident | Occupant/householder user (consumer-facing apps) |
| USR-H-REG | human_regulator | Regulator/auditor read-only user |
| USR-H-RSCH | human_researcher | Research user under data-access agreement |
| USR-S-API | service_api_client | Machine identity for external API integration |
| USR-S-MODEL | service_model_runtime | Model/agent execution identity |
| USR-S-EDGE | service_edge_mgmt | Edge fleet management identity |
| USR-S-CONN | service_connector | Internal integration/ETL identity |

Assurance level (`identityAssuranceLevel`), aligned with NIST 800-63 / eIDAS: IAL1 self_asserted, IAL2 verified_remote, IAL3 verified_in_person. Authenticator assurance: AAL1 password, AAL2 mfa, AAL3 hardware_token.

## 4. Hierarchy

Flat register of users; structure comes from relationships:

```text
User (EIG-USR-NNNNN)
├── belongsToOrg → Organisation (15)        [0..1; mandatory for USR-H-ORG/REG/RSCH]
├── representsActors → Actor (14)           [0..n; with representation basis]
├── roleAssignments → Role (16)             [0..n; the ONLY authority channel]
└── assignedResources → Building/Asset/Dashboard/Twin scopes
```

Service accounts (USR-S-*) must have an owning human sponsor (`sponsorUserID`) and owning org; orphan service accounts are illegal (V12).

## 5. Field Groups & Fields

### 5.1 Identity

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | userID | string | — | `EIG-USR-NNNNN` | M | Unique, immutable identifier |
| 2 | userKind | enum | — | §3 codes | M | User kind |
| 3 | username | string | — | unique, ≤ 64 chars | M | Login/principal name |
| 4 | displayName | string | — | ≤ 100 chars | C | Human users; pseudonym allowed for USR-H-RES |
| 5 | email | string | — | RFC 5322 | C | Mandatory human users; verified flag required |
| 6 | phoneNumber | string | — | E.164 | O | For MFA/alerts; verified flag |
| 7 | locale | string | — | BCP 47 (en-IE, ga-IE, …) | M | UI language/format preferences |
| 8 | belongsToOrg | string | — | EIG-ORG-NNNN | C | Org affiliation (mandatory per §4) |
| 9 | sponsorUserID | string | — | EIG-USR-NNNNN | C | Mandatory for USR-S-* |
| 10 | identityAssuranceLevel | enum | — | IAL1–IAL3 | M | Identity proofing level |
| 11 | status | enum | — | invited, pending_verification, active, suspended, deprovisioned, erased | M | Lifecycle status |

### 5.2 Authentication

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | authMethod | enum | — | local_password, sso_oidc, sso_saml, api_key, mtls_cert, oauth2_client_credentials | M | Primary method |
| 2 | idpRef | string | — | IdP entity ID | C | Required for sso_* |
| 3 | mfaEnabled | boolean | — | true/false | M | MFA state; must satisfy role mfaRequired (V5) |
| 4 | mfaMethods | array | — | totp, webauthn, push, sms_fallback | C | ≥ 1 if mfaEnabled |
| 5 | authenticatorAssuranceLevel | enum | — | AAL1–AAL3 | M | Achieved AAL |
| 6 | credentialRotationDays | integer | days | service accounts ≤ 90 | C | Mandatory USR-S-* (V13) |
| 7 | lastAuthAt | datetime | — | ISO 8601 UTC | D | Last successful authentication |
| 8 | failedAttemptPolicy | object | — | {maxAttempts: 5, lockoutMin: 15} | M | Lockout policy reference |
| 9 | sessionPolicy | object | — | {maxSessionH, idleTimeoutMin, concurrentMax} | M | Session limits; stricter for T3/T4 holders |
| 10 | apiKeys | array | — | [{keyID, scopes, createdAt, expiresAt, lastUsed}] | C | USR-S-API; secrets never stored here, hash refs only |

### 5.3 Authorisation (role assignments)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | roleAssignments | array | — | [{assignmentID, roleID, scope, scopeRefs[], validFrom, validTo, approvedBy, status}] | M | The operative authority grants; append-only event-backed |
| 2 | effectivePermissions | array | — | derived at runtime | D | Never stored authoritatively; resolved per request |
| 3 | representsActors | array | — | [{actorID, basis: self/mandate/employment/poa, validFrom, validTo, evidenceRef}] | C | Actor representation register |
| 4 | dataClassCeiling | enum | — | derived = max over roles, capped by clearance | D | Highest data class accessible |
| 5 | breakGlassEligible | boolean | — | true/false | O | May activate emergency role (dual control, 16 §20) |

### 5.4 Assigned resources

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | assignedBuildingIDs | array | — | EIG-BLD-NNNNNN[] | C | Buildings in `assigned` scope resolution |
| 2 | assignedAssetIDs | array | — | EIG-AST-NNNNNN[] | C | Assets in scope |
| 3 | assignedNetworkZoneIDs | array | — | EIG-NET-NZ-NNNNN[] | C | Network zones (control-room users) |
| 4 | assignedTwinIDs | array | — | EIG-DT-* | C | Digital twins accessible |
| 5 | defaultDashboardIDs | array | — | EIG-DSH-* | O | Landing dashboards (role defaults overridable) |
| 6 | notificationChannels | array | — | [{channel: email/push/sms, alertClasses[], quietHours}] | O | Alert routing preferences (76) |

Scope resolution rule: a permission with scope `assigned` grants access exactly to the union of assignedResource lists; scope `own` resolves through representsActors→owned entities; scope `org` through belongsToOrg.

### 5.5 GDPR & privacy

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | gdprLawfulBasis | enum | — | contract, consent, legitimate_interest, legal_obligation, public_task | M | Basis for processing the user record itself |
| 2 | privacyNoticeVersion | string | — | semver + acceptedAt | M | Accepted privacy notice |
| 3 | marketingConsent | object | — | {granted, channel[], at} | O | Separate from functional processing |
| 4 | dataExportRequests | array | — | [{requestedAt, fulfilledAt, ref}] | D | Art. 20 portability log |
| 5 | erasureRequest | object | — | {requestedAt, dueBy, status, exemptions[]} | C | Art. 17; exemptions cite legal holds (settlement/audit) |
| 6 | retentionSchedule | string | — | compliance catalogue (93) schedule ID | M | Post-deprovision retention |
| 7 | minorFlag | boolean | — | always false | M | Platform users must be ≥ 18; enforcement V15 |

### 5.6 Audit

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | auditLogPolicy | string | — | policy ID | M | What is logged for this user kind |
| 2 | auditEvents | stream | — | append-only external store | D | login, logout, permission_use (T2+ actions), role_change, resource_change, consent_change, export, erasure, break_glass |
| 3 | lastAccessReview | date | — | ISO 8601 | M | Last recertification (16 §20 cadence) |
| 4 | riskFlags | array | — | dormant_90d, impossible_travel, excessive_failures, privilege_creep | D | Security analytics outputs |

All T2+ permission exercises log: userID, roleID exercised, permission, resource, timestamp, result. Audit events are immutable, clock-synchronised (NTP, edge devices per 35), retained per 93.

### 5.7 Record metadata

recordVersion (semver), createdDate, modifiedDate, provisionedBy, deprovisionedBy/At, governanceStatus — programme standard.

## 6. Controlled Vocabularies

Closed: userKind, status, authMethod, mfaMethods, identityAssuranceLevel (IAL1–3), authenticatorAssuranceLevel (AAL1–3), representation basis, gdprLawfulBasis, riskFlags, audit event types. External: NIST SP 800-63B assurance definitions, BCP 47 locales, RFC 5322/E.164 formats, OIDC/SAML/OAuth2 standards.

## 7. Applicability Rules

| Rule | Statement |
|---|---|
| A1 | No authentication without a user record; no authorisation without an active role assignment (RBAC invariant) |
| A2 | USR-H-RES users: displayName may be pseudonymous; representsActors basis `self` links to their pseudonymised actor; data minimisation per actor catalogue GDPR classes |
| A3 | USR-H-REG: read-only roles only (16 A4); org = the regulator org; access auto-expires ≤ 12 months |
| A4 | USR-H-RSCH: access only under a data-access agreement contract reference; dataClassCeiling ≤ pseudonymised unless ethics approval recorded |
| A5 | USR-S-MODEL identities: one per model deployment (44); permissions enumerated; no interactive login; AAL via mtls/oauth2 |
| A6 | USR-S-EDGE identities: one per management plane, not per device; device telemetry credentials live in 36 |
| A7 | Suspension is immediate-effect (sessions revoked ≤ 60 s); deprovisioning removes all role assignments and resource lists same day |
| A8 | Org offboarding (15) triggers review of all its users within 5 working days |

## 8. Validation Rules

| ID | Rule | Severity |
|---|---|---|
| V1 | userID unique/immutable; username unique among status ∉ {deprovisioned, erased} | error |
| V2 | Status transitions: invited→pending_verification→active→{suspended↔active, deprovisioned→erased}; erased terminal | error |
| V3 | email verified before activation (human users) | error |
| V4 | belongsToOrg mandatory for USR-H-ORG/REG/RSCH and all USR-S-*; org must be active | error |
| V5 | mfaEnabled = true whenever any held role has mfaRequired (16 V11); AAL ≥ AAL2 for T2+, AAL3 or hardware-backed for T4 | error |
| V6 | Role assignment requires: role active, assignee kind allowed, approver holds assignmentApproverRoleID, SoD check against incompatibleRoles passes, actor-ceiling check (16 V8/V9) | error |
| V7 | roleAssignments validTo ≥ validFrom; expired assignments confer nothing | error |
| V8 | representsActors basis ≠ self requires evidenceRef (mandate/PoA/employment) | error |
| V9 | assignedResources must exist and be within belongsToOrg's reach unless cross-org grant approved | error |
| V10 | dormant 90 days (human) ⇒ auto-suspend; dormant 30 days (break-glass eligible) ⇒ review | warning→auto-action |
| V11 | Erasure requests honoured ≤ 30 days except documented legal-hold exemptions | error |
| V12 | USR-S-* require sponsorUserID active human; sponsor leaves ⇒ service account review ≤ 5 days | error |
| V13 | Service credentials rotate ≤ credentialRotationDays ≤ 90 | error |
| V14 | lastAccessReview within role's reviewCycleMonths for every active assignment | warning→suspend on 2× overdue |
| V15 | Age ≥ 18 attested at registration for human users | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 14 actor | representsActors register; consent operations executed by users on behalf of actors are attributed to both |
| 15 organisation | belongsToOrg; org userIDs mirror (15 §5.6); offboarding cascade A8 |
| 16 role | roleAssignments operate role definitions; all V5/V6 checks bind |
| 36 device / 35 edge | Edge management identities; commissioning actions by field_technician users logged against devices |
| 44 model | USR-S-MODEL execution identities per deployment |
| 57–65 decision/action | Every human decision/override carries userID + roleID; audit joins |
| 73–78 applications | Apps authenticate users, resolve permissions, render role-default dashboards, route alerts per notificationChannels |
| 90 project | Project team membership grants scoped assignments |
| 93 compliance | Access reviews, audit retention, GDPR request handling are compliance controls |

## 10. Benchmarking Requirements

Not benchmarked. Security posture metrics (MFA coverage = 100% target for T2+, review completion, dormant-account rate) are compliance KPIs.

## 11. Dataset Requirements

Authorisation and audit datasets: point-in-time reconstructible (joins with 16 assignment events). Personal fields (name, email, phone) confined to the identity store; analytical datasets reference userID only, pseudonymised on export. Research datasets never include user identity fields.

## 12. Feature Requirements

User attributes are not features for energy models. Security analytics (risk flags) may model login patterns under legitimate-interest basis with DPO sign-off; features limited to behavioural telemetry of the account itself.

## 13. Model Requirements

Models run as USR-S-MODEL identities (A5). UI personalisation models (dashboard layout, alert fatigue) train on aggregated interaction data; opt-out honoured. No model may infer special-category data about users.

## 14. KPI Requirements

Platform KPIs: active users by kind, MFA coverage, time-to-provision, time-to-deprovision, access-review completion, GDPR request SLA compliance, audit completeness. Surfaced on platform-admin dashboards.

## 15. Response Derivative Requirements

None directly. User-mediated overrides of RD-driven actions (occupant taps "boost" during a DR event) are logged with userID and feed override-propensity features on the ACTOR (14 §15) — the user record is the attribution channel only.

## 16. Decision Requirements

Human-in-the-loop decision steps resolve to users holding the required role; the decision record stores userID, roleID, timestamp, evidence viewed. Break-glass decisions require dual-control activation and mandatory post-review. No decision authority attaches to a user outside role assignments.

## 17. Ontology Mapping

Classes: User ⊐ {HumanUser ⊐ {PlatformStaff, OrgUser, ResidentUser, RegulatorUser, ResearchUser}, ServiceAccount ⊐ {APIClient, ModelRuntime, EdgeManagement, Connector}}, RoleAssignment (shared with 16), ActorRepresentation, Credential, AuditEvent.
Object properties: belongsToOrganisation, holdsAssignment (→ RoleAssignment → Role), represents (→ Actor), sponsoredBy, assignedTo (→ Building/Asset/Twin), generatedEvent (→ AuditEvent).
Datatype properties: userID, userKind, username, status, mfaEnabled, identityAssuranceLevel, authenticatorAssuranceLevel, gdprLawfulBasis.
Alignment: FOAF (foaf:OnlineAccount pattern), W3C ORG (org:Membership), NIST 800-63 assurance vocabulary, ODRL party semantics.

## 18. Knowledge Graph Mapping

Nodes: User (pseudonymised key only; no personal attributes in KG). Relationships: BELONGS_TO (→ Organisation), HOLDS (→ Role, temporal), REPRESENTS (→ Actor, temporal+basis), SPONSORS (service accounts), ASSIGNED_TO (→ Building/Asset/Twin), PERFORMED (→ Decision/Action nodes). Personal data stays in the identity store; KG carries userID references for lineage and attribution only.

## 19. Digital Twin Mapping

Twin access mediated by user identity + role (16 §19). Twin sessions log userID; scenario executions attribute author. Resident-facing twin views (home energy view) auto-scope to representsActors `self` resources.

## 20. Governance

Owner: EIG Catalogue Owner; security operations co-own authentication policy; DPO owns GDPR lifecycle fields. Joiner-mover-leaver workflows (64) are mandatory paths. Quarterly access recertification; monthly dormant-account sweep; annual penetration test covers identity flows. Identity store is a system-of-record service; this catalogue defines its schema and rules.

## 21. Versioning

Schema semver at catalogue level. User records: identity fields versioned; roleAssignments and auditEvents append-only event logs (never rewritten). Erasure replaces personal fields with tombstones while preserving event skeleton for audit (userID, event types, timestamps) per retention schedule.

## 22. Example Records

```text
userID: EIG-USR-00347 | userKind: USR-H-ORG | username: f.tahir | displayName: Farah Tahir
email: verified | locale: en-IE | belongsToOrg: EIG-ORG-0001 (EIG/Tyndall) | identityAssuranceLevel: IAL2
authMethod: sso_oidc (idp: ucc-azuread) | mfaEnabled: true | mfaMethods: [webauthn, totp] | AAL: AAL2 | status: active
roleAssignments: [{RA-9912, EIG-ROL-MLENG, org, —, 2024-02-01, —, approvedBy: EIG-USR-00012, active},
                  {RA-10288, EIG-ROL-CATOWN-44, global, [catalogue 44], 2025-09-01, —, approvedBy: EIG-USR-00012, active}]
assignedTwinIDs: [EIG-DT-BLD-000877] | defaultDashboardIDs: [EIG-DSH-ML-01]
gdprLawfulBasis: contract | lastAccessReview: 2026-03-15 | recordVersion: 1.6.0
```

```text
userID: EIG-USR-01588 | userKind: USR-H-RES | username: pseud-7f3a-app | displayName: (pseudonym)
locale: ga-IE | identityAssuranceLevel: IAL1 | authMethod: local_password | mfaEnabled: true (push) | status: active
representsActors: [{EIG-ACT-00214, self, 2025-06-10, —}]
roleAssignments: [{RA-11502, EIG-ROL-FLEXP, own, —, 2025-06-10, —, auto-approved per enrolment workflow, active}]
assignedBuildingIDs: [EIG-BLD-000877 via self] | notificationChannels: [{push, [dr_event, comfort], quiet 22:00–07:30}]
gdprLawfulBasis: contract | privacyNoticeVersion: 3.2 accepted 2025-06-10
```

```text
userID: EIG-USR-09001 | userKind: USR-S-MODEL | username: svc-model-hp-forecast-prod | status: active
belongsToOrg: EIG-ORG-0001 | sponsorUserID: EIG-USR-00347 | authMethod: oauth2_client_credentials | AAL: AAL2 (mtls)
credentialRotationDays: 60 | roleAssignments: [{RA-12044, EIG-ROL-SVC-FCAST, assigned, [EIG-AST-004211 cohort], …}]
permissions resolve to: [data:dataset:read:assigned, model:artifact:write:own, forecast:series:publish:assigned]
```

## 23. Completion Criteria

Complete when: every authenticated identity in every platform component resolves to a user record; zero permissions exist outside role assignments; MFA/AAL gates enforced and verified; representsActors evidence complete; JML workflows live; GDPR request paths tested end-to-end; audit point-in-time reconstruction demonstrated; Phase 14 confirms no other catalogue stores credentials or grants.
