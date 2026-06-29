---
type: catalogue
ontology: EIG-MLM
catalogue_no: 93
catalogue_id: EIG-CAT-093
entity: compliance
band: governance
status: active
tags: [eig-mlm, catalogue, band:governance, entity:compliance]
aliases: ["EIG-CAT-093", "Catalogue 93", "compliance"]
---

# CAT-93 — Compliance

> **EIG-CAT-093** · band: **Aggregation & Governance** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-16 role]] · [[CAT-49 explainability]] · [[CAT-59 constraint]] · [[CAT-64 workflow]] · [[CAT-77 report]] · [[CAT-91 policy]] · [[CAT-92 regulation]] · [[CAT-94 standard]]
**Used by (downstream):** [[CAT-75 dashboard]] · [[CAT-76 alert]] · [[CAT-77 report]] · [[CAT-90 project]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-093`
**Entity prefixes:** `EIG-CMP-` (compliance requirements), `EIG-CMP-I-` (compliance instances/assessments)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (compliance accountability) with DPO and security authority
**Depends on (upstream):** 16 role, 49 explainability, 59 constraint (CC-REG), 64 workflow, 77 report, 91 policy, 92 regulation, 94 standard
**Used by (downstream):** 75 dashboard (compliance status), 76 alert (deadline/breach), 77 report (RT-REG evidence), 90 project (grant compliance)

---

## 1. Definition

The **Compliance Catalogue** registers what the framework must *demonstrably satisfy* and the evidence that it does: compliance **requirements** (`EIG-CMP-*`) — discrete, trackable obligations derived from regulations (92), standards (94), policies (91), and contracts (23) — each with its evidence basis, responsible party (16), status, audit trail, and risk; and compliance **instances/assessments** (`EIG-CMP-I-*`) — point-in-time or continuous evaluations with their evidence. Purpose: **auditable compliance** — every obligation the framework is subject to is decomposed into checkable requirements, each owned, evidenced, statused, and risk-rated, so that "are we compliant, on what evidence, who is accountable?" answers continuously rather than in a pre-audit scramble (A1).

## 2. Scope
**In scope:** requirement schema (the spec's six: compliance requirement, evidence, responsible party, status, audit trail, risk); requirement derivation from sources (92/94/91/23); evidence types and binding (the registers as evidence: 77 reports, 47 validations, 64 workflow ledgers, 49 explanation packs, 80 audit queries); assessment lifecycle (continuous monitoring vs periodic audit); status and risk models; the audit trail as immutable record; deadline tracking (with 92 instruments / 76 alerts); finding/remediation handling. **Out of scope:** the regulations/standards themselves (92/94 — sources), policy content (91), constraint enforcement (59 — CC-REG *prevents*; this catalogue *demonstrates*), report generation (77 — evidence artefacts).

## 3. Classification Structure

### 3.1 Requirement domains (`domain`)

| Code | Domain | Sources | Examples |
|---|---|---|---|
| CD-DATA | Data protection | GDPR (92), 37 A7 | lawful basis, minimisation, subject rights, retention, DPIA |
| CD-SEC | Security | NIS2, IEC 62443 (92/94), 35 METIS-128 | risk management, incident reporting, access control, segmentation |
| CD-MKT | Market/energy | grid codes, I-SEM rules, MID metering (92/94) | settlement accuracy, metering compliance, prequalification rules |
| CD-FIN | Financial/grant | funder agreements (90), audit rules | eligible-cost evidence, deliverable compliance, reporting cadence |
| CD-ENV | Environmental/carbon | reporting mandates (92), 28 | emissions reporting accuracy, methodology disclosure |
| CD-AI | AI governance | emerging AI regulation (92), internal policy (91) | transparency (49), human oversight (60/72), risk management (66 containment), record-keeping |
| CD-SAF | Safety | building/electrical codes (94) | life-safety provisions (59 CC-SAF), interlock compliance (36) |

### 3.2 Compliance status (`status`)
COMPLIANT (current evidence satisfies) · PARTIAL (gaps identified, remediation tracked) · NON-COMPLIANT (failing, escalated) · PENDING (assessment due/in progress) · NOT-APPLICABLE (scoped out, with justification). Status is evidence-derived, never asserted (A2).

### 3.3 Risk rating
Per requirement: likelihood × impact → R1–R4 (aligned to 60 risk classes), factoring regulatory severity (fines, licence, reputational), blast radius, and remediation difficulty. Drives monitoring intensity and escalation (A5).

## 4. Hierarchy
```
Source (92 regulation / 94 standard / 91 policy / 23 contract)
  └── Compliance requirement (EIG-CMP-*: discrete obligation)
        ├── Evidence bindings (→77/47/64/49/80 + external attestations)
        ├── Responsible party (→16 role)
        └── Assessments (EIG-CMP-I-*: status over time + audit trail)
```

## 5. Field Groups & Fields

### 5.1 Compliance requirement (`EIG-CMP-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | requirementID | string | `EIG-CMP-\d{4}` | M | Requirement ID |
| 2 | domain / name | enum/string | §3.1 | M | Identity |
| 3 | requirement | json | the obligation: statement + source (→92 article / 94 clause / 91 / 23) + machine-checkable criteria where possible | M | Spec: Compliance requirement |
| 4 | evidence | array<json> | [{evidenceType, binding →77 report / 47 validation / 64 ledger / 49 pack / 80 query / external attestation, currencyRule}] | M | Spec: Evidence — register-grounded (A3) |
| 5 | responsibleParty | ref | →16 role (accountable owner; RACI where multiple) | M | Spec: Responsible party — role, not person |
| 6 | status | enum | §3.2 (evidence-derived, A2) | M | Spec: Status |
| 7 | auditTrail | array<ref> | →EIG-CMP-I-* assessments + evidence-version pins (immutable chain, A4) | M | Spec: Audit trail |
| 8 | risk | json | §3.3 rating + factors + monitoring intensity | M | Spec: Risk |
| 9 | deadlines | json | obligation dates (→92 instrument deadlines); recurrence; 76 alert bindings | C | Time-bound obligations |
| 10 | constraintLink | ref | →59 CC-REG/CC-SAF where the obligation is *also* enforced preventively (the prevent/demonstrate pair) | C | Enforcement↔evidence link |
| 11 | remediationPlan | json | for PARTIAL/NON-COMPLIANT: actions (64 workflows), target dates, owner | C | Gap closure |
| 12 | owner / version / status | — | standard | M | Governance |

### 5.2 Compliance assessment (`EIG-CMP-I-*`)
requirementRef@version · assessedAt · assessmentType {continuous-monitor, periodic-audit, external-audit} · evidenceSnapshot (versions pinned) · verdict §3.2 + rationale · findings (gaps, with severity) · assessor (16, SoD vs responsible party for audits) · resultingActions (→64) · environment. Immutable — the audit trail is append-only (A4).

## 6. Controlled Vocabularies
Domains (§3.1), status (§3.2), risk ratings (§3.3), evidence types {report, validation_record, workflow_ledger, explanation_pack, kg_query_result, external_attestation, configuration_evidence}, assessment types, finding severities. Status vocabulary frozen.

## 7. Applicability Rules
- **A1 — Obligations decompose to trackable requirements.** Every regulation (92), standard (94), policy (91), and compliance-bearing contract (23) decomposes into discrete EIG-CMP requirements; an undecomposed obligation is an open coverage finding (the obligation-coverage matrix, V8).
- **A2 — Status is evidence-derived.** Status computes from current, valid evidence against criteria — never self-declared; COMPLIANT requires resolving, in-currency evidence; expired evidence ⇒ PENDING automatically (evidence currency rules, V4).
- **A3 — The registers are the evidence.** Compliance leans on the framework's own discipline: 77 reports (frozen, reproducible), 47 validation records, 64 workflow ledgers (approvals, SoD proof), 49 explanation packs (AI transparency), 80 audit queries (e.g. the RD citation audit for settlement-accuracy obligations) — compliance evidence is mostly *already generated*, bound here, not separately manufactured.
- **A4 — Audit trail is immutable.** Assessments append; evidence versions pin; a requirement's history reconstructs exactly as it stood at any past date (regulatory audits demand this) — supersede-never-edit (27 A6 / 85 pattern) at the compliance layer.
- **A5 — Risk drives intensity.** R3/R4 requirements get continuous monitoring + leading-indicator alerts (76); deadlines drive 92-instrument-aligned alerts well ahead of due dates; NON-COMPLIANT R4 escalates to PI immediately.
- **A6 — Prevent and demonstrate are paired but distinct.** Where an obligation is enforced preventively (59 CC-REG/CC-SAF), constraintLink records the pair: the constraint *prevents* violation, this requirement *demonstrates* the prevention works (with evidence) — both are needed; neither substitutes.
- **A7 — Separation in assessment.** External/internal audit assessments enforce SoD (assessor ≠ responsible party, 16/64); self-monitoring is continuous but formal verdicts for high-risk requirements require independent assessment.

## 8. Validation Rules
V1 requirement sources resolve (92/94/91/23); criteria machine-checkable where claimed; V2 evidence bindings resolve and are in-currency per rules; V3 responsibleParty resolves to a role; RACI complete where multiple; V4 status evidence-derived (recomputation check); expired-evidence→PENDING automatic; V5 audit trail immutable; evidence-version pins resolve; historical reconstruction tested; V6 risk ratings present; monitoring intensity matches rating (A5); V7 deadlines bind 76 alerts; lead times adequate; V8 obligation-coverage matrix: every 92/94/91 obligation in scope → ≥1 requirement (the governance-ring closure, P13); V9 SoD on assessments (A7); V10 records versioned; remediation plans present for non-COMPLIANT.

## 9. Relationships to Other Catalogues
92/94/91/23 (obligation sources; A1 decomposition) · 59 (CC-REG/CC-SAF prevention pairing A6) · 77 (RT-REG reports as primary evidence; this catalogue is their consumer named in 77 §15) · 47/64/49/80 (evidence machinery A3) · 16 (responsible roles; SoD) · 76 (deadline/breach alerts) · 75 (compliance status dashboards) · 90 (grant/funder compliance, CD-FIN) · 37 (GDPR data-protection chain) · 35 (security compliance, METIS-128/NIS2).

## 10. Benchmarking Requirements
Compliance health: obligation-coverage %, COMPLIANT rate by domain, evidence-currency rate, finding-remediation cycle time, deadline-attainment (CD-FIN/CD-REG = 100% target), audit outcomes.

## 11. Dataset Requirements
Assessment archives + evidence-snapshot manifests retained per the longest applicable regulatory horizon (often ≥7 y); audit-pack exports (43) per external audit.

## 12. Feature Requirements
Compliance-state features (41): requirement risk exposure, days-to-deadline, evidence-currency flags — for risk dashboards and 90 project health.

## 13. Model Requirements
AI-governance requirements (CD-AI) bind model/learning evidence: validation records (47), containment telemetry (66), explanation coverage (49), licence states (71) — the framework's own model discipline *is* its AI-compliance evidence.

## 14. KPI Requirements
Compliance KPIs (57 KF-PLT/governance): coverage, COMPLIANT rate, deadline attainment — rendered on governance dashboards (75) and board reports (77 RT-GOV).

## 15. Response Derivative Requirements
RD-touching obligations: settlement-accuracy compliance (CD-MKT) uses the RD citation audit (80 EIG-KG-Q-0052) as evidence that flexibility settlements rest on valid RD records; AI-transparency obligations (CD-AI) cite per-action explanations (49) for automated dispatch — the RD layer's auditability discharges concrete compliance duties.

## 16. Decision Requirements
Automated-decision governance (CD-AI: human oversight, contestability) binds 60 coupling discipline, abstention machinery, and escalation workflows as evidence; the decision audit chain (60 A1) is compliance-grade by construction.

## 17. Ontology Mapping
Requirement ↔ EIG `eig:ComplianceRequirement` with `eig:derivedFrom`→regulation/standard, `eig:evidencedBy`→evidence artefacts (PROV); assessments ↔ activities; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `ComplianceRequirement`, `ComplianceAssessment`; edges `DERIVED_FROM`→92/94/91, `EVIDENCED_BY`→77/47/64/49, `OWNED_BY`→16, `PAIRED_WITH`→59, `ASSESSED_AS` {verdict, date}. "Show all R4 data-protection requirements and their current evidence" is one query.

## 19. Digital Twin Mapping
n/a directly; twin-related obligations (e.g. simulation-disclosure in reports) bind to twin fidelity evidence (32) where claims rest on twins.

## 20. Governance
Accountability: PI (overall), DPO (CD-DATA), security authority (CD-SEC); requirement decomposition reviewed on source changes (92/94 updates); audit calendar maintained; SoD enforced on assessments; board reviews compliance posture quarterly (RT-GOV).

## 21. Versioning
Requirements semver (source changes ⇒ review); assessments immutable. Catalogue 1.0.0.

## 22. Example Records

### EIG-CMP-0007 — GDPR data minimisation (CD-DATA)
- requirement: process only data necessary for stated purpose (GDPR Art. 5(1)(c) via 92 EIG-REG-GDPR); criteria: every stream (37) has lawful basis + purpose; behaviour analytics floored (53 A4)
- evidence: DPIA report (77), 37 registration records, 80 query showing no unfloored member-grain publication; responsible: DPO role; status COMPLIANT (evidence current); constraintLink: 59 CC-REG GDPR-processing bounds (prevent/demonstrate pair, A6); risk R3 (continuous monitor)

### EIG-CMP-0021 — settlement accuracy (CD-MKT)
- requirement: flexibility settlements rest on valid, evidenced quantities (I-SEM/grid-code via 92); criteria: every settled flex line cites RD records satisfying gates at citation time
- evidence: **EIG-KG-Q-0052 RD citation audit results (80)** + 27 settlement reconciliation reports (77); responsible: market operations role; status COMPLIANT; audit trail: monthly assessments pinned; risk R3

### EIG-CMP-0044 — AI transparency & oversight (CD-AI)
- requirement: automated decisions affecting consumers are explainable and human-overseeable (emerging AI reg + 91 internal policy); criteria: per-action explanations exist (49 V8), abstention/escalation paths active (60), human override honoured (29 A4)
- evidence: 49 explanation coverage query (80), 60 audit aggregates, 66 containment attestations, 71 licence states; responsible: AI/ML Lead + PI; status COMPLIANT; risk R4 (continuous + board attention)

## 23. Completion Criteria
☑ All six spec fields (compliance requirement, evidence, responsible party, status, audit trail, risk) ☑ Requirement-decomposition from regulations/standards/policies/contracts with coverage matrix (V8) ☑ Evidence-derived status leaning on the framework's own registers (A3) ☑ Immutable audit trail with historical reconstruction ☑ Risk-driven monitoring + prevent/demonstrate pairing (59) ☑ SoD on assessment ☑ Worked GDPR, settlement-accuracy (RD-audit-evidenced), and AI-governance requirements closing the compliance ring.
