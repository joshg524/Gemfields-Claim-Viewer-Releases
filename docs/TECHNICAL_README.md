# Gemfields Claim Viewer

Gemfields Claim Viewer is a Windows desktop research application for looking up Queensland mining claims and mining leases from official Queensland Government spatial services.

The current build is **v0.5.14**. The version appears in the main window, full-map view, and detached map title, and is recorded in the executable metadata. A custom sapphire/map-pin icon appears on the app, windows, desktop and Start menu shortcuts, installer and uninstaller.

In v0.5.14, **Current MC/ML** controls all non-selected granted boundaries, including the legacy Nearby Claims fallback. Previously that fallback still drew blue granted polygons when Current was off, so historical-only or applications-only views contained unwanted grants. Disabled-layer boundaries are now excluded from rendering, hover previews, hit testing and overlap choices, even with stale cached records or later nearby-result refreshes. The Nearby Claims table remains available independently. The teal selected boundary/marker remains as a location reference regardless of layer switches; it can still reopen its profile. Layer switches do not move or zoom the map. A 64-case regression matrix checks all eight switch combinations against loaded/fallback coverage, imagery on/off and labels on/off.

In v0.5.13, clicking a claim in full-map or detached-map view opens a scrollable **CLAIM PROFILE** panel beside the map. Previously the selected profile updated on the hidden research page only. The panel displays status, dates, area, holder, commodity, other available attributes, source/freshness and editable local notes. It shares the original notes draft and save protection; a save failure is shown inside the panel. **HIDE** returns the width to the map, and **CLAIM PROFILE** reopens it. Opening/hiding the panel preserves geographic centre and scale (the visible map width changes); it does not refit or zoom out. Clicking the already selected boundary reopens the panel without discarding its draft, including when optional claim layers are off. Granted, application and historical selections work independently; application/non-current warnings remain explicit. The compact profile map remains unchanged.

In v0.5.12:

- **Local Gemfields claims library:** the installer includes a verified snapshot of all six current granted, current application and historical MC/ML extent layers intersecting the working rectangle **147.400–148.000°E, 23.700–23.200°S (WGS84)**. This is an application-selected region, not an official district boundary or statewide download. The bundled snapshot was checked on **25 October 2026 at 12:30 AEST** and contains **1,502 granted, 89 application and 3,033 historical extents** (extents are not necessarily distinct authority numbers). Profile searches, nearby granted claims and visible-map boundaries use the local file where coverage allows. Outside the rectangle, live queries remain available. **UPDATE CLAIMS DATA** refreshes the entire bounded library, with progress and cancellation; only a complete validated download replaces the previous file. Failed/cancelled updates retain the last good copy. Data older than 24 hours is flagged, not silently described as live.
- **Independent Applications layer:** switch **Applications** on in the compact, full or detached map for MC/ML application boundaries in **pink dash-dot**. Applications remain separate from granted and historical layers and are labelled **Application—not granted** in previews, the overlap chooser and the selected profile. They can be shown over any imagery; this does not reconstruct tenure at that imagery's capture date.
- **Layer health and recovery:** the panel below the canvas shows granted, application, historical and imagery readiness. Temporary claim-service failures retry twice (normally after 1 and 3 seconds); previous valid boundaries are retained with an incomplete-coverage warning. **RETRY HISTORICAL** appears after a historical-layer failure. Retries do not move the map or change the selected claim/notes.
- **Protected draft notes:** an **Unsaved changes** indicator appears while editing. Changing claims or closing the app offers **Save / Discard / Cancel**. A failed save keeps the draft and cancels navigation. Saved notes are written atomically; unreadable existing files are not replaced with an empty list.
- **Hover previews:** pausing over loaded boundaries highlights them and shows claim numbers/status, overlapping candidates, and local-library date when relevant. Hover makes no service requests and does not open a different profile.
- **Layer settings:** three independent opacity sliders for fossicking, restricted/protected, and native-title/parcels overlays are below the canvas and shared across map views during the session. They change appearance only, not geometry, status or access rights. The compact canvas remains clean.

The library is stored separately from the program and from saved notes at `%LOCALAPPDATA%\QueenslandMiningClaimResearch\gemfields-claims-library.json.gz`. Installation, upgrade and uninstall retain existing user data. This release does **not** make imagery, roads, the aerial catalogue or zone overlays available offline, and does not claim change-only synchronisation: the published services do not provide a verified edit/change feed for these layers.

