---
type: catalogue
ontology: EIG-MLM
catalogue_no: 16
catalogue_id: EIG-CAT-016
entity: role
band: actors
status: active
tags: [eig-mlm, catalogue, band:actors, entity:role]
aliases: ["EIG-CAT-016", "Catalogue 16", "role"]
---

# CAT-16 — Role

> **EIG-CAT-016** · band: **Actors & Organisations** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Role Catalogue

## 1. Definition

The Role Catalogue defines every role that actors (14), organisations (15) and users (17) can hold within the EIG MLM framework. A role is a named, governed bundle of permissions, responsibilities and decision rights that is assignable, revocable and auditable. Roles are the single source of authority in the system: NO permission may be granted directly to a user or actor — all authority flows through role assignment (RBAC invariant R1). The actor catalogue's `controlAuthorityCeiling` caps what any role can confer on that actor's scope.

This catalogue defines role identity, category, permission sets, responsibility sets, decision rights, assignability constraints, separation-of-duties rules, and linkage to workflows, dashboards and catalogue governance duties.

## 2. Scope

In scope:

- Business roles (owner, tenant, energy manager, portfolio manager, billing administrator).
- Operational roles (facility operator, maintenance technician, field technician, control-room operator).
- Market roles (trader, settlement officer, BRP operator, aggregation manager, bid approver).
- Technical roles (data engineer, ML engineer, model owner, RD analyst, platform administrator, device commissioner).
- Regulatory & compliance roles (compliance officer, auditor, DPO, regulator read-only).
- Digital-twin roles (twin owner, twin operator, twin viewer, scenario author).
- Platform roles (catalogue owner, data steward, validation reviewer, version approver, support agent, API service role).
- Organisational roles (data_controller, data_processor, scheme_operator, project_coordinator) held by orgs.
- Catalogue governance duty roles referenced by every catalogue's §20 (owner, steward, reviewer, approver).

Out of scope: identity and authentication (user catalogue 17); actor typology (14); permissions implementation in specific applications (73–78 declare which permissions they enforce); HEMRM market-role semantics (those are actor TYPES in 14; this catalogue covers assignable operational roles).

## 3. Classification Structure

Role category (`roleCategory`) — closed vocabulary:

| Code | Category | Examples |
|---|---|---|
| ROLC-BUS | business | building_owner, tenant_rep, energy_manager, portfolio_manager |
| ROLC-OPS | operational | facility_operator, maintenance_tech, field_tech, control_room_operator |
| ROLC-MKT | market | trader, bid_approver, settlement_officer, aggregation_manager |
| ROLC-TEC | technical | data_engineer, ml_engineer, model_owner, rd_analyst, device_commissioner, platform_admin |
| ROLC-REG | regulatory | compliance_officer, auditor, dpo, regulator_readonly |
| ROLC-DT | digital_twin | twin_owner, twin_operator, twin_viewer, scenario_author |
| ROLC-PLT | platform | catalogue_owner, data_steward, validation_reviewer, version_approver, support_agent, service_account_role |
| ROLC-ORG | organisational | data_controller, data_processor, scheme_operator, project_coordinator |

Assignee kind (`assigneeKind`): user, actor, organisation, service_account — each role declares which kinds may hold it.

Authority tier (`authorityTier`) — ordered ladder used by separation-of-duties and ceiling checks: T0 view_only < T1 operate < T2 configure < T3 approve < T4 administer.

## 4. Hierarchy

```text
RoleCategory (8)
└── Role (EIG-ROL-XXXX)            ← assignable unit
    └── PermissionSet → Permission ← atomic, namespaced
```

- Roles may inherit from at most one parent role (`inheritsFrom`), gaining its permissions; inheritance depth ≤ 2; cycles illegal (V5).
- Permissions are atomic strings `domain:resource:action[:scope]` (e.g. `asset:setpoint:write:assigned`, `market:bid:approve:org`) drawn from the closed Permission Register (§6).
- A role NEVER contains another role's responsibilities implicitly — responsibilities do not inherit (only permissions do), to keep accountability explicit (V6).

