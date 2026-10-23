# KG sprint 1 — review checklist

Branch `kg-sprint-1`, 2026-10-23. MLM SPECS2 LO-10, SG-KG-1. One commit per note (16), plus this file. Nothing pushed.

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
☐ Approve ☐ Reject

**Changed.** Objective terms now in EUR, with the /1000 that was missing (SG-C01-8). `penalty_comfort` is an elastic comfort band with slack cost c_comfort (SG-C01-1); occ_state selects T_min (SG-C01-7). New input P_export_max, with P_grid,min = −P_export_max (SG-C01-2). P_base is an electrical input from L01; COP_base is removed (SG-C01-3). Q_HVAC = COP·P_HP, heating only (SG-C01-4). The b3 term takes Q_gain in W (solar plus internal gains), keeping C01's exact ZOH discretisation (SG-C01-5). P_EV is exogenous; P_shed = P_shift = 0 (SG-C01-6). Two-stage scheme: a dispatch MILP, then a flexibility LP per step with binaries fixed. Its duals are ∂Flex/∂x with schedule_held semantics, and the "Semantic Role" text claiming λ = ∂obj/∂x is corrected (SG-C01-9). Inputs I11–I17 added, plus 6 test vectors (TV-C01-4 reproduces the library's +38 000 W per unit SoC).

**Owner decisions.**
1. P_export_max = grid_max when G01 gives none (library D-10b).
2. COP source: (a) DS-HVAC constant, or (b) *recommended when DS-HVAC has no curve* the Ruhnau et al. (2019) air-source curve: 0.85·(6.08 − 0.0941ΔT + 0.000464ΔT²). Confirm heating only.
3. Shed and shift excluded until an L-series model defines them; the B05 V2G flag is unused for now.
4. Comfort band: 16 °C unoccupied (from O01), 19 °C occupied (O03), T_max 24 °C (T01). Note that T01's own default T_min is 18 °C.
5. Weights: **A (recommended)** monetise every term, with c_comfort = 100 EUR/(K·h) and p_CO2 = 0.10 EUR/kg (a placeholder; set your reference carbon price). **B** is the literal note (library D-10g), which implies a carbon price of γ/α = 400 EUR/t. Import and export use the same price.

**Follow-ups, not changed (outside the listed fixes).** T01's Euler step differs from C01's exact form by O((Δt/τ)²); either state that limit in T01 or adopt the exact form. No terminal-SoC constraint exists, so the MILP may empty the battery at the end of the horizon.

**Sources.** Nocedal & Wright 2006; Marler & Arora 2004, 2010; O'Neill et al. 2005; Boyd & Vandenberghe 2004; Bacher & Madsen 2011; Staffell et al. 2012; Ruhnau et al. 2019, plus the `when2heat` repository (coefficients read from the authors' code).

---

## R01 — PV Generation (complete note; KG-CR-06) · `3da6421`
☐ Approve ☐ Reject

**Changed.** Temperature factor changed to `1 + β(T_cell − 25)` with β negative, in both LaTeX lines and the pseudocode (SG-R01-1). Outputs and the night clamp now in W (SG-R01-2). Added a correction paragraph and 4 test vectors showing the old literal form too.
**Owner decisions.** None.
**Sources.** Skoplaki & Palyvos 2009 (the same law with a positive β_ref).

---

## T10 — Thermal Comfort PMV/PPD (SG-T10-1) · `ee316a5`
☐ Approve ☐ Reject

**Changed.** Full Fanger thermal load L, the clothing-temperature iteration and PPD, per the ISO 7730 Annex D algorithm. met and clo are now inputs. Added ISO validity limits and a `valid` output. Comfort-category labels corrected: ±0.5 is Category B and ±0.7 is Category C; the old note called them A and B. States that PPD cannot enter the C01 MILP directly. 14 test vectors.
**Owner decisions.** t_r = t_a when there is no MRT sensor; RH 50 %; v_ar 0.1 m/s; 1.2 met; clo 1.0 winter / 0.5 summer; target Category B (|PMV| ≤ 0.5); which ISO 7730 edition to cite.
**Sources.** Fanger 1970 (book); ISO 7730:2005 (by designation); Tartarini & Schiavon 2020 (pythermalcomfort); Tartarini et al. 2020 (CBE tool); ASHRAE 55 (URL).

