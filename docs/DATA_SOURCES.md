# Queensland Government data sources

Access review date: 27/09/2026 (Australia/Brisbane)

This register records the official source decision for the MVP and the sources selected for later phases. The application does not scrape government pages.

## 1. Current mining and exploration tenure series

- Dataset/service: Queensland mining and exploration tenure series; operational ArcGIS service title: `Mining permits: current`.
- Responsible department: Queensland Government Department of Resources.
- Service type: ArcGIS REST MapServer with queryable feature layers and GeoJSON/JSON output.
- Service endpoint: <https://spatial-gis.information.qld.gov.au/arcgis/rest/services/Economy/MinesPermitsCurrent/MapServer>
- Update frequency: the service description says the datasets are updated nightly.
- Authentication: none observed for read-only REST queries.
- License: Creative Commons Attribution 4.0; the service item requests Queensland Government attribution.
- Geometry: polygon geometry returned as ArcGIS `rings`; the application requests `outSR=4326` and converts rings to its internal polygon model.
- Relevant layers:

| Layer | Meaning | Key fields used |
|---:|---|---|
| 33 | MC permit application | `displayname`, `permittype`, `permitstatus`, `permitstate`, `lodgedate`, `approvedate`, `expirydate`, `authorisedholdername`, `permitminerals`, `permitpurpose`, `shapeareahectares`, `permitname`, `previouspermits`, `shape` |
| 36 | MC permit granted | Same public attribute family and polygon geometry |
| 40 | ML permit application | Mining lease application polygon and the same public attribute family |
| 44 | ML permit granted | Mining lease granted polygon and the same public attribute family |

The provider queries an exact `displayname`, for example:

```text
GET .../MapServer/36/query
  ?where=displayname%3D%27MC%2070402%27
  &outFields=*
  &returnGeometry=true
  &outSR=4326
  &f=json
```

The publicly exposed `authorisedholdername` field supports a principal-holder display when present. It does not establish that a full list of registered dealings, interests, or current title information is publicly available from this structured service.

## 2. Historical mining permits

- Dataset/service: `MinesPermitsHistoric`, including `Historical MC extent`.
- Responsible department: Queensland Government Department of Resources.
- Service type: ArcGIS REST MapServer.
- Service endpoint: <https://spatial-gis.information.qld.gov.au/arcgis/rest/services/Economy/MinesPermitsHistoric/MapServer>
- Relevant layer: `155` — Historical MC extent. The historical service describes this as the boundaries of non-current MCs in Queensland.
- License: Queensland Government CC BY 4.0 service attribution.
- Authentication: none observed for read-only REST queries.
- Current use: fallback after current MC/ML layers return no record. This is how the supplied `MC 70402` case is resolved.

Verified response for `MC 70402`:

```text
displayname: MC 70402
permittype: Mining Claim
permitstatus: Non-current
permitstate: Finalised
authorisedholdername: JONES, Allan
permitminerals: SAPP,ZIR
permitname: OSCAR
shapeareahectares: 0.0775
geometry: polygon rings, EPSG:4326 after query transform
```

The app displays the historical state prominently and does not convert it into a current tenure or vacant-ground conclusion.

The optional **Historical MC/ML** map switch shows historical extent layers 155 and 170. The independent **Current MC/ML** switch shows granted extent layers 36 and 44, and **Applications** shows application extent layers 33 and 40. The application boundaries are pink dash-dot and explicitly marked **not granted**. All switches reload on pan/zoom and can be drawn over any selected imagery. They use the dated local library for views fully contained in its coverage and live queries otherwise. These are official current and non-current datasets **as checked**, not a reconstruction of tenure at an aerial photograph's capture date. The app requests all intersecting object IDs, fetches records in batches, and asks the user to zoom in if a layer view exceeds 4,000 records rather than displaying a partial set.

### Local Gemfields claims library (v0.5.12)

