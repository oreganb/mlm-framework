# MLM Framework — Multi-Layer Modelling for Unified Energy System Modelling

**Version:** 0.4.0 | **Licence:** PolyForm Noncommercial 1.0.0 | **DOI:** [to be assigned on Zenodo deposit]

> Associated publication: *"A Multi-Layered Machine Learning Architecture for Unified Energy System Modelling: From Building Physics to Energy Trading"* — submitted to Energy and AI (Elsevier, Q1, IF 9.6)

---

## What this is

The MLM (Multi-Layer Modelling) framework is a four-layer, thirteen-series architecture for unified energy system modelling, developed by the [Energy Informatics Group (EIG)](https://www.tyndall.ie) at Tyndall National Institute / IERC, University College Cork.

The framework organises 129 sub-models across four epistemically distinct layers:

- **Deterministic layer** — physical laws, RC thermal models, power flow, engineering constraints
- **Stochastic layer** — probabilistic forecasting for weather, occupancy, and market conditions
- **AI/ML layer** — learned patterns, NILM decomposition, federated model updates
- **Q-Learning/RL layer** — reinforcement learning dispatch, trading, and carbon governance

The inter-layer currency is the **Response Derivative (RD)** — the partial derivative of realised system response with respect to a conditioning stimulus, evaluated at the current operating point. RD replaces static flexibility capacity metrics with a conditional, context-aware sensitivity measure that is estimated probabilistically, bounded by physical constraints, and consumed by the RL layer as a primary decision primitive.

**CONTINUUM**, the occupant-facing pricing extension, translates RD signals into building-level dynamic price schedules without exposing occupants to wholesale market dynamics.

The framework is validated against the **O-CEI Aran Islands deployment**: 20 buildings on a live island microgrid, 90 days of operational data, 96.8% RD computability.

---

## Repository structure

```
mlm-framework/
├── README.md                        ← this file
├── CITATION.cff                     ← machine-readable citation
├── LICENSE                          ← PolyForm Noncommercial 1.0.0
├── COMMERCIAL_LICENSING.md          ← commercial use enquiries
│
├── generator/
│   ├── mlm_sd_generator/            ← Python SD generator package (v0.4.0)
│   ├── requirements.txt
│   └── README.md                    ← generator-specific documentation
│
├── catalogue/
│   ├── series/                      ← one .md per series (W, O, M, L, R, T, B, G, C, D, P, K, F)
│   └── submodels/                   ← 129 JSON schema files (inputs, outputs, validation rules)
│
├── knowledge_graph/
│   ├── mlm_v7.ttl                   ← OWL 2 Turtle export (339 nodes, 1,993 edges)
│   ├── mlm_v7.json-ld               ← JSON-LD for API access
│   └── mlm_v7_summary.md            ← human-readable node/edge taxonomy
│
├── dataset/
│   ├── summary_statistics.csv       ← population-level statistics (5,000 buildings)
│   ├── sample_building_0001/        ← one complete building (8,760-hour record, 47 features)
│   └── validation/
│       └── run_19_checks.py         ← reproducible validation against all 19 criteria
│
└── examples/
    └── compute_rd_example.ipynb     ← demonstrates RD is computable from synthetic inputs
```

---

## The thirteen series

| ID | Series title | Layer | Representative equation |
|----|-------------|-------|------------------------|
| W | Weather & Exogenous | Stochastic | W_score = Σ wᵢWᵢ (weighted sub-model ensemble) |
| O | Occupancy, Behaviour & DHW | Stochastic | P_occ(t) = Σ πⱼ Sⱼ(t) [HMM] |
| M | Market, Tariff & Carbon Pricing | Stochastic | P_DA(t+1) = αP_DA(t) + βP_ID(t) + γXₜ + ε |
| L | Load & End-Use Decomposition | Deterministic + AI/ML | P_total(t) = Σᵢ Pᵢ(t) + ε_NILM |
| R | Renewables & DER | Deterministic | P_PV = G_POA · A · η_PV · [1 − β(T_cell − 25)] |
| T | Thermal Envelope & HVAC/Plant | Deterministic | C·dT/dt = Σₖ (Tᵢₙ,ₖ − T)/Rₖ + Q_int + Q_solar |
| B | Battery, EV & Storage | AI/ML + Deterministic | E_{t+1} = Eₜ + η_ch·P_ch·Δt − P_dis·Δt/η_dis |
| G | Grid, Microgrid & Protection | Deterministic | V_drop = I·Z_line = I(R + jX) |
| C | Optimisation & Control | Q-Learning/RL | min_x [α·C_energy + β·C_comfort + γ·C_carbon] |
| D | Data Quality & Imputation | AI/ML | x̂ₜ = f(xₜ₋₁, xₜ₋₂, …) + ε [EM/KNN] |
| P | Trading, Revenue & KPIs | Q-Learning/RL | R_total = Σₜ [E_sell(t)·P_sell(t) − E_buy(t)·P_buy(t)] |
| K | Carbon, Compliance & Policy | Q-Learning/RL | CO₂_avoided = (E_ref − E_use) × EF_marginal |
| F | Meta-Learning, Transfer & Privacy | AI/ML | θ_global = (1/N) · Σᵢ θᵢ [FedAvg] |

**Total sub-models: 129** (W: 20, O: 15, M: 12, L: 13, R: 10, T: 20, B: 10, G: 12, C: 10, D: 8, P: 8, K: 5, F: 6)

Execution order per 15-minute settlement period: `D → W → O → M → L → R → T → B → G → C → P → K → F`

Full sub-model specifications (input/output schemas, validation rules) are in `catalogue/submodels/`.

---

## Response Derivative

The Response Derivative (RD) is the primary inter-layer communication currency of the MLM framework.

**Formal definition:**

```
RD(s) = ∂R/∂x |_s
```

where `R` is system response (physical, behavioural, or economic), `x` is the conditioning stimulus (control signal, price signal, or physical perturbation), and `s` is the full state vector at the moment of evaluation.

RD is never unconditional. It is:
- **Estimated** within the Stochastic and AI/ML layers (probabilistically)
- **Bounded** by the Deterministic layer (physically — no RD estimate can imply an infeasible operating point)
- **Consumed** by the Q-Learning/RL layer as a primary decision primitive

### The five RD indicators

| Indicator | Definition | Operational use |
|-----------|-----------|-----------------|
| RD_mag | ∂R/∂x at current state | Dispatch sizing; bid quantity |
| RD_var | Var[∂R/∂x \| state] | Bid confidence interval; governance gating |
| RD_stab | Rolling variance of RD_mag over N activations | Fatigue detection; service qualification |
| RD_sat | d(RD_mag)/dx (second derivative) | Maximum useful activation depth |
| RD_conf | D-series data quality trust score | Advisory mode trigger |

See `examples/compute_rd_example.ipynb` for a worked demonstration that RD is computable from the synthetic dataset inputs.

---

## Synthetic dataset

The synthetic dataset is generated from six real Irish national datasets:

| Source dataset | Provider | MLM series |
|---------------|----------|-----------|
| National Energy Balance 2021 | SEAI | L, K |
| I-SEM Price Series | ENTSO-E | M |
| Census 2022 Microdata | CSO | O, L |
| Climate Normals 1991–2020 | Met Éireann | W, T |
| TABULA Building Typology | IEA/EPISCOPE | T, R |
| BER National Database | SEAI | T, K |

**Scale:** 5,000 buildings × 8,760 hours × 47 features = 43.8 million data points

**Validation:** 19 checks across structural completeness, distributional fidelity, physical feasibility, and RD computability — all 19 pass.

| Key result | Value |
|------------|-------|
| RD computable building-timestep pairs | 97.3% |
| Thermal model energy balance error (mean) | 0.8% |
| BER band distribution vs SEAI register (KS D) | 0.04 |
| I-SEM price distribution vs ENTSO-E historical (KS D) | 0.07 |
| Peak RAM during generation (DuckDB streaming) | 4.2 GB |

The full 5,000-building dataset is not distributed here due to size. Summary statistics are in `dataset/summary_statistics.csv`. One complete building is in `dataset/sample_building_0001/`. To generate the full dataset, run the SD Generator (see below).

---

## MLM Knowledge Graph

The MLM Knowledge Graph (KG) is a 339-node, 1,993-edge semantic network connecting every component of the framework.

**Node types:** Model Series (13), Sub-Models (129), Datasets (47), Concepts (62), EU Directives and National Regulations (23), Deployment Artefacts (18), Publications (27), Platform Components (20)

**Edge types:** FEEDS, VALIDATES, IMPLEMENTS, REFERENCES, COMPLIES_WITH, EXTENDS

**Files:**
- `knowledge_graph/mlm_v7.ttl` — OWL 2 Turtle, load in Protégé or any RDF triplestore
- `knowledge_graph/mlm_v7.json-ld` — JSON-LD for programmatic access
- `knowledge_graph/mlm_v7_summary.md` — human-readable summary of all nodes and edges

**To load the TTL in Protégé:** File → Open → select `mlm_v7.ttl`. The ontology uses standard OWL 2 DL with RDFS labels on all nodes.

---

## How to run the SD Generator

```bash
# Clone the repo
git clone https://github.com/oreganb/mlm-framework.git
cd mlm-framework

# Install dependencies
pip install -r generator/requirements.txt

# Generate a synthetic building portfolio (default: 100 buildings)
cd generator
python -m mlm_sd_generator --n_buildings 100 --output_dir ../output/

# Generate the full 5,000-building dataset (requires ~4.5 GB RAM)
python -m mlm_sd_generator --n_buildings 5000 --output_dir ../output/ --stream
```

The `--stream` flag activates DuckDB streaming mode to manage memory at scale.

---

## How to run the 19 validation checks

```bash
cd dataset/validation
python run_19_checks.py --dataset_path ../output/ --report_path ./validation_report.csv
```

The script outputs a CSV with PASS / WARN / FAIL for each of the 19 checks. Expected result: 19 PASS, 0 FAIL.

---

## How to reproduce the RD computability result

Open `examples/compute_rd_example.ipynb` in Jupyter. The notebook:
1. Loads one complete building from `dataset/sample_building_0001/`
2. Runs the four-layer MLM inference pipeline
3. Computes RD_mag, RD_var, RD_stab, RD_sat, and RD_conf for each timestep
4. Reports the percentage of timesteps where RD is computable (expected: ~97%)
5. Plots RD_mag over a 24-hour period showing diurnal responsiveness patterns

---

## Citing this work

If you use this framework in your research, please cite:

**The paper (primary citation):**
```
O'Regan, B., Tahir, F., Mould, K., & O'Leidhin, E. (2026).
A Multi-Layered Machine Learning Architecture for Unified Energy System Modelling:
From Building Physics to Energy Trading.
Energy and AI. [under review]
```

**This software:**
```
O'Regan, B. (2026). MLM Framework v0.4.0 [Software].
Zenodo. https://doi.org/10.5281/zenodo.[TO BE ASSIGNED]
```

**The full technical specification:**
```
O'Regan, B., Tahir, F., Mould, K., & O'Leidhin, E. (2026).
Multi-Layer Modelling (MLM) Framework: Full Technical Specification v1.0.
EIG Technical Report EIG-TR-2026-001.
Tyndall National Institute / IERC, University College Cork.
https://doi.org/10.5281/zenodo.[TO BE ASSIGNED]
```

A `CITATION.cff` file is included for automated citation extraction by GitHub and Zenodo.

---

## Licence

This software is released under the **PolyForm Noncommercial License 1.0.0**.

You may use, modify, and redistribute this software for non-commercial purposes, including academic research, education, and personal use. Commercial use requires a separate licence — see `COMMERCIAL_LICENSING.md`.

---

## Authors

| Name | Role | Affiliation |
|------|------|-------------|
| Brian O'Regan | Principal Investigator, lead developer | EIG, Tyndall National Institute / IERC, UCC |
| Farah Tahir | AI/ML Lead | EIG, Tyndall National Institute / IERC, UCC |
| Karen Mould | Data Management Lead | EIG, Tyndall National Institute / IERC, UCC |

**Contact:** b.oregan@tyndall.ie

**Energy Informatics Group (EIG)**
International Energy Research Centre (IERC)
Tyndall National Institute, University College Cork
Cork, Ireland