In v0.5.11, single-clicking a claim boundary opens its details without zooming out, recentering the map or leaving full-map mode. The geographic camera is preserved independently in the compact, full and detached views, including when the new claim has a different size or centre and when subsequent nearby/road requests finish. **RESET VIEW**, double-click reset, a manual search and an explicit map-extent change still fit the selected claim intentionally.

In v0.5.10, **Fill imagery gaps** optionally places the mixed Queensland state basemap behind the selected aerial project in all three map views. It is off by default and available when imagery and a specific aerial project are selected. Aerial pixels retain priority; transparent areas reveal the background, whose sources/dates can differ and may include 2017 satellite imagery. The source notice and combined State/Planet attribution remain outside the compact map. A missing background does not discard a successful aerial image; if the selected aerial fails but the background loads, the app explicitly labels the view as state basemap only. Switching off immediately removes the gap background. It does not promise imagery everywhere or newer imagery in uncovered areas.

In v0.5.9, the compact profile map defaults to a clean canvas: no title banner, imagery-information shade, legend/count panels, instructions or road/claim-name labels cover the map. Boundaries, imagery, the selected-claim marker, scale and temporary top-right imagery-loading activity remain. **Map labels** below the viewer restores road and claim names; **Map information** expands the legend and context outside the canvas. Imagery capture/source details, coverage notices, full attribution and source/fallback warnings are shown below the map, not drawn across it. Full-map and detached-map views retain their detailed display.

In v0.5.8, temporary road-service errors retry twice automatically (normally after 1 and 3 seconds). A **Retrying roads…** status appears during recovery. If loading still fails, a road-only warning and **RETRY ROADS** button appear in the profile, full map and detached map. A successful retry removes the warning. Previously loaded roads remain visible if wider coverage fails; changing claim or extent cancels superseded requests. Claim-status messages, saved claims and unsaved notes are not replaced by road retries.

The first release is a Phase 1 MVP. It accepts inputs such as `MC70402`, `MC 70402`, `mc70402`, and `ML12345`, normalises them, queries official Queensland ArcGIS REST layers, displays the returned attributes and polygon geometry, finds nearby current MC/ML tenures, and caches successful research locally.

## What is implemented

- WPF desktop shell for Windows 10/11.
- Search and normalisation for MC and ML authorities.
- Current MC layers (application and granted) and current ML layers (application and granted).
- Historical MC fallback, which is important for `MC 70402` because that authority is not in the current MC layer.
- Structured display of status, dates, holder, minerals, purpose, area, name, previous permits, source, and retrieval time.
- Native interactive boundary map with pointer-centred zoom, pan, clipping, nearby-tenure overlays, official Queensland Foundation Data road linework, classification styling, collision-aware road labels, a Roads visibility toggle, and reset/double-click reset. Click a blue nearby-tenure boundary to open its profile. Map extent can be set to Claim, 1 km, 2 km, or 5 km beyond the claim boundary in each direction; the default is 2 km. Road coverage expands when a wider extent is chosen, and small claims gain a visible marker at district scale. The sidebar Map command or VIEW FULL MAP gives the map the app's main area; DETACH WINDOW opens a separate maximised map.
- Optional Queensland Government imagery in each map view. After a claim is selected, the app queries the public `AerialOrtho_AllUsers` catalogue at that claim's centre and offers only aerial capture years actually listed there; the latest listed aerial year is selected initially. Choose another year or the separate mixed state basemap from the dropdown. The map labels the displayed centre-point source as **aerial** or **satellite**, shows capture dates when supplied, and warns clearly when the basemap centre uses the **2017 satellite fallback**. The mixed basemap may also contain 2017 satellite imagery away from its centre; a selected aerial project may not cover the entire map extent. No newer imagery is inferred for places without listed public coverage. Imagery stays aligned while zooming/panning, and the vector map remains usable if the image service is unavailable.
- Three independent claim-boundary switches in each map view: **Current MC/ML** shows granted mining claim and lease extents, **Historical MC/ML** shows non-current extents, and **Applications** shows applications, not grants. They use the dated local library for contained map views and live services otherwise, updating on pan/zoom regardless of imagery year. Granted claims are blue/violet; historical footprints are dashed amber/orange; applications are pink dash-dot; the selected claim remains teal. Click a boundary to open its profile; overlapping boundaries offer a choice. Hover previews do not navigate. These layers are separate from the Nearby search radius and are **not** tenure at the photograph's capture date. Broad views with over 4,000 records in a layer ask the user to zoom in instead of silently truncating results.
- Three independent map-layer switches in every map view: fossicking areas/designated land/general permission areas; mining restricted areas/protected areas/forests; and indicative native-title land/QSCF lot parcels. Click an overlaid location to query the official layers and inspect matching features, source links, and cautions. These live overlays require an internet connection and do not grant access or establish legal boundaries.
- Light, dark-text dropdown menus and contrasting dark table headers in the nearby and saved-claims lists.
- Dark, light-text tooltips throughout the app, including map-layer and imagery selectors.
- A prominent **Loading imagery…** panel with an animated teal bar at the top-right of the profile, full-map and detached map views. It appears immediately when imagery is enabled or a new image is requested after changing year, extent, pan or zoom, and clears on completion, cancellation or failure. The animation indicates activity rather than a measured percentage. The map remains interactive, and an unavailable-imagery message is shown if the request fails.
- Nearby current MC/ML query with adjoining, 100 m, 250 m, 500 m, 1 km, and 5 km choices. Distances use polygon-boundary segments, not just vertices. Overlapping tenures are distinguished from adjoining ones, and all returned object IDs are fetched in batches instead of silently stopping at 100 results.
- Local cache and locally stored saved claims/notes under `%LOCALAPPDATA%\QueenslandMiningClaimResearch`.
- VIEW SAVED CLAIMS is beside SAVE CLAIM + NOTES on the claim profile; the saved list has a BACK TO CLAIM button and a confirmation-protected REMOVE SELECTED CLAIM action that removes only the local bookmark and notes. The sidebar shortcut remains available.
- Prominent local-library/live/cached/offline status. Regional library age is labelled and refreshed using UPDATE CLAIMS DATA; REFRESH FROM SOURCE bypasses the library and cache for an individual claim. Outside the library's known records, ordinary cached profiles are refreshed after 24 hours where reachable. The source panel links to GeoResGlobe and the resource-authority public report form (enter the authority number on the government page).
- Sources view and explicit wording for the difference between “not shown in the loaded current dataset” and legal availability.
- Automated test harness covering parser, invalid input, geometry parsing, adjoining detection, distance calculations, wider map bounds, multi-path/wide-area road data, cache round-trip, and service failure handling.

