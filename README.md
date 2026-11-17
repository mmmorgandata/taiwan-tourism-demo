# Taiwan Tourism Intelligence — concept demo (v4)

Independent buildless EN/JA prototype, conceptually derived from the DHDE Fukui app (map-first layout, layers, place brief, compact timeline). The Fukui production site is not touched.

Run locally: `python3 -m http.server 4180 --directory docs`

Public demo (no login): https://mmmorgandata.github.io/taiwan-tourism-demo/ · repo https://github.com/mmmorgandata/taiwan-tourism-demo. Hosted on GitHub Pages, deployed from branch `main`, folder `/docs`. Each push to `main` updates the site; there is no build step. `docs/` is the published copy; the old `dist/` folder can be deleted.

## Map
The map uses Leaflet 1.9.4, stored in `docs/vendor/` so the page needs no CDN, plus the same key-free basemaps as the Fukui DHDE app:
- Dark: Esri World Dark Gray Canvas (base + reference)
- Streets: OpenStreetMap standard tiles
- Satellite: Esri World Imagery + boundaries/places labels

Stations and destinations use real coordinates. Metro, HSR and east-line overlays are straight lines between stations, not real track geometry; a later step can use TDX `Shape` or OSM route geometry. Each place links to Google Maps through a public search URL, which needs no API key. For production traffic, check the tile providers' usage policies; OSM's public tile server is not meant for heavy use.

## Live data (v4, 17 Nov 2026) — read by the browser, Fukui DHDE pattern
Each source is fetched when the page loads and again every 5 minutes, and each can fail without affecting the others. No keys are used, so nothing secret is in the page.

| Source | Use | Evidence tag |
|---|---|---|
| Open-Meteo forecast API (CC BY 4.0; free tier is non-commercial, same as DHDE) | Weather now, next 5 h, 6-h rain chance, rain class 0–3, 7-day rain bars on the timeline | Forecast (model) |
| TDX `Road/Traffic/VD/City/Taipei` + `Live/VD` (anonymous) | Lane-average speed and occupancy from detectors within 600 m of each hub, plus a map layer | Observed |
| TDX `Rail/Metro/Alert/TRTC` (anonymous) | Taipei Metro service status | Observed |
| YouBike Taipei real-time feed (tcgbusfs) | Bikes available within 400 m, plus a map layer | Observed |
| DGPA holiday calendar via the ruyut/TaiwanCalendar jsDelivr mirror | Holidays and make-up workdays on the timeline and calendar | Observed (unofficial mirror) |

Caveats:
- TDX's anonymous quota is not published; register a key and move fetching into a scheduled job before heavy use.
- CWA replaces Open-Meteo once a key exists. A key cannot sit in a public page, so it would be fetched by a scheduled job.
- New Taipei detectors (Tamsui) are not loaded yet.
- Taipei City's parking feed (TCMSV) responds, but its data was a month old on 17 Nov, so it is not used.

## Node cards (DHDE SiteCards pattern, v4.1)
Each hub or destination has one card. The card shows its name plus one row per active layer:
- **Weather:** icon, temperature and rain chance.
- **Road:** a speed dot and km/h.
- **YouBike:** bikes available out of the total.

Illustrative rows (visitors, events, lodging) appear only on the selected card.

Hover a card or point to see the full detail (weather and the next 3 hours; road detector count and occupancy; YouBike stations; metro status), each with its source. Click to select.

A declutter pass (like DHDE's Declutter) places the selected card first. Each other card tries all four sides, then drops to one row, then to its name only, and is hidden if it still overlaps; it shows again when you zoom in. On phones, unselected cards show only the weather row.

## Ideas taken from the NCKU 2023 paper
- **Calendar page:** holidays plus user-added events. Events are stored in the viewer's browser only for now.
- **Walking catchment layer:** circles at 5/10/15 min, using a straight-line 400/800/1,200 m approximation.
- **Rain class 0–3:** none (0 mm), light (under 2.5), moderate (under 10), heavy (10 mm/h or more).
- **"Next hours" strip:** a 1–5 h horizon.
- **Data-timing panel** (from the paper's Fig. 6): what is known before prediction time, what is available at prediction time, and what only arrives after the fact.

## What to predict (following Fukui DHDE)
1. **Daily visitors per hub, 7 days ahead.** Start from a same-weekday 4-week mean baseline (DHDE's fallback), with Taipei Metro hourly entries as the proxy.
2. **Monthly Japan↔Taiwan visitors, 12 months ahead.** All inputs are public series.
3. **Hourly crowd at attractions, 1–5 h ahead** (the NCKU paper's target). This needs telecom or gate data.

## What changed in v2 (16 Nov 2026)
- **Data readiness** rebuilt from the supplied inventory CSV (`Taiwan_Tourism_Data_Source - Untitled.csv`, 27 rows × 18 fields). The old "inventory cannot be read" notice has been removed. Each source is in one of four groups, with the decision it supports, its limits, and what was checked on 16 Nov.
- **Map** (v3, 17 Nov): a real Leaflet basemap (Dark / Streets / Satellite) with a scope switch. *Taipei pilot* shows 10 tourist-hub stations; *All Taiwan* shows 6 destinations. Both use real coordinates. There is a place selector and a Google Maps link for each place. Layers state their source and connection status.
- **Evidence tags** on every number: Observed / Estimated / Forecast / Illustrative / Not connected. Since v4, the live layers carry Observed or Forecast tags; visitor counts, events, lodging and the demand curve stay **Illustrative**.
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
- TDX: an OAuth key (1-day token) is documented for members. Anonymous browser calls to the VD and Metro Alert endpoints also worked on 17 Nov; the per-IP quota is unpublished.
- Taipei Metro: the hourly station OD set on data.taipei replaced the legacy set from Jan 2017; no membership needed. Files not sampled.
- JNTO: July 2026 release (Taiwan 763,800, preliminary, +26.4%); Sep 2026 = 595,600 (+13.0%) via trade press.

## Not done / blocked
- data.gov.tw dataset pages could not be read by the fetch tool (robots restriction). Fields for the immigration, airport, holiday and tour-bus sets are still unconfirmed.
- No credentialed API calls and no account registration (TDX, CWA, MOENV).
- Bank of Taiwan FX CSV not fetched; stat.taiwan.net.tw tables not sampled; CWA key not registered.

## Rules kept in the UI
- Metro and airport passengers are not visitors. Monthly statistics are not real-time crowds and cannot back a 7-day forecast. A hotel registry is not live availability.
- Forecast gate: needs daily ground truth (partner tickets/gates), weather available at prediction time, holidays, a same-weekday baseline and rolling backtests. Nothing here is a validated forecast.

## Suggested phases
1. Taipei: CWA + holidays + Tourism Info DB events + Metro hourly entries at hub stations → weekly hub-pressure view.
2. Markets: monthly Japan↔Taiwan series from both official sides, with definitions side by side.
3. Get 1–2 partner daily-count sources before showing any visitor forecast.
