# Changelog

All notable changes to oxide-newspaperjob will be documented in this file.

## [1.5.2] - 2026-10-26

### Changed
- In `/newspaper settings` → General, the **Shift summary** option that shows the summary on screen is now called **Notification**, and the help for **Use phone** and **Shift summary** says what each one does. Saved settings keep working.
- Removed **Spawn distance** (`Config.Vehicle.spawnDistance`), which did nothing.

### Fixed
- The pay summary names the accounts the pay and tips went to (`Config.Payment.payoutAccount` and `Config.Payment.tipAccount`). It always said Bank and Cash. This covers the clock-out notification, the pop-up and the pay summary sent to the phone.
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.

### Migration Guide
- No SQL to run. An edited or translated `locales/en.json` keeps working, and lines added in this version show in English until you add them. To show the account name in the pay summary, copy `notify.clocked_out_deposited`, `notify.clocked_out_tips`, `nui.net_pay`, `nui.tips_cash`, `summary.email_net`, `summary.email_tips` and the `account.*` lines from this version's file.

## [1.5.1] - 2026-10-23

### Fixed
- Daily streaks survive reconnects and restarts. A player who had already worked that day or the day before went back to a 1-day streak.
- Progression no longer stops saving. The job timeout and the streak date were written back as numbers, which strict-mode MySQL refuses and other servers store as a zero date that made every later save fail.
- A player whose saved job timeout is `0000-00-00 00:00:00` can load their progression again.

## [1.5.0] - 2026-10-18

### Added
- Works with Oxide HUD (free): the route card and tutorial cards can be moved or switched off in `/hud` (needs o-link 1.12.1 or newer).
- `Config.UseHudAccent` (`shared/config.lua`, default on), config file only.
- **Fastest believable trip** in `/newspaper settings` → Payment (`Config.Payment.maxTravelSpeed`, 50 m/s).

### Changed
- Depots can be edited during shifts. Deleting one ends and pays its shifts.
- The job creates all its database tables itself; nothing to import.
- Refused admin and builder attempts are logged once per player, per action, per minute.

### Fixed
- Pay is no longer lost on a disconnect, logout or character switch; it is paid at the next login.
- A restart now pays every running shift and sends the pay stub. It used to pay nothing.
- A payment the framework refuses (including at the payout after a hospital respawn) is kept for the next login.
- Running out of papers showed the raw text `error.out_of_ammo`; it now shows a proper message.

## [1.4.0] - 2026-10-04

### Added
- Shift messages arrive as texts from the depot on phones that support texts. Other phones get them as mail, as before. Texts need o-link 1.11.0 or newer.
- Gigs app support for Oxide Phone: the app shows the depots, the player's current route and pay so far, and their level. Shift updates appear in the app instead of as texts; the pay stub still arrives as a text.
- New read-only server exports `GetInfo`, `GetShift` and `GetProgression`, plus `SetUpdateHandler` so another resource, such as a phone app, can show shift updates itself.

### Changed
- The settings editor's remaining English text (the Admin badge, Move up, Move down, Remove, Place in world, Teleport here, and the empty-list and entry labels) now comes from `locales/en.json` and can be translated.
- The server log now records refused `/newspaper settings` attempts by non-admins, and whether each settings change came from the menu or another resource.

### Fixed
- If a setting can't be saved to the database, the editor says it couldn't be saved and the old value stays. Before, the server used the new value even though it was never saved.
- A setting reset that fails now shows a message instead of doing nothing.
- Logging out with the settings editor open now closes it instead of leaving it on screen.

### Documentation
- `docs/EXPORTS.md`: `GetInfo`, `GetShift`, `GetProgression` and `SetUpdateHandler`.
- `docs/CONFIGURATION.md`, `docs/FEATURES.md`, `docs/INSTALLATION.md`, `docs/TROUBLESHOOTING.md`: shift messages as texts or mail, the Gigs app on Oxide Phone, o-link 1.11.0 for texts, Oxide Phone and qs-smartphone-pro in the supported phones, and the list of phone messages the job actually sends.

