---
type: catalogue
ontology: EIG-MLM
catalogue_no: 43
catalogue_id: EIG-CAT-043
entity: dataset
band: data
status: active
tags: [eig-mlm, catalogue, band:data, entity:dataset]
aliases: ["EIG-CAT-043", "Catalogue 43", "dataset"]
---

# CAT-43 — Dataset

> **EIG-CAT-043** · band: **Data, Features & Datasets** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** [[CAT-16 role]] · [[CAT-37 data_source]] · [[CAT-38 data]] · [[CAT-39 metadata]] · [[CAT-40 data_quality]] · [[CAT-41 feature]] · [[CAT-42 synthetic_data]]
**Used by (downstream):** [[CAT-44 model]] · [[CAT-46 forecast]] · [[CAT-47 validation]] · [[CAT-50 training]] · [[CAT-52 response_derivative]] · [[CAT-75 dashboard]] · [[CAT-77 report]]
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

**Catalogue ID:** `EIG-CAT-043`
**Entity prefix:** `EIG-DSET-` (dataset packages)
**Version:** 1.0.0
**Status:** ACTIVE
**Owner:** EIG Data Management Lead
**Depends on (upstream):** 16 role, 37 data_source, 38 data, 39 metadata, 40 data_quality, 41 feature, 42 synthetic_data
**Used by (downstream):** 44 model, 46 forecast, 47 validation, 50 training, 52 response_derivative, 75 dashboard, 77 report

---

## 1. Definition

A **dataset** (`EIG-DSET-*`) is the framework's unit of data **release, training, and reproducibility**: a versioned, immutable package of data entities (38) — optionally including feature tables (41) and synthetic instances (42) — with a pinned schema, enumerated files/tables, complete metadata (39 at CL-FULL), quality assessment (40), and machine-evaluable access rules. Datasets are what models train on (50), what validation cites (47), what partners and the public receive, and what reproducibility claims point at: "trained on EIG-DSET-X@4.1" must mean exactly one bit-identifiable corpus forever (A1).

A dataset is a *package*, not a pipeline: entities keep living and growing in 38; a dataset version freezes a defined slice of them.

## 2. Scope

**In scope:** package identity and versioning; member enumeration (entities, files, tables, variables); slice definition (temporal/spatial/population window); format/serialisation register; access rule grammar; release lifecycle; distribution channels; checksums and immutability; citation; training-corpus and release obligations.

**Out of scope:** entity schemas (38), metadata element definitions (39 — datasets *hold* records), quality rules (40 — datasets *inherit* fitness), generator definitions (42), storage engines (31 — lakehouse pattern referenced only).

## 3. Classification Structure

### 3.1 Dataset kinds (`datasetKind`)

| Code | Kind | Contents | Typical consumers |
|---|---|---|---|
| DK-RAW | Curated source slice | L1 entities, single domain | analysts, regulators |
| DK-ANALYTIC | Harmonised analytic | L2/L3 entities, cross-domain joins | research, KPIs, dashboards |
| DK-TRAIN | Training corpus | L2+ entities + feature tables (41), splits declared | 50 training, 47 validation |
| DK-SYN | Synthetic release | 42 instances, visibly labelled | sandbox, public (hyperGrid-class) |
| DK-REF | Reference/registry extract | registry snapshots (07 archetypes, 09 climatology, 28 EF series) | reproducibility pinning |

### 3.2 Access classes (inherited from 39 §5.2, enforced here)

| Code | Class | Rule consequence |
|---|---|---|
| open | Public | open licence required (39 V9); no personal grain; cohort n≥10 for building-derived aggregates (38 A7) |
| internal | EIG/consortium | role-gated (16 RBAC), project scoping permitted |
| personal | Personal-grain data | lawful basis + DPIA in metadata; pseudonymised; contract-bound recipients only |
| confidential | Commercially restricted | contract refs (23) enumerate recipients; NDA flag |

### 3.3 Release states (`status`)

DRAFT → CANDIDATE (validation running) → RELEASED (immutable) → SUPERSEDED (newer version) → WITHDRAWN (defect/legal; tombstone retained, never deleted silently)

## 4. Hierarchy

```
Dataset (EIG-DSET-<NAME>)                ← name-stable lineage across versions
  └── Version (@MAJOR.MINOR)            ← immutable package
        ├── Members: 38 entities (slice) / 41 feature tables / 42 instances
        ├── Files/tables (formats, checksums)
        ├── Metadata record (39, CL-FULL)
        ├── Quality inheritance (40 minimum fitness across members)
        └── Access rules (machine-evaluable)
```

