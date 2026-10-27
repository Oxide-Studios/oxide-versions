# Changelog

## [1.1.2] - 2026-10-26

### Fixed
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.
- A `/weather settings` change that can't be saved to the database is no longer shown as applied. The menu shows "Could not be saved, see the server console" for that setting and the old value stays.

### Migration Guide
1. No database changes.
2. If you translated `locales/en.json`, copy the four new lines into your translation: `errors.setting_save_failed`, `log.setting_function_ignored`, `log.setting_function_fix` and `log.setting_save_failed`.

## [1.1.1] - 2026-10-10

### Fixed
- The weather settings menu and the Weather app on the phone and tablet no longer show raw text keys for a moment when they open. They now wait for their translations before drawing.

## [1.1.0] - 2026-10-04

### Added
- Weather app for `oxide-phone`. Players install it from the phone's Store, or add `weather` to the phone's preinstalled apps. Each place gets its own page over an animated sky: conditions now, today's high and low, upcoming changes with daybreak and nightfall, a day-by-day forecast, detail tiles (precipitation, wind, daybreak or nightfall, radar, plus snow cover, power cuts and season when they apply), severe weather alerts and the radar.
- Phone home-screen widget in small and medium sizes with the current conditions, plus the region's alert or upcoming changes.
- Severe weather alerts for the player's region arrive as phone notifications. Tapping one opens the alert, and it is removed when the alert ends.
- `Config.Broadcast.Phone` (default on) turns the phone app off. It is also in `/weather settings` → Broadcast as **Phone app**.

### Changed
- `Config.Broadcast.Widget` now covers both the tablet and the phone widgets.

### Fixed
- The radar's front count on the tablet can now be translated.

### Documentation
- `docs/FEATURES.md`: the phone app, widget sizes and alert notifications.
- `docs/CONFIGURATION.md`: `Config.Broadcast.Phone`.
- `README.md`, `docs/INSTALLATION.md`, `docs/ESX_INSTALL.md`, `docs/QBCORE_INSTALL.md`, `docs/QBX_INSTALL.md`: the optional phone app and the `o-link` 1.11.0 it needs.
- `docs/TROUBLESHOOTING.md`: "The weather app does not appear on the phone".

### Migration Guide
1. No database changes. Saved settings keep their values and gain **Phone app** switched on.
2. The phone app needs `o-link` 1.11.0 or newer and `oxide-phone`. On an older `o-link` everything else works and the app does not appear.
3. Restart the whole server, not only the resource. This version adds new files.

## [1.0.0] - 2026-09-21

- Initial public release of `oxide-weather`.
- One server-synced clock and sky for every player, run from the game clock or mirrored from real time (`/weather clock`).
- Day and night that pass at different speeds (`/weather timescale <day> [night]`), a clock you can freeze (`/weather freeze`), and per-region time offsets.
- Fifteen weather types with weighted transitions and a forecast queue you can read ahead of time and edit from the admin panel.
- Weather regions: six starting zones (Los Santos, Sandy Shores, Paleto Bay, Mount Chiliad, Fort Zancudo, Cayo Perico) with five climate profiles and smooth blending where regions meet.
- In-game zone editor (`/weather zones`) to draw, edit and delete regions on the map without touching files.
- Seasons that advance with the game calendar, follow the real-world month, or stay where you put them, each with its own temperature and snow profile.
- Christmas and Halloween presets toggled with one command (`/weather christmas`, `/weather halloween`).
- Temperature per location shaped by altitude, coast, time of day and weather, shown in °C or °F.
- Snow that builds up during snowfall and melts as it warms, with ground cover, vehicle tracks and footprints.
- Moving storm fronts that cross the map on a heading, up to eight at once, spawned automatically or by hand (`/weather front`).
- Lightning near the player, flickering lights, and region-wide power cuts with scheduled outages (`/weather blackout`).
- Fog and storm visibility effects that fade in and out and ease off at night and in snow.
- Severe weather alerts at three levels (advisory, watch, warning) worked out from each region's forecast and any storm heading its way.
- Weather app and home-screen widget for `oxide-tablet` with forecast, live radar and alerts pages, plus tablet notifications for new alerts.
- Weazel News weather report export for news roleplay.
- Live admin panel (`/weather`) covering conditions, forecast, radar, power, scenes and fronts in one window.
- Real-world weather mode (`/weather realworld`) that mirrors a real city's weather, temperature, wind and hourly forecast through Open-Meteo, with no API key needed and an option to follow the city's local time.
- Chat commands for the essentials: `/weather set`, `/weather time`, `/weather dynamic`, `/weather forecast`, `/weather season` and `/weather inspect`.
- Scene locks that freeze weather, time, wind, blackout and snow for one player or for everyone (`/weather lock`, `/weather unlock`).
- Shelter and exposure queries for other resources, worked out from drawn covered areas, an upward probe, interiors and vehicles.
- Road conditions on a 500 m grid: wetness after rain and ice below freezing, drying and thawing over time.
- Live settings editor (`/weather settings`) that changes settings in game and saves them to the database.
- Everything survives a restart: clock, weather, forecasts, regions, season, snow cover, storms and outages are saved to `oxide_weather_state`.
- Database tables are created automatically at first start; `sql/install.sql` ships for manual installs.
- Runs on QBCore, QBX and ESX through `o-link`, with the `weather` bridge namespace plus 62 server and 28 client exports for other scripts.
- English locale (`locales/en.json`) covering every notification, command, panel and app screen.
