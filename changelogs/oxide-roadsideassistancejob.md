# Changelog

## [1.7.2] - 2026-10-26

### Changed
- **Crew size** under Crew rules (`Config.Crew.maxSize`) can be set to `1` to allow only solo shifts. It was fixed at 2. The `GetInfo` export then reports `crew` as `false`.
- In `/roadside settings` → General, the **Shift summary style** option that sends the summary to the phone is now called **Phone message**, and the help for **Use phone messages** and **Shift summary style** says what each one does. Saved settings keep working.
- Removed the held prop under Fuel animation (`Config.FuelAnimation.prop`), which did nothing. The jerry can in hand is set under Tool props.

### Fixed
- **Spawn clearance** under Work truck (`Config.Vehicle.spawnDistance`) now works: clocking in is refused while another player or vehicle is this close to the truck spawn point. It was always 5 m, the default.
- The pop-up pay summary shows one percent sign in the gas fee and tax lines ("Gas Fee (5%)", not "Gas Fee (5%%)").
- The pay summary on the phone names the account the pay went to (`Config.Payment.payoutAccount`). It always said Bank.
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.

### Documentation
- `docs/CONFIGURATION.md`, `docs/FEATURES.md`, `docs/EXPORTS.md`, `docs/INSTALLATION.md` and `docs/TROUBLESHOOTING.md` use the new option names and cover the crew size.

### Migration Guide
- No SQL to run. An edited or translated `locales/en.json` keeps working, and lines added in this version (the solo-shift message and the account names) show in English until you add them. To show the account name in the phone pay summary, copy `summary.net_pay` and the `account.*` lines from this version's file.

## [1.7.1] - 2026-10-23

### Fixed
- Daily streaks survive reconnects and restarts. A player who had already worked that day or the day before went back to a 1-day streak.
- A player whose saved job timeout is `0000-00-00 00:00:00` (left by earlier versions on some databases) can load their progression again.

## [1.7.0] - 2026-10-18

### Added
- Works with Oxide HUD (free): job timer, incoming call card and tutorial pop-ups, placed in the `/hud` editor (needs o-link 1.12.1 or newer). A switched-off call card still rings.
- `Config.UseHudAccent` (`shared/config.lua`, default on), config file only.
- **Fastest believable trip** (`Config.Calls.maxTravelSpeed`, 50 m/s) in `/roadside settings` → Calls & Dispatch.
- The **Tow delivery point** can be cleared in `/roadside settings` (the × button). Towed cars then go to each worker's own tow yard. Reset to default brings the point back.

### Changed
- Owed pay after a disconnect, logout or character switch is paid at the next login, once only. Logging out now ends the shift, and a crew partner keeps the truck and the call.
- Each crew member is paid from their own record of the shift (own level, tips and streak, co-op bonus on own pay). The Oxide Phone Gigs app shows each partner's own pay.
- Shifts that end without a clock-out send the text summary in either summary mode, including a restart.
- Refused admin attempts are logged once per player, per action, per minute.

### Fixed
- A restart pays every player on shift.
- Editing Tow yards during shifts no longer breaks shifts. Deleting a yard ends and pays its shifts and tells the players.
- The pop-up summary opens with `Config.menuStyle = 'menu'`.
- A payment the framework refuses is kept for the next login.

## [1.6.1] - 2026-10-07

### Fixed
- Stowing the flatbed winch no longer makes nearby ropes from other resources, such as a water hose, disappear.

### Migration Guide
- No SQL or config changes are required. Restart oxide-roadsideassistancejob after updating.

## [1.6.0] - 2026-10-04

### Added
- Shift messages arrive as texts from the tow yard on phones that support texts. Other phones get them as mail, as before.
- An incoming call also rings the player's phone (the crew leader's in a crew). Accept or decline it on the call card as before.
- Texts and call rings need o-link 1.11.0 or newer and `Config.usePhone` on.
- Gigs app support for Oxide Phone: the app shows the tow yards, the player's current call and pay so far, and their level. Shift updates and incoming calls appear in the app instead of as texts and phone notifications; the pay summary still arrives as a text.
- New read-only server exports `GetInfo`, `GetShift` and `GetProgression`, plus `SetUpdateHandler` so another resource, such as a phone app, can show shift updates itself.