## 5. Field Groups & Fields

### 5.1 Identity & versioning

| # | Field | Type | Unit | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | datasetID | string | — | `EIG-DSET-[A-Z0-9-]{3,40}` | M | Name-stable ID (e.g. EIG-DSET-ARAN-ELEC-HH) |
| 2 | version | semver | — | MAJOR.MINOR (no PATCH — packages immutable) | M | MAJOR: schema/slice semantics change; MINOR: additive period extension |
| 3 | datasetKind | enum | — | §3.1 | M | Kind |
| 4 | title / description | string | — | per 39 MC-DST | M | Mirrors metadata |
| 5 | status | enum | — | §3.3 | M | Lifecycle |
| 6 | supersedes / supersededBy | ref | — | →EIG-DSET-*@ver | C | Version chain |
| 7 | metadataRef | ref | — | →39 EIG-MET-* (CL-FULL) | M | The release gate (39 §11) |
| 8 | citation | string | — | recommended citation incl. version + DOI where minted | M | Scholarly/contractual reference |

### 5.2 Membership & slice

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | memberEntities | array<ref> | →38 EIG-DE-* @ explicit watermark | M | Entities + frozen high-watermark timestamps |
| 2 | featureTables | array<ref> | →41 EIG-FT-SET-* @version + materialisation hash | C | For DK-TRAIN |
| 3 | syntheticInstances | array<ref> | →42 EIG-SYN-DS-* | C | DK-SYN only; mixing synthetic into non-DK-SYN forbidden (V7) |
| 4 | sliceDefinition | json | {temporal: interval, spatial: →08 refs, population: selector} | M | Reproducible slice predicate |
| 5 | variables | array<ref> | →38 EIG-VAR-* | M | Full variable enumeration (union of members) |
| 6 | rowCounts | json | per entity | M | Frozen counts (V3 checksum companion) |
| 7 | splits | json | {train/val/test predicates, leakage rule per 41 A3} | C | Mandatory for DK-TRAIN (A4) |

### 5.3 Files, tables & formats

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | distributions | array<json> | {format, path/URI, bytes, sha256} | M | Every physical artefact checksummed |
| 2 | formats | array<enum> | parquet, csv, jsonl, duckdb, sqlite, geojson | M | parquet canonical for tabular; csv courtesy export |
| 3 | tableMap | json | logical table → file mapping + DDL ref | M | For db-style distributions |
| 4 | compression | enum | zstd, gzip, none | M | Declared per distribution |
| 5 | manifest | string | path to MANIFEST.json (members, checksums, schema hashes) | M | Single machine-readable truth (V3) |
| 6 | storageClass | enum | hot, warm, cold_b2 | M | Operational placement (31 pattern: lakehouse hot, Backblaze B2 cold) |

### 5.4 Access rules (machine-evaluable grammar)

| # | Field | Type | Allowed Values/Format | Req | Description |
|---|---|---|---|---|---|
| 1 | accessClass | enum | §3.2 | M | Inherited from 39, enforced here |
| 2 | licence | enum | 39 §5.2 vocab | M | Reuse terms |
| 3 | accessRules | array<expr> | `permission grammar (16): data:dataset:read[:scope]` + conditions | M | Evaluated by RBAC at request time |
| 4 | recipientContracts | array<ref> | →23 EIG-CON-* | C | For personal/confidential |
| 5 | embargoUntil | datetime | ISO 8601 | C | Pre-publication hold |
| 6 | usageNotice | string | verbatim notices: 42 forbidden-use text for DK-SYN; attribution (31 A6) where PSR-lineage | C | Shipped inside package |
| 7 | retention / disposalRule | duration/json | per accessClass + 27 lineage needs (7 y where settlement-linked) | M | Lifecycle end |

## 6. Controlled Vocabularies
datasetKind (§3.1), status (§3.3), formats/compression/storageClass (§5.3), accessClass/licence (39-aligned). DOI minting policy: open RELEASED datasets of DK-RAW/DK-ANALYTIC/DK-SYN kinds eligible.

## 7. Applicability Rules

