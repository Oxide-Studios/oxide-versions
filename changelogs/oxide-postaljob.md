# Changelog

## [1.6.2] - 2026-10-26

### Changed
- In `/postaljob settings` → General, the **Shift summary** option that sends the summary to the phone is now called **Phone message**, and the help for **Use phone** and **Shift summary** says what each one does. Saved settings keep working.
- Removed three settings that did nothing: **Pickup time** and **Van proximity** under Pallet & boxes (`Config.Pallet.pickupTime`, `Config.Pallet.vanProximity`) and the mail bag's **Pickup time** (`Config.Letter.pickupTime`). A pickup takes as long as its pickup animation's `duration`.
- The `GetInfo` export reports `crew` as `false` when crews are switched off.

### Fixed
- **Max size** under Crews (`Config.Crew.maxSize`) now works: `1` allows only solo shifts, `2` allows a partner. A crew is always a leader and one partner, so the menu offers 1 or 2, and a saved size above 2 becomes 2 at the next start.
- **Spawn distance** under Delivery van (`Config.Vehicle.spawnDistance`) now works: clocking in is refused while another player or vehicle is this close to the van spawn point. It was always 5 m, the default.
- **Pallet proximity** (`Config.Pallet.palletProximity`) now sets how close a player must be to pick up a box. It was always 2.5 m, the default.
- The pay summary on the phone names the account the pay went to (`Config.Payment.payoutAccount`). It always said Bank. The tutorial no longer says pay goes to the bank.
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.

### Documentation
- `docs/CONFIGURATION.md`, `docs/FEATURES.md` and `docs/EXPORTS.md` cover the crew size, the spawn distance and the removed settings.

### Migration Guide
- No SQL to run. An edited or translated `locales/en.json` keeps working, and lines added in this version (the solo-shift message and the account names) show in English until you add them. To show the account name in the phone pay summary, copy `summary.net_pay` and the `account.*` lines from this version's file.

## [1.6.1] - 2026-10-23

### Fixed
- Daily streaks survive reconnects and restarts. A player who had already worked that day or the day before went back to a 1-day streak.
- On MySQL servers in strict mode, a player's progression could stop saving after they reconnected.
- A player whose saved job timeout is `0000-00-00 00:00:00` (left by earlier versions on some databases) can load their progression again.

## [1.6.0] - 2026-10-18

### Added
- Works with Oxide HUD (free): the delivery card and tutorial cards can be moved or switched off in `/hud` (needs o-link 1.12.1 or newer).
- `Config.UseHudAccent` (`shared/config.lua`, default on), config file only.
- **Fastest believable trip** in `/postaljob settings` → Payment (`Config.Payment.maxTravelSpeed`, 50 m/s).

### Changed
- Crew pay: each carrier is paid on their own level and streak, only for the deliveries credited to them.
- `Config.shiftSummary = 'nui'` shows the pop-up with either menu style and no longer also sends it to the phone. Shifts that end without a clock-out always get the text summary.
- Depots can be edited during shifts. Deleting one ends and pays its shifts.
- The job creates all its database tables itself; nothing to import.
- Refused admin and builder attempts are logged once per player, per action, per minute.

### Fixed
- Pay is no longer lost on a disconnect, logout or character switch; it is paid at the next login. Going down still forfeits it.
- A restart pays every shift, and one slow payment no longer leaves the rest unpaid.
- A leaving crew leader's deliveries were paid twice.
- A depot added only in the settings menu threw a server error on a package round. It now tells the player it has no parcel routes yet and warns in the console, naming the depot.
- `/postalbuilder` Import Config and Export All now keep each depot's menu camera.
- Saving depots during shifts reset the slot counts.
- A payment the framework refuses is kept for the next login.

## [1.5.0] - 2026-10-04

### Added
- `Config.usePhone` (default `true`), also under General in `/postaljob settings`. Turn it off to show shift messages as on-screen notifications instead, with a short net-pay notification at clock-out. Existing servers get it switched on automatically.
- Shift messages arrive as texts from the depot on phones that support texts. Other phones get them as mail, as before. Texts need o-link 1.11.0 or newer.
- Gigs app support for Oxide Phone: the app shows the depots, the player's current shift (loading or deliveries) and pay so far, and their level. Shift updates appear in the app instead of as texts; the pay summary still arrives as a text.
- New read-only server exports `GetInfo`, `GetShift` and `GetProgression`, plus `SetUpdateHandler` so another resource, such as a phone app, can show shift updates itself.

### Changed
- The settings editor's remaining English text (the Admin badge, Move up, Move down, Remove, Place in world, Teleport here, and the empty-list and entry labels) now comes from `locales/en.json` and can be translated.
- The server log now records refused `/postaljob settings` attempts by non-admins, and whether each settings change came from the menu or another resource.

