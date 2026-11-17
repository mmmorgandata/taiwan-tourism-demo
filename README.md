# Taiwan Tourism Intelligence — concept demo (v2)

Independent buildless EN/JA prototype, conceptually derived from the DHDE Fukui app (map-first layout, layers, place brief, compact timeline). The Fukui production site is not touched.

Run locally: `python3 -m http.server 4180 --directory docs`

Public hosting: GitHub Pages, deployed from branch `main`, folder `/docs`. Each push to `main` updates the site; there is no build step. `docs/` is the published copy; the old `dist/` folder can be deleted.

## Map
The map uses Leaflet 1.9.4, stored in `dist/vendor/` so the page needs no CDN, plus the same key-free basemaps as the Fukui DHDE app:
- Dark: Esri World Dark Gray Canvas (base + reference)
- Streets: OpenStreetMap standard tiles
- Satellite: Esri World Imagery + boundaries/places labels

Stations and destinations use real coordinates. Metro, HSR and east-line overlays are straight lines between stations, not real track geometry; a later step can use TDX `Shape` or OSM route geometry. Each place links to Google Maps through a public search URL, which needs no API key. For production traffic, check the tile providers' usage policies; OSM's public tile server is not meant for heavy use.

## What changed in v2 (16 Nov 2026)
- **Data readiness** rebuilt from the supplied inventory CSV (`Taiwan_Tourism_Data_Source - Untitled.csv`, 27 rows × 18 fields). The old "inventory cannot be read" notice has been removed. Each source is in one of four groups, with the decision it supports, its limits, and what was checked on 16 Nov.
- **Map**: has a scope switch. *Taipei pilot* (default) shows 10 tourist-hub stations on a schematic metro-map layout. *All Taiwan* shows 6 destinations on a simplified coastline at approximate real coordinates. Layers state their source and connection status.
- **Evidence tags** on every number: Observed / Estimated / Forecast / Illustrative / Not connected. All map, detail and 7-day values are still **Illustrative**.
- **7-day chart**: one-line header; the curve is taller; days are picked with compact buttons or by clicking the chart (the full-width slider is gone).
- **Taiwan ↔ Japan**: shows published figures typed in by hand and labelled as such (JNTO Taiwan→Japan Jul/Sep/Jan–Jul 2026, preliminary). Japan→Taiwan has only secondary-report figures (2025 ≈1.5M; Japan ranked #1 for Jan–Jun 2026). The monthly series is not connected.

## Source triage (27 rows)
| Group | Sources |
|---|---|
| Integrate first (8) | Tourism Statistics DB; Tourism Information DB; TDX; CWA; holiday calendar; Taipei Metro hourly OD; data.gov.tw (discovery); data.taipei |
| To verify (7) | Immigration airport API (counted or forecast?); Bank of Taiwan FX; Taoyuan airport daily; hotel occupancy table; CAA airport traffic; Taiwan Tour Bus; Google Trends terms |
| Later (9) | SRDA inbound survey; annual tourism CSVs; monthly recreation-site visits; YouBike; OSM; Overture; national parks; Wikimedia; New Taipei open data |
| Not suitable now (3) | Event visitors/benefit (method unknown); AQI (weak driver); Taichung city set (outside pilot) |

## Verified on 16 Nov 2026
- CWA F-D0047 township forecasts: 368 townships; 1-day hourly / 3-day 3-hourly / 7-day 12-hourly; issued 4×/day; XML/JSON; membership required.
- TDX: OAuth client_credentials token (lasts 1 day); a call without a token gets 401; rate-limit errors are documented but quotas are not published.
- Taipei Metro: the hourly station OD set on data.taipei replaced the legacy set from Jan 2017; no membership needed. Files not sampled.
- JNTO: July 2026 release (Taiwan 763,800, preliminary, +26.4%); Sep 2026 = 595,600 (+13.0%) via trade press.

## Not done / blocked
- data.gov.tw dataset pages could not be read by the fetch tool (robots restriction). Fields for the immigration, airport, holiday and tour-bus sets are still unconfirmed.
- No credentialed API calls and no account registration (TDX, CWA, MOENV).
- Bank of Taiwan CSV and the holiday JSON mirror were not fetched. stat.taiwan.net.tw tables were not sampled.

## Rules kept in the UI
- Metro and airport passengers are not visitors. Monthly statistics are not real-time crowds and cannot back a 7-day forecast. A hotel registry is not live availability.
- Forecast gate: needs daily ground truth (partner tickets/gates), weather available at prediction time, holidays, a same-weekday baseline and rolling backtests. Nothing here is a validated forecast.

## Suggested phases
1. Taipei: CWA + holidays + Tourism Info DB events + Metro hourly entries at hub stations → weekly hub-pressure view.
2. Markets: monthly Japan↔Taiwan series from both official sides, with definitions side by side.
3. Get 1–2 partner daily-count sources before showing any visitor forecast.