- **A1 — Immutability.** A RELEASED version never changes: same ID@version ⇒ same checksums forever. Corrections ⇒ new version + supersedes link (27 A6 discipline); defects ⇒ WITHDRAWN tombstone with reason.
- **A2 — Metadata gate.** RELEASED requires 39 record at CL-FULL (39 §11); CANDIDATE blocks until conformance passes.
- **A3 — Quality inheritance & disclosure.** Package fitness = minimum fitnessResult across member entity-windows (40 §11); imputation rates and flag statistics disclosed in release notes; consumers see what they're getting.
- **A4 — Training corpora declare splits.** DK-TRAIN ships split predicates honouring leakage rules (41 A3); models cite dataset@version + split hash, making every training run corpus-reproducible (50/47).
- **A5 — Synthetic separation & labelling.** DK-SYN only for synthetic content; visibly labelled (42 §11); forbidden-use notice shipped verbatim; syntheticLineage taint never enters DK-RAW/DK-ANALYTIC/DK-TRAIN members (42 A2) except declared augmentation tables inside DK-TRAIN with share disclosure (42 A8).
- **A6 — Access enforcement is machine-side.** accessRules evaluate in RBAC (16); no manual gatekeeping; personal-grain access requires recipientContracts and logs every grant (GDPR accountability, 37 A7).
- **A7 — Sandbox firewall.** environment=sandbox packages (NEST/SYNTH_GRID_IE) never satisfy production data dependencies (chain 20 A2 … 39 A5); production model promotion cites production datasets only.
- **A8 — Open release floor.** accessClass=open: no personal grain, cohort n≥10 aggregates (38 A7), licence open, SYNTHETIC visibly badged — the hyperGrid publication standard.

## 8. Validation Rules

- **V1:** datasetID pattern; name-stability (same conceptual corpus keeps its ID across versions).
- **V2:** version monotonicity; supersedes chain acyclic and complete.
- **V3:** manifest audit: recomputed checksums + row counts match MANIFEST.json; any mismatch ⇒ version invalid, incident raised.
- **V4:** every memberEntity watermark ≤ entity's actual high-watermark at freeze time; slice predicate re-execution reproduces row counts.
- **V5:** variables = exact union of member entity schemas (no phantom/missing variables).
- **V6:** DK-TRAIN: splits present, disjoint, leakage-checked against 41 contracts.
- **V7:** kind/content coherence: synthetic members only under A5 conditions.
- **V8:** accessRules parse in 16 grammar; personal/confidential require recipientContracts.
- **V9:** retention ≥ longest obligation among members (settlement-linked ⇒ ≥7 y).
- **V10:** WITHDRAWN versions: tombstone with reason + notification to all logged consumers (44 dependency graph, 27 where cited).

## 9. Relationships to Other Catalogues

| Catalogue | Relationship |
|---|---|
| 38 data | members are frozen entity slices; variable enumeration; cohort/privacy floors |
| 39 metadata | CL-FULL release gate; citation/licence/coverage mirrored; DCAT export |
| 40 data_quality | inherited fitness + disclosure; settlement/RD gates flow through |
| 41 feature | materialised feature tables pinned by set version + hash |
| 42 synthetic_data | DK-SYN packaging duties; labelling; augmentation disclosure |
| 37 data_source | lineage to streams/providers; trust class minimums surfaced |
| 16 role / 23 contract | access rule evaluation; recipient contracts |
| 44/50/47 | training citation discipline; corpus reproducibility; validation references |
| 52 response_derivative | RD studies cite dataset versions for evidence reproducibility |
| 75/77 | dashboards/reports cite dataset versions for figure provenance |

## 10. Benchmarking Requirements
Release metrics: time-to-release (entity-ready → RELEASED), consumer counts, defect/withdrawal rate, checksum-audit pass rate. Quarterly registry review.

## 11. Dataset Requirements
(Self) — registry completeness: every training run (50), settlement-cited corpus (27), public release, and RD study (52) resolves to a RELEASED EIG-DSET@version; loose file paths in any of those contexts are a validation failure.

## 12. Feature Requirements
Feature tables inside DK-TRAIN are materialisations of pinned EIG-FT-SET versions; recomputation from sources must reproduce the shipped table hash (41 V6 parity extended to packages).

## 13. Model Requirements
Model releases (44) cite training dataset@version + split hash in metadata (39 MC-LIN); retraining on a new dataset version ⇒ new model version. Dataset WITHDRAWN ⇒ dependent models flagged for review.

## 14. KPI Requirements
Published KPI figures (57/77) cite the dataset version they were computed from; re-publication after dataset supersession states which version applies.

## 15. Response Derivative Requirements
RD evidence reproducibility: any RD value entering product gates (22) or settlement baselines (27 BL-*) must be recomputable from cited RELEASED datasets meeting 40 rd fitness with syntheticLineage=FALSE (42 §15). EIG-DSET-ODEON-ASSET-TELEM is the canonical RD-grade corpus pattern.

