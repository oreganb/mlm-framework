---
type: catalogue
ontology: EIG-MLM
catalogue_no: 76
catalogue_id: EIG-CAT-076
entity: alert
band: applications
status: active
tags: [eig-mlm, catalogue, band:applications, entity:alert]
aliases: ["EIG-CAT-076", "Catalogue 76", "alert"]
---

# CAT-76 — Alert

> **EIG-CAT-076** · band: **Applications & Reporting** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-16 role]] · [[CAT-17 user]] · [[CAT-34 communication]] · [[CAT-63 event]] · [[CAT-64 workflow]]
**Used by (downstream):** [[CAT-40 data_quality]] · [[CAT-41 feature]] · [[CAT-52 response_derivative]] · [[CAT-56 response_event]] · [[CAT-73 application]] · [[CAT-74 service]] · [[CAT-75 dashboard]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-076`
**Entity prefixes:** `EIG-ALR-` (alert definitions), `EIG-ALERT-CHN-` (channels), `EIG-ALR-I-` (alert instances)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** Operations
**Depends on (upstream):** 16 role, 17 user, 34 communication, 63 event, 64 workflow
**Used by (downstream):** 40 data_quality (escalations), 41 feature (drift alerts), 56/52 (RD-layer notices), 73/75 (in-app surfaces), 74 service (support SLAs)

---

## 1. Definition

The **Alert Catalogue** governs human notification: alert definitions (`EIG-ALR-*`) — each with trigger, threshold, severity, recipients, required action, and escalation — delivered over registered **channels** (`EIG-ALERT-CHN-*`), with instances (`EIG-ALR-I-*`) tracking delivery and acknowledgement. Alerts are the human-attention edge of the event system (63): events are facts with routing; alerts are the deliveries that put the right facts in front of the right people in time. Purpose: **operational monitoring** — attention is a scarce resource managed like one: routed by role, deduplicated at source (63 A3), escalated mechanically, and never cried wolf (A4).

This catalogue formally defines **EIG-ALERT-CHN-STD**, the standard operations channel cited across the registers (40 escalations, 41 drift, 63 routing).

## 2. Scope
**In scope:** alert definition schema (the spec's seven: ID, trigger, threshold, severity, recipients, required action, escalation); channel register (delivery mechanics, reliability classes, quiet rules); recipient resolution (roles 16 → users 17, on-call rotations, delegation); acknowledgement and escalation ladders; notification ergonomics (batching, digests, quiet hours, severity overrides); occupant-notification rules (consent, 03/17); delivery assurance. **Out of scope:** event semantics (63 — alerts consume routed events), workflow tasks (64 — required actions may *be* workflow refs), message transport internals (34 — channels bind transports).

## 3. Classification Structure

### 3.1 Channel register (`EIG-ALERT-CHN-*`)

| Channel | Transport (34) | Reliability | Use |
|---|---|---|---|
| **EIG-ALERT-CHN-STD** | in-app (75 W-EVT) + email digest | standard, at-least-once with receipt | the default ops channel: SEV-2/3 routing, quality/drift escalations (40/41), routine notices |
| EIG-ALERT-CHN-PAGE | paging/SMS + push | high, delivery-confirmed, quiet-hour override | SEV-1 (63): security (EF-SEC), safety-adjacent, kill-switch |
| EIG-ALERT-CHN-OPS-RT | ops-console push (RF-LIVE boards) | live | dispatch-floor awareness (EF-CTL/EF-NET) |
| EIG-ALERT-CHN-OCC | occupant app push (EIG-APP-18) | consent-gated, quiet-hours honoured absolutely | event notices, AU-OCC explanations (49), pre-notification (RDD-OCC) |
| EIG-ALERT-CHN-DIG | daily/weekly digests | batch | SEV-4 summaries, trend notices |

Channel admission requires delivery-receipt capability declaration; quiet rules per channel (A5).

### 3.2 Severity inheritance
Alert severity inherits from the triggering event type (63 §3.2) or threshold definition; alerts never inflate severity (the event register is the truth, 63 A4) — they may *route* one severity to multiple channels.

### 3.3 Escalation ladder forms
Time-boxed acknowledgement → re-deliver → next recipient tier (on-call ladder) → channel upgrade (STD→PAGE) → workflow escalation (64) → severity-raise request back to 63 (mechanical recurrence rules). Each rung declared per definition.

## 4. Hierarchy
```
Channel (EIG-ALERT-CHN-*) ← delivery mechanics
Alert definition (EIG-ALR-*: trigger/threshold → recipients × channel × escalation)
  └── Instance (EIG-ALR-I-*: deliveries, receipts, acks, escalations)
```

## 5. Field Groups & Fields

### 5.1 Alert definition (`EIG-ALR-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | alertID | string | `EIG-ALR-\d{4}` | M | Spec: Alert ID |
| 2 | trigger | json | →63 event-type subscription (preferred) OR registered threshold expression (57 bands / 46 staleness / 71 confidence floors) | M | Spec: Trigger — register-sourced only (A2) |
| 3 | threshold | json | values + hysteresis where expression-based (69 dwell discipline — no flapping alerts) | C | Spec: Threshold |
| 4 | severity | enum | inherited §3.2 | M | Spec: Severity |
| 5 | recipients | json | →16 roles (+ on-call rotation refs); resolution to 17 at fire time; never hard-coded persons (64 A3 symmetry) | M | Spec: Recipients |
| 6 | channels | array<ref> | →EIG-ALERT-CHN-* per severity routing | M | Delivery |
| 7 | requiredAction | json | →64 workflow ref / acknowledgement-only / informational | M | Spec: Required action — registered responses (63 A1 symmetry) |
| 8 | escalation | json | ladder §3.3 with timings per rung | M | Spec: Escalation |
| 9 | content | json | template: what/where/severity/evidence link (drilldown to 63 instance / source record — 75 A4 provenance) | M | Message contract |
| 10 | batching | json | digest eligibility, correlation-group folding (63 A3 inherited) | M | Ergonomics |
| 11 | quietRules | json | per-channel quiet windows + override severities | M | Attention stewardship |
| 12 | owner / version / status | — | standard | M | Governance |

### 5.2 Alert instance (`EIG-ALR-I-*`)
definitionRef@version · firedAt · triggering ref (63 instance / threshold evidence) · resolved recipients (17) · deliveries {channel, sentAt, receipt} · acknowledgements {who, when} · escalations executed · outcome {acked, actioned →64 instance, expired} · environment. Immutable.

## 6. Controlled Vocabularies
Channels (§3.1 register — admission governed), escalation rung forms (§3.3), outcomes, batching classes, quiet-rule forms. Content templates use plain-language severity phrasing standards.

## 7. Applicability Rules
- **A1 — Events route, alerts deliver.** Alert definitions subscribe to 63 routing (or registered thresholds); free-standing monitors that bypass the event register are forbidden — one fact stream, one attention stream.
- **A2 — Register-sourced triggers.** Thresholds reference registered quantities (57/46/71); the alert layer computes nothing novel (75 A2 symmetry at the notification edge).
- **A3 — Recipients are roles.** Resolution to users at fire time with rotation/delegation (16/17); vacations don't lose pages; departures don't orphan alerts.
- **A4 — Wolf-crying is engineered out.** Dedup/folding inherited from 63 correlation; hysteresis on threshold alerts; digest routing for SEV-4; alert-volume KPIs reviewed (A7) — a noisy definition gets fixed or demoted, because ignored alerts are worse than none.
- **A5 — Quiet hours with severity override.** Channels declare quiet rules; only declared override severities (SEV-1 on PAGE) breach them; occupant channel quiet rules are absolute except life-safety-adjacent service notices (consent-scoped, 03).
- **A6 — Delivery is verified.** Receipts per channel capability; failed deliveries retry then escalate channel; an undelivered SEV-1/2 alert is itself an EF-SYS event (63 V10 symmetry).
- **A7 — Occupant attention is consent-scoped.** EIG-ALERT-CHN-OCC sends only consented categories (03), honours preferences, and pairs event notices with AU-OCC explanations (49) — notification as courtesy, not noise.

## 8. Validation Rules
V1 triggers resolve (63 types / registered thresholds); V2 hysteresis present on expression thresholds; flap detector configured; V3 recipients resolve to roles + rotations; resolution tested; V4 channels admitted with receipt capability; routing per severity complete; V5 escalation ladders terminate (no dead rungs); timings sane vs 74 support SLAs; V6 content templates carry evidence links (provenance test); V7 instances complete: deliveries+receipts+acks logged; undelivered-critical events emitted (A6); V8 quiet/override configs audited; occupant consent checks structural; V9 volume/ack analytics flowing (A4 review feed); V10 definitions immutable per version.

## 9. Relationships to Other Catalogues
63 (the fact source; routing receipts) · 64 (required-action workflows; escalation handoffs) · 16/17 (roles→users, rotations) · 34 (transport bindings per channel) · 40/41/46/57/71 (threshold sources: quality escalations, drift, staleness, KPI bands, confidence floors — EIG-ALERT-CHN-STD citations resolved) · 73/75 (in-app surfaces; W-EVT) · 49/03 (occupant notices + explanations + consent) · 74 (support SLA alignment).

## 10. Benchmarking Requirements
Attention-health metrics: ack latency per severity, alert volume per recipient-role (overload detection), false/ignored-alert rates, delivery success, quiet-override incidents — quarterly review (A4).

## 11. Dataset Requirements
Instance archives joined with 63/64 for incident analytics; occupant-notification logs retained per consent records (privacy-scoped).

## 12. Feature Requirements
Alert-load features (41): per-role volumes, ack latencies — for rotation planning and definition tuning.

## 13. Model Requirements
Model-health alerts (drift 41 V9, demotions 60 A5, licence degradations 71) route via STD with board-digest summaries — the model lifecycle is humanly attended.

## 14. KPI Requirements
KPI band-transition alerts (57 A5 / 63 EF-KPI) route per definition; alert-ops KPIs themselves register (57 KF-PLT).

## 15. Response Derivative Requirements
RD-layer notices ride the same machinery: invalidation notifications (52 A7) to affected service owners, campaign milestones (56) to campaign managers, event VOIDs to operations, occupant pre-notifications (RDD-OCC triggers) via CHN-OCC with explanations.

## 16. Decision Requirements
Decision demotions/abstention-storms alert ops (60 A5/63); escalated decisions (risk > auto class) deliver as actionable alerts with the 64 workflow link — the human-in-the-loop is paged, not assumed.

## 17. Ontology Mapping
Alert ↔ EIG `eig:Alert` (notification specialisation of event handling); channels ↔ delivery endpoints with capability properties; alignment owned by 79.

## 18. Knowledge Graph Mapping
Nodes `AlertDefinition`, `Channel`, `AlertInstance`; edges `SUBSCRIBES`→63 types, `NOTIFIES`→16 roles {channel}, `ESCALATES_TO`, `ACTIONED_BY`→64. "Who was told, when, and did they act?" is the incident-review query.

## 19. Digital Twin Mapping
Twin-condition alerts (fidelity degradation, calibration due) route via STD; twin views surface active alerts in subject context (75 W-EVT embedding).

## 20. Governance
Owner: Operations; channel admissions board-reviewed; occupant-channel definitions jointly with DPO; quarterly attention-health review (A4/§10); rotation configs owned by team leads (16).

## 21. Versioning
Definitions/channels semver; instances immutable. Catalogue 1.0.0.

## 22. Example Records

### EIG-ALERT-CHN-STD — the standard operations channel (formal definition, resolving 40/41/63 citations)
- transport: in-app (75 W-EVT on ops boards) + email with daily digest folding; reliability: at-least-once with read receipts; quiet: none in-app, email digest outside hours; intended load: SEV-2/3 + routine register notices
- admission evidence: receipt capability ✓; the default `requiredActions` target across the registers

### EIG-ALR-0040 — quality-regime breach (the 40 escalation made concrete)
- trigger: 63 EF-DAT type for EIG-DQ-R-014 suspectRate>2% daily; severity SEV-2; recipients: data steward role (rotation); channels [CHN-STD]; required action: WF data-quality triage (64); escalation: ack 1 h → re-deliver → data lead → CHN-PAGE at 4 h; content links assessment (EIG-DQ-A-*) + stream (37)

### EIG-ALR-0107 — METIS-128 quarantine page
- trigger: EIG-EVT-T-0107 (SEV-1); channels [CHN-PAGE quiet-override, CHN-OPS-RT]; recipients: security on-call ladder; required action: WF-SEC-QUARANTINE; escalation: 10 min ack → next tier → platform owner

### EIG-ALR-0203 — occupant event pre-notification (CHN-OCC)
- trigger: scheduled dispatch affecting subject (60→56 ARMED); consent category: service notices ✓; content: plain-language notice + AU-OCC explanation link (49); quiet hours honoured; pairing: post-event credit notice

## 23. Completion Criteria
☑ All seven spec fields (ID, trigger, threshold, severity, recipients, required action, escalation) ☑ Channel register with **EIG-ALERT-CHN-STD formally defined** (resolves 40/41/63 forward citations) ☑ Role-resolved recipients with rotations ☑ Engineered-out wolf-crying (dedup, hysteresis, digests, volume review) ☑ Verified delivery with undelivered-critical events ☑ Consent-scoped occupant notification with explanations ☑ Worked quality, security, and occupant alerts closing the attention chain.