## 5. Field Groups & Fields

### 5.1 Identity & classification

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | roleID | string | — | `EIG-ROL-XXXX` (mnemonic suffix) | M | Unique role identifier |
| 2 | roleName | string | — | snake_case ≤ 60 chars, unique | M | Canonical name |
| 3 | roleCategory | enum | — | §3 ROLC-* | M | Category |
| 4 | description | string | — | ≤ 500 chars | M | What the role is for |
| 5 | assigneeKind | array | — | user, actor, organisation, service_account | M | Who may hold it |
| 6 | authorityTier | enum | — | T0–T4 | M | Highest action tier the role confers |
| 7 | inheritsFrom | string | — | roleID or null | O | Parent role (permissions only) |
| 8 | status | enum | — | draft, active, deprecated, retired | M | Lifecycle |

### 5.2 Permissions

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | permissions | array | — | permission strings from Permission Register | M | Directly granted permissions (≥ 1 unless pure-responsibility org role) |
| 2 | effectivePermissions | array | — | derived | D | permissions ∪ inherited (computed, never stored authoritatively) |
| 3 | scopeModel | enum | — | global, org, site, assigned, own | M | Default scope qualifier applied to unscoped permissions |
| 4 | controlPermissionsMax | enum | — | actor controlAuthorityLevel ladder value | C | Required if any `*:setpoint:write` or `*:command:*` permission present; cannot exceed assignee actor's ceiling (V8) |
| 5 | dataClassMax | enum | — | open, internal, confidential, personal, special_category | M | Highest data classification accessible |
| 6 | environments | array | — | production, staging, sandbox | M | Where permissions apply |

### 5.3 Responsibilities & decision rights

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | responsibilities | array | — | [{respCode, description, raci}] | M | RACI-coded duties; respCode from Responsibility Register §6 |
| 2 | decisionRights | array | — | [{decisionClass, right}] | C | right ∈ propose, approve, veto, execute, escalate; decisionClass from decision catalogue (60) classes |
| 3 | approvalLimits | object | — | {financialEUR?, powerKW?, durationH?} | C | Quantitative caps on approvals (e.g. bid_approver ≤ 50,000 EUR) |
| 4 | escalationRoleID | string | — | roleID | C | Where decisions above limits escalate |
| 5 | onCallEligible | boolean | — | true/false | O | Role participates in 24/7 rotas |

### 5.4 Assignment constraints

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | prerequisites | array | — | [{kind: training/cert/clearance/role, ref}] | O | Required before assignment (e.g. field_tech ⇒ safe-electrical cert) |
| 2 | incompatibleRoles | array | — | roleID[] | C | Separation of duties: cannot be co-held (V9) |
| 3 | maxHoldersPerScope | integer | — | ≥ 1 or null | O | E.g. exactly 1 catalogue_owner per catalogue |
| 4 | minHoldersPerScope | integer | — | ≥ 0 | O | Continuity floor (e.g. ≥ 2 control_room_operator per network) |
| 5 | assignmentApproverRoleID | string | — | roleID | M | Who may grant this role |
| 6 | reviewCycleMonths | integer | months | 1–24 | M | Recertification interval (T3/T4 ≤ 6, V10) |
| 7 | maxAssignmentDurationMonths | integer | months | ≥ 1 or null | O | Hard expiry if set |
| 8 | mfaRequired | boolean | — | true/false | M | True mandatory for T2+ (V11) |

### 5.5 Linkages

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | workflowIDs | array | — | EIG-WF-* | O | Workflows (64) the role participates in, with step bindings |
| 2 | dashboardIDs | array | — | EIG-DSH-* | O | Default dashboards (75) |
| 3 | catalogueDuties | array | — | [{catalogueNo, duty: owner/steward/reviewer/approver}] | C | Governance duties over catalogues 01–94 |
| 4 | applicableOrgTypes | array | — | ORG-* codes | O | Org types where role makes sense |
| 5 | applicableActorTypes | array | — | ACT-* codes | O | Actor types where role makes sense |

