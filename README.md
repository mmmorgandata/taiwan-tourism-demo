# Taiwan Tourism Intelligence — rough concept

Independent buildless EN/JA prototype derived conceptually from DHDE's map-first layout, layers, destination brief and prominent compact timeline. No code or data changes to the Fukui production app.

Run: `python3 -m http.server 4180 --directory dist`

## Scope and provenance
- All operational numbers, seven-day indices, weather scenarios and accommodation figures are fabricated. No live feed, forecast model, or inventory dataset is connected.
- Map is a schematic with approximate positions, not a geographic basemap. Transport lines are conceptual connections, not routes or timetables.
- Six example destinations: Taipei, Sun Moon Lake, Alishan, Tainan, Kaohsiung, Hualien. Their inclusion does not indicate availability of usable datasets or operating status.
- Language switching, destination selection, layer toggles, seven-day slider, bilateral market direction and source-readiness view work locally.
- Linked inventory spreadsheet could not be read: connector 403 and browser organisation access restriction. No claims about its contents or dataset coverage are made.

## Proposed first data integrations
1. Attraction monthly visits: https://data.gov.tw/en/datasets/8116 — seasonality and regional context. The portal specifies annual file updates; monthly observation grain is not live freshness. Audit each attraction's counting method before classifying measured vs estimated. Counts are visits, not necessarily unique people.
2. CWA weather: https://opendata.cwa.gov.tw/dist/opendata-swagger.html — weather observations and forecasts must remain separate; verify API key, location, issue/valid time and feed cadence.
3. TDX: https://tdx.transportdata.tw/ — destination registry and transport access. Verify endpoint coverage, credentials, quotas, coordinate systems, licences. Passenger counts are not tourist counts.
4. Hotel reports and Taiwan inbound arrivals: https://admin.taiwan.net.tw/english/info/IssuePage?a=14986 — monthly hotel operations and arrivals by residence. Neither monthly occupancy nor hotel registry means current room availability.
5. JNTO: https://statistics.jnto.go.jp/en/ — Taiwan-origin visits to Japan, regional lodging and spending context. Confirm source definitions, survey precision and reuse/download terms. Do not silently join nationality and residence or monthly arrivals and annual survey metrics.
6. Partner daily tickets and entry/exit: not connected, source to acquire. Prerequisite for credible daily visitor prediction and validation. Entry count minus exit count needs correct initial occupancy, matching coverage and sensor checks.
7. Reviews/social: later phase; not automatically open. Verify licensing, language coverage, representativeness and sentiment validation.

## Forecasting gate
Use daily partner ground truth, weather available at prediction time, calendar and holidays. Establish a same-weekday baseline, rolling temporal backtests, site-level MAE/WAPE and interval coverage. Avoid training/evaluation leakage from realised future weather. Forecast accuracy, training model and update schedule are currently unimplemented, not unknown working capabilities. Monthly-only data supports monthly seasonality, not validated hourly crowds.

## Taiwan–Japan product design
Primary users: tourism agencies, DMOs and destination operators. Keep Taiwan destination operations separate from bilateral source-market strategy. Compare Japan→Taiwan and Taiwan→Japan on aligned months, definitions and units; show missing data explicitly. In a future connected version, track data coverage, observed/estimated/forecast/demo provenance, source observation period, ingestion time and publication date separately.

Action workflow proposal: signal → evidence → owner / due date → intervention → outcome. Pilot one destination and one decision before expanding nationwide. KPIs: overnight stays, visitor spend, access friction and satisfaction; any estimated lost revenue must show assumptions rather than imply causal measurement.

Sources checked 2026-11-09. This is a provisional source assessment, not an audit of the user's inaccessible inventory.