---

## O01 — Occupancy HMM (SG-O01-1) · `1119cba`
☐ Approve ☐ Reject

**Changed.** Three-state HMM: hour-of-day transition matrices trained by Baum–Welch; Bernoulli emissions for PIR and Poisson for BLE/WiFi counts. Sensors fused by weighted geometric pooling with the note's 0.5/0.3/0.2 weights, renormalised when a sensor is missing. Forward filter and k-step forecast; outputs π, P_occ_fc and a confidence (max π). P_occ formula kept. 7 test vectors.
**Owner decisions.** Read the weights as geometric-pooling exponents. Read λ = 0.05 as mixing each transition row with a uniform row. Cold-start A, θ and μ values (b_WiFi = 2 static devices; "partial" = 50 % of the household). Retrain monthly. Switch to a negative-binomial emission if counts are overdispersed.
**Sources.** Rabiner 1989; Baum et al. 1970; Page et al. 2008; Genest & Zidek 1986.

---

## C03 — Determinization Bridge (SG-C03-1, -2) · `0cfa1a3`
☐ Approve ☐ Reject

**Changed.** Quantile mode (inverse CDF; Hyndman–Fan type-7 sample quantiles). Ensemble (robust) mode is the scenario approach: one C01 solve per member and the minimum flexibility, with a (1−ε)^S guarantee (0.5 % at S = 50) (SG-C03-1). Chance-constraint mode: Gaussian μ ± zσ, with an explicit individual/Bonferroni option (SG-C03-2). Occupancy rule added; `det_meta` provenance output. 9 test vectors.
**Owner decisions.** Conservative side = −sign(RD at p50), with the old PV/load table as fallback. Individual (default) vs joint chance constraints. Treat the home as unoccupied only if π₀ ≥ 0.9.
**Sources.** Gneiting & Katzfuss 2014; Hyndman & Fan 1996; Calafiore & Campi 2006; Campi & Garatti 2008; Charnes & Cooper 1959; Nemirovski & Shapiro 2006.

---

## C08 — RD Engine, synthesis rule (SG-C08-4) · `8d38ddf`
☐ Approve ☐ Reject

