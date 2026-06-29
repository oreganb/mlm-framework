---
type: catalogue
ontology: EIG-MLM
catalogue_no: 8
catalogue_id: EIG-CAT-008
entity: geographic
band: physical
status: active
tags: [eig-mlm, catalogue, band:physical, entity:geographic]
aliases: ["EIG-CAT-008", "Catalogue 08", "geographic"]
---

# CAT-08 — Geographic

> **EIG-CAT-008** · band: **Physical & Spatial Stack** · part of the EIG MLM 94-catalogue specification suite.

**Depends on (upstream):** —
**Used by (downstream):** —
**Realises via models:** [[MLM Knowledge Graph v7]] · see model bridges below.

---

# EIG MLM Geographic Catalogue

Catalogue ID prefix: **EIG-GEO** · Catalogue number: 08 · Version 1.0 · Status: Active

---

## 1. Definition

The Geographic Catalogue defines every geographic entity referenced anywhere in the EIG MLM framework: countries, regions, counties, cities, districts, postal areas and climate zones. It is the single authority for spatial identity. Every building, site, asset, weather station, network segment, market zone, community, district and benchmark cohort resolves its location through a record in this catalogue. No other catalogue may define a geographic entity.

## 2. Scope

Applies to: spatial analytics, benchmarking cohort construction, climate-adjusted normalisation, synthetic building generation, weather-station-to-building assignment, market/network zone resolution, regulatory jurisdiction resolution, digital twin geolocation, and clustering. Primary territory: Republic of Ireland (full enumeration); secondary: EU Member States + UK (country-level, extensible to full hierarchy per project).

## 3. Classification Structure

Geographic entities classify on one axis — **geographic level** — with a strict containment order:

| Level code | Level | Definition | Irish instantiation |
|---|---|---|---|
| GL-01 | Country | Sovereign state, ISO 3166-1 | IE |
| GL-02 | Region | NUTS 2 statistical region | Northern & Western (IE04), Southern (IE05), Eastern & Midland (IE06) |
| GL-03 | County | Administrative county / LA area | 26 counties / 31 local authorities |
| GL-04 | City | CSO settlement ≥ 1,500 population or designated city | Dublin, Cork, Limerick, Galway, Waterford, … |
| GL-05 | District | Sub-city electoral/planning district or rural ED | Electoral Divisions (3,440 in IE) |
| GL-06 | Postal Area | Eircode Routing Key area | 139 routing keys (e.g. T12, D04, H91) |
| GL-07 | Climate Zone | Climatic classification area (cross-cutting; may span GL-02–GL-06) | EIG-GEO-CZ-* (see §6.3) |

GL-07 is an overlay level: it does not sit in the containment chain but maps onto it via `coversEntities`.

## 4. Hierarchy

```
Country (GL-01)
 └── Region (GL-02, NUTS 2)
      └── County (GL-03)
           ├── City (GL-04)
           │    └── District (GL-05, urban ED)
           └── District (GL-05, rural ED)
                └── Postal Area (GL-06, Eircode routing key)   [n:m with GL-05]
Climate Zone (GL-07) ──coversEntities──▶ any GL-02…GL-06
```

Containment rules: every record except GL-01 has exactly one `parentGeoID` at the next level up, except GL-06 which may map to multiple GL-05 districts (routing keys cross ED boundaries); the canonical parent of a GL-06 record is the GL-03 county containing the plurality of its area.

## 5. Field Groups & Fields

### 5.1 Identification

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 1 | geoID | string | — | `EIG-GEO-<ISO2>-<LEVEL>-<NNNN>` e.g. EIG-GEO-IE-CTY-0004 | M | Unique geographic entity ID |
| 2 | geoName | string | — | UTF-8, official English name | M | Official name |
| 3 | geoNameIrish | string | — | UTF-8 | C (IE records) | Official Irish-language name |
| 4 | geoLevel | enum | — | GL-01…GL-07 | M | Geographic level |
| 5 | parentGeoID | string | — | valid geoID | C (all except GL-01, GL-07) | Containing entity |
| 6 | externalCodes | object | — | keys: iso3166_1, iso3166_2, nuts, lau, cso_ed, eircode_rk, osm_relation | O | Cross-system identifier map |
| 7 | status | enum | — | active, merged, dissolved, proposed | M | Lifecycle status |