## 16. Decision Requirements
Offline decision-policy evaluation (60/71) runs on RELEASED corpora; policy promotion documentation cites them. Live decisions consume streams (37), not packages — datasets are the audit trail, not the hot path.

## 17. Ontology Mapping
Dataset ↔ `dcat:Dataset`; version ↔ `dcat:hasVersion`/PAV; distribution ↔ `dcat:Distribution` (format, checksum via SPDX); access ↔ ODRL policy expressions; alignment owned by 79.

## 18. Knowledge Graph Mapping
Node `Dataset` (per version); edges `CONTAINS`→38 entities, `MATERIALISES`→41 sets, `PACKAGES`→42 instances, `CITED_BY`→{44 models, 47 validations, 77 reports}, `SUPERSEDES`. Consumer-impact queries on withdrawal are first-class.

## 19. Digital Twin Mapping
Twin templates (33) express dataset requirements as registry queries (39 §19); calibration evidence for F3 twins (32) cites RELEASED datasets; twin replays pin dataset versions for deterministic re-simulation.

## 20. Governance
Owner: EIG Data Management Lead; duties per 16 A6 (owner/steward/reviewer/approver — approver signs RELEASED transitions and WITHDRAWN tombstones). Open releases additionally reviewed against A8 floor. Access grant logs audited quarterly.

## 21. Versioning
MAJOR.MINOR per §5.1; packages immutable (A1); registry catalogue 1.0.0.

## 22. Example Records

### EIG-DSET-ARAN-ELEC-HH @4.1 — Aran Islands half-hourly electricity (resolves 37/39 refs)
- kind DK-RAW→DK-ANALYTIC (L1 entity slice); status RELEASED; metadataRef EIG-MET-000412 (CL-FULL)
- members: EIG-DE-ARAN-ELEC-HH-L1 watermark 2026-05-31T23:30Z; slice {temporal 2024-01-01/2026-05-31, spatial EIG-GEO-CZ-IE-ISL, population 20 ODEON buildings + CFOAT}; rows 1 326 932
- variables [EIG-VAR-0102 …]; distributions: parquet (zstd) sha256:…, csv courtesy; manifest ✓; storage hot + cold_b2 replica
- access: personal; licence contractual; rules `data:dataset:read:project-odeon` + recipientContracts [EIG-CON-… ODEON consortium]; retention P7Y (settlement-linked, V9)
- quality: inherits EIG-DQ-A-00045112 line — fitness [settlement, rd, training, reporting]; imputation 0.74%/month disclosed
- citation: "EIG (2026). Aran Islands Half-Hourly Electricity Dataset v4.1. ODEON/O-CEI."

### EIG-DSET-ODEON-ASSET-TELEM @2.3 — heat-pump telemetry, RD-grade corpus
- kind DK-TRAIN; members EIG-DE-ODEON-ASSET-TELEM-L2 + feature table EIG-FT-SET-0009-adjacent thermal set @hash; splits {train ≤2025-12, val 2026-01/02, test 2026-03/05} leakage-checked
- fitness [rd, training]; syntheticLineage FALSE; cited by EIG-MDL-FLEXENV-01@1.4.2 (39 EIG-MET-000587)
- access internal; storage hot; RD reproducibility anchor for EIG-RD-SPEC-THERM-SHED (52)

### EIG-DSET-SYNTH-GRID-IE-V1 @1.0 — national synthetic release (hyperGrid)
- kind DK-SYN; members EIG-SYN-DS-… instances (EIG-SYN-GEN-0004@2.1); environment sandbox; visibly labelled SYNTHETIC; usageNotice ships 42 §3.3 forbidden-use text verbatim
- access open; licence CC-BY-4.0; A8 floor ✓ (no personal grain — generator unseeded by personal data); DOI minted; distributions parquet+duckdb

## 23. Completion Criteria
☑ Package identity/immutability/version chain ☑ Member enumeration with frozen watermarks + manifest checksums ☑ Slice reproducibility ☑ Machine-evaluable access rules incl. contracts ☑ Training-split discipline ☑ Synthetic separation + labelling ☑ Quality inheritance + disclosure ☑ Examples resolve EIG-DSET-ARAN-ELEC-HH / EIG-DSET-ODEON-ASSET-TELEM / SYNTH_GRID_IE refs from 37/38/39/42.
