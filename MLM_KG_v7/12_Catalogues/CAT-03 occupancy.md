---
type: catalogue
ontology: EIG-MLM
catalogue_no: 3
catalogue_id: EIG-CAT-003
entity: occupancy
band: physical
status: active
tags: [eig-mlm, catalogue, band:physical, entity:occupancy]
aliases: ["EIG-CAT-003", "Catalogue 03", "occupancy"]
---

# CAT-03 — Occupancy

> **EIG-CAT-003** · band: **Physical & Spatial Stack** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Occupancy Catalogue

## 1. Definition

The Occupancy Catalogue defines every occupancy-related concept in the EIG MLM framework: occupancy types, schedules, arrival/departure/presence patterns, density, and behaviour. Occupancy is the dominant stochastic driver of building energy demand and the primary human-aware dimension of Response Derivatives; this catalogue makes it a first-class, fully specified modelling entity rather than an assumption.

## 2. Scope

Applies to: load forecasting, thermal simulation, comfort modelling, flexibility availability estimation, RD context conditioning, schedule optimisation, anomaly detection (occupancy-inconsistent consumption), synthetic occupancy generation, GDPR-compliant occupancy sensing governance.

Out of scope: schedules of assets/markets (24), behavioural archetypes as clustering outputs (07 — seeded from here), individual person data (never stored; profiles are aggregate by design).

## 3. Classification Structure

```text
Occupancy Profile Family (per sector)        EIG-OCC-FAM-xx
 └─ Occupancy Profile                        EIG-OCC-[0-9]{5}
     ├─ Schedule Component                   EIG-OCC-SCH-xxxxx
     ├─ Pattern Component (arrival/departure/presence)  EIG-OCC-PAT-xxxxx
     ├─ Density Component                    EIG-OCC-DEN-xxxxx
     └─ Behaviour Component                  EIG-OCC-BEH-xxxxx
```

Profile families (complete set): residential_family, residential_single, residential_retired, residential_shift_worker, residential_student, communal_residential, office_standard, office_flexible_hybrid, office_24_7, retail_standard, retail_extended, hospitality_hotel, hospitality_f_and_b, education_school, education_university, healthcare_acute, healthcare_clinic, industrial_single_shift, industrial_two_shift, industrial_continuous, warehouse, transport_terminal, cultural_event_led, religious_service_led, agricultural, data_centre_unmanned, public_office, sports_leisure, seasonal_holiday.

## 4. Hierarchy

A building (02) references exactly one occupancy profile; a space (04) may override with a space-level profile. Profiles compose: Schedule × Patterns × Density × Behaviour. Archetype clustering (07) consumes profiles; synthetic generation (42) samples from them.

## 5. Field Groups & Fields

### 5.1 Occupancy Profile

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | occupancyProfileID | string | — | `EIG-OCC-[0-9]{5}` | Y | Unique ID |
| 2 | profileName | string | — | ≤120 chars | Y | Name |
| 3 | profileFamily | enum | — | §3 family list | Y | Family |
| 4 | applicableClassificationIDs | array[string] | — | EIG-BC nodes | Y | Where valid |
| 5 | occupancyType | enum | — | residential, workplace, visitor_led, patient_led, student_led, process_led, mixed | Y | Dominant occupant type |
| 6 | provenance | enum | — | standard_assumption, survey_derived, sensor_derived, hybrid, synthetic | Y | Data basis |
| 7 | granularity | enum | — | hourly, 30min, 15min | Y | Time resolution of fractions |
| 8 | scheduleComponentID | string | — | EIG-OCC-SCH ID | Y | Linked schedule |
| 9 | arrivalPatternID / departurePatternID / presencePatternID | string | — | EIG-OCC-PAT IDs | Y | Linked patterns |
| 10 | densityComponentID | string | — | EIG-OCC-DEN ID | Y | Linked density |
| 11 | behaviourComponentID | string | — | EIG-OCC-BEH ID | Y | Linked behaviour |
| 12 | seasonalVariants | array[object] | — | {season: enum(winter,spring,summer,autumn,term,holiday,custom), overrideScheduleID} | N | Seasonal overrides |
| 13 | specialDayCalendarID | string | — | EIG-SCH calendar ID | N | Public holidays, events |
| 14 | uncertaintyClass | enum | — | deterministic, low_variance, medium_variance, high_variance, stochastic_event | Y | Predictability class |
| 15 | gdprClass | enum | — | aggregate_only, occupancy_inferable, individual_inferable_prohibited | Y | Always aggregate; flag governs sensing |
| 16 | status / version / owner / createdDate / modifiedDate / governanceStatus | — | — | as standard | Y | Lifecycle block |