Not implemented yet: automatic claim-to-parcel intersection, title/ownership reporting, geology, GSQ report search, claim-history event extraction, PDF/CSV/JSON export, comparison mode, statewide offline claims and offline imagery. Optional claim layers are off by default; they work offline inside complete local coverage. Imagery, roads, zones, the public aerial-year lookup, library updates and live queries outside that coverage require internet access.

## Official source decision

The primary integration is the Queensland Government Department of Resources ArcGIS REST service:

`https://spatial-gis.information.qld.gov.au/arcgis/rest/services/Economy/MinesPermitsCurrent/MapServer`

The provider queries the following polygon layers by exact `displayname`:

| Authority | Current application | Current granted | Historical fallback |
|---|---:|---:|---:|
| MC | 33 | 36 | 155 |
| ML | 40 | 44 | 170 |

The service exposes fields including permit number, type, status, sub-status, lodge date, grant date, expiry/non-current date, authorised holder name, minerals, purpose, area, permit name, previous permits, and geometry. The source service reports nightly updates and CC BY 4.0 licensing. See [DATA_SOURCES.md](DATA_SOURCES.md) for the source register, exact query shape, and planned providers.

`MC 70402` was verified against the official historical MC extent layer on 15/08/2026. The returned record is `MC 70402`, status `Non-current`, sub-status `Finalised`, holder `JONES, Allan`, name `OSCAR`, minerals `SAPP,ZIR`, area `0.0775 ha`, and a polygon geometry. The application labels it historical and does not imply that the area is legally available.

## Framework and project structure

The app targets `net9.0-windows` because the available build environment contains the .NET 9 Windows Desktop reference packs. It uses only the .NET/WPF standard libraries, so there are no NuGet runtime dependencies. Windows 10/11 support is expected; install the .NET 9 Desktop Runtime for framework-dependent execution, or publish self-contained for a standalone folder.

```text
src/QueenslandMiningClaimResearch/
  Controls/MapViewport.cs           native zoom/pan boundary renderer
  Models/TenureModels.cs            authority, geometry, cache, source models
  Services/TenureNumberParser.cs    input normalisation and validation
  Services/GeometryMath.cs          distance, bounds, touching, direction
  Services/ArcGisTenureProvider.cs  current/historical ArcGIS provider
  Services/FoundationMapProvider.cs official road-linework provider
  Services/QueenslandImageryProvider.cs official public aerial catalogue, selected rasters, and basemap metadata
  Services/QueenslandZoneProvider.cs official zone raster overlays and point identification
  Services/CacheFreshness.cs      automatic profile refresh policy
  Services/LocalStores.cs           local cache and saved-claim persistence
  MainWindow.xaml(.cs)              WPF shell and workflow
  LargeMapWindow.xaml(.cs)          maximised, resizable map window
  ZoneDetailsWindow.xaml(.cs)       clicked-zone details and official links
tests/QueenslandMiningClaimResearch.Tests/
  Program.cs                        dependency-free automated test runner
```