### Changed
- The settings editor's remaining English text (the Admin badge, Move up, Move down, Remove, Place in world, Teleport here, and the empty-list and entry labels) now comes from `locales/en.json` and can be translated.
- The server log now records refused `/roadside settings` attempts by non-admins, and whether each settings change came from the menu or another resource.

### Fixed
- If a setting can't be saved to the database, the editor says it couldn't be saved and the old value stays. Before, the server used the new value even though it was never saved.
- A setting reset that fails now shows a message instead of doing nothing.
- Logging out with the settings editor open now closes it instead of leaving it on screen.

### Documentation
- `docs/EXPORTS.md`: `GetInfo`, `GetShift`, `GetProgression` and `SetUpdateHandler`.
- `docs/CONFIGURATION.md`, `docs/FEATURES.md`, `docs/INSTALLATION.md`, `docs/TROUBLESHOOTING.md`: shift messages as texts or mail, the incoming-call ring, the Gigs app on Oxide Phone, o-link 1.11.0 for texts and the ring, Oxide Phone and qs-smartphone-pro in the supported phones, and the list of phone messages the job actually sends.

### Migration Guide
- No SQL to run. Restart the server fully once after updating (new server file and a rebuilt settings UI).

## [1.5.0] - 2026-09-28

### Added
- `IsOnShift` server export: `exports['oxide-roadsideassistancejob']:IsOnShift(source)` tells your other resources whether a player is on a roadside assistance shift, so they can stop someone working two jobs at once.

### Changed
- Missing-model warnings are now capped per player and in total, so one misbehaving client can't flood admins with notifications or fill the server log.

### Removed
- `Config.Blip.label`: it had no effect. The tow yard blip always shows the tow yard's `name`.

### Fixed
- **Dying on shift went unnoticed.** The shift now ends, earned pay is paid out, the work uniform comes off and the death complaint is filed. A crew partner still on shift keeps the truck and the current call. On QBCore and QBX, being downed counts too.
- Switched-off settings no longer turn themselves back on after a restart.
- If the database returned nothing at startup, saved settings could be overwritten with the config file defaults. Startup now only adds settings that are missing.
- Decimal settings no longer show rounding noise like `0.90000000000009` in the editor.
- Pressing ESC to cancel an in-world placement no longer closes the whole settings editor too.

### Documentation
- New `docs/EXPORTS.md` covering `IsOnShift`, `GetSetting` and `SetSetting`, with integration examples.

## [1.4.1] - 2026-08-24

### Fixed
- Safety cones could not be placed — taking a cone off the truck never showed the placement preview, and the worker could be left stuck carrying the cone. Placement works again in both tow truck and flatbed modes.
- Flatbed: returning a cone to the truck put it at the tow truck's storage spot on the chassis instead of the flatbed's cone stack. Returned cones now sit on the bed and ride along when it raises or lowers.

## [1.4.0] - 2026-08-05

### Added
- **Model swaps** — new Compatibility section in `/roadside settings` (`Config.ModelSwaps`): replace any prop, ped or vehicle model with one from your own packs — including server-spawned ones like the tow truck and stranded vehicles. If a player's game build is missing a model, the server warns once naming the model; the bundled flatbed models get their own "install is incomplete" message instead (`Config.ModelHints`).

### Changed
- A model missing from a player's game build is skipped instantly with a named warning instead of freezing the load. A missing ped in the stranded-driver pool no longer randomly leaves a call with a driverless car.

### Fixed
- Settings added by resource updates now appear on existing installs automatically — previously a new setting could silently not exist until the whole section was reset.
- Changing a setting could break the dispatcher menu camera (an extremely zoomed-in or empty view). Saved numbers now keep their exact values and previously saved settings repair themselves automatically on the next restart.

### Migration Guide
- No SQL to run. Restart the server fully once after updating.

## [1.3.0] - 2026-07-29

### Added
- Cancel Current Task: while on an accepted call, the crew leader — or a solo worker — can cancel it from the dispatcher menu or by targeting the work truck. The scene (stranded vehicle, driver, cones) cleans up for both crew members, the winch stows, a lowered flatbed bed raises itself, and the truck stays; the worker goes back to waiting for calls. Cancelling doesn't count as a missed call, and calls already completed still pay at clock-out. Crew members can't cancel; they clock out to leave, exactly as before
- `Config.Calls.cancelCooldownSeconds` (default 60) — the next call is dispatched only after this wait. Also editable live in `/roadside settings` under Calls & Dispatch → "Cancel cooldown"; set 0 to disable

