---
type: catalogue
ontology: EIG-MLM
catalogue_no: 39
catalogue_id: EIG-CAT-039
entity: metadata
band: data
status: active
tags: [eig-mlm, catalogue, band:data, entity:metadata]
aliases: ["EIG-CAT-039", "Catalogue 39", "metadata"]
---

# CAT-39 — Metadata

> **EIG-CAT-039** · band: **Data, Features & Datasets** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-15 organisation]] · [[CAT-16 role]] · [[CAT-37 data_source]] · [[CAT-38 data]]
**Used by (downstream):** [[CAT-40 data_quality]] · [[CAT-41 feature]] · [[CAT-42 synthetic_data]] · [[CAT-43 dataset]] · [[CAT-44 model]] · [[CAT-79 ontology]] · [[CAT-80 knowledge_graph]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-039`
**Entity prefix:** `EIG-MET-` (metadata records), `EIG-MET-STD-` (metadata standards/profiles)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Data Management Lead
**Depends on (upstream):** 15 organisation, 16 role, 37 data_source, 38 data
**Used by (downstream):** 40 data_quality, 41 feature, 42 synthetic_data, 43 dataset, 44 model, 79 ontology, 80 knowledge_graph

---

## 1. Definition

The **Metadata Catalogue** defines the metadata standards of the framework: what descriptive, structural, administrative, lineage, provenance, and quality metadata MUST accompany every data-bearing entity (sources 37, variables/data entities 38, features 41, synthetic data 42, datasets 43, models 44). It defines:

- **Metadata standard/profile** (`EIG-MET-STD-*`): a named, versioned set of mandatory and optional metadata elements for a given object class (e.g. dataset metadata profile, model metadata profile), with element definitions, datatypes, and conformance rules.
- **Metadata record** (`EIG-MET-*`): an instance of a profile attached to exactly one object (one dataset, one data entity, one model release).

Metadata is **data about data**: it never alters the object it describes, and it is the machinery by which discovery (search/catalogue browsing), governance (ownership, retention, access), and traceability (lineage, provenance) are delivered. The provenance envelope mandated framework-wide (31 A3, 34 A4, 37 A2) is a *runtime projection* of provenance metadata defined here.

## 2. Scope

**In scope:** the six metadata classes (dataset, schema, source, lineage, provenance, quality); element registers per class; profile definitions per object type; conformance levels; serialisation bindings (JSON, DCAT/Dublin Core export); identifier and timestamp conventions; metadata lifecycle and stewardship duties.

**Out of scope:** the quality *rules* themselves (40 — quality metadata here records which regime applied and the resulting scores); ontology axioms (79 — mappings declared in §17); access enforcement (16 RBAC enforces what access metadata declares); storage implementation (31).

## 3. Classification Structure

### 3.1 Metadata classes (`metadataClass`)

| Code | Class | Describes | Primary consumers |
|---|---|---|---|
| MC-DST | Dataset metadata | Identity, description, coverage, licence of a dataset (43) | Discovery, release, reuse |
| MC-SCH | Schema metadata | Structure: variables, keys, grain, types of a data entity (38) | Validation, parsers, JSON Schema/SQL generators |
| MC-SRC | Source metadata | Origin stream/provider, acquisition method, trust class (37) | Ingestion governance, trust decisions |
| MC-LIN | Lineage metadata | Upstream objects + transformations that produced this object | Impact analysis, reproducibility, debugging |
| MC-PRV | Provenance metadata | Agent/process/time/dataClass attestation per object or value | Audit, no-fabrication enforcement, settlement evidence |
| MC-QLY | Quality metadata | Applied quality regime (40), dimension scores, flag statistics | QA/QC gates, model-eligibility decisions |

### 3.2 Conformance levels (`conformanceLevel`)

| Code | Level | Meaning |
|---|---|---|
| CL-FULL | Full | All mandatory + all conditional elements present and valid |
| CL-CORE | Core | All mandatory elements present and valid |
| CL-PARTIAL | Partial | Mandatory elements incomplete — object NOT releasable/trainable |
| CL-NONE | None | No metadata record — object invisible to discovery, blocked from all downstream use |

### 3.3 Object-type profiles (`EIG-MET-STD-*`)

| Profile | Object class | Mandatory classes |
|---|---|---|
| EIG-MET-STD-DSET | Dataset (43) | MC-DST, MC-SCH, MC-SRC, MC-LIN, MC-PRV, MC-QLY (all six) |
| EIG-MET-STD-DE | Data entity (38) | MC-SCH, MC-SRC, MC-LIN, MC-PRV, MC-QLY |
| EIG-MET-STD-DS | Source stream (37) | MC-SRC, MC-PRV |
| EIG-MET-STD-FT | Feature (41) | MC-SCH, MC-LIN, MC-PRV, MC-QLY |
| EIG-MET-STD-SYN | Synthetic dataset (42) | MC-DST, MC-SCH, MC-LIN, MC-PRV (generator attribution mandatory) |
| EIG-MET-STD-MDL | Model release (44) | MC-LIN (training data), MC-PRV, MC-QLY (validation refs 47) |

## 4. Hierarchy

```
Metadata standard (EIG-MET-STD-*)            ← versioned profile per object class
  └── Metadata record (EIG-MET-*)            ← one per described object
        ├── MC-DST block
        ├── MC-SCH block
        ├── MC-SRC block
        ├── MC-LIN block (lineage graph edges)
        ├── MC-PRV block (provenance assertions)
        └── MC-QLY block (regime + scores)
```

A record belongs to exactly one object; an object of a class with a profile MUST have exactly one ACTIVE record (V1).

## 5. Field Groups & Fields

### 5.1 Metadata record header

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | metadataID | string | — | `EIG-MET-\d{6}` | M | Record ID |
| 2 | profileRef | ref | — | →EIG-MET-STD-* @version | M | Profile + version this record conforms to |
| 3 | objectRef | ref | — | any EIG-* object ID | M | The single described object |
| 4 | objectClass | enum | — | dataset, data_entity, source_stream, feature, synthetic_dataset, model_release | M | Class of described object |
| 5 | conformanceLevel | enum | — | §3.2 | M | Computed at validation |
| 6 | status | enum | — | DRAFT, ACTIVE, SUPERSEDED, RETIRED | M | Lifecycle state |
| 7 | createdBy / updatedBy | ref | — | →17 EIG-USR-* | M | RBAC-resolved identity |
| 8 | createdAt / updatedAt | datetime | — | ISO 8601 UTC | M | Timestamps (UTC stored, 38 rule) |

### 5.2 MC-DST — dataset metadata elements

| # | Element | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | title | string | ≤200 chars | M | Human title |
| 2 | description | string | ≤2000 chars | M | Abstract: content, purpose, method |
| 3 | keywords | array<string> | →83 vocabulary terms preferred | M | Discovery terms |
| 4 | spatialCoverage | ref/geo | →08 EIG-GEO-* or WKT bbox | M | Where |
| 5 | temporalCoverage | interval | ISO 8601 interval | M | When (start/end) |
| 6 | temporalResolution | duration | ISO 8601 duration (e.g. PT30M) | M | Grain |
| 7 | licence | enum | CC-BY-4.0, CC-BY-NC-4.0, internal, contractual, restricted | M | Reuse terms |
| 8 | accessClass | enum | open, internal, personal, confidential | M | Drives 16 RBAC + 43 access rules |
| 9 | publisher | ref | →15 EIG-ORG-* | M | Publishing organisation |
| 10 | contactPoint | ref | →17 EIG-USR-* or role →16 | M | Steward contact |
| 11 | dcatExportable | bool | — | M | Eligible for DCAT-AP catalogue export |

### 5.3 MC-SCH — schema metadata elements

| # | Element | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | variables | array<ref> | →38 EIG-VAR-* | M | Variable register refs only (38 A1 — no ad-hoc names) |
| 2 | primaryKeys | array<string> | field names | M | Uniqueness keys |
| 3 | grain | string | e.g. building×half-hour | M | One row = one … |
| 4 | processingLevel | enum | L0, L1, L2, L3 | M | 38 level |
| 5 | partitioning | string | e.g. year/month | O | Physical layout hint |
| 6 | schemaHash | string | SHA-256 | M | Pinned schema fingerprint; change ⇒ new version |

### 5.4 MC-SRC — source metadata elements

| # | Element | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | sourceStreams | array<ref> | →37 EIG-DS-* | M | Contributing streams |
| 2 | providers | array<ref> | →37 EIG-DSP-* | M | Origin providers |
| 3 | trustClassMin | enum | TR-A..TR-D | M | Weakest trust class among contributors (37 §3) |
| 4 | acquisitionMethod | enum | 37 accessMethod vocab | M | How acquired |

### 5.5 MC-LIN — lineage metadata elements

| # | Element | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | upstreamObjects | array<ref> | any data-bearing EIG-* | M | Direct parents (empty only for L0) |
| 2 | transformation | string | named pipeline/script + version + hash | M | What produced this object |
| 3 | transformationParams | json | — | C | Parameters if parameterised |
| 4 | executedAt | datetime | ISO 8601 UTC | M | Run timestamp |
| 5 | executedBy | ref | →17 EIG-USR-* (incl. USR-S-* service users) | M | Agent |
| 6 | reproducible | bool | — | M | TRUE ⇒ params+inputs+code suffice to regenerate |

### 5.6 MC-PRV — provenance metadata elements

| # | Element | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | dataClass | enum | REAL, CALIBRATED, SYNTHETIC, COMPUTED | M | Framework data class (37 A2 — immutable origin) |
| 2 | originAttestation | string | provider statement / contract ref | M | Why dataClass is asserted |
| 3 | environment | enum | production, sandbox | M | Sandbox firewall tag (chain: 20 A2 … 38 V9) |
| 4 | attributionNotices | array<string> | — | C | e.g. PSR/PULSEse outputs → "OPTICS project, PULSE Programme" (31 A6) |
| 5 | provEnvelopeTemplate | json | — | M | Runtime provenance envelope fields projected onto API responses (31 A3) |

### 5.7 MC-QLY — quality metadata elements

| # | Element | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | qualityRegimeRef | ref | →40 EIG-DQ-R-* | M | Applied regime |
| 2 | dimensionScores | json | {completeness, accuracy, timeliness, consistency, plausibility} ∈ [0,1] | M | Latest assessment (40 §5) |
| 3 | flagStatistics | json | counts per {valid, suspect, estimated, missing, quarantined} | M | 40 §6 vocabulary |
| 4 | assessedAt | datetime | ISO 8601 UTC | M | Assessment time |
| 5 | fitForPurpose | array<enum> | settlement, rd, training, reporting, exploratory | M | Uses the scores permit (40 §7 thresholds) |

## 6. Controlled Vocabularies

Defined here and binding framework-wide: `metadataClass` (§3.1), `conformanceLevel` (§3.2), `licence`, `accessClass` (§5.2), object-type profile register (§3.3). All keyword vocabularies SHOULD draw from 83 (vocabulary) once available; free-text keywords permitted at CL-CORE only.

## 7. Applicability Rules

- **A1 — No metadata, no use.** An object at CL-NONE or CL-PARTIAL MUST NOT be released (43), used for training (50), settlement (27), or RD computation (52). Discovery surfaces only CL-CORE and above.
- **A2 — One ACTIVE record per object.** Supersede, never edit: corrections create a new record version; prior versions retained (mirrors 27 A6 / 38 A3 discipline).
- **A3 — dataClass is origin-immutable.** MC-PRV.dataClass is inherited from 37 originDataClass / 38 per-value class and can never be upgraded (SYNTHETIC never relabels — hyperGrid no-fabrication standard, 37 A2).
- **A4 — Lineage closure.** Every non-L0 object's upstreamObjects MUST resolve to objects that themselves hold ACTIVE metadata (transitive traceability to L0 or to a registered generator 42).
- **A5 — Sandbox propagation.** environment=sandbox propagates to all downstream objects; a production object MUST NOT list a sandbox object upstream (firewall chain).
- **A6 — Personal data minimisation.** accessClass=personal requires a documented lawful basis in MC-DST and pseudonymisation noted in MC-SRC (37 A7; 38 A7 cohort n≥10 for derived open release).

## 8. Validation Rules

- **V1:** exactly one ACTIVE EIG-MET-* per object of a profiled class.
- **V2:** profileRef version exists and is ACTIVE; record elements satisfy that profile's mandatory set, else conformanceLevel ≤ CL-PARTIAL.
- **V3:** schemaHash recomputation matches stored value; mismatch ⇒ record auto-flagged SUPERSEDED-pending.
- **V4:** all variable refs resolve in the 38 register; unknown names rejected.
- **V5:** MC-LIN.upstreamObjects acyclic (lineage is a DAG).
- **V6:** MC-PRV.environment consistent with all upstream environments (sandbox one-way valve).
- **V7:** MC-QLY.qualityRegimeRef resolves in 40; dimensionScores ∈ [0,1].
- **V8:** trustClassMin correctly computed as the minimum across MC-SRC.providers (TR-A < TR-B < …).
- **V9:** licence=open requires accessClass=open and no personal grain (A6).
- **V10:** datasets exported to DCAT must hold CL-FULL.

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 37 data_source | MC-SRC elements drawn from stream/provider records; trust classes inherited |
| 38 data | MC-SCH binds to variable register; processing levels; per-value dataClass aggregated here |
| 40 data_quality | MC-QLY records regime + outcomes; 40 defines the rules and thresholds |
| 41 feature | EIG-MET-STD-FT profile governs feature lineage/provenance |
| 42 synthetic_data | EIG-MET-STD-SYN mandates generator attribution; SYNTHETIC class lock |
| 43 dataset | EIG-MET-STD-DSET is the release gate; access rules read accessClass/licence |
| 44 model | EIG-MET-STD-MDL ties model releases to training lineage + validation (47) |
| 16 role | Catalogue duties (owner/steward/reviewer/approver) for the metadata registry itself |
| 79/80 | §17/§18 mappings; DCAT export feeds the KG ingestion path |

## 10. Benchmarking Requirements
Metadata coverage KPIs: % objects at CL-FULL, median lineage depth resolvable, % datasets DCAT-exportable. Benchmarked per platform (31) quarterly; cohort rules (06) do not apply (registry-level, not building-level).

## 11. Dataset Requirements
Every 43 dataset MUST present an EIG-MET-STD-DSET record at CL-FULL before status=RELEASED. Dataset version bumps require metadata record versioning in lockstep.

## 12. Feature Requirements
Features (41) MUST carry MC-LIN naming source data entities (38, L2+) and the formula version hash; a feature without lineage is not executable in 44 pipelines.

## 13. Model Requirements
Model releases (44) MUST carry MC-LIN enumerating exact training dataset versions (43) and MC-QLY referencing validation runs (47). Retraining ⇒ new MC record.

## 14. KPI Requirements
KPIs (57) computed from data MUST be traceable through MC-LIN to L-level entities; KPI definitions reference metadata records, not raw paths.

## 15. Response Derivative Requirements
RD computations (52) MUST verify, via MC-PRV/MC-QLY, that inputs are REAL/CALIBRATED, carry source timestamps (36 V11), and contain no silent imputation (38 A5). Metadata is the machine-checkable evidence for the 38 §15 RD-grade bar.

## 16. Decision Requirements
Decision intelligence (60) consumes fitForPurpose: an automated action whose evidence chain includes an object not fit for purpose=rd/settlement MUST degrade to advisory mode.

## 17. Ontology Mapping
MC-DST ↔ `dcat:Dataset` / `dct:*` (Dublin Core); MC-LIN/MC-PRV ↔ W3C PROV-O (`prov:wasDerivedFrom`, `prov:wasGeneratedBy`, `prov:wasAttributedTo`); MC-SCH ↔ `dcat:Distribution` + CSVW; alignment axioms owned by 79.

## 18. Knowledge Graph Mapping
Node `MetadataRecord`; edges `DESCRIBES`→object, `CONFORMS_TO`→standard, `DERIVED_FROM` (lineage edges materialised), `ATTESTED_BY`→17, `ASSESSED_UNDER`→40 regime. The lineage DAG is loaded as first-class KG edges to enable impact queries.

## 19. Digital Twin Mapping
Twin templates (33) declare dataset requirements by metadata query (e.g. "EIG-MET records with spatialCoverage=site, fitForPurpose⊇{rd}"); twins (32) bind to objects via metadataID, not file paths.

## 20. Governance
Owner: EIG Data Management Lead (Karen Mould role-equivalent, 16 RESP bindings). Steward maintains profiles; reviewer audits conformance quarterly; approver signs profile version changes. Profile changes are MINOR if additive-optional, MAJOR if mandatory-set changes (objects re-validate within 90 d).

## 21. Versioning
Profiles semver `EIG-MET-STD-* @MAJOR.MINOR.PATCH`. Records versioned per object (supersede chain, A2). Catalogue version 1.0.0.

## 22. Example Records

### EIG-MET-000412 — dataset metadata for EIG-DSET-ARAN-ELEC-HH
- profileRef EIG-MET-STD-DSET@1.0.0; objectRef EIG-DSET-ARAN-ELEC-HH (43); conformance CL-FULL
- MC-DST: title "Aran Islands half-hourly electricity consumption (pseudonymised)"; spatial EIG-GEO-CZ-IE-ISL; temporal 2024-01-01/2026-05-31; resolution PT30M; licence contractual; accessClass personal; publisher EIG-ORG-0044
- MC-SCH: variables [EIG-VAR-0102 …]; keys [building_pseudonym, ts_utc]; grain building×half-hour; level L1; schemaHash sha256:…
- MC-SRC: streams [EIG-DS-00211]; providers [EIG-DSP-0003]; trustClassMin TR-A; acquisition file_drop
- MC-LIN: upstream [EIG-DE-ARAN-ELEC-HH-L1 ← L0 landing]; transformation ingest_hh_v3.2 (hash …); reproducible TRUE
- MC-PRV: dataClass REAL; environment production; envelope template per 31 A3
- MC-QLY: regime EIG-DQ-R-014; scores {completeness 0.991, accuracy 0.97, timeliness 0.99, consistency 0.98, plausibility 0.99}; fitForPurpose [settlement, rd, training, reporting]

### EIG-MET-000587 — model release metadata for EIG-MDL-FLEXENV-01@1.4.2
- profileRef EIG-MET-STD-MDL@1.0.0; conformance CL-FULL
- MC-LIN: training datasets [EIG-DSET-ODEON-ASSET-TELEM@2.3, EIG-DSET-ARAN-ELEC-HH@4.1]; pipeline train_flexenv v1.4 (hash …)
- MC-PRV: dataClass COMPUTED; environment production
- MC-QLY: validation EIG-VAL-00077 (47); fitForPurpose [rd, training]

### EIG-MET-000601 — synthetic dataset metadata (sandbox)
- objectRef EIG-DSET-SYNTH-GRID-IE-V1 (42/43); profile EIG-MET-STD-SYN
- MC-PRV: dataClass SYNTHETIC (locked, A3); environment sandbox; attribution "SYNTH_GRID_IE generator EIG-SYN-GEN-0004"
- V6 ✓: no production object may cite this upstream

## 23. Completion Criteria
☑ Six metadata classes defined with element registers ☑ Profiles per object type with conformance levels ☑ Lineage DAG + provenance immutability rules ☑ RD/settlement evidence path machine-checkable ☑ DCAT/PROV-O mappings declared ☑ Examples resolve cross-catalogue refs (37/38/40/42/43/44).
