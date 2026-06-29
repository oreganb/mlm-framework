---
type: architecture_node
ontology: MLM
status: active
tags: [mlm, architecture, relationships, semantic]
aliases: [Semantic Relationships, MLM Relationship Types]
---

# Semantic Relationships

## Relationship Type Registry

| Relationship | Meaning | Direction | Example |
|---|---|---|---|
| `feeds_into` | Output of A is direct input to B | A → B | W01 feeds_into R01 |
| `bounded_by` | A's output is constrained by B | A ↔ B | C01 bounded_by T01 |
| `calibrated_by` | A's weights use data from B | A ← B | W01 calibrated_by W17 |
| `validated_by` | A's outputs are checked by B | A → B | W01 validated_by D01 |
| `aggregated_by` | Multiple A instances combined into B | A → B | C01 aggregated_by C07 |
| `refines` | B corrects or improves A | B → A | B09 refines B01 |
| `consumes_RD` | Uses Response Derivative from C08 | B ← C08 | C09 consumes_RD C08 |
| `draws_from` | Model reads dataset | M → DS | T01 draws_from DS-BUILDING |
| `parameterised_by` | Model parameters come from B | A ← B | T01 parameterised_by T11 |
| `surrogate_of` | A is ML approximation of B | A → B | T20 surrogate_of T01 |

## Cross-Layer Relationships

The determinization bridge (C03) is the key cross-layer relationship:
- Stochastic layer → **C03** → Deterministic layer

Response Derivative (C08) propagates cross-layer:
- Deterministic layer (shadow prices) → **C08** → RL layer (governance)

## Navigation
- [[MLM Architecture]]
- [[07_Relationships/Cross-Series Dependencies]]
- [[00_Core/Governance/Naming Conventions]]
