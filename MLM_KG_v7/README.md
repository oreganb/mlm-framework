# MLM Knowledge Graph — v7

**End-to-end knowledge graph: 339 nodes, 1993 edges.** Merges the MLM model layer (139 sub-models, datasets, assets, formulas, Response Derivative map, applications) with the 94 EIG MLM specification catalogues into one connected graph.

## Start here
1. `MLM Knowledge Graph v7.md` — root navigation (read this first)
2. `12_Catalogues/00_Catalogue_Index.md` — the 94 catalogues by domain band
3. `00_Model_Index.md` — the 139 models by series
4. `08_Response_Derivative/Response Derivative Map v4.md` — RD theory, now bridged to CAT-52..56
5. `99_Exports/mlm_kg_v7.json` — the full merged graph

## What v7 adds over v4
- **94 catalogue nodes** in `12_Catalogues/`, each with YAML frontmatter + wikilinks (Obsidian graph-view ready)
- **737 catalogue→catalogue edges** (`depends_on` / `used_by`) parsed from each catalogue's own declared dependencies
- **74 model↔catalogue bridges** (`governed_by_catalogue`, `specified_by_catalogue`, `realised_by`, `classifies`) connecting the model series and RD map to the catalogues that govern/specify them
- **12 CatalogueBand** grouping nodes

The v4 exports and notes are retained for provenance; v7 is a strict superset.

---

<details><summary>Original v4 README</summary>

# MLM Knowledge Graph v4

Generated 2026-05-13. **233 nodes, 1076 edges** across model, series, dataset, asset, formula, RD and application layers.

## What's in v4

**v4 merges the best of v2 and v3:**

| Layer | v2 | v3 | v4 |
|---|---|---|---|
| Model nodes (139) | Deep: LaTeX, weights, input/output tables | Generic template | Deep + semantic role + validation |
| Formula nodes | Inline in model nodes | 13 series-level stubs | Both: inline detail + series families |
| Weight Registry | Full coefficients (0.295, 0.95, etc.) | Semantic types only | Full coefficients + semantic types |
| Dataset nodes | 19 specific datasets (DS-WEATHER etc.) | 5 families only | 19 specific + 5 families |
| Asset layer | Equipment nodes (7) | 24 rich asset nodes | 24 v3 assets + 7 equipment nodes |
| RD Map | Model-level shadow price detail | Canonical fields | Both + per-series contribution table |
| Applications | ODEON, FLEXUS, ARC (08_Applications) | Empty | ODEON, FLEXUS, ARC (09_Applications) |
| Exports | None | JSON, TTL, CSV | Corrected JSON, TTL, CSV (233 nodes) |
| Model names | Correct (Solar Irradiance=W01) | Reshuffled (W01=Outdoor Temp) | Corrected to v2 canonical names |

## Start here
1. `MLM Knowledge Graph v4.md` — root navigation
2. `00_Model_Index.md` — all 139 models with folder paths
3. `08_Response_Derivative/Response Derivative Map v4.md` — RD theory and governance

## Machine-readable
- `99_Exports/mlm_kg_v4.json` — full graph
- `99_Exports/mlm_kg_v4_edges.csv` — 1076 edges with predicate types
- `99_Exports/mlm_kg_v4_nodes.csv` — 233 nodes
- `99_Exports/mlm_kg_v4.ttl` — RDF/Turtle

## Key correction from v3
Model IDs were reshuffled in v3. Canonical mapping is:
- **W01 = Solar Irradiance Model** (not Outdoor Temperature)
- **W02 = Ambient Temperature Model** (not Solar Irradiance)
- **C08 = Response Derivative Engine** (not Device Scheduling Optimiser)
- **C03 = Determinization Bridge** (not RL/Q-Learning Policy)
All corrected in v4.

</details>