- Working rectangle, WGS84: longitude **147.400 to 148.000°E**, latitude **23.700 to 23.200°S**. Chosen by the application for regional research; not an official Gemfields administrative boundary. Polygons intersecting the rectangle are stored whole, but completeness is claimed only for requests contained within the rectangle.
- Sources: all intersecting extents from current granted layers **36/44**, current application layers **33/40**, and historical layers **155/170**. These are extents; multiple extents can belong to one authority. Nearby results continue to exclude applications/historical boundaries and use granted layers only.
- Bundled snapshot completed **25 October 2026, 02:30:47 UTC / 12:30:47 AEST**: **1,502 granted**, **89 application**, **3,033 historical** extents. Each record retains its official source URL, status, attributes and geometry. The snapshot's download date is not the imagery date or a guarantee of an instantaneous legal-status check.
- A refresh fetches each layer's complete object-ID list, all requested batches (200 features each), verifies returned IDs/counts and valid geometry, and checks the ID list again for changes during that layer's download. All six layers must complete before atomic file replacement. This guards against truncated/missing batches, but is not a server-side transaction and cannot guarantee that attributes stayed unchanged across all six requests.
- The published layer metadata checked on 25 October 2026 did not expose a verified synchronisation/change feed or general record-modified field for these layers. Consequently, the app makes a bounded **full regional refresh**, not a misleading new-ID-only sync that would miss status/geometry edits or removals. There is no continuous background poll. UPDATE CLAIMS DATA is explicit and cancellable; transient HTTP/ArcGIS failures retry at most twice per request.
- Persistent data: `%LOCALAPPDATA%\QueenslandMiningClaimResearch\gemfields-claims-library.json.gz`, separate from `saved-claims.json` and per-profile cache files. A validated bundled seed is copied only if no library file already exists. Existing libraries are preserved across upgrades, with age visible and a warning after 24 hours. Failed/cancelled downloads retain the previous complete file; an unreadable file is not silently overwritten on startup.
- Source attribution remains Queensland Government / State of Queensland, with per-record official links. The app does not label locally stored data as live, infer that a historical boundary is vacant, grant access rights, or bundle aerial images, roads or zone rasters in this library.

## 3. Queensland Foundation Data — road layer

- Dataset/service: Queensland Foundation Data, `Road` feature layer.
- Responsible department: Queensland Government Department of Resources.
- Service type: ArcGIS REST Feature Layer with queryable polyline geometry and JSON/GeoJSON/PBF output.
- Service endpoint: <https://spatial-gis.information.qld.gov.au/arcgis/rest/services/Basemaps/FoundationData/MapServer/23>
- Geometry: road and street centerlines returned as ArcGIS `paths`; the app requests EPSG:4326 and renders them as contextual linework around the selected tenure.
- Fields used: `road_name_full`, `class`, `road_id`, and `segment_id`.
- License/attribution: Queensland Government Foundation Data is published for reuse under Creative Commons Attribution terms; the app retains the source name in its map context.
- Current use: optional map overlay for roads, enabled by default and controlled by the `Roads` checkbox. The app queries at least a 750-metre context, widening with the chosen map extent, and places only non-overlapping road labels to keep the map readable.

