# Changelog

All notable changes to oxide-landscapingjob will be documented in this file.

## [1.8.0] - 2026-10-26

### Added
- **Shift summary** in `/landscaping settings` → General (`Config.shiftSummary`) now works. **Popup summary** (`'nui'`) opens a pay summary screen at clock-out with the yards completed, gross pay, co-op bonus, each deduction and the net pay, with either menu style. Nothing is sent to the phone. **Phone message** (`'text'`, the default) works as before: a net-pay notification, and the full summary on the phone as a text or mail when **Use phone** is on. A shift that ends any other way always gets the phone message.

### Changed
- The **Shift summary** option `'text'` is now called **Phone message**, and the help for **Use phone** and **Shift summary** says what each one does. Saved settings keep working.
- The `GetInfo` export reports `crew` as `false` when crews are switched off.

### Fixed
- **Max size** under Crews (`Config.Crew.maxSize`) now works: `1` allows only solo shifts, `2` allows a partner. A crew is always a leader and one partner, so the menu offers 1 or 2, and a saved size above 2 becomes 2 at the next start.
- The pay summary on the phone names the account the pay went to (`Config.Payment.payoutAccount`). It always said Bank. The tutorial no longer says pay goes to the bank.
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.

### Documentation
- `docs/CONFIGURATION.md`, `docs/FEATURES.md`, `docs/INSTALLATION.md` and `docs/TROUBLESHOOTING.md` describe `Config.shiftSummary` and `Config.Crew.maxSize`.

### Migration Guide
- No SQL to run. An edited or translated `locales/en.json` keeps working, and lines added in this version (the pop-up summary, the solo-shift message and the account names) show in English until you add them. To show the account name in the phone pay summary, copy `summary.net_pay` and the `account.*` lines from this version's file.

## [1.7.1] - 2026-10-23

### Fixed
- Daily streaks survive reconnects and restarts. A player who had already worked that day or the day before went back to a 1-day streak.
- On MySQL servers in strict mode, a player's progression could stop saving after they reconnected.
- A player whose saved job timeout is `0000-00-00 00:00:00` (left by earlier versions on some databases) can load their progression again.

## [1.7.0] - 2026-10-18

### Added
- Works with Oxide HUD (free): the yard card and tutorial cards can be moved or switched off in the `/hud` editor (needs o-link 1.12.1 or newer). The HUD hides while an admin uses the visual yard editor.
- `Config.UseHudAccent` (`shared/config.lua`, default on), config file only.

### Changed
- Crew members are each paid on their own level, tip chance and streak, with the co-op bonus on their own pay, and each sees their own pay per yard.
- Offices can be edited, reordered or deleted in `/landscaping settings` during shifts. Deleting one ends and pays its shifts.
- The job creates all its database tables itself; nothing to import.
- Refused admin and builder attempts are logged once per player, per action, per minute.

### Fixed
- Pay is no longer lost when a player disconnects, logs out or switches character mid-shift; it is paid at their next login. A shift lost to going down still pays nothing.
- A restart now pays every running shift and sends the summary. It used to pay nothing.
- Crew overpayment: a partner taking over, or a member rejoining, could be paid twice for the same yards.
- Saving offices during shifts reset the slot counts, so more players than `Config.MaxSlotsPerLocation` could clock in.
- A payment the framework refuses is kept for the next login.

## [1.6.0] - 2026-10-04

### Added
- Shift messages arrive as texts from the office on phones that support texts. Other phones get them as mail, as before. Texts need o-link 1.11.0 or newer.
- Gigs app support for Oxide Phone: the app shows the offices, the player's current shift (yards done) and pay so far, and their level. Shift updates appear in the app instead of as texts; the pay summary still arrives as a text.
- New read-only server exports `GetInfo`, `GetShift` and `GetProgression`, plus `SetUpdateHandler` so another resource, such as a phone app, can show shift updates itself.

### Changed
- The settings editor's remaining English text (the Admin badge, Move up, Move down, Remove, Place in world, Teleport here, and the empty-list and entry labels) now comes from `locales/en.json` and can be translated.
- The server log now records refused `/landscaping settings` attempts by non-admins, and whether each settings change came from the menu or another resource.