### 5.2 Schedule Component

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | scheduleComponentID | string | — | `EIG-OCC-SCH-[0-9]{5}` | Y | ID |
| 2 | dayTypeProfiles | array[object] | fraction | {dayType: enum(mon,tue,wed,thu,fri,sat,sun,holiday,special), fractions: array[decimal 0…1] of length 24/48/96} | Y | Occupied-fraction series per day type |
| 3 | nominalStartTime / nominalEndTime | time | — | HH:MM local | Y | Core occupied period |
| 4 | annualOccupiedHours | decimal | h/yr | 0…8784 | Y | Derived total |
| 5 | shiftStructure | enum | — | none, single, double, triple, continuous_rotating | Y | Shift pattern |
| 6 | timezone | string | — | IANA TZ | Y | e.g. Europe/Dublin |

### 5.3 Pattern Components (arrival / departure / presence)

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | patternID | string | — | `EIG-OCC-PAT-[0-9]{5}` | Y | ID |
| 2 | patternType | enum | — | arrival, departure, presence, intermittency | Y | Pattern kind |
| 3 | distributionForm | enum | — | normal, lognormal, beta, empirical_histogram, point_mass | Y | Statistical form |
| 4 | parameterSet | object | — | {mu, sigma} or {alpha, beta} or {bins[], probs[]} per form | Y | Distribution parameters |
| 5 | peakTime | time | — | HH:MM | N | Mode of distribution |
| 6 | spreadMinutes | decimal | min | ≥0 | N | Dispersion summary |
| 7 | weekendDifferential | boolean | — | true/false | Y | Distinct weekend pattern flag |
| 8 | intermittencyRate | decimal | events/h | ≥0 | Cond | For presence/intermittency: short-absence rate |
| 9 | meanAbsenceDuration | decimal | min | ≥0 | Cond | With intermittencyRate |

### 5.4 Density Component

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | densityComponentID | string | — | `EIG-OCC-DEN-[0-9]{5}` | Y | ID |
| 2 | designDensity | decimal | m²/person | >0 | Y | Design value |
| 3 | typicalPeakDensity | decimal | m²/person | ≥ design basis check | Y | Observed/assumed peak |
| 4 | averageOccupiedDensity | decimal | m²/person | ≥0 | Y | Mean while occupied |
| 5 | maxHeadcount | integer | persons | ≥0 | Y | Capacity ceiling |
| 6 | diversityFactor | decimal | — | 0…1 | Y | Simultaneity of presence |
| 7 | metabolicRateAssumption | decimal | met | 0.7…4.0 | Y | For thermal/comfort models |
| 8 | sensibleGainPerPerson / latentGainPerPerson | decimal | W | ≥0 | Y | Internal gains basis |

### 5.5 Behaviour Component

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | behaviourComponentID | string | — | `EIG-OCC-BEH-[0-9]{5}` | Y | ID |
| 2 | setpointAdjustmentPropensity | enum | — | never, rare, occasional, frequent | Y | Manual override tendency |
| 3 | windowOpeningModel | enum | — | none, temperature_driven, CO2_driven, habit_driven, mixed | Y | Window behaviour |
| 4 | applianceUseIntensity | enum | — | very_low, low, medium, high, very_high | Y | Plug-load behaviour |
| 5 | dhwUseProfile | enum | — | minimal, standard_residential, high_residential, commercial_kitchen, healthcare, sports | Y | Hot water pattern |
| 6 | flexibilityEngagementClass | enum | — | opt_out, passive, responsive_automated, responsive_engaged, gamified | Y | DR participation behaviour |
| 7 | overrideRate | decimal | overrides/event | 0…1 | N | Observed DR override probability |
| 8 | comfortToleranceBand | decimal | °C | 0…5 | Y | Tolerated deviation before complaint/override |
| 9 | reboundPropensity | enum | — | none, low, medium, high | Y | Post-event rebound behaviour |
| 10 | occupantFeedbackChannel | enum | — | none, app, bms_panel, survey_periodic | N | Feedback instrumentation |

