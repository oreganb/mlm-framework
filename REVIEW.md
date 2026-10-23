# KG sprint 2 — review checklist

Branch `kg-sprint-2`, 2026-10-23. MLM SPECS2 V2R5: ML-KG-04 (B01, W01), KG-CR-07 to KG-CR-10, ML-KG-01. One commit per item. Nothing pushed; the MLM Library is untouched.

**How to use.** Tick *Approve* or *Reject* per item, and per owner decision where you disagree. To reject an item: `git revert <commit>`. B01 and W01 were approved by the owner on 2026-10-23 and are now `status: specified`.

## Owner decisions this sprint

| # | Decision | Applied in |
|---|---|---|
| — | **SG-W01-2: OD-113 stands.** W01 ensemble members are **not** clamped; W14 clips its own G_POA at 0; library D-20 unchanged. `b896f84` first applied a clip at 0. That followed the answer recorded to my question during the sprint and the brief's wording; the owner corrected it on 2026-10-23 and `e555fec` reverts W01 to OD-113 | W01 `b896f84`, `e555fec` |
| — | **B01 approved** (2026-10-23): B01-1 (a) power limiting before the step; B01-2, B01-3, B01-4 as written | B01 |
| — | **W01 approved** (2026-10-23): W01-1 ρ_g = 0; W01-2 (a) cloud attenuation only in clear-sky mode, with k_cloud = 0.11 replaced by Kasten & Czeplak (1980), 1 − 0.75 (N/8)^3.4 (DOI 10.1016/0038-092X(80)90391-6 resolves and matches Crossref); TV-W01-6 recomputed | W01 |

---

## B01 — Battery SoC Prediction (ML-KG-04 review) · `6e5b4b2`
☑ Approve ☐ Reject — owner, 2026-10-23. `status: specified`.

**Found.** The note had formula, pseudocode, inputs, outputs and parameters, but no method, RD role, validity limits, test vectors or references. Three content problems: (1) SoC was clipped *after* the step, so energy disappears silently: a 4000 W charge into a 0.88-SoC battery is reported as delivered while only 842 W fits (TV-B01-4b); (2) the LSTM correction had no training target and fed back into the state, where it would accumulate; (3) the RD text "10 % SoC ≈ E_nom × 0.10/Δt W" omitted η_dis.
**Changed.** Physics path, efficiencies and limits unchanged. Added the power-limiting rule with applied powers and a `limited` flag as outputs; the correction is trained on SoC_BMS − SoC_phys and applied to the reported SoC only, with the state re-anchored to the BMS; RD corrected to η_dis·E_nom/Δt = 38 000 W per unit SoC (matches C01 TV-C01-4). Method, RD role, validity limits, 12 test vectors, references.
**Owner decisions.**
Decided 2026-10-23: B01-1 (a); B01-2, B01-3, B01-4 as written.
1. **B01-1 SoC limits:** ✔ (a) limit the power before the step and report it (energy conserved); (b) the literal clip after the step (library D-23 today).
2. **B01-2 state:** physics state re-anchored to the BMS; the correction applies to the reported SoC only.
3. **B01-3:** simultaneous charge and discharge is refused (error), not netted.
4. **B01-4 LSTM:** one layer, 32 units, MSE on the residual, ≥ 30 days of BMS data, retrained by F01; correction = 0 until trained.
**Library impact.** D-23 (clip after the step) must change to power limiting (B01-1 (a)).
**Sources.** Ng et al. 2009; Hochreiter & Schmidhuber 1997; Chemali et al. 2018; IEC 62619 (by designation, not checked against a copy).

---

## W01 — Solar Irradiance (ML-KG-04 review; SG-W01-1, SG-W01-2) · `b896f84`, `e555fec`
☑ Approve ☐ Reject — owner, 2026-10-23. `status: specified`.