The road layer is cartographic context. It is not a navigable routing network and a displayed centerline is not, by itself, proof of public access, road tenure, or a legal right of way. See the official [Queensland Foundation Data road layer](https://spatial-gis.information.qld.gov.au/arcgis/rest/services/Basemaps/FoundationData/MapServer/23) and the [Queensland Roads and Tracks dataset](https://www.data.qld.gov.au/dataset/queensland-roads-and-tracks) for source documentation.

## 4. Queensland Government aerial catalogue and mixed imagery basemap — implemented

- Public aerial catalogue: <https://spatial-img.information.qld.gov.au/arcgis/rest/services/TimeSeries/AerialOrtho_AllUsers/ImageServer>.
- Mixed state basemap: <https://spatial-img.information.qld.gov.au/arcgis/rest/services/Basemaps/LatestStateProgram_AllUsers/ImageServer>.
- Service type: ArcGIS ImageServer. The app queries the public aerial catalogue by the selected claim's centre point and filters to aerial `product_type` 3 or 4. It offers only the years returned there. A selected year exports the specific public aerial raster using `esriMosaicLockRaster`. One public project is chosen per year; this does **not** assert coverage across the whole visible map.
- The separate state-basemap option exports the current mixed basemap for the visible EPSG:4326 bounds. `identify` at the map centre supplies the source type and capture period when available. The basemap uses aerial where available and can use the 2017 Planet satellite mosaic elsewhere. A **2017 SATELLITE FALLBACK** warning appears when the map-centre basemap source is that satellite imagery; other parts of the map can have different source types and dates.
- **Fill imagery gaps** is off by default. When a selected aerial project and imagery are enabled, it optionally requests that same mixed state basemap for the identical visible bounds and draws it underneath the transparent selected-project PNG. Selected aerial pixels take priority. This is a mixed-date aerial/satellite background, not an extension of the selected project's date or a guarantee of coverage. The app does not identify individual gap pixels' capture dates from the basemap's centre-point metadata. It warns that gap-filled areas can include 2017 satellite imagery, retains State/Planet attribution, and labels a basemap-only fallback if the selected aerial fails. Failures in one layer do not discard the other; turning the option off removes the background immediately.
- Current use: optional `Imagery` background in the profile, full map, and detached map. The image request is delayed briefly after pan/zoom, and the last image stays geographically aligned while the next request loads. The vector map continues to work if imagery cannot be fetched. Public aerial-year lookup and imagery require internet access.
- Attribution and licence: the basemap's full copyright notice and required no-warranty acknowledgement appear in the app's Sources view; source-specific copyright is also drawn on the map when imagery is visible. The basemap metadata specifies CC BY-SA terms. Imagery is contextual, not a legal survey or proof of access. No newer imagery is presumed where the catalogue has no public aerial capture at the claim centre.

## 5. Queensland Spatial Catalogue and cadastral services — map context implemented

- Dataset family: Queensland Spatial Cadastral Fabric (QSCF), Property boundaries Queensland, and the Queensland land parcel property framework (LPPF).
- Responsible department: Queensland Government spatial data service.
- Official access guidance: <https://www.business.qld.gov.au/running-business/support-services/mapping-data-imagery/data/digital-cadastral/access>
- Catalogue: <https://qldspatial.information.qld.gov.au/catalogue/>
- Service type: official ArcGIS MapServer/FeatureServer and OGC services published through the Spatial Portal. The LPPF service group is catalogued at <https://spatial.information.qld.gov.au/arcgis/home/group.html?id=bc6445f21d1c42e3aeacc06fee92ce99>.
- License: Creative Commons Attribution.
- Authentication: the public access guidance describes open download and live web map service access.
- Current use: the `Native title / parcels` map switch requests the QSCF Land Parcel Property Framework `MapServer/120` as a transparent live overlay. Clicking the map queries the layer for parcels at the chosen point and shows the source link. The native-title indication comes separately from the Mining Administrative Areas service layer 9.
- Limitation: parcel outlines are graphical context, not surveyed legal boundaries. Automated claim-to-parcel intersection, title searches, ownership, and landholder permission checks are not implemented.
- Live layer: <https://spatial-gis.information.qld.gov.au/arcgis/rest/services/PlanningCadastre/QSCF_LandParcelPropertyFramework/MapServer/120>

## 6. Fossicking, restricted and protected zones — implemented as optional map layers

- Mining Administrative Areas service: <https://spatial-gis.information.qld.gov.au/arcgis/rest/services/Boundaries/MiningAdministrativeAreas/MapServer>.
- `Fossicking` switch: current fossicking areas (layer 11), designated fossicking land (15), and general permission areas (20). The `status='Current'` filter excludes proposed or non-current parcels.
- `Restricted / protected` switch: mining restricted areas (layer 13), plus Queensland protected areas and forests (layer 10) from <https://spatial-gis.information.qld.gov.au/arcgis/rest/services/Environment/ParksTerrestrialProtectedAreas/MapServer/10>.
- `Native title / parcels` switch: land subject to native title, *indication only* (Mining Administrative Areas layer 9), and the QSCF layer above.
- All three switches work independently in the profile, full and detached maps. The app fetches transparent images for the visible extent and queries the official feature layers when the user clicks a location. It displays matching feature names, source links and cautions.
- The official [fossicking rules](https://www.qld.gov.au/recreation/activities/areas-facilities/fossicking/rules/designated-areas) require a licence, and additional site or landholder conditions can apply. Some protected estate types prohibit fossicking; state forests and timber reserves require a general permission area. These overlays do not determine access or legal rights.

## 7. Geological Survey of Queensland Open Data Portal — planned

- Portal: <https://geoscience.data.qld.gov.au/>
- Responsible department: Geological Survey of Queensland, Queensland Government Department of Resources.
- Portal/API documentation: <https://github.com/geological-survey-of-queensland/gsq-data-api-docs>
- API base: `https://geoscience.data.qld.gov.au/api/3/action/`
- API type: CKAN Action API. The documented actions include `package_search` and `package_show`, with spatial filtering through `ext_bbox` and filters such as report type and commodity.
- Example report search: `package_search` with `type:report`, `georesource_report_type`, claim/tenure text, locality text, or a boundary-derived bounding box.
- Datasets advertised by the official portal include geological mapping, mineral occurrences, boreholes, geochemistry, geophysics, maps, publications, and open-file company statutory reports.
- License/authentication: dataset-specific metadata and licence must be read from each returned package; the portal is publicly searchable without registration. No authentication is assumed by the planned read-only provider.
- MVP status: not yet used. Phase 3 will implement a provider that searches claim number, locality, bounding box, and relevant report vocabularies, then displays links without downloading large reports automatically.

## 8. GeoResGlobe and authority reports — official reference links, not application APIs

- Official viewer: <https://georesglobe.information.qld.gov.au/>
- Official description: <https://www.business.qld.gov.au/industries/mining-energy-water/resources/minerals-coal/online-services/georesglobe>
- Public authority report form: <https://www.business.qld.gov.au/industries/mining-energy-water/resources/petroleum-energy/online-services/searches>. Enter the authority type and number on that page to request a public authority report.
- Use in this project: the source panel opens these official pages for manual verification. GeoResGlobe is the government’s public interactive viewer and exposes more than 600 spatial layers, but the app uses documented ArcGIS REST services directly where available. The app does not automate browser scraping of GeoResGlobe or its report form.