### 5.2 Geometry

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 8 | centroidLat | decimal(9,6) | deg | WGS84, −90…90 | M | Centroid latitude |
| 9 | centroidLon | decimal(9,6) | deg | WGS84, −180…180 | M | Centroid longitude |
| 10 | boundaryRef | string | — | URI/path to GeoJSON or WKT boundary | C (GL-02…GL-06) | Boundary geometry reference |
| 11 | boundaryCRS | enum | — | EPSG:4326, EPSG:2157 (ITM) | C with boundaryRef | CRS of boundary file |
| 12 | areaKm2 | decimal(12,4) | km² | > 0 | C (GL-01…GL-05) | Land area |
| 13 | elevationMeanM | decimal(7,2) | m | −430…8848 | O | Mean elevation above sea level |
| 14 | coastalFlag | boolean | — | true/false | M (GL-03…GL-06) | Touches coastline (drives exposure class) |

### 5.3 Demographic & contextual

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 15 | population | integer | persons | ≥ 0 | C (GL-01…GL-05) | Latest census population |
| 16 | populationCensusYear | integer | yr | 1991–2100 | C with population | Census vintage |
| 17 | dwellingCount | integer | count | ≥ 0 | O | Census dwelling count |
| 18 | urbanRuralClass | enum | — | city, satellite_urban, town, rural_high_urban_influence, rural, remote_rural, island | M (GL-04…GL-06) | CSO-aligned urban/rural class |
| 19 | gridDSORegion | string | — | EIG-NET zone ID | O | Distribution network region (forward ref → utility_network) |
| 20 | regulatoryJurisdiction | string | — | EIG-REG jurisdiction ID | M (GL-01) | Governing regulatory regime (forward ref → regulation) |

### 5.4 Climate linkage

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 21 | climateZoneID | string | — | EIG-GEO-CZ-* | M (GL-03…GL-06) | Assigned climate zone |
| 22 | primaryWeatherStationID | string | — | EIG-WX-ST-* | M (GL-03…GL-06) | Default weather station for entities in this area |
| 23 | degreeDayRegion | enum | — | DD-IE-N, DD-IE-E, DD-IE-S, DD-IE-W, DD-IE-MID | M (GL-03…GL-06) | Degree-day normalisation region |

### 5.5 Audit

| # | Field | Type | Unit | Allowed Values / Format | Req | Description |
|---|---|---|---|---|---|---|
| 24 | version | string | — | semver | M | Record version |
| 25 | owner | string | — | EIG-ROL role ID | M | Governance owner |
| 26 | createdDate / modifiedDate | date | — | ISO 8601 | M | Audit dates |
| 27 | source | enum | — | cso, osi_tailte, eurostat, osm, eircode, manual | M | Authoritative source |

Req codes: M mandatory · C conditional · O optional.

## 6. Controlled Vocabularies

### 6.1 Level vocabulary
GL-01…GL-07 as §3. Closed; extension requires major version.

### 6.2 ID level segment codes
CRY (country), RGN (region), CTY (county), CIT (city), DST (district/ED), PST (postal/routing key), CZ (climate zone).

### 6.3 Climate zone register (EIG-GEO-CZ-*) — authoritative; referenced by weather, building, archetype catalogues

| ID | Name | Basis | Definition |
|---|---|---|---|
| EIG-GEO-CZ-IE-ATL | Irish Atlantic Maritime | Köppen Cfb, high-wind coastal | Western seaboard; wind exposure class E3–E4; driving rain index ≥ 56.5 l/m² per spell |
| EIG-GEO-CZ-IE-INL | Irish Inland Temperate | Köppen Cfb inland | Midlands; higher diurnal range; frost days > 40/yr |
| EIG-GEO-CZ-IE-EST | Irish East Coast | Köppen Cfb, rain-shadow | Lower rainfall (< 800 mm/yr); Dublin/Wicklow/Wexford coastal strip |
| EIG-GEO-CZ-IE-STH | Irish South Coast | Köppen Cfb mild | Mildest winters; HDD15.5 lowest band |
| EIG-GEO-CZ-IE-ISL | Irish Offshore Island | Köppen Cfb extreme maritime | Aran Islands, Achill, etc.; exposure E4; constrained grid (ODEON context) |

Each CZ record additionally carries: hddBase15_5AnnualMean (K·d), cddBase18AnnualMean (K·d), designTempHeatingC (°C, e.g. −3 inland / −1 coastal), designTempCoolingC, meanWindSpeedMs, drivingRainIndex, solarAnnualKWhM2.

### 6.4 Degree-day regions
DD-IE-N, DD-IE-E, DD-IE-S, DD-IE-W, DD-IE-MID — each binds to one synoptic station set in the Weather & Climate Catalogue.

## 7. Applicability Rules

1. Every EIG-BLD building record MUST reference exactly one GL-06 postal area (Eircode routing key) and inherit county, region, climate zone and degree-day region transitively — buildings never store these redundantly.
2. Benchmark cohorts (EIG-BBM, EIG-ABM) MUST be constructed within a single climate zone unless the metric is climate-normalised (weather-corrected per §10).
3. Synthetic building generation (EIG-SYN) MUST sample locations only from active GL-05/GL-06 records and respect urbanRuralClass joint distributions.
4. GL-07 climate zones apply to all outdoor-climate-sensitive models; indoor-only models may omit geographic conditioning but MUST declare the omission.
5. Island entities (urbanRuralClass = island) trigger constrained-grid applicability flags downstream (network, flexibility, edge catalogues).