### Fixed
- If a setting can't be saved to the database, the editor says it couldn't be saved and the old value stays. Before, the server used the new value even though it was never saved.
- A setting reset that fails now shows a message instead of doing nothing.
- Logging out with the settings editor open now closes it instead of leaving it on screen.

### Documentation
- `docs/EXPORTS.md`: `GetInfo`, `GetShift`, `GetProgression` and `SetUpdateHandler`.
- `docs/CONFIGURATION.md`, `docs/FEATURES.md`, `docs/INSTALLATION.md`, `docs/TROUBLESHOOTING.md`: `Config.usePhone`, shift messages as texts or mail, the Gigs app on Oxide Phone, o-link 1.11.0 for texts, Oxide Phone and qs-smartphone-pro in the supported phones, and the list of phone messages the job actually sends.

### Migration Guide
- No SQL to run. Restart the server fully once after updating (new server file and a rebuilt settings UI).

## [1.4.0] - 2026-09-28

### Added
- `IsOnShift` server export: `exports['oxide-postaljob']:IsOnShift(source)` tells your other resources whether a player is on a postal shift, so they can stop someone working two jobs at once.

### Changed
- Missing-model warnings are now capped per player and in total, so one misbehaving client can't flood admins with notifications or fill the server log.

### Removed
- `Config.Blip.label`: it had no effect. The depot blip always shows the depot's `name`.

### Fixed
- Switched-off settings no longer turn themselves back on after a restart.
- If the database returned nothing at startup, saved settings could be overwritten with the config file defaults. Startup now only adds settings that are missing.
- Decimal settings no longer show rounding noise like `0.90000000000009` in the editor.
- Pressing ESC to cancel an in-world placement no longer closes the whole settings editor too.

### Documentation
- New `docs/EXPORTS.md` covering `IsOnShift`, `GetSetting` and `SetSetting`, with integration examples.
- `docs/CONFIGURATION.md`: new "How Settings Work" section. Adding a depot now points to `/postaljob settings`, and the depot `name` field explains that plain text works.
- `docs/ADMIN.md`: `/postaljob settings` added to the command list. Builder exports only go in the config file before the resource's first start.
- `docs/TROUBLESHOOTING.md`: timer, cancel cooldown and debug logging fixes now point to `/postaljob settings`.

## [1.3.0] - 2026-08-05

### Added
- **Live in-game settings editor — `/postaljob settings`** — nearly every setting now lives in the database and is editable in-game by admins, applying live with no restart: pay, levels, timers, crew, complaints, vehicles, locations (with in-world placement previews), NPCs and blips, delivery markers, uniforms, props, animations and tutorials. Values are checked before saving and bad ones rejected with a reason.
- **Model swaps** — new Compatibility section (`Config.ModelSwaps`): replace any prop, ped or vehicle model with one from your own packs. If a player's game build is missing a model, the server warns once naming the model and pointing at the settings menu.
- **Custom mailbox models** — new `Config.PostBoxModels` and `Config.LetterBoxModels` lists: maps that replace the stock mailboxes can register their own props so the route builder's scanner finds them.
- New `/postaljob` command: `/postaljob settings` (admin), `/postaljob tutorials`, `/postaljob tutorialreset`. The old commands still work.

### Changed
- **Config files are now factory defaults.** On the first start after updating, everything in the config files — including your customizations — is imported into the database automatically. From then on the database is the source of truth: editing the config files does nothing on an existing install; use `/postaljob settings`.
- A model missing from a player's game build is skipped instantly with a named warning instead of freezing the load. The delivery-customer ped pool no longer burns a long timeout per missing ped, and a missing van model cleanly cancels the spawn instead of erroring mid-shift.

### Fixed
- Changing a setting could break the clock-in menu camera (an extremely zoomed-in or empty view). Saved numbers now keep their exact values and previously saved settings repair themselves automatically on the next restart.

### Migration Guide
- No SQL to run — the settings table is created automatically on first start and your existing config values are imported. Restart the server fully once after updating.

## [1.2.0] - 2026-07-29

### Added
- Cancel Current Round (crew-aware): while loading or delivering, the crew leader — or a solo worker — can cancel the round from the supervisor menu or by targeting the van. Both crew members return to the clocked-in state, the pickup pallet is cleaned up for everyone, and the van stays. Deliveries already completed still pay at clock-out. Crew members can't cancel; they clock out to leave, exactly as before
- `Config.Cancel.cooldownSeconds` in `shared/config/job.lua` (default 60) — how long the crew waits before starting a new round (or clocking in fresh) after cancelling. Tied to the character, so relogging doesn't skip it; set 0 to disable

### Changed
- Docs: the cancel feature is documented; a new "adding a second depot" guide with full field tables for depots, letter boxes and delivery zones; new troubleshooting entries; assorted corrections from the docs review

