# Changelog

## [1.0.3] - 2026-07-26

### Changed
- **Dashcam renderer files now ship inside the app bundle.** Internal packaging change for reliability — no gameplay or configuration changes.

## [1.0.2] - 2026-05-20

### Added
- **Localization support.** Every player-facing string (notifications, dashboard, remote controller, dashcam recorder, event feed) now loads from `locales/<lang>.json`. Copy `locales/en.json` to your locale code (e.g. `locales/de.json`), translate the values, and set `SetConvar('ox:locale', 'de')` to switch languages.
- **Packaged `screwdriver.png` item image.** Drop-in art for the default `Config.RemovalToolItem = 'screwdriver'` for servers whose inventory framework does not already ship a screwdriver image.

## [1.0.1] - 2026-05-13

### Added
- **Tablet animation for the dashcam watcher.** Officers visibly hold a tablet while watching a live dashcam feed instead of standing idle. Toggle with `Config.Dashcam.tabletAnimation` in `shared/config.lua`.

## [1.0.0] - 2026-05-11

### Added

- Initial public release of `oxide-baitcar`.
- Chip deployment flow: `baitcar_chip` inventory item, server-side validation that the officer is physically inside the target vehicle, automobile-only check, plate-uniqueness check, per-officer deployment cap (`Config.MaxDeploymentsPerOfficer`, default `3`), and `Config.InstallDurationMs` cancellable progress bar with the `mini@repair / fixing_a_ped` animation.
- Two mutually-exclusive operating modes selected by `Config.UseRemoteController`:
  - **Remote controller** (default): the `baitcar_remote` inventory item is the only entry point; `/baitcar` is not registered and the radial submenu is skipped.
  - **Dashboard**: `/baitcar` opens the Vue dashboard and the `o-link` radial provider gets a `Bait Car` root entry with `Open Manager` and `Watch Nearest Dashcam` actions.
- Vue 3 / Vite frontend serving both the dashboard and the remote controller views, plus a dashcam HUD overlay and the recorder bridge.
- Live dashcam viewer with a layered camera profile system (`Config.Dashcam.ByModel` > `Config.Dashcam.ByClass` > `Config.Dashcam.Default`), per-frame heading tracking, optional timecycle modifier for a CCTV look, and configurable exit keys.
- `/baitcardashcamtune` admin command gated on the `command` ACE: spawns a chosen vehicle, attaches the live dashcam, exposes WASD / QE / RF / arrow-key / scroll bindings (with `Shift` for fine mode), and copies a paste-ready `Config.Dashcam.<slot>.<key>` Lua snippet to the clipboard on commit.
- Remote actions, every one of them server-authoritative, statebag-synced, and audited: door lock / unlock, engine kill / restore, four steering modes (`off` / `lock` / `left` / `right`, using `SetVehicleSteerBias`), speed limiter at `Config.SpeedLimitMph`, headlight strobe at `Config.StrobeIntervalMs` cadence, vehicle alarm with auto-renewal across GTA's natural alarm expiry, and one-shot horn pulses driven by a nonce-bearing `oxide:baitcar:horn` statebag scoped to entity-aware clients.
- Drive-off trigger detection: configurable speed (`Config.TriggerSpeedMps`) and duration (`Config.TriggerDurationMs`) thresholds, anti-spoof check that the source is the actual driver, LEO immunity, one-shot per deployment, optional `Config.FakeWantedLevel` applied to the suspect, and `Config.Dispatch` payload forwarded to `olink.dispatch.CreateAlert`.
- Entry dispatch alert (`Config.NotifyOfficerOnEntry` + `Config.EntryDispatch`): broadcasts an `olink.dispatch.CreateAlert` payload to every responder in the configured job list the first time a non-LEO sits in the driver seat, with a per-plate 30-second rate-limit window. Lower-priority defaults than the trigger alert (`10-66` vs `10-16`) since the suspect may still walk away. Routes through `Config.DispatchJobs` like the trigger alert, so server-wide responders see entries — not just the deploying officer.
- Civilian counterplay: `Config.RemovalToolItem` (default `screwdriver`) is registered as a usable inventory item. Used outside a vehicle it silently no-ops; used inside any seat of any vehicle it runs a cancellable `Config.RemovalDurationMs` progress bar and a `Config.RemovalSuccessChance` roll. The server re-verifies the source is seated in the vehicle before processing (trust boundary against modded clients). The same ambiguous "found nothing" message fires for non-bait vehicles, failed rolls, not-in-vehicle rejects, and rate-limited calls so the item can't be used as a bait-car detector.
- Dashcam clip recording integrated with `oxide-police`: F8 hotkey (`Config.Recording.hotkey`) while the dashcam is open, configurable max duration / bitrate / thumbnail capture, Fivemanage or Discord upload selectable via `Config.Recording.provider`, and clips persisted into `police_bodycam_recordings` so they appear in the MDT Video Storage tab. Pipeline gated on `GetResourceState('oxide-police') == 'started'` and silently no-ops without `oxide-police`.
- Per-event audit log table `baitcar_events`: 20 logged event types covering install, every state toggle, trigger, civilian removal, decommission, and horn pulse. Indexed on `officer_charid`, `plate`, and `event_type`. Coords and arbitrary extras stored as JSON columns.
- Officer-only live event feed: every audit row is also broadcast as `oxide:baitcar:client:event` to clients whose job satisfies `Config.AllowedJobs`, powering the dashboard's real-time feed. Civilians never receive the broadcast.
- Officer-side map blips on every active deployment, configurable via `Config.OfficerBlip`. Blips auto-attach to vehicles, refresh on job-change and deployment-change events, and clear on resource stop.
- Per-source rate limiting (`server/ratelimit.lua`) on every civilian-facing endpoint: vehicle search (3 s window), entry notify (1.5 s), trigger (1.5 s), and the three video-upload callbacks (2–5 s windows).
- Structured logging through `olink.logger` for every gameplay path, with distinct categories for install, lock, engine, steering, speed limit, strobe, alarm, horn, trigger, entry, search, dispatch, recording, dashcam, dashcam tuner, statebag, radial, security, and errors.
- Hot-restart safe initialization: clients re-evaluate `BaitCar.IsAllowed()` immediately on resource start if the player is already loaded, instead of waiting for the next `olink:client:playerReady` (which only fires on initial character load).
- `o-link` capability fallbacks: every namespace used (`framework`, `character`, `job`, `notify`, `inventory`, `target`, `progressbar`, `menu`, `input`, `radial`, `dispatch`, `logger`) is checked via `olink.supports(...)` before use; missing capabilities log a warning and degrade gracefully instead of crashing.
