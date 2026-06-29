---
type: catalogue
ontology: EIG-MLM
catalogue_no: 78
catalogue_id: EIG-CAT-078
entity: use_case
band: applications
status: active
tags: [eig-mlm, catalogue, band:applications, entity:use_case]
aliases: ["EIG-CAT-078", "Catalogue 78", "use_case"]
---

# CAT-78 — Use Case

> **EIG-CAT-078** · band: **Applications & Reporting** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-14 actor]] · [[CAT-44 model]] · [[CAT-57 kpi]] · [[CAT-60 decision_intelligence]] · [[CAT-73 application]]
**Used by (downstream):** [[CAT-47 validation]] · [[CAT-64 workflow]] · [[CAT-73 application]] · [[CAT-89 scenario]] · [[CAT-90 project]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-078`
**Entity prefix:** `EIG-UC-`
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Principal Investigator (requirements authority)
**Depends on (upstream):** 14 actor, 44 model, 57 kpi, 60 decision_intelligence, 73 application
**Used by (downstream):** 47 validation (acceptance evidence), 64 workflow (UC-derived processes), 73 (capability census), 89/90 (scenario/project framing), proposal/deliverable writing (the RAD-pattern interface)

---

## 1. Definition

The **Use Case Catalogue** registers use cases (`EIG-UC-*`): structured statements of *who achieves what with the framework, how, and how we know it worked* — each with scenario, actors (14), inputs, outputs, embedded models (44), embedded decisions (60), and **success criteria**. Use cases are the requirements-and-validation backbone: they translate stakeholder needs into testable capability claims, bind those claims to the registers that implement them (73 applications, 44/60 components), and define the acceptance evidence by which delivery is judged (47-style verification at system level). Purpose: **requirements and validation** — the bridge between proposals/deliverables (the Bruegge & Dutoit RAD lineage of the house workflow) and the operational registers, kept live rather than buried in documents (A1).

## 2. Scope
**In scope:** use case schema (the spec's eight: ID, scenario, actors, inputs, outputs, models, decisions, success criteria); use case families aligned to the 21-application census (73); scenario structure (main flow, alternates, exceptions — classical UC discipline); actor binding (14 roles, not personas-only); success criteria as machine-evaluable acceptance (KPI thresholds 57, behavioural assertions, evidence requirements); traceability (requirement ↔ UC ↔ implementing registers ↔ acceptance evidence); lifecycle (proposed → specified → implemented → verified → operational → retired); Gherkin export bindings (user-stories pattern). **Out of scope:** application/feature internals (73), validation method machinery (47 — acceptance cites it), project management (90), planning scenarios (89 — narrative what-ifs, distinct from UC scenarios).

## 3. Classification Structure

### 3.1 Use case families (`family`) — aligned to 73 stages

| Code | Family | Census anchor |
|---|---|---|
| UCF-AWR | Awareness & monitoring | apps 01–04 |
| UCF-DIA | Diagnosis & asset care | apps 05–07 |
| UCF-FC | Forecasting | apps 08–09 |
| UCF-FLX | Flexibility enablement | apps 10–12 (the RD-layer UCs) |
| UCF-MKT | Market participation | apps 13–15 |
| UCF-OPT | Optimisation & investment | apps 16–17 |
| UCF-ENG | Engagement & community | apps 18–19 |
| UCF-GOV | Governance & learning | apps 20–21 |
| UCF-SYS | Cross-cutting/system | platform, data, security UCs (31/35/37 capabilities) |

### 3.2 Scenario structure (per UC)
Preconditions → Main flow (numbered actor/system steps) → Alternate flows → Exception flows (failure handling expectations — fallbacks, abstentions, events per 60/61/63 semantics) → Postconditions. Steps reference register operations, not prose-only behaviour (A2).

### 3.3 Success criteria classes
SC-KPI (→57 threshold over period) · SC-BEH (behavioural assertion, Gherkin-expressible: given/when/then over register states) · SC-EVID (evidence existence: validation records 47, settled outcomes 27, coverage counts 52/56) · SC-UX (measured task/satisfaction outcomes, instrumented or surveyed 18). Every UC carries ≥1 machine-evaluable criterion (V4).

### 3.4 Lifecycle
PROPOSED (need captured) → SPECIFIED (full schema, criteria agreed) → IMPLEMENTED (register bindings live) → VERIFIED (acceptance evidence attached) → OPERATIONAL (criteria monitored ongoing) → RETIRED. Verification is renewable: register changes affecting bindings flag re-verification (44 A4 pattern at UC grain).

## 4. Hierarchy
```
Family (UCF-*) → Use case (EIG-UC-*: scenario + bindings + criteria)
                     ├── traces to requirements (proposal/RAD source refs)
                     ├── implemented by → 73 apps / 44 models / 60 decisions / 64 workflows
                     └── verified by → acceptance evidence (47/27/57 refs)
```

## 5. Field Groups & Fields

### 5.1 Use case (`EIG-UC-*`)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | useCaseID | string | `EIG-UC-\d{4}` | M | Spec: Use case ID |
| 2 | family / name | enum/string | §3.1 | M | Identity |
| 3 | scenario | json | §3.2 structure: pre/main/alternate/exception/post, steps referencing register operations | M | Spec: Scenario |
| 4 | actors | array<ref> | →14 EIG-ACT-* classes / 16 roles (primary + supporting; system actors = 72 agents where autonomous) | M | Spec: Actors |
| 5 | inputs | array<ref> | data/registers consumed (37/38/43), consents (03), configurations | M | Spec: Inputs |
| 6 | outputs | array<ref> | records/effects produced (registry refs), value delivered (27/57 terms) | M | Spec: Outputs |
| 7 | models | array<ref> | →44 (via 70 slots where adaptive) embedded in the flow | M | Spec: Models |
| 8 | decisions | array<ref> | →60 definitions exercised (coupling modes noted) | M | Spec: Decisions |
| 9 | successCriteria | array<json> | [{class §3.3, expression/threshold, evaluation window, evidence binding}] | M | Spec: Success criteria — acceptance, machine-evaluable |
| 10 | requirementTrace | array<ref> | source requirements (proposal refs, RAD sections, stakeholder needs 18) | M | Upward traceability |
| 11 | implementationTrace | array<ref> | →73 apps, 64 workflows, 74 services realising | M | Downward traceability |
| 12 | acceptanceEvidence | array<ref> | →47 records / 27 settled outcomes / 57 V-refs / instrumented results (attached at VERIFIED) | C | The proof |
| 13 | gherkinExport | ref | generated feature file ref (user-stories pattern) for SC-BEH criteria | C | Test-suite bridge |
| 14 | owner / version / status | — | §3.4 lifecycle | M | Governance |

## 6. Controlled Vocabularies
Families (§3.1), criteria classes (§3.3), lifecycle (§3.4), flow step forms (actor-step, system-step, decision-point, exception-handler). Gherkin export conventions per the user-stories house skill.

## 7. Applicability Rules
- **A1 — Live, not literary.** Use cases are register records with resolvable bindings, not document prose; deliverables/proposals *cite* UC IDs (the RAD-pattern documents render from this register, not vice versa) — one requirements truth.
- **A2 — Steps bind to registers.** Main/alternate/exception flows reference real operations (60 evaluations, 61 actions, 64 steps, app surfaces 73); a step no register can perform is a gap finding, not a hopeful sentence.
- **A3 — Exceptions are first-class.** Every UC specifies failure behaviour expectations (abstentions, fallbacks, events, alerts) consistent with the registers' semantics — graceful degradation is a required capability, specified and verified, not an apology.
- **A4 — Acceptance is evaluable.** ≥1 machine-evaluable criterion per UC; SC-KPI/SC-EVID criteria evaluate from register truth; SC-BEH criteria export to executable Gherkin where feasible; "stakeholders are satisfied" alone is not acceptance (SC-UX requires instrumentation/survey design).
- **A5 — Verification is renewable.** Binding changes (model retirement, decision redefinition, app census change) flag affected VERIFIED UCs for re-verification; OPERATIONAL UCs monitor their SC-KPI criteria continuously (57 hooks) — delivery is maintained, not declared once.
- **A6 — Coverage discipline.** The UC register collectively covers the 21-app census capabilities (73) and contracted service promises (74) — uncovered capabilities and unimplemented UCs are both visible gaps (the census audit, §10).
- **A7 — Sandbox UCs for trial capabilities.** NEST/sandbox capabilities carry their own UCs verified against sandbox evidence, labelled; production claims never rest on them (42/77 chain — the no-fabrication standard reaches requirements too).

## 8. Validation Rules
V1 schema completeness (eight spec fields); bindings resolve (14/44/60/73); V2 scenario structure complete (pre/main/exception/post); steps register-bound (A2 audit); V3 actors resolve; autonomous steps name agents (72); V4 ≥1 machine-evaluable criterion; expressions parse/evaluate; V5 traceability bidirectional (requirements ↔ UC ↔ implementations); orphans flagged both directions; V6 VERIFIED status requires attached acceptance evidence resolving and passing; V7 re-verification flags on binding changes (A5 monitors); OPERATIONAL criteria monitoring live; V8 Gherkin exports regenerate consistently from SC-BEH; V9 census coverage audit (A6) maintained; V10 UCs immutable per version; lifecycle transitions evidenced.

## 9. Relationships to Other Catalogues
73/74 (capability census; service promises — coverage targets) · 44/60/70 (embedded components) · 47 (acceptance evidence machinery; system-level verification) · 57/27 (criteria evaluation truth) · 14/16/72 (actors human and autonomous) · 64 (UC-derived operational workflows) · 18 (stakeholder needs as requirement sources; SC-UX) · 89/90 (planning scenarios and projects frame UC portfolios) · 49 (explanation expectations in occupant/regulator UCs) · house skills (RAD/user-stories/definition-of-done: this register is their living backend).

## 10. Benchmarking Requirements
Requirements health: census coverage % (A6), verification currency (% VERIFIED/OPERATIONAL with in-date evidence), re-verification backlog, criteria pass rates for OPERATIONAL UCs.

## 11. Dataset Requirements
Acceptance evidence packages (43) per verification campaign; UC register exports feed deliverable documents (RAD renders).

## 12. Feature Requirements
Criteria expressions use registered KPIs/features (57/41) — acceptance and operations measure with the same instruments.

## 13. Model Requirements
UC-embedded models resolve via slots (70) where adaptive; model changes flag dependent UCs (A5); a model no UC needs is a census question (44 §10 unused review meets requirements from the other side).

## 14. KPI Requirements
SC-KPI criteria bind registered KPIs with thresholds; OPERATIONAL monitoring rides 57 band machinery (breaches → 63 → review).

## 15. Response Derivative Requirements
UCF-FLX use cases specify the RD layer's delivered value in acceptance terms: "subject achieves ACTIVE RD coverage for product P within N weeks of enrolment" (SC-EVID over 52/56), "portfolio delivers ≥95% across a season" (SC-KPI FLEX-DELIV at settlement fitness) — the RD concept's success is defined, measured, and renewable.

## 16. Decision Requirements
UCs exercising automation specify coupling expectations, abstention behaviour, and explanation availability (49) as criteria — governed autonomy is an acceptance requirement, not a footnote.

## 17. Ontology Mapping
Use case ↔ EIG `eig:UseCase` (UML UC alignment) with flow structures; criteria ↔ evaluable specification individuals; traceability via dct/PROV relations; alignment owned by 79.

## 18. Knowledge Graph Mapping
Node `UseCase`; edges `PERFORMED_BY`→14/72, `EMBEDS`→44/60, `REALISED_BY`→73/64/74, `TRACES_TO`→requirements, `ACCEPTED_BY`→evidence, `COVERS`→capability census. Traceability matrices are KG views (the RAD appendix, live).

## 19. Digital Twin Mapping
UC verification campaigns may pre-run flows on twins (32) for rehearsal; twin-rehearsed evidence is labelled and supplements, never substitutes, field acceptance (measured-evidence primacy, 47 A3 spirit).

## 20. Governance
Owner: PI (requirements authority); family owners align with app product owners (73); verification sign-offs follow 64 approval patterns with SoD; census audits quarterly; proposal teams draw from (and contribute back to) this register.

## 21. Versioning
UCs semver; lifecycle per §3.4. Catalogue 1.0.0.

## 22. Example Records

### EIG-UC-0014 — Automated residential flexibility dispatch (UCF-MKT, the worked chain as requirements)
- scenario: pre {enrolled subject, ACTIVE RD, LICENSED policy} → main {market signal → 60 gates → 61 verified shed → 56 event → 53 capture → 27 settlement → occupant credit + explanation} → exceptions {RD stale ⇒ abstain+fallback; comfort guard ⇒ partial per 22 terms; comms loss ⇒ MODE-LOCAL bounded}
- actors: flexumer (14), occupant (consent/override), ops role, EIG-AGT-0007 (system actor); models [FLEXENV via slot]; decisions [EIG-DEC-FLEX-DISPATCH-RES]
- criteria: SC-KPI FLEX-DELIV ≥95% seasonal (settlement fitness); SC-BEH "given override, when issued, then action curtails ≤60 s and no penalty applies" (Gherkin-exported); SC-EVID per-action explanations exist for 100% automated actions (49 V8)
- status OPERATIONAL; acceptance: winter 25/26 evidence attached (the worked records ARE the evidence); monitoring live

### EIG-UC-0031 — Occupant understands and controls participation (UCF-ENG)
- scenario: occupant reviews events, reads plain-language explanations, adjusts consent, overrides freely; criteria: SC-UX comprehension survey ≥ threshold (18 instrument), SC-BEH override honoured (29 A4), SC-EVID consent-change propagation ≤24 h (03)

### EIG-UC-0007 — RD coverage establishment for a new enrolment (UCF-FLX)
- scenario: enrolment → estimation campaign (56) → coverage to minEvidence per required cells → prequal (22); criteria: SC-EVID coverage within 8 weeks (52/56 counts), campaign within event budgets (contract terms) — the learning loop as a delivery promise

## 23. Completion Criteria
☑ All eight spec fields (ID, scenario, actors, inputs, outputs, models, decisions, success criteria) ☑ Live register with bidirectional traceability (requirements ↔ implementation ↔ evidence) ☑ Register-bound flows incl. first-class exceptions ☑ Machine-evaluable, renewable acceptance with Gherkin bridges ☑ Census coverage discipline against the 21 apps and service promises ☑ Worked UCs turning the framework's closed operational chain into verified requirements.