## [1.1.4] - 2026-07-07

### Changed
- `/postalbuilder` and its location export now use the standard admin permission — the old `admin.postaljob` permission is no longer needed, and standard QBCore/QBX/ESX admins pass automatically
- Admin docs updated to describe the standard two-line admin setup

### Fixed
- Complaint suspension timers could be misread when loaded from the database on newer database library versions; they now count down correctly

## [1.1.3] - 2026-06-12

### Changed
- Builder and crew invite dialogs now open through your server's configured input UI instead of always using ox_lib
- Documentation rewritten in plainer language for server owners

### Removed
- Removed config entries that had no effect in-game: `Config.CustomerReactions`, `Config.Animations.pickup`, and the unused letterbox scan settings in `Config.Letter`

## [1.1.2] - 2026-05-19

### Added
- `Config.Payment.payoutAccount` to choose which account net shift pay is deposited to (default `'bank'`)
- `Config.Payment.fineAccount` to choose which account the vehicle-destroy fine is taken from (default `'cash'`)
- `Config.Vehicle.livery` to set the spawn livery index on the delivery van (default `-1`, leave model default)
- `Config.Vehicle.extras` to force vehicle extras on or off when the van spawns (useful for custom replacement models)

## [1.1.1] - 2026-05-13

### Added
- `Config.Blip.enabled` option to hide the depot map blip
- Locale support — NUI text now reads from `locales/en.json` so the shift menu and summary can be translated
- oxide-logger integration for shifts, payouts, crew ownership transfers, admin commands, progression saves, and errors

## [1.1.0] - 2026-04-14

### Changed
- Migrated from community_bridge to o-link — all `Bridge.*` calls replaced with `olink.*` equivalents across client, server, and shared code
- Unified progression table — `postaljob_progression` merged into shared `job_progression` table with composite `(char_id, job)` primary key
- Vehicle spawning now uses `lib.requestModel()` + `CreateVehicle()` instead of the bridge utility
- Death monitoring replaced with client-side health polling, removing dependency on bridge death events
- `Config.uniformsEnabled` default changed from `true` to `false` (uniforms now opt-in)

### Removed
- Deleted deprecated `exported_locations.lua` file

### Migration (existing servers)
Run `sql/migrate_unified_progression.sql` **once** before updating. This migrates data from `postaljob_progression` into the unified `job_progression` table and drops the old table. Safe to run multiple times.

### Migration Guide
- Back up your database before updating
- Run `sql/migrate_unified_progression.sql` once before starting the updated resource
- This copies data from `postaljob_progression` into `job_progression` and removes the old table
- If you are updating multiple Oxide jobs that use this migration, only run the script once total

## [1.0.5] - 2026-04-05

### Fixed
- Crew member names now display character names (first + last) instead of Steam names in the NUI, notifications, and invite prompts
- Supervisor target zones no longer duplicate when the resource is restarted
- Uniform system now snapshots appearance before applying the outfit, fixing cases where clothing bridges (e.g. illenium-appearance) corrupted the restore data
- Removed dependency on custom `GetIsPlayerDowned` bridge function that prevented the resource from working with the original community_bridge

## [1.0.4] - 2026-03-18

### Fixed
- Fixed race condition in clock-out callback that allowed duplicate shift payments when spamming the clock-out button — shift state is now cleared before payment processing
- Added missing active shift validation on clock-out, preventing callbacks from executing without a valid shift

## [1.0.3] - 2026-03-16

### Added
- `Config.uniformsEnabled` option in `config/outfits.lua` to disable work uniforms entirely — set to `false` to hide the uniform button from both NUI and context menus and block all uniform server callbacks

## [1.0.2] - 2026-03-05

### Fixed
- Phone notifications now use email instead of SMS, fixing errors on phone resources (lb-phone, okokPhone, qb-phone, etc.)
- Players can no longer clock in when the vehicle spawn area is occupied by another player or vehicle
- Co-op partners now receive vehicle keys so they can open and close van doors
- Fixed carry prop occasionally failing to attach without blocking the delivery flow

### Changed
- Updated map blip icon and color

## [1.0.1] - 2026-03-03

### Fixed
- Fixed "Failed to load data" error when interacting with the GoPostal NPC on ESX, QBCore, and QBX frameworks
- Player progression now works correctly with all identifier formats (ESX license strings, QBCore citizenids)

### Changed
- `char_id` column type changed from `INT UNSIGNED` to `VARCHAR(60)` to support all framework identifier formats

### Migration (existing servers)
If you already have the `postaljob_progression` table from v1.0.0, run this SQL before updating:

```sql
ALTER TABLE postaljob_progression MODIFY char_id VARCHAR(60) NOT NULL;
```

New installs require no action — the updated `sql/install.sql` handles this automatically.