**Found.** The formula $G_{DNI}\cos\theta_z + G_{DHI}$ is horizontal irradiance, not plane-of-array; `G_reflected` was never defined (SG-W01-1). Members could be negative (SG-W01-2). k_cloud = 0.11 was attributed to Perez et al. (1990), which is a transposition model, not a cloud attenuation law; and applying a cloud factor to NWP irradiance counts cloud twice, because NWP irradiance is all-sky.
**Changed.** Isotropic-sky transposition (angle of incidence from tilt and azimuth, sky-diffuse view factor, ground-reflected term), which reduces exactly to the old formula at β = 0. `G_reflected` = ρ_g·GHI·(1 − cos β)/2, with ρ_g = 0 by default (OD-112). Members not clamped (OD-113), and the note now says so; quantiles by type 7 from the raw members; G_POA_det = 0 at night. New inputs: solar azimuth (W08 `theta_az`), array tilt and azimuth (DS-PV, as R01). Method, RD role, validity limits, 9 test vectors (TV-W01-1 to -4 also reproduced exactly by pvlib 0.16.1), references.
**Owner decisions.**
Decided 2026-10-23: W01-1 ρ_g = 0; W01-2 (a) with Kasten & Czeplak.
1. **W01-1 albedo:** ✔ ρ_g = 0 (as OD-112) or 0.2 (adds 1.3 % at β = 35°, TV-W01-3).
2. **W01-2 cloud factor:** ✔ (a) apply cloud attenuation only to clear-sky input, never to all-sky NWP irradiance; (b) always apply it (the old note). ✔ In clear-sky mode, Kasten & Czeplak (1980), G/G_clear = 1 − 0.75 (N/8)^3.4, replaces the unsourced 0.11. TV-W01-6 recomputed: N = 8 → 167.628274 (was 596.756657), N = 4 → 622.873669, all-sky unchanged at 670.513098; the note's pseudocode reproduces them. Literature_Alignment.md row W-W01-KCLOUD replaced with W-W01-KCA/KCB.
**Library impact.** D-20's clamp rule (none) and `G_reflected = 0` are unchanged. The transposition is new, though: the library computes horizontal irradiance, so it matches the note only for β = 0 (TV-W01-1) until it adds the transposition. W01 now needs `theta_az` from W08, whose note does not state its azimuth convention.
**Sources.** Liu & Jordan 1960, 1963; Duffie & Beckman 2013; Perez et al. 1990; Loutzenhiser et al. 2007; Kasten & Czeplak 1980; Hyndman & Fan 1996.

---

## KG-CR-07 — M09 outputs P_ETS · `c92fd1e`
☑ Approve ☐ Reject — owner, 2026-10-23.

M09 now lists `P_ETS[k]` (EUR/tCO₂, median and quantiles) as an output to C03 → C01 (`p_CO2` = `P_ETS`/1000). The AI Context line saying ETS "does not directly enter C01" is corrected. C01's SG-C01-8 dependency sentence now says the output exists. The rest of M09 is still a generic template (not RD-path; ML-KG-04 not attempted).

## KG-CR-08 — T01 clamp line removed · `e8c4b11`
☑ Approve ☐ Reject — owner, 2026-10-23.

The line "T_in[k+1] clamp applied before returning" is replaced by the D-22 rule: T_in is returned unclamped and `comfort_ok` is reported (OD-107). The neighbouring line "T_in must stay within [T_min, T_max] at all times" implied a clamp too, so it now says that keeping the band is C01's job. AI Context updated to match. This closes SG-T01-1.

## KG-CR-09 — P07, C08 (and W01) · `cb35711`, `9b12412`
☑ Approve ☐ Reject — owner, 2026-10-23.