### 5.6 Audit & metadata

recordVersion (semver), createdDate, modifiedDate, owner (catalogue owner userID), governanceStatus (draft/in_review/approved/deprecated) — as per programme standard. Every permission change is a version event (§21).

## 6. Controlled Vocabularies

**Permission Register** (closed; namespaced `domain:resource:action[:scope]`). Domains: building, asset, space, device, data, dataset, feature, model, rd, kpi, decision, action, control, market, bid, trade, settlement, contract, flex_service, twin, scenario, dashboard, alert, report, catalogue, user, role, org, workflow, audit. Actions: read, write, create, delete, execute, approve, publish, export, configure, commission, override. Scopes: global, org, site, assigned, own. Examples: `data:dataset:export:org`, `rd:profile:read:assigned`, `control:setpoint:write:assigned`, `market:bid:approve:org`, `catalogue:record:publish:global`, `twin:scenario:execute:assigned`, `audit:log:read:global`.

**Responsibility Register** (closed; RESP-NNN): RESP-001 maintain_data_quality, RESP-002 approve_catalogue_changes, RESP-003 respond_to_alerts, RESP-004 execute_market_bids, RESP-005 validate_models, RESP-006 manage_consents, RESP-007 commission_devices, RESP-008 review_access, RESP-009 incident_response, RESP-010 settlement_reconciliation, RESP-011 comfort_complaint_handling, RESP-012 rd_recalibration_signoff, RESP-013 scenario_governance, RESP-014 statutory_reporting, RESP-015 safety_isolation_authority. (Extensible by minor release.)

RACI values: responsible, accountable, consulted, informed. Decision-right values: propose, approve, veto, execute, escalate.

## 7. Applicability Rules