## 6. Controlled Vocabularies

All enums above plus dayType, season, distributionForm sets are governed vocabularies (82). Profile families (§3) are closed; extension by governance only. Sectors covered explicitly: residential, commercial, industrial, healthcare, education, public — every family in §3 maps to ≥1 sector node in (01).

## 7. Applicability Rules

- Space-level profiles (04) override building-level only for spaces with distinct function (e.g. server room within office).
- sensor_derived provenance requires gdprClass review and a registered Data Source (37) with lawful basis recorded.
- flexibilityEngagementClass and overrideRate are mandatory before a building enrols in any occupant-affecting flexibility service (29).
- Stochastic-event profiles (uncertaintyClass=stochastic_event) require an event calendar (specialDayCalendarID).

## 8. Validation Rules

- All fraction series ∈ [0,1]; length matches granularity (24/48/96).
- annualOccupiedHours consistent with dayTypeProfiles within ±2%.
- Arrival peakTime < departure peakTime for same-day single-shift profiles.
- diversityFactor ∈ (0,1]; maxHeadcount ≥ typical peak headcount implied by density × area.
- comfortToleranceBand ≤ building comfort band half-width (02 §5.6.10) — else flag conflict.
- empirical_histogram bins/probs: probs sum to 1 ± 0.001.
- No field may identify an individual; individual_inferable_prohibited blocks ingestion of person-level traces.

## 9. Relationships to Other Catalogues

Upstream: Building Classification (01), Building (02), Space (04), Schedule (24 — calendars), Geographic (08 — holidays by jurisdiction). Downstream: Feature (41), Model (44 — forecasting/thermal/comfort), Synthetic Data (42), Archetype (07), RD (52 — human-aware dimension), Flexibility (30), Decision Intelligence (60), KPI (57 — comfort, occupancy-normalised metrics), Data Quality (40 — occupancy plausibility checks).

## 10. Benchmarking Requirements

Occupancy-normalised benchmarks: kWh/person/yr, kWh/occupied-hour, W/person peak. Cohorts keyed by profile family. Schedule-adherence benchmark: deviation between profile and sensed occupancy where instrumented.

## 11. Dataset Requirements

Per instrumented building: aggregate occupancy proxy series (people counter, CO₂, Wi-Fi association count — all aggregate), at profile granularity, registered in (38) with gdprClass. Non-instrumented buildings: profile is the dataset (standard_assumption provenance).

## 12. Feature Requirements

Canonical features (41): occupied-fraction series, occupancy-lag features, arrival/departure deltas, diversity factor, occupancy-weighted degree days, presence entropy (predictability), override rate, rebound propensity encoding.

## 13. Model Requirements

Consumed by: short-term load forecasting (exogenous regressor), thermal RC models (internal gains), comfort models (PMV inputs via metabolic rate), flexibility availability models (occupancy-gated capacity), anomaly detection (consumption vs occupancy), RL state spaces (occupancy state variable). Occupancy presence/absence must be an explicit model input wherever consumption is occupancy-correlated — never folded silently into time-of-day.

## 14. KPI Requirements

KPIs: comfort compliance % (occupied hours within band), kWh/person, occupancy prediction accuracy (MAE of occupied fraction), out-of-hours consumption ratio, override rate per DR event.

## 15. Response Derivative Requirements

Every occupant-affecting RD must carry: occupancy state at estimation, flexibilityEngagementClass, overrideRate, comfortToleranceBand, reboundPropensity. RD validity is conditional on occupancy context — an RD estimated for occupied state must not be applied unoccupied without revalidation.

## 16. Decision Requirements

Decision gating: occupant-affecting actions blocked when predicted occupied fraction > threshold unless service contract permits; comfort constraints activate with occupancy; rebound mitigation actions scheduled per reboundPropensity.

## 17. Ontology Mapping

