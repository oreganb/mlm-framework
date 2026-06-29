---
type: application_node
ontology: MLM
status: active
tags: [mlm, application, flexus, platform]
aliases: [FLEXUS, FLEXUS Platform]
---

# FLEXUS — Flexibility Platform

## Overview
FLEXUS is the execution layer platform that consumes MLM outputs. It provides cloud-edge orchestration, market interface, federated learning coordination, and the API layer for all MLM model deployments.

## MLM Integration Points
- **C-Series** dispatch signals → FLEXUS actuation API
- **P-Series** bids → FLEXUS market interface
- **F-Series** federated rounds → FLEXUS cloud aggregation
- **G-Series** grid signals → FLEXUS islanding controller

## Components
- FLEXUS Cloud: C07 MARL training, F01 federated aggregation, M-Series market data
- FLEXUS Edge Gateway: C09 islanding, G07 detection, community aggregation
- FLEXUS Lite: Simplified local dispatch for buildings without Jetson Nano

## Related
- [[ODEON]] — Primary FLEXUS deployment
- [[C-Series]] — Control signals consumed by FLEXUS
- [[F-Series]] — Federated learning run by FLEXUS cloud