### Migration Guide
- No SQL to run. Restart the server fully once after updating (new server file and a rebuilt settings UI).

## [1.3.0] - 2026-09-28

### Added
- `IsOnShift` server export: `exports['oxide-newspaperjob']:IsOnShift(source)` tells your other resources whether a player is on a newspaper shift, so they can stop someone working two jobs at once.
- Paste-ready ox_inventory entry for the newspaper weapon in `itemimages/ox_inventory_weapons.lua`.

### Changed
- Missing-model warnings are now capped per player and in total, so one misbehaving client can't flood admins with notifications or fill the server log.

### Fixed
- Switched-off settings no longer turn themselves back on after a restart.
- If the database returned nothing at startup, saved settings could be overwritten with the config file defaults. Startup now only adds settings that are missing.
- Decimal settings no longer show rounding noise like `0.90000000000009` in the editor.
- Pressing ESC to cancel an in-world placement no longer closes the whole settings editor too.

### Documentation
- New `docs/EXPORTS.md` covering `IsOnShift`, `GetSetting` and `SetSetting`, with integration examples.
- `docs/CONFIGURATION.md`: new "How Settings Work" section. Depots and houses now point to `/newspaper settings`, and `/newspaperbuilder` only captures coordinates into an export file.
- `docs/ADMIN.md`, `docs/TROUBLESHOOTING.md`: builder exports and the uniform toggle now point to `/newspaper settings`.
- `docs/INSTALLATION.md`: ox_inventory step now points to `itemimages/ox_inventory_weapons.lua`.

## [1.2.0] - 2026-08-05

### Added
- **Live in-game settings editor — `/newspaper settings`** — nearly every setting now lives in the database and is editable in-game by admins, applying live with no restart: pay, levels, timers, throwing, supplies, the bike, depots and houses (with in-world placement previews), NPCs and blips, uniforms, the paper bag, animations and tutorials. Values are checked before saving and bad ones rejected with a reason.
- **Model swaps** — new Compatibility section (`Config.ModelSwaps`): replace any prop, ped or vehicle model with one from your own packs. If a player's game build is missing a model, the server warns once naming the model and pointing at the settings menu.
- New `/newspaper` command: `/newspaper settings` (admin), `/newspaper tutorials`, `/newspaper tutorialreset`. The old commands still work.

### Changed
- **Config files are now factory defaults.** On the first start after updating, everything in the config files — including your customizations — is imported into the database automatically. From then on the database is the source of truth: editing the config files does nothing on an existing install; use `/newspaper settings`.
- The throwable paper is configured as `Config.Throw.weaponName` (default `'WEAPON_ACIDPACKAGE'`, needs game build 2802+). On older builds the route now runs normally with a clear owner-facing warning instead of erroring — throws simply don't register until the weapon exists or is swapped. Unused keys `pollInterval`, `maxPollTime` and `minThrowCooldown` were removed.
- A model missing from a player's game build is skipped instantly with a named warning instead of freezing the load. A missing bike model now cleanly cancels clocking in instead of aborting mid-way and leaving the player half clocked in.

### Fixed
- Changing a setting could break the shift menu camera (an extremely zoomed-in or empty view). Saved numbers now keep their exact values and previously saved settings repair themselves automatically on the next restart.

### Documentation
- `docs/CONFIGURATION.md`: `Config.Throw` updated for `weaponName` and the removed keys.

### Migration Guide
- No SQL to run — the settings table is created automatically on first start and your existing config values are imported. Restart the server fully once after updating.
- If you customized `Config.Throw.weaponHash`, move the value to `weaponName` as a weapon name string.

## [1.1.0] - 2026-07-29