Phase 1 uses versioned JSON files rather than adding a database package. This keeps the first Windows build straightforward and offline-friendly. The storage boundary is isolated in `LocalCacheStore` and `SavedClaimsStore`, so the same model can move to SQLite in Phase 2 without coupling the UI to file formats.

The app and executable are named Gemfields Claim Viewer. The existing local-data folder name is retained so saved claims and notes from earlier builds remain available.

## Build and run

### Windows installer

Run `GemfieldsClaimViewer-Setup-v0.5.14-win-x64.exe` to install for your Windows account. The installer bundles the .NET runtime and the Gemfields claims snapshot, adds a Start menu shortcut and offers a desktop shortcut. No administrator rights or separate .NET download are required. This release is unsigned.

Keep **Create a desktop shortcut** selected during setup to get the custom icon automatically. To change an existing shortcut manually, right-click it, choose Properties > Shortcut > Change Icon, browse to the installed `GemfieldsClaimViewer.exe`, select the sapphire/map-pin icon, and apply. The icon is embedded in the EXE, so a separate icon file is not required. The editable source image and multi-size Windows icon live in `src/QueenslandMiningClaimResearch/Assets`; `installer/create-icon.ps1` repackages the PNG into ICO sizes from 16 to 256 pixels.

Uninstall through Windows Settings > Apps. Saved claims, notes and cache in `%LOCALAPPDATA%\QueenslandMiningClaimResearch` are retained on installation, upgrade and uninstall. Future installers use a stable application ID to upgrade the existing installation.

To rebuild with the .NET 9 SDK and Inno Setup 6.7.3 or later, run `powershell -ExecutionPolicy Bypass -File installer\build-installer.ps1 -Compiler "C:\path\to\ISCC.exe"`. The publish step downloads the official Windows x64 runtime packages from NuGet; the generated installer itself installs offline.

From a Windows machine with the .NET 9 SDK:

```powershell
dotnet build QueenslandMiningClaimResearch.sln --configuration Release
dotnet run --project src\QueenslandMiningClaimResearch\QueenslandMiningClaimResearch.csproj --configuration Release
```

Run the automated checks:

```powershell
dotnet run --project tests\QueenslandMiningClaimResearch.Tests\QueenslandMiningClaimResearch.Tests.csproj --configuration Release
```

For a self-contained Windows folder publish:

```powershell
dotnet publish src\QueenslandMiningClaimResearch\QueenslandMiningClaimResearch.csproj -c Release -r win-x64 --self-contained true
```

The app needs network access when querying or refreshing official sources, checking the public aerial-year catalogue, or using the optional imagery layer. Cached profiles and local notes remain on the computer and are not uploaded anywhere.

For the packaged build, extract the ZIP into a new folder and run `GemfieldsClaimViewer.exe`. Keep all extracted companion files beside the executable; do not run the EXE directly from inside the ZIP. The Map extent selector changes the geographic area shown, while the separate Nearby search radius controls which other tenures appear in the nearby results and overlay.

## Important limitations

- A missing polygon is reported as missing data, not as vacant or legally available ground.
- The public tenure layer supplies the principal authorised holder field when present; it is not a full dealings or title register.
- The native map is a research boundary canvas with official tenure polygons, Queensland Foundation Data road centerlines, and optional aerial/satellite imagery. A year in the selector confirms catalogue coverage at the claim centre, not across the whole viewport. Current and historical claim switches do not create a time-specific tenure map for that year. The mixed basemap may vary by location and contain 2017 Planet satellite imagery where public aerial imagery is unavailable. The map is not a legal survey, a turn-by-turn navigation product, or evidence that a road is a public right of way.
- The nearby calculation uses projected polygon-boundary segment distances suitable for research triage. It is not a cadastral or legal boundary determination.
- A mapped fossicking area still requires a licence and may have site-specific conditions. Protected and native-title overlays are indications for further checking, not definitive access or title advice; QSCF parcel geometry is not a survey plan.
- Government schemas and endpoints can change. The provider is isolated so service-layer changes do not require rewriting the UI.
- No scraping is used. Features whose official structured access is not yet confirmed are documented as planned rather than guessed.