| Rule | Statement |
|---|---|
| A1 | All authority via roles: applications (73–78) MUST resolve user/actor permissions exclusively from active role assignments |
| A2 | Control-capable roles (controlPermissionsMax set) bind to actor ceilings: effective control level = min(role.controlPermissionsMax, actor.controlAuthorityCeiling, asset.controlAuthorityLevel) |
| A3 | Life-safety circuits/assets (FN-CRITICAL, space catalogue exclusions) are outside ALL role control permissions except RESP-015 holders in manual, on-site procedures |
| A4 | regulator_readonly roles: dataClassMax internal, no write permissions, global read on compliance/report domains only |
| A5 | service_account_role: no interactive login, permissions enumerated exhaustively, no inheritance, mandatory expiry |
| A6 | Catalogue duties: each of catalogues 01–94 must have exactly one owner-duty holder and ≥ 1 steward, reviewer, approver (closes every catalogue's §20) |
| A7 | DPO role mandatory wherever personal data processed (mirrors org V11) |

## 8. Validation Rules

| ID | Rule | Severity |
|---|---|---|
| V1 | roleID/roleName unique; pattern-valid | error |
| V2 | Every permission string exists in Permission Register | error |
| V3 | authorityTier consistent with permissions (approve ⇒ ≥ T3; administer-class ⇒ T4) | error |
| V4 | dataClassMax ≥ classification of all data-domain permissions held | error |
| V5 | inheritsFrom acyclic, depth ≤ 2, parent status active | error |
| V6 | Responsibilities not inherited; every role with decisionRights has ≥ 1 accountable responsibility | error |
| V7 | approvalLimits present for every approve decision right with financial/power consequence | error |
| V8 | controlPermissionsMax ≤ ceiling of every current assignee actor (checked at assignment time) | error |
| V9 | incompatibleRoles symmetric; no assignment may co-hold incompatible pair (e.g. trader ⊥ settlement_officer; model_owner ⊥ validation_reviewer for same model; version_approver ⊥ author of change) | error |
| V10 | reviewCycleMonths ≤ 6 for T3/T4 roles | error |
| V11 | mfaRequired = true for T2+ | error |
| V12 | min/maxHoldersPerScope satisfied by current assignments (continuity & exclusivity) | warning→error on T4 |
| V13 | Deprecated roles: no new assignments; retired roles: zero active assignments | error |
| V14 | Every workflow step requiring a role resolves to an active role with matching permissions | error |

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 14 actor | Actor.roles[] reference roleIDs; actor ceiling caps control roles (A2) |
| 15 organisation | Org roles (ROLC-ORG); assignmentApprover chains respect org governance |
| 17 user | User.roleAssignments are the operative grants; user catalogue stores assignment records, this catalogue stores definitions |
| 57–65 decision layer | decisionRights reference decision classes; approval limits bound action authorisation |
| 60 decision_intelligence / 61 action | Every automated action declares the role whose authority it executes under |
| 64 workflow | Workflow steps bind to roles; V14 |
| 73–78 applications | Apps enforce permissions; dashboards default by role |
| 79–85 semantic layer | Role/Permission classes exported to ontology |
| 93 compliance | Access reviews, SoD attestations are compliance controls |

## 10. Benchmarking Requirements

Not benchmarked. Access-governance metrics (review completion rate, orphaned-permission count, SoD violations = 0) are compliance KPIs, not performance benchmarks.

## 11. Dataset Requirements

Role definitions and assignment events form the authorisation reference dataset: required joins for every audit log, decision record and action record (who-could-do-what-when). Assignment history append-only; point-in-time permission resolution must be reconstructible for any past timestamp (settlement disputes, incident forensics).

## 12. Feature Requirements

Roles are not model features. Exception: workflow-optimisation and alert-routing models may use roleCategory/onCallEligible as routing features. User-behaviour analytics over role dimensions require DPO approval.

## 13. Model Requirements

Models execute under service_account roles with enumerated permissions (A5): a forecasting model holds `data:dataset:read:assigned` + `model:artifact:write:own` only. Control-loop models executing actions hold the executing role's authority and are bounded by A2/A3. Model promotion to production requires version_approver role sign-off (SoD with model author per V9).

## 14. KPI Requirements

Governance KPIs: % assignments recertified on time, SoD violation count (target 0), orphaned accounts, mean time to deprovision, emergency-access invocations. Surfaced on compliance dashboards (75) and statutory reports where required (93).

## 15. Response Derivative Requirements

RD lifecycle is role-gated: rd_analyst computes/publishes profiles (`rd:profile:write`), RESP-012 holder signs off recalibrations, market roles consume (`rd:profile:read`). No RD-derived control action executes above the A2 effective ceiling.

## 16. Decision Requirements

Every decision record (60) carries: executing roleID, decision right exercised, approval-limit check result, escalation path if exceeded. Automated decisions cite the service-account role; human overrides cite the human's role. Decisions lacking a valid role resolution are rejected pre-execution (hard gate).

## 17. Ontology Mapping

Classes: Role, RoleCategory, Permission, PermissionSet, Responsibility, DecisionRight, RoleAssignment (reified: assignee, role, scope, validFrom/To, approver).
Object properties: hasPermission, inheritsFrom, hasResponsibility, hasDecisionRight, incompatibleWith (symmetric), assignedTo, approvedBy, scopedTo, escalatesTo.
Datatype properties: roleID, roleName, roleCategory, authorityTier, dataClassMax, mfaRequired, reviewCycleMonths, status.
Alignment: W3C ORG ontology (org:Role, org:Membership ≈ RoleAssignment), ODRL for permission semantics (odrl:Permission, odrl:action), RBAC NIST model (core+hierarchical+constrained RBAC).

## 18. Knowledge Graph Mapping

Nodes: Role, Permission, Responsibility. Relationships: HAS_PERMISSION, INHERITS_FROM, HAS_RESPONSIBILITY, INCOMPATIBLE_WITH, ASSIGNED_TO (→ User/Actor/Org, with temporal properties), APPROVED_BY, BINDS_STEP (→ Workflow), GOVERNS (→ Catalogue). Keys: roleID, permission string. Assignment edges carry validFrom/validTo for point-in-time queries (§11).

## 19. Digital Twin Mapping

Twins enforce role-scoped views and commands: twin_viewer (read state), twin_operator (execute permitted commands within A2 ceiling), scenario_author (create/run scenarios in sandbox), twin_owner (configure). Twin command APIs validate effective permissions per request; ceiling violations rejected and audited.

## 20. Governance

Owner: EIG Catalogue Owner; security architecture co-owns Permission Register. New permissions/roles require security review + version_approver. Quarterly access recertification campaign (T3/T4 monthly per V10 cycle settings). Emergency break-glass role exists (T4, dual-control activation, 4-hour expiry, mandatory post-incident review).

## 21. Versioning

Role definitions semver: permission additions minor, permission removals or tier changes major (require re-attestation of all holders). Permission Register changes are catalogue releases. Assignment records are immutable events (grant/revoke), never edited.

## 22. Example Records

```text
roleID: EIG-ROL-FLEXP | roleName: flex_provider_participant | roleCategory: ROLC-BUS | authorityTier: T1
assigneeKind: [actor] | applicableActorTypes: [ACT-PRO, ACT-FLX] | status: active
permissions: [flex_service:enrolment:read:own, rd:profile:read:own, control:envelope:configure:own, data:consent:write:own]
controlPermissionsMax: supervised_control | dataClassMax: personal | scopeModel: own | mfaRequired: true
responsibilities: [{RESP-006 manage_consents, accountable}] | reviewCycleMonths: 12
```

```text
roleID: EIG-ROL-AGGM | roleName: aggregation_manager | roleCategory: ROLC-MKT | authorityTier: T3
assigneeKind: [user] | applicableOrgTypes: [ORG-CEO, ORG-COM] | status: active
permissions: [market:bid:approve:org, flex_service:pool:configure:org, rd:profile:read:org, settlement:statement:read:org]
decisionRights: [{flex_bid_submission, approve}, {pool_membership_change, approve}]
approvalLimits: {financialEUR: 50000, powerKW: 2000} | escalationRoleID: EIG-ROL-ORGDIR
incompatibleRoles: [EIG-ROL-SETTL] | mfaRequired: true | reviewCycleMonths: 6
workflowIDs: [EIG-WF-BID-01 (step: approve), EIG-WF-POOL-02] | dashboardIDs: [EIG-DSH-AGG-01]
```

```text
roleID: EIG-ROL-FTECH | roleName: field_technician | roleCategory: ROLC-OPS | authorityTier: T2
assigneeKind: [user] | permissions: [device:device:commission:assigned, asset:config:write:assigned, data:telemetry:read:assigned]
prerequisites: [{cert, safe_electrical_work}, {training, jetson_odeon_deploy_guide}]
responsibilities: [{RESP-007 commission_devices, responsible}, {RESP-015 safety_isolation_authority, responsible}]
controlPermissionsMax: local_manual | mfaRequired: true | reviewCycleMonths: 6 | onCallEligible: true
```

```text
roleID: EIG-ROL-CATOWN-44 | roleName: catalogue_owner_model | roleCategory: ROLC-PLT | authorityTier: T4
assigneeKind: [user] | catalogueDuties: [{44, owner}] | maxHoldersPerScope: 1
permissions: [catalogue:record:publish:global, catalogue:vocabulary:configure:global]
incompatibleRoles: [EIG-ROL-VALREV-44] | reviewCycleMonths: 6 | mfaRequired: true
```

## 23. Completion Criteria

Complete when: every permission enforced by any application exists in the Permission Register; every catalogue 01–94 has its four governance duties bound (A6); all workflow steps resolve (V14); SoD matrix complete and violation-free; control roles reconcile with actor ceilings; point-in-time permission resolution demonstrated; Phase 14 confirms no permission grants exist outside role assignments.