### Added
- Cancel Current Task: while on a route, cancel it from the shift menu or by targeting the delivery bike — a confirmation prompt appears, the route is forfeited, and you stay clocked in with your bike. Houses already delivered still pay at clock-out
- `Config.Cancel.cooldownSeconds` in `shared/config/job.lua` (default 60) — how long a player waits before requesting a new route after cancelling. The wait is tied to the character, so relogging doesn't skip it; set 0 to disable

### Changed
- `/newspaperbuilder` and its location export now use the standard admin permission — the old `admin.newspaperjob` permission is no longer needed, and standard QBCore/QBX/ESX admins pass automatically (the chat hint no longer names the old permission)
- Docs: the cancel feature is documented; depot configuration now has a field-by-field guide and an "adding another depot" walkthrough; new troubleshooting entries for stuck routes and the cancel cooldown; corrected the builder permission docs, o-link module list, and shipped depot/zone counts

## [1.0.3] - 2026-05-19

### Added
- `Config.Vehicle.livery` and `Config.Vehicle.extras` options for customizing replacement bike models

### Fixed
- Configuration reference now documents `Config.Item.weapon`, the payout/tip/fine account fields, the newspaper bag prop, and the pickup animation settings
- Corrected depot blip sprite default and shipped depot/zone counts in the documentation

## [1.0.2] - 2026-05-13

### Added
- `Config.Blip.enabled` option to hide the depot map blip

## [1.0.1] - 2026-05-09

### Added
- Newspaper bag prop now attaches to the player's back when a route is picked up and detaches when the shift ends or the route is cleared
- Structured logging across shifts, deliveries, progression, and the depot builder via `o-link.logger` for easier troubleshooting and audit trails
- Server-side validation when a player reports running out of newspapers, preventing false out-of-ammo events while papers are still in inventory

### Changed
- Ammo system rewritten to track a per-shift ammo budget — players can refill the newspaper weapon up to the amount the server issued, eliminating transient "out of ammo" misreports
- Delivery blip label now uses the localized text from `locales/en.json`

### Fixed
- Edge cases where the ammo monitor could trigger a false suspension while the player still had newspaper items to reload

## [1.0.0] - 2026-04-16

### Added
- Vue 3 + Vite + Tailwind frontend with shift menu, route HUD, ammo display, shift summary, tutorial overlays, and waypoint overlay
- Route-based delivery flow: players pick up a generated house route at the depot, ride a company BMX, and throw newspapers at marked house targets
- Throwable delivery mechanic using `weapon_newspaper` with limited supply and configurable spare papers for recovering from misses
- 5-tier progression system (Paper Carrier through Route Legend) with persistent database tracking via unified `job_progression` table
- Route timer system with per-house time limits, speed bonus for fast completion, and penalty for late routes
- Detailed pay breakdown: base pay, distance bonus, streak bonus, tips, speed bonus, and tax deductions
- Daily streak bonus system for consecutive shift days
- Complaint and suspension system for bike loss, death on shift, and running out of newspapers mid-route
- Bike loss handling with cash fine, complaint filing, route clearing, and forced return to depot flow
- Uniform system wired through `o-link.clothing` (disabled by default in config)
- NUI shift menu with cinematic camera and sway animation per location
- `olink.menu` fallback context menu for servers preferring simplicity
- Shift summary delivered via NUI popup or notification text (configurable via `Config.shiftSummary`)
- Phone integration for shift event notifications via `o-link.phone` (configurable via `Config.usePhone`)
- 9-step tutorial system with KVP persistence covering route pickup, throwing, misses, ammo management, late routes, and leveling
- Player commands: `/newspapertutorials` (toggle), `/newspapertutorialreset` (reset progress)
- Admin depot builder: `/newspaperbuilder` with full menu-driven workflow and config export/import support
- Server-side anti-teleport validation based on delivery distance
- Rate limiting on all server callbacks and events
- Automatic cleanup on disconnect, character unload, death, and resource restart
- 2 pre-configured depots with 80 delivery zones
- Localization support via `locales/en.json`