- **P07:** RD-7 tolerance = 1 % of the median |d_i| over the window (OD-110); RD-10 = 1 − Var(RD_i)/mean(RD_i²), in [0, 1] (OD-111); 3 new test vectors (TV-P07-8 to -10). SG-KPI-7 and SG-KPI-10 closed.
- **C08:** SG-C08-3 closed (confidence stays caller-supplied; the six notes' own confidence outputs are used, OD-108). SG-C08-6 closed (RDObject checks the nine §7.2 fields; the 11-field check is dropped, OD-120).
- **W01:** `G_reflected = 0` (OD-112) and ensemble not clamped (OD-113), both applied.

## KG-CR-10 — DOPAMINE monitoring sections · `d146d3c`
☑ Approve ☐ Reject — owner, 2026-10-23.

All 19 RD-path notes (the 17 specified notes plus B01 and W01) gain a "Monitoring (DOPAMINE)" section: monitored quantities and metrics, regimes, and the FLEXUS O-08 ladder (≥ 0.80 → 0; 0.60–0.79 → 1; 0.40–0.59 → 2; < 0.40, quarantine or retraining → 3) on the note's own confidence or the caller-supplied one. **Per-series regimes (approved with KG-CR-10, 2026-10-23):** (W, R: weather regime; O: occupancy class; T: weather × occupancy; B, P: tariff period; C: tariff × weather; D: weather regime), because OD-114 fixed the thresholds but not the regimes. Weather regimes fall back to season × clear/overcast until W10/W18 define classes (KG-CR-02). The 17 specified notes keep `status: specified`, because OD-114 decided the change.

## ML-KG-01 — model count 149 · `c387fcd`
☑ Approve ☐ Reject — owner, 2026-10-23.

129/139 corrected to 149 in the seven documents: README.md, `mlm_v7_summary.md`, `knowledge_graph/README.md`, `00_Model_Index.md`, `MLM Knowledge Graph v7.md`, CAT-41, CAT-44 (149 notes counted in `02_Models`; README's series breakdown already summed to 149). Two things are flagged in the text rather than fixed: CAT-44 §3.2's per-series table uses the catalogue's own SER taxonomy and still sums to 139; the summary's other node-type rows do not match the v7 export. Still outside the seven: `MLM Knowledge Graph.md` and `MLM Knowledge Graph v4.md` state the old counts, the demo's deployment JSON is not in this repo, and the exporter count check (second half of ML-KG-01) is not implemented.

## Verification
- **DOIs.** All 10 DOIs in B01 and W01 were re-resolved after the last edit (each redirects, 302). Title, authors, journal, volume and pages were checked against Crossref.
- **Test vectors** were produced by independent scratch scripts (Python 3, numpy 2.5.2, scipy 1.18.0), not by the MLM Library; the scripts are not in the repo. W01 transposition values were cross-checked against pvlib 0.16.1 (they match exactly). The notes' own pseudocode was then run and reproduces every B01 and W01 vector, including the seeded W01 statistical check.

## Follow-ups (open)
- MLM Library: W01 transposition and the Kasten & Czeplak clear-sky factor (D-20's no-clamp and `G_reflected = 0` stay), D-23 (B01 power limiting, B01-1 (a)); emit the KG-CR-10 monitoring fields in ModelSpecs; re-pin to this branch once merged.
- W08: state its azimuth convention (W01 assumes 180° = south, as DS-PV).
- MLM SPECS2 §5.4 and ML-KG-04's status sentence: add B01 and W01 (now specified).
- W20: cloud factor aligned with W01-2 (Kasten & Czeplak) on branch `kg-w20-cloud`. Still open in W20: the output is named PV_avail_fraction (0–1) but the formula gives W/m²·η, and the input `clear_sky_estimate` is not a W01 output.

---

# KG sprint 1 — review checklist

Branch `kg-sprint-1`, 2026-10-23. MLM SPECS2 LO-10, SG-KG-1.

## Owner decisions, 2026-10-23 (applied)

| # | Decision | Applied in |
|---|---|---|
| 1 | C01: objective in EUR; the carbon price is a per-step input from the M-series carbon-price model ([[M09]] `P_ETS` ÷ 1000, via C03), not a constant | C01 `563749f` |
| 2 | C10: normalised RD_min approved; the fixed 5 %/step limit is replaced by ramp limits, min(asset ramp capability, product ramp for the signal type) × Δt | C10 `daa3275` |
| 3 | C08: within-type pooling approved | (as drafted) |
| 4 | O15: latest command wins; **library D-28 must change** | O15 `2894303` |
| 5 | D04: threshold calibrated per sensor to a 5 % false-alarm rate on clean reference data; 0.55 is the uncalibrated default | D04 `965e48e` |
| 6 | T01: exact discretisation and 19 °C occupied minimum; C01: terminal condition SoC_end ≥ SoC_start | T01 `2518934`, C01 `563749f`, `1d2dacd` |
| 7 | ISO 7730 quoted values marked "to verify against the standard" (owner to check a copy) | T10 `1f4f6cc` |
| — | All other notes and owner decisions approved as drafted (the recommended option where a choice was offered) | status `b3d84a8` |

**Status now:** C01, R01 and the 14 sprint notes are `status: specified` (`b3d84a8`). T01 was edited per decision 6 and confirmed by the owner on 2026-10-23, so it is also `status: specified`. Its one open point is SG-T01-1: the Validation Checks line "T_in[k+1] clamp applied before returning" does not say what to clamp to (library D-22 reads it as no clamp, with `comfort_ok` reported instead). T10 is specified with three values flagged "to verify against the standard".

**New gaps found while applying the decisions.**
- `00_shared/registers/signal_types.csv` has no ramp column. The per-product ramp exists only in FLEXUS's product catalogue (FX-FLX-01). Until a ramp field is added, C10 applies the asset ramp alone.
- The [[M09]] note outputs only the carbon cost `C_carbon`; it must expose its `P_ETS` forecast for C01 to consume.
- The terminal condition changed C01 TV-C01-5: Flex↑ is now limited by recharge, at 4512.5 W instead of 5000 W.

---

## Original review (KG sprint 1 drafts)

One commit per note (16), plus this file. Nothing pushed.

**How to use.** Each note has one section below: what changed, the **owner decisions** (proposals made because the choice is yours; each is also marked `OWNER DECISION` in the note) and the sources. Tick *Approve* or *Reject* per note, and per owner decision where you disagree. To reject a whole note: `git revert <commit>`.

**Status.** The 14 template notes are `status: draft-for-review`; none is marked `specified`. C01 and R01 are complete notes: only the listed fixes were made, their frontmatter is unchanged, and the changed passages are labelled *draft-for-review* in the text.

**Every note now has** the ML-KG-04 fields: purpose, formula (LaTeX and words, plus reference pseudocode), method, inputs with source and unit, outputs with consumers, parameters with defaults, dataset dependencies, RD role, validity limits, test vectors, gap closure, references.

**Verification done.**
- All 50 DOIs/URLs cited in the 16 notes were re-resolved after the last edit: every DOI redirects (302) and every URL loads (200). Each DOI's title, authors, volume and pages were also checked against Crossref.
- ISO standards (ISO 7730) are cited by designation only, because iso.org refuses automated requests and no URL could be checked. Values quoted from ISO 7730:2005 (comfort categories, 1.2 met, 1.0/0.5 clo) come from the 2005 edition and were not checked against a copy of the standard; pythermalcomfort states the 2025 edition uses the same PMV/PPD equations and limits.
- Test vectors were produced by independent scratch scripts (numpy 2.0.2, scipy 1.18.1, scikit-learn 1.9.1), not by the MLM Library. T10 was cross-checked against pythermalcomfort (largest difference 2×10⁻⁴ in PMV). Two hand-typed values were wrong and were fixed before their commits (D04 spike scale, D06 variogram). The scripts are not in the repo.
- The MLM Library is untouched. Its pinned copies of the notes (`tests/kg_notes/` @ `62fc534`) no longer match these drafts. Where a draft differs from a library decision, the section below says so.

---

## C01 — MILP Flexibility Engine (complete note; SG-C01-1..9) · `52da680`
☑ Approve ☐ Reject

**Changed.** Objective terms now in EUR, with the /1000 that was missing (SG-C01-8). `penalty_comfort` is an elastic comfort band with slack cost c_comfort (SG-C01-1); occ_state selects T_min (SG-C01-7). New input P_export_max, with P_grid,min = −P_export_max (SG-C01-2). P_base is an electrical input from L01; COP_base is removed (SG-C01-3). Q_HVAC = COP·P_HP, heating only (SG-C01-4). The b3 term takes Q_gain in W (solar plus internal gains), keeping C01's exact ZOH discretisation (SG-C01-5). P_EV is exogenous; P_shed = P_shift = 0 (SG-C01-6). Two-stage scheme: a dispatch MILP, then a flexibility LP per step with binaries fixed. Its duals are ∂Flex/∂x with schedule_held semantics, and the "Semantic Role" text claiming λ = ∂obj/∂x is corrected (SG-C01-9). Inputs I11–I17 added, plus 6 test vectors (TV-C01-4 reproduces the library's +38 000 W per unit SoC).

**Owner decisions.**
1. P_export_max = grid_max when G01 gives none (library D-10b).
2. COP source: (a) DS-HVAC constant, or (b) *recommended when DS-HVAC has no curve* the Ruhnau et al. (2019) air-source curve: 0.85·(6.08 − 0.0941ΔT + 0.000464ΔT²). Confirm heating only.
3. Shed and shift excluded until an L-series model defines them; the B05 V2G flag is unused for now.
4. Comfort band: 16 °C unoccupied (from O01), 19 °C occupied (O03), T_max 24 °C (T01). Note that T01's own default T_min is 18 °C.
5. Weights: **A (recommended; chosen, with the carbon price from M09 instead of a constant)** monetise every term, with c_comfort = 100 EUR/(K·h) and p_CO2 = 0.10 EUR/kg (a placeholder; set your reference carbon price). **B** is the literal note (library D-10g), which implies a carbon price of γ/α = 400 EUR/t. Import and export use the same price.

**Follow-ups (done 2026-10-23, decision 6).** T01 now uses the exact form; C01 has the terminal condition SoC_end ≥ SoC_start.

**Sources.** Nocedal & Wright 2006; Marler & Arora 2004, 2010; O'Neill et al. 2005; Boyd & Vandenberghe 2004; Bacher & Madsen 2011; Staffell et al. 2012; Ruhnau et al. 2019, plus the `when2heat` repository (coefficients read from the authors' code).

---

## R01 — PV Generation (complete note; KG-CR-06) · `3da6421`
☑ Approve ☐ Reject

**Changed.** Temperature factor changed to `1 + β(T_cell − 25)` with β negative, in both LaTeX lines and the pseudocode (SG-R01-1). Outputs and the night clamp now in W (SG-R01-2). Added a correction paragraph and 4 test vectors showing the old literal form too.
**Owner decisions.** None.
**Sources.** Skoplaki & Palyvos 2009 (the same law with a positive β_ref).

---

## T10 — Thermal Comfort PMV/PPD (SG-T10-1) · `ee316a5`
☑ Approve ☐ Reject

**Changed.** Full Fanger thermal load L, the clothing-temperature iteration and PPD, per the ISO 7730 Annex D algorithm. met and clo are now inputs. Added ISO validity limits and a `valid` output. Comfort-category labels corrected: ±0.5 is Category B and ±0.7 is Category C; the old note called them A and B. States that PPD cannot enter the C01 MILP directly. 14 test vectors.
**Owner decisions.** t_r = t_a when there is no MRT sensor; RH 50 %; v_ar 0.1 m/s; 1.2 met; clo 1.0 winter / 0.5 summer; target Category B (|PMV| ≤ 0.5); which ISO 7730 edition to cite.
**Sources.** Fanger 1970 (book); ISO 7730:2005 (by designation); Tartarini & Schiavon 2020 (pythermalcomfort); Tartarini et al. 2020 (CBE tool); ASHRAE 55 (URL).

---

## O01 — Occupancy HMM (SG-O01-1) · `1119cba`
☑ Approve ☐ Reject

**Changed.** Three-state HMM: hour-of-day transition matrices trained by Baum–Welch; Bernoulli emissions for PIR and Poisson for BLE/WiFi counts. Sensors fused by weighted geometric pooling with the note's 0.5/0.3/0.2 weights, renormalised when a sensor is missing. Forward filter and k-step forecast; outputs π, P_occ_fc and a confidence (max π). P_occ formula kept. 7 test vectors.
**Owner decisions.** Read the weights as geometric-pooling exponents. Read λ = 0.05 as mixing each transition row with a uniform row. Cold-start A, θ and μ values (b_WiFi = 2 static devices; "partial" = 50 % of the household). Retrain monthly. Switch to a negative-binomial emission if counts are overdispersed.
**Sources.** Rabiner 1989; Baum et al. 1970; Page et al. 2008; Genest & Zidek 1986.

---

## C03 — Determinization Bridge (SG-C03-1, -2) · `0cfa1a3`
☑ Approve ☐ Reject

**Changed.** Quantile mode (inverse CDF; Hyndman–Fan type-7 sample quantiles). Ensemble (robust) mode is the scenario approach: one C01 solve per member and the minimum flexibility, with a (1−ε)^S guarantee (0.5 % at S = 50) (SG-C03-1). Chance-constraint mode: Gaussian μ ± zσ, with an explicit individual/Bonferroni option (SG-C03-2). Occupancy rule added; `det_meta` provenance output. 9 test vectors.
**Owner decisions.** Conservative side = −sign(RD at p50), with the old PV/load table as fallback. Individual (default) vs joint chance constraints. Treat the home as unoccupied only if π₀ ≥ 0.9.
**Sources.** Gneiting & Katzfuss 2014; Hyndman & Fan 1996; Calafiore & Campi 2006; Campi & Garatti 2008; Charnes & Cooper 1959; Nemirovski & Shapiro 2006.

---

## C08 — RD Engine, synthesis rule (SG-C08-4) · `8d38ddf`
☑ Approve ☐ Reject

**Changed.** Four estimation routes defined. Synthesis: group estimates by stimulus, response, direction, scope, validity and semantic class; `schedule_held` and `total` are never pooled together. Within a group, DerSimonian–Laird random-effects inverse-variance pooling, with I² reported as route incoherence and Gaussian q10/q90. Units fixed: W per stimulus unit, with the rd_stimuli.csv conversions (λ was wrongly given as €/W) (SG-C08-5). Clip/invalidate rule against deterministic bounds (SG-C08-7). 8 test vectors.
**Owner decisions.** Observed routes classed with `total`. Random effects rather than fixed effects. Clip tolerance k_SE = 2.
**Still open.** SG-C08-3 (no established confidence formula; library D-09 stands); SG-C08-6 (the RD Map's 11 fields are not in the bundle); SG-C08-8 (a register question).
**Sources.** Boyd & Vandenberghe 2004; O'Neill et al. 2005; Bates & Granger 1969; DerSimonian & Laird 1986; Cochran 1954; Higgins & Thompson 2002.

---

## C10 — Governance, RD_min unit (LO-08, SG-C10-1..3) · `44d1123`
☑ Approve ☐ Reject

**Changed.** RD_min = 0.1 now applies to the σ-normalised RD = RD·σ_x/P_ref, which is dimensionless and the same in any stimulus units (TV-C10-7 shows the old rule flipping with units). A failed safety check, RD ≤ 0, or an invalidated RD all block (SG-C10-3). The rate limit is based on rated power (SG-C10-2). Most-restrictive reconciliation with FLEXUS G-1..G-7 writes operating_mode and requires_approval (ML-RD-02). 11 test vectors.
**Owner decisions.** Normalised reading of RD_min, rather than a per-stimulus threshold table. σ over the trailing 30 days. Placeholder σ values when there are fewer than 7 days of history. **Rate limit:** replaced by ramp limits (decision 2).
**Sources.** Saltelli et al. 2008; Hamby 1994; FLEXUS SPECS2 §12.1.

---

## P07 — RD KPI (SG-P07-1, -2; KG-CR-04) · `29ba725`
☑ Approve ☐ Reject

**Changed.** Formula kept. Added RD_total and the capacity-normalised RD. All inputs must share one stimulus, unit, response, direction and semantics (SG-P07-2). Standard error = √(wᵀΣw), with ρ = 1 by default, so assets are not treated as independent. Coherence is reported, not penalised. Takes the LO-09 position: P07 is the reporting KPI and FX-RD-02 is operational (SG-P07-1). RD-1..RD-10 table with the library's D-15 estimators. 7 test vectors.
**Owner decisions.** ρ = 1 default; headline KPI (normalised for comparing communities, the portfolio RD over time for one community); RD-5 threshold f = 0.9 (t90); confirm LO-09.
**Still open.** SG-KPI-7 (tolerance), SG-KPI-10 (normalisation).
**Sources.** JCGM 100:2008 (GUM).

---

## B05 — V2G Availability (SG-B05-1) · `a6cb54d`
☑ Approve ☐ Reject

**Changed.** Permission = contract ∧ bidirectional charger ∧ grid permit. Departure-aware SoC floor and available V2G power, after Kempton & Tomić. Availability probability and confidence passed through from B03's presence forecast, so no confidence is invented (SG-LAYER-1). 7 test vectors.
**Owner decisions.** η_ch = η_dis = 0.90; t_disp = 1 h; departure SoC 0.80 when the driver gives none; grid_permit = true when there is no DSO signal.
**Sources.** Kempton & Tomić 2005.

---

## W02 — Ambient Temperature (SG-W02-1) · `41ce88b`
☑ Approve ☐ Reject

**Changed.** EMOS/MOS Gaussian forecast. Mean = a + b1·T_NWP + b2·T_hist, defaulting to the note's 0.7/0.3. Variance from the NWP spread, otherwise 0.8 °C (lead ≤ 1 h) or 1.5 °C. Minimum-CRPS training over 30 days. Deterministic quantile ensemble and a calibration-based confidence. ARIMA dropped. W17 bias correction is not applied on top of a trained W02. 7 test vectors.
**Owner decisions.** T_hist = same-hour mean of the last 14 days of local observations (not the last observation); the 1 h nowcast boundary; the 30-day training window; the confidence rule; dropping ARIMA.
**Sources.** Gneiting et al. 2005; Glahn & Lowry 1972; Gneiting & Katzfuss 2014.

---

## W14 — Weather Ensemble (SG-W14-1) · `7ba57b1`
☑ Approve ☐ Reject

**Changed.** Gaussian copula with separable correlation: exp(−lag/ℓ) in time, combined with the correlation implied by Σ across variables. Per-step transform onto the W01/W02/W03 marginals. Output N×T×d with declared units; explicit PCG64 seed. 7 test vectors (seed-independent, plus one statistical check).
**Owner decisions.** ℓ fitted from forecast errors (6 h placeholder until fitted); separable structure; G_POA marginal (piecewise-linear with Gaussian tails, clipped ≥ 0).
**Sources.** Pinson et al. 2009; Schefzik et al. 2013.

---

## O03 — Comfort Feedback (SG-O03-1) · `e5a4626`
☑ Approve ☐ Reject

**Changed.** Two-sided |PMV| > 1.0. override_type ∈ {manual, t_min, pmv_warm, pmv_cool, none}, with a priority order. Triggers apply only when occupied. Adds an override probability (untrained: PPD/100 as an upper bound; trained: logistic) and its probability over an activation, which C08 applies as a human-factor RD discount (SG-LAYER-1). Inputs table corrected from 18 °C to 19 °C. 7 test vectors.
**Owner decisions.** T_min,occ = 19 °C; the occupancy gate; the priority order; PPD/100 as the placeholder; logistic training (≥ 30 events, monthly).
**Sources.** Haldi & Robinson 2009; ISO 7730 / Fanger.

---

## O15 — Override & HMI merge (SG-O15-1) · `fe40854`
☑ Approve ☐ Reject

**Changed.** ⊕ = priority arbitration, safety > occupant > automatic (after BACnet command prioritisation). The latest occupant command replaces earlier ones. Signals typed: setpoint °C (passed to C01 as a tracking band), power W, mode. Commands clipped to asset limits. Override rate and duration statistics added (SG-LAYER-1). 8 test vectors.
**Owner decisions.** The occupant outranks RD governance but not safety. "Latest replaces" semantics, which differs from library D-28 when a later override ends first (TV-O15-4); approved, and D-28 must change. Tracking band ±0.5 °C. 30-day statistics window.
**Sources.** ANSI/ASHRAE 135-2020 (URL); FLEXUS FX-GOV-05.

---

## D04 — Sensor Fault Detection (SG-D04-1) · `0a32145`
☑ Approve ☐ Reject

**Changed.** Features: robust-scaled value, step change, 4 h s.d. and neighbour residual. Isolation Forest (100 trees, ψ = 256) scored on the Liu et al. scale, threshold s > 0.55; the "5 % contamination" wording is removed. fault_type ∈ {none, missing, stuck, spike, drift, other}, assigned by rules. Per-sensor quality score; 30-day training requirement. 9 test vectors.
**Owner decisions.** Threshold: decided per decision 5 (calibrated per sensor to 5 % false alarms; 0.55 as default). In TV-D04-5, 0.55 flags 7.2 % of clean data. Window W = 16 steps; the spike, drift and stuck thresholds; dropping the LSTM autoencoder.
**Sources.** Liu et al. 2008, 2012; Ni et al. 2009; Sharma et al. 2010; scikit-learn documentation.

---

## D06 — Imputation (SG-D06-1) · `c862e1e`
☑ Approve ☐ Reject

**Changed.** Normalised 1/d weights (unnormalised weights gave 284.9 instead of 20.3 °C in TV-D06-2). Distances are made commensurate: temporal = variogram RMSE at the lag; spatial = offset-corrected RMSE. Selects 3 temporal + 2 spatial neighbours by distance. Online and offline modes; uncertainty output; archetype fallback beyond 4 h. EM and forward-fill dropped. 7 test vectors.
**Owner decisions.** Weight power p = 1 (as the note) or p = 2; the distance definition; the 30-day and 7-day windows; online mode as default; dropping EM and forward-fill.
**Sources.** Shepard 1968; Troyanskaya et al. 2001; Cressie 1993.

---

## D08 — Drift Detection (SG-D08-1) · `401df9c`
☑ Approve ☐ Reject

**Changed.** Two-sided tabular CUSUM on daily mean errors, standardised against the 30-day reference period after each retraining. h = 5, k = 0.5, reset after an alarm. Siegmund run lengths: about 469 samples between false alarms, about 10 samples to detect a 1σ shift. Quality output. Model count in Validation Checks corrected from 139 to 149 (ML-KG-01). 6 test vectors.
**Owner decisions.** k = 0.5; reading the "30-day window" as the reference period; daily aggregation; ADWIN not specified.
**Sources.** Page 1954; Hawkins & Olwell 1998; Siegmund 1985; Bifet & Gavaldà 2007.

---

## Cross-cutting follow-ups (open)
- MLM Library: re-pin `tests/kg_notes/` to the merged notes, and change the decisions the KG now overrides: D-10g (C01 weights in EUR, carbon price from M09), D-16b (C10 ramp limits), D-22/T01 (exact discretisation, 19 °C), D-28 (O15 latest command wins), D-32 (D08 k = 0.5 default), D-33 (D04 calibrated threshold), D-35 (O01 now specified). Add the C01 terminal condition.
- Add a ramp field to `signal_types.csv`, or point C10 at the FLEXUS product catalogue.
- Make the M09 note output its `P_ETS` forecast.
- ISO 7730 values in T10: owner to verify against the standard.
- T01 SG-T01-1: define or remove the "clamp applied before returning" check (library D-22: no clamp, report `comfort_ok`).
- SG-DOP-1 (monitored quantities, regimes and demotion thresholds per model) is not covered by ML-KG-04 and was not attempted.