**Changed.** Four estimation routes defined. Synthesis: group estimates by stimulus, response, direction, scope, validity and semantic class; `schedule_held` and `total` are never pooled together. Within a group, DerSimonian–Laird random-effects inverse-variance pooling, with I² reported as route incoherence and Gaussian q10/q90. Units fixed: W per stimulus unit, with the rd_stimuli.csv conversions (λ was wrongly given as €/W) (SG-C08-5). Clip/invalidate rule against deterministic bounds (SG-C08-7). 8 test vectors.
**Owner decisions.** Observed routes classed with `total`. Random effects rather than fixed effects. Clip tolerance k_SE = 2.
**Still open.** SG-C08-3 (no established confidence formula; library D-09 stands); SG-C08-6 (the RD Map's 11 fields are not in the bundle); SG-C08-8 (a register question).
**Sources.** Boyd & Vandenberghe 2004; O'Neill et al. 2005; Bates & Granger 1969; DerSimonian & Laird 1986; Cochran 1954; Higgins & Thompson 2002.

---

## C10 — Governance, RD_min unit (LO-08, SG-C10-1..3) · `44d1123`
☐ Approve ☐ Reject

**Changed.** RD_min = 0.1 now applies to the σ-normalised RD = RD·σ_x/P_ref, which is dimensionless and the same in any stimulus units (TV-C10-7 shows the old rule flipping with units). A failed safety check, RD ≤ 0, or an invalidated RD all block (SG-C10-3). The rate limit is based on rated power (SG-C10-2). Most-restrictive reconciliation with FLEXUS G-1..G-7 writes operating_mode and requires_approval (ML-RD-02). 11 test vectors.
**Owner decisions.** Normalised reading of RD_min, rather than a per-stimulus threshold table. σ over the trailing 30 days. Placeholder σ values when there are fewer than 7 days of history. **Rate limit:** 5 % of rated power per 15-min step takes 5 h to reach full power, which would stop most flexibility services; consider applying it per control-loop tick.
**Sources.** Saltelli et al. 2008; Hamby 1994; FLEXUS SPECS2 §12.1.

---

## P07 — RD KPI (SG-P07-1, -2; KG-CR-04) · `29ba725`
☐ Approve ☐ Reject

**Changed.** Formula kept. Added RD_total and the capacity-normalised RD. All inputs must share one stimulus, unit, response, direction and semantics (SG-P07-2). Standard error = √(wᵀΣw), with ρ = 1 by default, so assets are not treated as independent. Coherence is reported, not penalised. Takes the LO-09 position: P07 is the reporting KPI and FX-RD-02 is operational (SG-P07-1). RD-1..RD-10 table with the library's D-15 estimators. 7 test vectors.
**Owner decisions.** ρ = 1 default; headline KPI (normalised for comparing communities, the portfolio RD over time for one community); RD-5 threshold f = 0.9 (t90); confirm LO-09.
**Still open.** SG-KPI-7 (tolerance), SG-KPI-10 (normalisation).
**Sources.** JCGM 100:2008 (GUM).

---

## B05 — V2G Availability (SG-B05-1) · `a6cb54d`
☐ Approve ☐ Reject

**Changed.** Permission = contract ∧ bidirectional charger ∧ grid permit. Departure-aware SoC floor and available V2G power, after Kempton & Tomić. Availability probability and confidence passed through from B03's presence forecast, so no confidence is invented (SG-LAYER-1). 7 test vectors.
**Owner decisions.** η_ch = η_dis = 0.90; t_disp = 1 h; departure SoC 0.80 when the driver gives none; grid_permit = true when there is no DSO signal.
**Sources.** Kempton & Tomić 2005.

---

## W02 — Ambient Temperature (SG-W02-1) · `41ce88b`
☐ Approve ☐ Reject

**Changed.** EMOS/MOS Gaussian forecast. Mean = a + b1·T_NWP + b2·T_hist, defaulting to the note's 0.7/0.3. Variance from the NWP spread, otherwise 0.8 °C (lead ≤ 1 h) or 1.5 °C. Minimum-CRPS training over 30 days. Deterministic quantile ensemble and a calibration-based confidence. ARIMA dropped. W17 bias correction is not applied on top of a trained W02. 7 test vectors.
**Owner decisions.** T_hist = same-hour mean of the last 14 days of local observations (not the last observation); the 1 h nowcast boundary; the 30-day training window; the confidence rule; dropping ARIMA.
**Sources.** Gneiting et al. 2005; Glahn & Lowry 1972; Gneiting & Katzfuss 2014.

---

## W14 — Weather Ensemble (SG-W14-1) · `7ba57b1`
☐ Approve ☐ Reject

**Changed.** Gaussian copula with separable correlation: exp(−lag/ℓ) in time, combined with the correlation implied by Σ across variables. Per-step transform onto the W01/W02/W03 marginals. Output N×T×d with declared units; explicit PCG64 seed. 7 test vectors (seed-independent, plus one statistical check).
**Owner decisions.** ℓ fitted from forecast errors (6 h placeholder until fitted); separable structure; G_POA marginal (piecewise-linear with Gaussian tails, clipped ≥ 0).
**Sources.** Pinson et al. 2009; Schefzik et al. 2013.

---

## O03 — Comfort Feedback (SG-O03-1) · `e5a4626`
☐ Approve ☐ Reject

**Changed.** Two-sided |PMV| > 1.0. override_type ∈ {manual, t_min, pmv_warm, pmv_cool, none}, with a priority order. Triggers apply only when occupied. Adds an override probability (untrained: PPD/100 as an upper bound; trained: logistic) and its probability over an activation, which C08 applies as a human-factor RD discount (SG-LAYER-1). Inputs table corrected from 18 °C to 19 °C. 7 test vectors.
**Owner decisions.** T_min,occ = 19 °C; the occupancy gate; the priority order; PPD/100 as the placeholder; logistic training (≥ 30 events, monthly).
**Sources.** Haldi & Robinson 2009; ISO 7730 / Fanger.

---

## O15 — Override & HMI merge (SG-O15-1) · `fe40854`
☐ Approve ☐ Reject

**Changed.** ⊕ = priority arbitration, safety > occupant > automatic (after BACnet command prioritisation). The latest occupant command replaces earlier ones. Signals typed: setpoint °C (passed to C01 as a tracking band), power W, mode. Commands clipped to asset limits. Override rate and duration statistics added (SG-LAYER-1). 8 test vectors.
**Owner decisions.** The occupant outranks RD governance but not safety. "Latest replaces" semantics, which differs from library D-28 when a later override ends first (TV-O15-4). Tracking band ±0.5 °C. 30-day statistics window.
**Sources.** ANSI/ASHRAE 135-2020 (URL); FLEXUS FX-GOV-05.

---

## D04 — Sensor Fault Detection (SG-D04-1) · `0a32145`
☐ Approve ☐ Reject

**Changed.** Features: robust-scaled value, step change, 4 h s.d. and neighbour residual. Isolation Forest (100 trees, ψ = 256) scored on the Liu et al. scale, threshold s > 0.55; the "5 % contamination" wording is removed. fault_type ∈ {none, missing, stuck, spike, drift, other}, assigned by rules. Per-sensor quality score; 30-day training requirement. 9 test vectors.
**Owner decisions.** Keep 0.55, or use a contamination quantile instead: in TV-D04-5, 0.55 flags 7.2 % of clean data. Window W = 16 steps; the spike, drift and stuck thresholds; dropping the LSTM autoencoder.
**Sources.** Liu et al. 2008, 2012; Ni et al. 2009; Sharma et al. 2010; scikit-learn documentation.

---

## D06 — Imputation (SG-D06-1) · `c862e1e`
☐ Approve ☐ Reject

**Changed.** Normalised 1/d weights (unnormalised weights gave 284.9 instead of 20.3 °C in TV-D06-2). Distances are made commensurate: temporal = variogram RMSE at the lag; spatial = offset-corrected RMSE. Selects 3 temporal + 2 spatial neighbours by distance. Online and offline modes; uncertainty output; archetype fallback beyond 4 h. EM and forward-fill dropped. 7 test vectors.
**Owner decisions.** Weight power p = 1 (as the note) or p = 2; the distance definition; the 30-day and 7-day windows; online mode as default; dropping EM and forward-fill.
**Sources.** Shepard 1968; Troyanskaya et al. 2001; Cressie 1993.

---

## D08 — Drift Detection (SG-D08-1) · `401df9c`
☐ Approve ☐ Reject

**Changed.** Two-sided tabular CUSUM on daily mean errors, standardised against the 30-day reference period after each retraining. h = 5, k = 0.5, reset after an alarm. Siegmund run lengths: about 469 samples between false alarms, about 10 samples to detect a 1σ shift. Quality output. Model count in Validation Checks corrected from 139 to 149 (ML-KG-01). 6 test vectors.
**Owner decisions.** k = 0.5; reading the "30-day window" as the reference period; daily aggregation; ADWIN not specified.
**Sources.** Page 1954; Hawkins & Olwell 1998; Siegmund 1985; Bifet & Gavaldà 2007.

---

## Cross-cutting follow-ups (not done in this sprint)
- Re-pin the MLM Library's `tests/kg_notes/` to this branch once you approve, and revisit library decisions that now differ: D-10g (C01 weights, if option A is chosen), D-28 (O15 overlap rule), D-32 (D08 k now has a default), D-35 (O01 is now specified).
- T01: Euler step vs exact discretisation; default T_min 18 °C vs 19 °C.
- C01: terminal-SoC condition.
- SG-DOP-1 (monitored quantities, regimes and demotion thresholds per model) is not covered by ML-KG-04 and was not attempted.