## 8. Validation Rules

| # | Rule |
|---|---|
| V1 | geoID unique across catalogue; pattern-valid; level segment matches geoLevel |
| V2 | parentGeoID must exist, be active, and be exactly one level above (GL-06 exception: parent GL-03 allowed) |
| V3 | Containment geometry: child centroid must fall inside parent boundary (tolerance 100 m) |
| V4 | Σ child areaKm2 ≤ parent areaKm2 × 1.02 |
| V5 | climateZoneID, primaryWeatherStationID, degreeDayRegion mandatory for GL-03…GL-06 and must resolve |
| V6 | population requires populationCensusYear; census year ≤ current year |
| V7 | merged/dissolved records must carry successor geoID in externalCodes.successor |
| V8 | No circular parent chains; max depth 6 |
| V9 | Climate zone records (GL-07) must not carry parentGeoID; must carry all §6.3 climatic attributes |
| V10 | coastalFlag = true required if boundary intersects coastline layer; auto-check on ingest |

## 9. Relationships to Other Catalogues

| Direction | Catalogue | Relationship |
|---|---|---|
| ◀ consumed by | building (02) | building.geoPostalAreaID → GL-06 |
| ◀ consumed by | weather_climate (09) | station.locatedIn → GL-03…GL-06; CZ register defined here, profiled there |
| ◀ consumed by | building_benchmark (06), asset_benchmark (12) | cohort.climateZoneID |
| ◀ consumed by | utility_network (13) | networkSegment.servesGeoIDs |
| ◀ consumed by | market (20), tariff (21) | marketZone.geoScope |
| ◀ consumed by | community (86), district (87), site (88) | spatial aggregates resolve boundaries here |
| ◀ consumed by | regulation (92), policy (91) | jurisdiction scoping |
| ▶ depends on | — | none (root catalogue; external sources only) |

## 10. Benchmarking Requirements

Geographic records enable: (a) climate normalisation — every weather-sensitive benchmark metric must be degree-day-corrected using the entity's degreeDayRegion before cohort comparison; (b) cohort stratification keys: climateZoneID, urbanRuralClass, county; (c) minimum cohort n = 10 within any geographic stratum (inherited from EIG-BBM rule); strata failing n roll up one level (GL-06→GL-05→GL-03→GL-02).

## 11. Dataset Requirements

Datasets referencing location MUST store geoID (not free-text place names). Required geographic columns in any building/asset dataset: geoPostalAreaID, derived climateZoneID, degreeDayRegion. Boundary files delivered as GeoJSON (EPSG:4326) in object storage under `EIG_GEO_ROOT/boundaries/<geoID>.geojson`. CSO/Tailte source vintages recorded in metadata catalogue (39).

## 12. Feature Requirements

Standard geographic features derivable for any located entity: latitude, longitude, climateZone one-hot, urbanRuralClass one-hot, coastalFlag, elevationMeanM, distanceToCoastKm, hdd/cdd annual means, designTempHeatingC, drivingRainIndex, populationDensity (population/areaKm2). Feature catalogue (41) must register these under prefix FEA-GEO-*.

## 13. Model Requirements

Models conditioned on location MUST consume geographic features via the feature catalogue, never raw place names. Spatial models (clustering, kriging, archetype assignment) MUST declare CRS (EPSG:2157 for distance computation in Ireland; EPSG:4326 for storage). Transfer of a model trained in one climate zone to another requires a validation record (47) with zone-shift testing.

## 14. KPI Requirements

KPIs aggregated geographically MUST declare aggregation level (GL-01…GL-06) and method (sum, mean, population-weighted mean, area-weighted mean). Climate-sensitive KPIs report both raw and degree-day-normalised variants.

## 15. Response Derivative Requirements

RDs aggregated across buildings MUST be stratified by climateZoneID (response to temperature signals differs by zone) and flag island/constrained-grid entities, whose RDs carry network-constraint context (EIG-CTX). Geographic coherence of aggregated response (EIG-RD aggregation coherence metric) is computed per GL-05 district.

## 16. Decision Requirements

Decision intelligence (60) uses geographic scoping to bound action applicability (e.g. DSO constraint actions apply per gridDSORegion). Any action targeting a geographic aggregate MUST enumerate the affected geoIDs at decision time and log them in the decision record.

## 17. Ontology Mapping