Classes: OccupancyProfile, OccupancySchedule, ArrivalPattern, DeparturePattern, PresencePattern, OccupancyDensity, OccupantBehaviour, DayTypeProfile.
Object properties: hasSchedule, hasArrivalPattern, hasDeparturePattern, hasPresencePattern, hasDensity, hasBehaviour, appliesTo (Building/Space), overriddenBy, seededFrom (sensing source).
Datatype properties: occupancyProfileID, profileFamily, granularity, annualOccupiedHours, diversityFactor, designDensity, overrideRate, comfortToleranceBand, gdprClass.
External alignment: SAREF occupancy extensions, SSN/SOSA for sensed occupancy observations, schema.org OpeningHoursSpecification (via 81).

## 18. Knowledge Graph Mapping

Nodes: OccupancyProfile, ScheduleComponent, Pattern, DensityComponent, BehaviourComponent, Building, Space.
Relationships: HAS_COMPONENT, APPLIES_TO, OVERRIDES, DERIVED_FROM {sourceID, method}, CONDITIONS_RD {rdID}, GATES_ACTION {actionID, threshold}.

## 19. Digital Twin Mapping

Operational+ twins bind a live occupancy estimate channel where instrumented; otherwise the profile drives the twin's occupancy state. Predictive twins run the pattern components as stochastic generators; autonomous twins update behaviour parameters (overrideRate, comfortToleranceBand) from observed events — updates are versioned.

## 20. Governance

Roles: Catalogue Owner, Occupancy Domain Expert, GDPR/DPO (mandatory reviewer for any sensor_derived or hybrid provenance), Validation Reviewer, Version Approver. Behaviour parameter learning from live data requires DPO-approved aggregation method on record.

## 21. Versioning

Profile semver; behaviour components version independently (they drift fastest). Historical model runs pin the profile version used. Deprecated profiles retained ≥24 months.

## 22. Example Records

```text
OccupancyProfileID: EIG-OCC-00021 | Name: Hybrid Office (3-day pattern)
Family: office_flexible_hybrid | Type: workplace | Provenance: hybrid | Granularity: 30min
Schedule: EIG-OCC-SCH-00021 (Tue–Thu peak 0.65, Mon/Fri 0.30, w/e 0.02; core 08:30–18:00; 2,540 h/yr)
Arrival: normal(μ=08:55, σ=35min) | Departure: normal(μ=17:20, σ=50min) | Presence intermittency: 0.4/h, mean 22min
Density: design 10 m²/p, avg occupied 16 m²/p, diversity 0.72, 1.2 met, 75 W sens / 55 W lat
Behaviour: overrides occasional, plug high, flex responsive_automated, overrideRate 0.08, tolerance 1.5 °C, rebound medium
Uncertainty: medium_variance | GDPR: aggregate_only | Version 2.1.0 | approved
```

```text
OccupancyProfileID: EIG-OCC-00104 | Name: Primary School (Irish calendar)
Family: education_school | Provenance: standard_assumption | Granularity: hourly
Schedule: Mon–Fri 0.9 (08:30–15:10), term calendar EIG-SCH-CAL-IE-EDU-01; holidays 0.02
Seasonal variants: {holiday: EIG-OCC-SCH-00105} | Uncertainty: deterministic
Density: 2.2 m²/p classrooms | maxHeadcount 460 | diversity 0.95 | 1.4 met
Behaviour: windows temperature_driven, flex passive, tolerance 1.0 °C, rebound low
```

```text
OccupancyProfileID: EIG-OCC-00188 | Name: Continuous Acute Ward
Family: healthcare_acute | Type: patient_led | Granularity: hourly
Schedule: 24/7 occupied fraction 0.85–1.0 | Shift: triple | Uncertainty: low_variance
Behaviour: overrides never (clinical control), flex opt_out, tolerance 0.5 °C
Decision note: occupant-affecting actions NOT permitted (gated via 60)
```

## 23. Completion Criteria

Complete when all profile families enumerated with at least one validated profile each; all five component types fully fielded with types/units/allowed values; GDPR classes enforced in validation; pattern distributions parameterised (not described); relationships, ontology, KG, twin, governance and versioning sections populated; ≥3 examples spanning ≥3 sectors.
