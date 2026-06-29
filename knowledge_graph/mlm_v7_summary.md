# MLM Knowledge Graph v7 — Summary

**Version:** 7  
**Nodes:** 339  
**Edges:** 1,993  
**Format:** OWL 2 (Turtle), JSON, CSV  
**Vault:** Obsidian (776 files)  
**Date:** June 2026  

---

## What is in MLM_v7.zip

| Folder | Contents |
|--------|----------|
| `99_Exports/` | Primary export files — use these for analysis |
| `12_Catalogues/` | 80+ catalogue markdown files (one per concept category) |
| `00_Core/` | Architecture, governance, and layer definitions |
| `04_Assets/` | Asset-level node definitions |
| `05_Formulas/` | Formula registry (one .md per series) |
| `08_Response_Derivative/` | RD map and cognitive stack documentation |
| `09_Applications/` | ODEON, ARC, FLEXUS application nodes |

---

## Primary export files (use these first)

| File | Format | Description |
|------|--------|-------------|
| `99_Exports/mlm_kg_v7.ttl` | OWL 2 Turtle | Full ontology — load in Protégé or any RDF triplestore |
| `99_Exports/mlm_kg_v7.json` | JSON | Full graph — load in Python, JavaScript, or Neo4j |
| `99_Exports/mlm_kg_v7_nodes.csv` | CSV | Node list with IDs, labels, and categories |
| `99_Exports/mlm_kg_v7_edges.csv` | CSV | Edge list with source, target, and relationship type |

---

## Node types

| Type | Count |
|------|-------|
| Model Series | 13 |
| Sub-Models | 129 |
| Datasets | 47 |
| Concepts | 62 |
| EU Directives and National Regulations | 23 |
| Deployment Artefacts | 18 |
| Publications | 27 |
| Platform Components | 20 |
| **Total** | **339** |

---

## Edge types

| Relationship | Meaning |
|-------------|---------|
| FEEDS | Series A outputs feed Series B inputs |
| VALIDATES | Dataset validates model |
| IMPLEMENTS | Platform component implements sub-model |
| REFERENCES | Publication references concept |
| COMPLIES_WITH | Model output satisfies directive |
| EXTENDS | Sub-model extends parent series |

---

## How to load the TTL in Protégé

1. Download and install Protégé from protege.stanford.edu
2. File → Open → select `99_Exports/mlm_kg_v7.ttl`
3. The ontology uses OWL 2 DL with RDFS labels on all nodes
4. Use the Entities tab to browse nodes by type
5. Use the OntoGraf tab to visualise the graph

## How to load the JSON in Python

```python
import json
import networkx as nx

with open("99_Exports/mlm_kg_v7.json") as f:
    kg = json.load(f)

# Or load as a NetworkX graph from the CSV exports
import pandas as pd
nodes = pd.read_csv("99_Exports/mlm_kg_v7_nodes.csv")
edges = pd.read_csv("99_Exports/mlm_kg_v7_edges.csv")

G = nx.from_pandas_edgelist(edges, source="source", 
    target="target", edge_attr="relationship")
```

---

## Citing the Knowledge Graph

```
O'Regan, B., Tahir, F., & Mould, K. (2026).
MLM Knowledge Graph v7 [Data set].
Zenodo. https://doi.org/10.5281/zenodo.[TO BE ASSIGNED]
```
