---
type: knowledge_graph_root
ontology: EIG-MLM
version: 7.0
status: active
tags: [eig-mlm, kg, root]
aliases: [MLM Knowledge Graph v7, MLM v7]
---

# MLM Knowledge Graph — v7 (end-to-end)

Generated 2026-06-18. **339 nodes, 1968 edges** — a single connected graph spanning the **model layer** (139 sub-models in 13 series, datasets, assets, formulas, the Response Derivative map and applications) and the **specification layer** (the 94 EIG MLM catalogues), bridged so that every model series resolves to the catalogues that govern it and the Response Derivative map resolves to the RD-family catalogues that specify it.

## What v7 adds over v4

| Layer | v4 | v7 |
|---|---|---|
| Nodes | 233 | **339** (+106) |
| Edges | 1076 | **1968** (+892) |
| Catalogues | — | **94** as first-class nodes, foldered in `12_Catalogues/`, each with YAML frontmatter + wikilinks |
| Catalogue spine | — | **737** `depends_on` / `used_by` edges parsed from each catalogue's own upstream/downstream declarations |
| Model↔catalogue bridges | — | **31** `governed_by_catalogue` edges (W→weather, T→fabric, C→decision, …) + RD map → RD-family catalogues |
| Bands | — | 12 `CatalogueBand` grouping nodes |

## Start here
1. **[[00_Catalogue_Index]]** — the 94 catalogues by domain band (in `12_Catalogues/`)
2. [[00_Model_Index]] — the 139 models by series
3. [[Response Derivative Map v4]] — RD theory; now linked to [[CAT-52 response_derivative]]
4. `99_Exports/mlm_kg_v7.json` — the full merged graph

## The bridge (how the two layers connect)

- **Model series → catalogues** via `governed_by_catalogue`: e.g. the W-Series (weather) is governed by [[CAT-09 weather_climate]] and [[CAT-08 geographic]]; the C-Series (control) by [[CAT-60 decision_intelligence]], [[CAT-62 control_strategy]], [[CAT-59 constraint]], [[CAT-58 objective]].
- **RD engine (C08) + RD map → RD catalogues** via `specified_by_catalogue` / `realised_by`: [[CAT-52 response_derivative]] · [[CAT-53 response_behaviour]] · [[CAT-54 sensitivity]] · [[CAT-55 response_profile]] · [[CAT-56 response_event]].
- **Catalogue → catalogue** via `depends_on` / `used_by`: the specification spine, parsed directly from each catalogue's declared dependencies.

## Machine-readable (99_Exports)
- `mlm_kg_v7.json` — 339 nodes, 1968 edges
- `mlm_kg_v5_KG_nodes.csv` · `mlm_kg_v5_KG_edges.csv`
- `mlm_kg_v5_KG.ttl` — RDF/Turtle

> v4 exports are retained alongside for provenance. v7 is a superset: it contains every v4 node/edge plus the catalogue layer and the bridges.