### Fixed
- `Config.usePhone` now works. Set it to `false` and the job sends nothing to the player's phone; the net-pay notification at clock-out still shows. Before, it was ignored.
- If a setting can't be saved to the database, the editor says it couldn't be saved and the old value stays. Before, the server used the new value even though it was never saved.
- A setting reset that fails now shows a message instead of doing nothing.
- Logging out with the settings editor open now closes it instead of leaving it on screen.

### Documentation
- `docs/EXPORTS.md`: `GetInfo`, `GetShift`, `GetProgression` and `SetUpdateHandler`.
- `docs/CONFIGURATION.md`, `docs/FEATURES.md`, `docs/INSTALLATION.md`, `docs/TROUBLESHOOTING.md`: shift messages as texts or mail, the Gigs app on Oxide Phone, o-link 1.11.0 for texts, Oxide Phone and qs-smartphone-pro in the supported phones, and the list of phone messages the job actually sends.
- The docs now say `Config.shiftSummary` has no effect in this job. It never changed how the pay summary is shown: the summary follows `Config.usePhone`, and the net-pay notification always shows.

### Migration Guide
- No SQL to run. Restart the server fully once after updating (new server file and a rebuilt settings UI).

## [1.5.0] - 2026-09-28

### Added
- `IsOnShift` server export: `exports['oxide-landscapingjob']:IsOnShift(source)` tells your other resources whether a player is on a landscaping shift, so they can stop someone working two jobs at once.

### Changed
- Missing-model warnings are now capped per player and in total, so one misbehaving client can't flood admins with notifications or fill the server log.

### Fixed
- Switched-off settings no longer turn themselves back on after a restart.
- If the database returned nothing at startup, saved settings could be overwritten with the config file defaults. Startup now only adds settings that are missing.
- Decimal settings no longer show rounding noise like `0.90000000000009` in the editor.
- Pressing ESC to cancel an in-world placement no longer closes the whole settings editor too.

### Documentation
- New `docs/EXPORTS.md` covering `IsOnShift`, `GetSetting` and `SetSetting`, with integration examples.

## [1.4.0] - 2026-08-05