Classes: GeographicEntity ⊐ {Country, Region, County, City, District, PostalArea, ClimateZone}.
Object properties: hasParent (functional, irreflexive, asymmetric), coversEntities (ClimateZone→GeographicEntity), locatedIn (inverse: contains), hasPrimaryWeatherStation, hasClimateZone, inDegreeDayRegion.
Datatype properties: geoID, geoName, centroidLat, centroidLon, areaKm2, population, coastalFlag, urbanRuralClass.
Alignment: geo:SpatialThing (W3C), GeoSPARQL geo:Feature / geo:hasGeometry, schema:Place, SAREF4CITY s4city:CityObject. OWL 2 DL; boundary geometries via GeoSPARQL WKT literals.

## 18. Knowledge Graph Mapping

Nodes: GeographicEntity (label per level), ClimateZone, WeatherStation.
Relationships: PARENT_OF / CHILD_OF, COVERS (CZ→entity), LOCATED_IN (building/asset/station→entity), PRIMARY_STATION (entity→station), IN_DD_REGION.
Node keys: geoID. Indexes: geoLevel, climateZoneID, urbanRuralClass. Spatial index on centroid point property for radius queries.

## 19. Digital Twin Mapping

Every Building/Asset/Community/District twin carries a resolved geographic context block (geoID chain, climate zone, DD region, coordinates) injected at twin instantiation from this catalogue. Twin templates (33) MUST declare which geographic levels they bind. Geographic records themselves are not twinned but versioned reference data within the twin platform.

## 20. Governance

Owner: EIG Data Management Lead. Reviewers: Domain Expert (geospatial), Validation Reviewer. Source-sync cadence: CSO census per release; Eircode routing keys quarterly; boundary files on Tailte Éireann release. Changes to climate zone definitions are major-version events requiring re-validation of all climate-conditioned models (cascade notice to model registry).

## 21. Versioning

Semver per record + catalogue version. Major: level scheme or CZ definition change. Minor: new entities, boundary updates. Patch: name/metadata corrections. Merged/dissolved entities are never deleted; status transition + successor pointer preserves historical joins.

## 22. Example Records

```text
geoID: EIG-GEO-IE-CRY-0001 | geoName: Ireland | geoLevel: GL-01 | externalCodes: {iso3166_1: IE, nuts: IE}
regulatoryJurisdiction: EIG-REG-IE | status: active | source: eurostat | version: 1.0.0
```

```text
geoID: EIG-GEO-IE-CTY-0004 | geoName: Cork | geoNameIrish: Corcaigh | geoLevel: GL-03
parentGeoID: EIG-GEO-IE-RGN-0002 (Southern, IE05) | areaKm2: 7457.2 | population: 584156 (2022)
coastalFlag: true | climateZoneID: EIG-GEO-CZ-IE-STH | primaryWeatherStationID: EIG-WX-ST-0007 (Cork Airport)
degreeDayRegion: DD-IE-S | urbanRuralClass: n/a (county) | source: cso | version: 1.2.0
```

```text
geoID: EIG-GEO-IE-PST-0058 | geoName: T12 (Cork South Central) | geoLevel: GL-06
parentGeoID: EIG-GEO-IE-CTY-0004 | externalCodes: {eircode_rk: T12} | urbanRuralClass: city
climateZoneID: EIG-GEO-CZ-IE-STH | primaryWeatherStationID: EIG-WX-ST-0007 | degreeDayRegion: DD-IE-S
```

```text
geoID: EIG-GEO-IE-DST-1893 | geoName: Árainn (Inis Mór ED) | geoLevel: GL-05
parentGeoID: EIG-GEO-IE-CTY-0011 (Galway) | urbanRuralClass: island | coastalFlag: true
climateZoneID: EIG-GEO-CZ-IE-ISL | primaryWeatherStationID: EIG-WX-ST-0021 (Mace Head)
degreeDayRegion: DD-IE-W | note: ODEON/O-CEI pilot district (~70 buildings, IE_B0001–IE_B0020 instrumented)
```

```text
geoID: EIG-GEO-CZ-IE-ISL | geoName: Irish Offshore Island | geoLevel: GL-07
hddBase15_5AnnualMean: 1980 | cddBase18AnnualMean: 8 | designTempHeatingC: -1
meanWindSpeedMs: 8.9 | drivingRainIndex: 92.4 | solarAnnualKWhM2: 1010
coversEntities: [EIG-GEO-IE-DST-1893, ...] | status: active
```

## 23. Completion Criteria

Complete when: all GL-01…GL-03 Irish entities enumerated; all 139 routing keys loaded; all GL-03…GL-06 records carry climate zone, station and DD region; all boundaries deposited; every building in EIG-BLD resolves a full geographic chain with zero dangling references; CZ register validated against Met Éireann climatology; consistency pass (Phase 14) confirms no other catalogue defines geographic entities.