### Changed
- Docs: the cancel feature is documented, and the "add a tow yard" instructions now describe the correct path — tow yards are added live in-game through `/roadside settings` (editing the config file on a live server has no effect once settings live in the database)

## [1.2.1] - 2026-07-27

### Fixed
- Placing the dispatcher NPC with the settings editor's Place button stored the position too low, sinking the NPC into the ground — placed NPCs now stand at the right height.
- Emptying a list in `/roadside settings` no longer breaks the editor.

## [1.2.0] - 2026-07-07

### Added
- Flatbed towing mode: `Config.Vehicle.mode` in `shared/config/vehicles.lua` selects `'flatbed'` (new default) or `'towtruck'`. The flatbed is a custom streamed truck with an animated tilting bed and a hands-on winch — lower the bed, carry the hook to the stranded car, attach it, winch it aboard, raise the bed and deliver
- Flatbed geometry, winch and timing options in the new `shared/config/flatbed.lua`
- In-game settings editor: `/roadside settings` (admin only) opens a live editor covering nearly every option — payments, levels, calls, vehicles, tow yards, breakdown scenes, NPCs, uniforms and more. Changes save to the database, apply immediately for everyone, and every setting can be reset to its config-file default. Coordinates can be placed in-world with a ghost preview (requires an updated o-link)
- `Config.Calls.enabledJobTypes` in `shared/config/job.lua` — restrict dispatch calls to specific job types (fuel, wheel, towing, battery)
- `Config.Calls.deliveredVehicleDespawn` — delivered vehicles now fade out smoothly after a configurable delay instead of vanishing
- `Config.StrandedMarker` — the stranded-vehicle indicator is now a name card shown while holding H nearby, replacing the always-on floating marker
- A flatbed-specific tutorial card for the first towing job in flatbed mode
- `THIRDPARTY-LICENSES.md` crediting the MIT-licensed flatbed model

### Changed
- Settings now live in the database: on first boot the resource imports your current config values into a new `oxide_settings` table, which becomes the source of truth from then on. Editing config files afterward only supplies defaults for brand-new options — use `/roadside settings` for everything else
- `/roadsidetutorials` and `/roadsidetutorialreset` are now `/roadside tutorials` and `/roadside tutorialreset`
- The `/roadsidebuilder` command and its `admin.roadsidejob` permission were removed — tow yards and breakdown scenes are now edited live in `/roadside settings`
- The work truck is now managed by the server: it survives a crew member crashing or disconnecting, transfers to the crew partner, and is reliably cleaned up on clock-out, respawn, disconnect and resource restart

### Fixed
- Complaint suspension timers could be misread when loaded from the database on newer database library versions; they now count down correctly
- Job-completion rejections (rate limited, not on shift, too far away) now show an error notification instead of failing silently

### Documentation
- All docs rewritten: new Settings Editor guide in `docs/ADMIN.md`, new Flatbed, Winch and Stranded Vehicle Marker sections in `docs/CONFIGURATION.md`, builder sections removed

### Migration Guide
- No SQL to run: the new `oxide_settings` table is created automatically at boot (`sql/install.sql` was updated for fresh installs), and your existing customized config values are imported as the starting settings
- Flatbed mode is the new default. To keep the classic tow truck, set `Config.Vehicle.mode = 'towtruck'` in `shared/config/vehicles.lua` before your first boot on this version, or change it afterward in `/roadside settings`

## [1.1.2] - 2026-05-19

### Added
- `Config.Payment.payoutAccount` and `Config.Payment.fineAccount` — choose which account shift payouts deposit into and which account the vehicle-destroy fine is taken from
- `Config.Vehicle.livery` and `Config.Vehicle.extras` — set a livery index and toggle extras on the tow truck (useful for custom replacement models)

### Documentation
- `CONFIGURATION.md` now documents `shared/config/outfits.lua`, the new payment/vehicle options, and `Config.Blip.enabled`
- `FEATURES.md` covers the optional uniform system
- `ADMIN.md` lists the `H` waypoint key binding
- `INSTALLATION.md` references the full set of config files