### Added
- **Live in-game settings editor — `/landscaping settings`** — nearly every setting now lives in the database and is editable in-game by admins, applying live with no restart: pay, levels, timers, crew, complaints, vehicles and the mower, locations, NPCs and blips, markers, uniforms, props, animations and tutorials. Values are checked before saving and bad ones rejected with a reason. Yards themselves stay in `/moweryard` (they're too large for live sync); the menu says so.
- **Model swaps** — new Compatibility section (`Config.ModelSwaps`): replace any prop, ped or vehicle model with one from your own packs. Saving a swap rebuilds standing yard grass and obstacles live. If a player's game build is missing a model, the server now warns once naming the model and pointing at the settings menu — nothing is silently substituted.
- New `/landscaping` command: `/landscaping settings` (admin), `/landscaping tutorials`, `/landscaping tutorialreset`. The old commands still work.

### Changed
- **Config files are now factory defaults.** On the first start after updating, everything in the config files — including your customizations — is imported into the database automatically. From then on the database is the source of truth: editing the config files does nothing on an existing install; use `/landscaping settings`.
- A model missing from a player's game build is skipped instantly with a named warning instead of freezing the load and rendering nothing. Yard rendering in particular no longer stalls several seconds per grass patch when a model is unavailable.

### Fixed
- Changing a setting could break the clock-in menu camera (an extremely zoomed-in or empty view). Saved numbers now keep their exact values and previously saved settings repair themselves automatically on the next restart.

### Migration Guide
- No SQL to run — the settings table is created automatically on first start and your existing config values are imported. Restart the server fully once after updating (new files and a rebuilt UI).

## [1.3.0] - 2026-07-29

### Added
- Cancel Current Yard: while mowing, the crew leader — or a solo worker — can cancel the current yard from the office menu or by targeting the work van. The yard is forfeited (it can't be restarted that shift), the rest of the round continues, and if it was the last yard the worker is sent back to the office. Yards already finished still pay at clock-out. Crew members can't cancel; they clock out to leave, exactly as before
- `Config.Cancel.cooldownSeconds` in `shared/config/job.lua` (default 60) — how long the crew waits before starting the next yard after cancelling; applies to both crew members; set 0 to disable

### Changed
- Docs: the cancel feature is documented; location and yard configuration now have full field tables, plus an "adding a second office" guide that explains yards are a shared pool — a new office needs yards authored nearby to give local work

## [1.2.3] - 2026-07-07

### Changed
- Builder commands now use the standard admin permission — the old `admin.landscapingjob` permission is no longer needed, and standard QBCore/QBX/ESX admins pass automatically
- The crew invite dialog now opens through your server's configured input UI instead of always using ox_lib
- SQL files are now viewable in escrowed builds
- Admin docs updated to describe the standard admin setup; configuration docs clarified

### Fixed
- Complaint suspension timers could be misread when loaded from the database on newer database library versions; they now count down correctly

## [1.2.2] - 2026-05-19

### Added
- `Config.Payment.payoutAccount` and `Config.Payment.fineAccount` to choose which accounts shift pay is deposited to and vehicle fines are taken from (defaults `'bank'` / `'cash'`)
- `Config.Rumpo.livery` / `Config.Rumpo.extras` and `Config.Mower.livery` / `Config.Mower.extras` for replacement vehicle models

### Changed
- Documentation updated to cover all current config options, the tutorial system, and player keybinds (`G` for blades, `H` to highlight obstacles)

## [1.2.1] - 2026-05-13

### Added
- `Config.Blip.enabled` option to hide the office map blip
- Locale support — NUI text now reads from `locales/en.json` so the shift menu and summary can be translated
- oxide-logger integration for shifts, payouts, crew ownership transfers, admin commands, progression saves, and errors

## [1.2.0] - 2026-04-14

### Added
- SQL migration script (`sql/migrate_unified_progression.sql`) for migrating existing per-job progression tables into the unified `job_progression` table — run once across all Oxide job resources
- Client-side death monitor thread for framework-independent death and respawn detection

### Changed
- Migrated from `community_bridge` to `o-link` — all bridge calls, lifecycle events, and dependency references now use o-link APIs
- Progression data moved from `landscapingjob_progression` to shared `job_progression` table with composite `(char_id, job)` primary key
- Death/respawn handling replaced framework bridge events with resource-owned `oxide:landscapingjob:playerDied` / `oxide:landscapingjob:playerRespawned` server events
- Company van now initializes fuel to 100% on spawn
- Shift payout and vehicle fine transactions now include reason strings for audit trails
- `Config.uniformsEnabled` now defaults to `false`
- Documentation updated for o-link references

### Migration Guide
- Back up your database before updating
- Run `sql/migrate_unified_progression.sql` once before starting the updated resource
- This copies data from `landscapingjob_progression` into `job_progression` and removes the old table
- If you are updating multiple Oxide jobs that use this migration, only run the script once total

## [1.1.5] - 2026-04-05

### Fixed
- Crew member names now display character names instead of Steam names in the NUI and notifications
- Uniform system now snapshots appearance before applying the outfit, fixing cases where clothing bridges corrupted the restore data

## [1.1.4] - 2026-03-26

### Added
- Tutorial tooltip system with KVP persistence, queueing, and auto-dismiss timer
- 10 tutorial steps covering clock-in, mower usage, blades, obstacles, yard completion, timer expiry, vehicle damage, mower breakdown, level up, and crew join
- Tutorial toggle (`/landscapingtutorials`) and reset (`/landscapingtutorialreset`) commands
- Payment breakdown notifications on shift completion showing base pay, bonuses, and deductions

### Fixed
- Mower duplication when crew partner joins — crew members now adopt the existing networked mower instead of spawning a duplicate
- Mower duplication on rapid unload/load — added mutex guards to prevent concurrent state changes
- Mower duplication on load/unload cycle in crew — added ownership tracking so crew partners don't delete or re-spawn the leader's mower entity
- Crew partner mower state desync — partner's local state now updates immediately on unload sync with background entity resolution retry
- ox_target NPC zones persisting after resource restart — switched from sphere zones to entity-based targets matching the roadsideassistance pattern
- Grass patches blocking ox_target raycasts to obstacle rocks — disabled collision on grass prop entities
- Players could enter the cosmetic mower while attached to the truck roof — mower doors now locked for all players when on roof, unlocked when unloaded
- Players could load the mower while another player was driving it — added occupancy check before and after progress bar

## [1.1.3] - 2026-03-17

### Fixed
- Fixed race condition in clock-out callback that allowed duplicate shift payments when spamming the clock-out button — shift state is now cleared before payment processing

## [1.1.2] - 2026-03-09

### Added
- `Config.uniformsEnabled` option in `config/outfits.lua` to disable work uniforms entirely — set to `false` to hide the uniform button from both NUI and context menus and block all uniform server callbacks

## [1.1.1] - 2026-03-05

### Fixed
- Phone notifications now use email instead of SMS, fixing errors on phone resources (lb-phone, okokPhone, qb-phone, etc.)
- Players can no longer clock in when the vehicle spawn area is occupied by another player or vehicle

## [1.1.0] - 2026-03-02

### Added
- **NUI shift menu** — new full-screen Vue menu with progression card, stats grid, next-level preview, and action buttons. Togglable via `Config.menuStyle` (`'nui'` or `'menu'`), so you can keep the original context menu if preferred
- **Per-location menu camera** — when the NUI menu opens, a scripted sky camera with a gentle sway effect is shown. Configure camera position, heading, and FOV per location via the `menuCamera` field in `config/locations.lua`
- **Work uniform system** — players can equip/remove a landscaping uniform from the shift menu. Uniforms auto-revert on clock out, death, or disconnect. Customize outfits in `config/outfits.lua`
- **ID-based crew invites** — players now enter a server ID to invite a crew partner instead of auto-targeting the nearest player. Works in both NUI and context menu modes
- **Crew headshot display** — NUI menu shows player headshots with a leader crown indicator when in a crew

### Changed
- Crew invite range increased from 10m to 15m (`Config.Crew.inviteRange`)
- All NUI menu strings are now locale-driven — add or override them in `locales/en.json` under the `nui.*` keys
- Uniform apply/remove and crew invite response callbacks are now rate-limited server-side
- Player clothing is automatically reverted on disconnect if they were wearing a work uniform

## [1.0.1] - 2026-02-24

### Added
- **Locale system** — all player-facing strings (notifications, progress labels, menu text, level titles, blip labels) are now driven by locale files in `locales/` using `ox_lib` i18n, making the resource fully translatable
- **Split configuration files** — the monolithic `shared/config.lua` has been broken into focused domain files under `shared/config/`:
  - `config/job.lua` — Payment, levels, crew, timer, yard tiers, complaints
  - `config/vehicles.lua` — Company van and mower settings (blades, damage, particles, sound)
  - `config/mowing.lua` — Grass/obstacle props, detection settings, animations
  - `config/visuals.lua` — NPC, blips, markers, waypoints, yard editor
  - `config/locations.lua` — Office locations and yard definitions (~590 lines of coordinate data)
- **Mower breakdown repair-and-resume** — new `Config.Mower.allowFinishYardOnBreakdown` setting (default `true`) lets players repair a broken mower and continue the current yard instead of forfeiting it. The yard timer pauses during repair and resumes with the remaining time. A complaint is always filed on breakdown regardless of this setting

### Changed
- `shared/config.lua` now only initializes `Config = {}` and sets core toggles (`Debug`, `usePhone`, `MaxSlotsPerLocation`)
- `fxmanifest.lua` updated to load `shared/config/*.lua` between `config.lua` and `exported_locations.lua`
- Hardcoded UI strings in config replaced with locale keys (e.g. `'Trainee'` → `'level.trainee'`, `'Landscaping Office'` → `'blip.landscaping_office'`)
- Mower destruction now always issues a complaint (`Config.Complaints.vehicleDestroyPenalty`), previously it did not

## [1.0.0] - 2026-02-28

### Features
- Landscaping/mowing gig job with 10 hand-built yards across Los Santos
- 5-tier progression system (Trainee → Foreman) with increasing pay, speed, and tip chances
- Company van + mower vehicle system with roof rack attachment
- Mower blade toggle with speed limiting, particle effects, and 3D positional audio (xsound)
- Obstacle hazard system with mower damage and on-foot pickup
- Yard timer with speed bonus and expiry penalty
- Complaint/suspension system for vehicle destruction and death on shift
- 2-player crew co-op with invite system and bonus pay
- Visual yard editor (freecam) for building new yards in-game
- Coordinate builder admin commands for locations and yards
- Floating 3D waypoint markers above assigned yards
- Phone integration for shift updates via SMS/email (Community Bridge)
- Multi-framework support via Community Bridge (QBCore, ESX, QBox, etc.)