## [1.1.1] - 2026-05-13

### Added
- `Config.Blip.enabled` option to hide the office map blip
- Locale support — NUI text now reads from `locales/en.json` so the shift menu, summary, and call notification can be translated
- oxide-logger integration for shifts, payouts, call ownership transfers, auto-clockouts, progression saves, and errors

## [1.1.0] - 2026-04-14

### Added
- Configurable outfit system — opt-in uniforms for male/female with automatic revert on clock-out, respawn, and disconnect (`shared/config/outfits.lua`)
- Tow truck now spawns with full fuel

### Changed
- Migrated progression data from `roadsideassistancejob_progression` to unified `job_progression` table (migration script: `sql/migrate_unified_progression.sql`)
- Refactored shift finalization into reusable `RoadsideFinalizeShift()` function with proper partner handling during auto-clockout
- Centralized auto-clockout logic in `RoadsideHandleAutoClockOut()` — removed duplicate code from call rejection and missed call handlers

### Fixed
- Tow vehicle reattachment no longer breaks mission flow when detaching and re-hooking mid-tow
- Vehicle spawn failure during clock-in now properly aborts shift and notifies the player instead of silently failing
- Clock-out now validates server response and displays error notification on failure
- Partner damage charges and breakdowns now correctly applied during simultaneous crew finalization

### Migration Guide
- Back up your database before updating
- Run `sql/migrate_unified_progression.sql` once before starting the updated resource
- This copies data from `roadsideassistancejob_progression` into `job_progression` and removes the old table
- If you are updating multiple Oxide jobs that use this migration, only run the script once total

## [1.0.2] - 2026-04-05

### Fixed
- Crew member names now display character names instead of Steam names in the NUI and notifications

## [1.0.1] - 2026-03-26

### Changed
- Mission timers are now server-authoritative and distance-based — replaced hardcoded `missionTime` per event with dynamic calculation using `Config.Timer.baseTime + distance * Config.Timer.timePerDistanceUnit`
- Removed `extraTime` config option — timer is now a single computed value
- Removed `missionTime` field from all event definitions in `shared/config/events.lua`

### Fixed
- Towed vehicle freezing permanently when detached mid-tow — removed the re-freeze on detach outside delivery zone so players can re-hook and continue the mission

## [1.0.0] - 2026-03-19

### Added
- Initial release of the roadside assistance gig job
- 4 service types: fuel help, wheel change, battery replacement, and vehicle towing
- 40 preconfigured events across all job types (10 fuel, 10 wheel, 10 battery, 10 towing)
- Call dispatch system with configurable intervals (15-45s) and 30-second response window
- 5-tier progression system (Trainee, Mechanic, Senior Technician, Team Lead, Master Technician) with persistent database tracking
- 2-player crew co-op system with shared calls, vehicle sharing, and 15% co-op pay bonus
- Safety cone system with 3 placeable cones, ghost preview placement, tiered speed zones, and per-cone pay bonus
- Props system with jack, spare wheel, jerry can, and battery — truck-mounted with third-eye take/return interactions
- Stranded NPC behavior with exit animations, proximity-based animation cycling, and impatient reactions
- Breakdown visual effects: hazard lights, cosmetic damage, engine smoke, flat tires
- Payment system with base pay, distance bonus, speed bonus, cone bonus, level multiplier, streak bonus, tips, gas fees, and taxes
- Complaint system with vehicle destruction and death penalties, 30-minute timeout after 3 strikes, complaint decay per completed job
- NUI frontend with mission timer, incoming call notification, shift menu with cinematic camera, shift summary screen, and tutorial tooltips
- 3D floating waypoint system (hold H to show) with distance-based scaling
- Dual menu style support: NUI (cinematic camera, crew headshots) and Bridge.Menu (lightweight context menu)
- Phone integration via community_bridge for shift emails and notifications
- Admin builder tool (`/roadsidebuilder`) for creating tow yard locations and job events
- Tutorial commands (`/roadsidetutorials` toggle, `/roadsidetutorialreset` reset)
- Database table `roadsideassistancejob_progression` for persistent progression
- English locale (`locales/en.json`)
