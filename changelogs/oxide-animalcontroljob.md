# Changelog

## [1.7.3] - 2026-10-26

### Changed
- In `/animalcontrol settings` → General, the **Shift summary** option that sends the summary to the phone is now called **Phone message**, and the help for **Use phone** and **Shift summary** says what each one does. Saved settings keep working.

### Fixed
- The pop-up pay summary shows one percent sign in the gas fee and tax lines ("Gas Fee (5%)", not "Gas Fee (5%%)").
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.

## [1.7.2] - 2026-10-23

### Fixed
- Daily streaks survive reconnects and restarts. A player who had already worked that day or the day before went back to a 1-day streak.
- A player whose saved job timeout is `0000-00-00 00:00:00` (left by earlier versions on some databases) can load their progression again.

## [1.7.1] - 2026-10-19

### Fixed
- The tranquilizer gun shows its proper model in players' hands.
- Darts load into the tranquilizer gun with ox_inventory when you press R or use a dart from the inventory.

### Documentation
- `docs/CONFIGURATION.md`: the default package has 12 aggressive animal events, not 13.

### Migration Guide
- No SQL to run and nothing to change in your inventory files. Restart the server fully once after updating so players load the updated gun data.

## [1.7.0] - 2026-10-18

### Added
- Works with Oxide HUD (free): players can move or switch off the job timer, the incoming call card and the tutorial cards in the `/hud` editor. They hide with the HUD. Needs o-link 1.12.1 or newer; without Oxide HUD nothing changes. A switched-off call card still rings and still answers to E and Backspace.
- `Config.UseHudAccent` (`shared/config.lua`, default on): tutorial cards take the server's accent colour from Oxide HUD. Config file only.
- **Fastest believable trip** in `/animalcontrol settings` → Calls & Dispatch (`Config.Calls.maxTravelSpeed`, 50 m/s): a call reached faster than that pays no distance or speed bonus.

### Changed
- Pay for a player who disconnects, logs out or switches character on shift is kept and paid a few seconds after they next load in, once only. Logging out or switching character now ends the shift, and a crew partner keeps the truck, the call and the animal.
- Crew members are each paid on their own level, tip chance and streak, with the 15% co-op bonus on their own pay.
- With `Config.shiftSummary = 'nui'`, a shift that ends any way other than a clock-out sends the text summary. None is sent after a disconnect or logout.
- The job creates all its database tables itself; nothing to import.
- Refused admin attempts are logged once per player, per action, per minute.

### Fixed
- A restart now pays every player on shift.
- Editing Offices during shifts no longer resets slot counts or breaks shifts. Deleting an office ends and pays its shifts and tells the players.
- The pop-up summary opens with `Config.menuStyle = 'menu'`.
- A payment the framework refuses is kept and paid at the next login instead of being lost.
- Bosnian: the settings menu, the command help, and the owed-pay and office messages are now translated.

## [1.6.0] - 2026-10-04

### Added
- Shift messages arrive as texts from the office on phones that support texts. Other phones get them as mail, as before.
- An incoming call also rings the player's phone (the crew leader's in a crew). Accept or decline it on the call card as before.
- Texts and call rings need o-link 1.11.0 or newer and `Config.usePhone` on.
- Gigs app support for Oxide Phone: the app shows the offices, the player's current call and pay so far, and their level. Shift updates and incoming calls appear in the app instead of as texts and phone notifications; the pay summary still arrives as a text.
- New read-only server exports `GetInfo`, `GetShift` and `GetProgression`, plus `SetUpdateHandler` so another resource, such as a phone app, can show shift updates itself.

### Changed
- The settings editor's remaining English text (the Admin badge, Move up, Move down, Remove, Place in world, Teleport here, and the empty-list and entry labels) now comes from `locales/en.json` and can be translated.
- The server log now records refused `/animalcontrol settings` attempts by non-admins, and whether each settings change came from the menu or another resource.

### Fixed
- If a setting can't be saved to the database, the editor says it couldn't be saved and the old value stays. Before, the server used the new value even though it was never saved.
- When the settings editor refuses a change, it now shows the actual reason. Before, every refusal said to end active shifts first.
- A setting reset that fails now shows a message instead of doing nothing.
- Logging out with the settings editor open now closes it instead of leaving it on screen.

### Documentation
- `docs/EXPORTS.md`: `GetInfo`, `GetShift`, `GetProgression` and `SetUpdateHandler`.
- `README.md`, `docs/CONFIGURATION.md`, `docs/FEATURES.md`, `docs/INSTALLATION.md`, `docs/TROUBLESHOOTING.md`: shift messages as texts or mail, the incoming-call ring, the Gigs app on Oxide Phone, o-link 1.11.0 for texts and the ring, Oxide Phone and qs-smartphone-pro in the supported phones, and the list of phone messages the job actually sends.

### Migration Guide
- No SQL to run. Restart the server fully once after updating (new server file and a rebuilt settings UI).

## [1.5.0] - 2026-09-28

### Added
- `IsOnShift` server export: `exports['oxide-animalcontroljob']:IsOnShift(source)` tells your other resources whether a player is on an animal control shift, so they can stop someone working two jobs at once.
- Paste-ready ox_inventory entries for the dart gun and `ammo_dart` in `itemimages/ox_inventory_weapons.lua`.

### Changed
- Missing-model warnings are now capped per player and in total, so one misbehaving client can't flood admins with notifications or fill the server log.

### Removed
- `Config.Blip.label`: it had no effect. The office blip always shows the office's `name`.

### Fixed
- Pressing ESC to cancel an in-world placement no longer closes the whole settings editor too.

### Documentation
- New `docs/EXPORTS.md` covering `IsOnShift`, `GetSetting` and `SetSetting`, with integration examples.
- `docs/INSTALLATION.md`: ox_inventory steps now point to `itemimages/ox_inventory_weapons.lua`.

## [1.4.0] - 2026-09-11

### Added
- **Dedicated tranquilizer darts.** The dart gun now uses its own `ammo_dart` item and its own ammo type instead of pistol ammo, and it does no health damage. An item image ships in `itemimages/ammo_dart.png`. **You must register the new item before updating; see the Migration Guide.**
- **Tranquilize players** setting (`Config.Tranquilizer.playerEffects`). This used to be always on. It's now off by default and only works for officers on shift.
- The incoming call pop-up now shows the animal, the street, and an icon for the call type.

### Changed
- Extra dart packs are now paid from pay already earned this shift. If earnings don't cover the price, the purchase is refused. The first pack is still free.
- When a shift ends for any reason, the dart gun and any unused job darts are taken back.
- Destroying the truck now ends the shift for both crew members, with the fine and complaint. Before, they stayed on shift with no truck and no new calls.
- `Config.Crew.maxSize` now works: set it to `1` for solo-only shifts. Crews are pairs, so saved values above `2` are lowered to `2`.
- Players can't clock in while dead or downed, or while the dart gun or dart item is missing from your inventory's item list. They get an equipment error instead of half a kit.
- The dart gun, gun item and dart item can't be changed in `/animalcontrol settings` while anyone is on shift.
- The server now checks every step of a call (darting, capturing, loading and delivery). A call completes only once the animal is loaded and the truck is at the drop-off.

### Removed
- `Config.AnimalLoading.loadDuration` and `Config.AnimalLoading.unloadDuration`: they had no effect. Loading and unloading times are set by `Config.LoadAnimation.duration` and `Config.UnloadAnimation.duration`.

### Fixed
- **Killing the call's animal went unnoticed.** The call now ends with the kill fine (`Config.Complaints.animalKillFine`) and a complaint.
- **Dying on shift went unnoticed.** The shift now ends, earned pay is paid out and the death complaint is filed. On QBCore and QBX, being downed counts too.
- **Reaching the complaint limit mid-shift left the officer on shift with no clock-out option.** The shift now ends and earned pay is paid out before the suspension starts.
- Being auto clocked out for missed calls left the player with the dart gun and the work uniform.
- When one crew member clocked out mid-call, the animal, bait and reporting civilian could be deleted, leaving the partner with a call they couldn't finish. The partner now keeps the scene, including remains or bait the other officer was holding.
- Darted animals are no longer dropped to 1 health while sedated, so a small knock can't kill them.
- A truck that disappears (for example, removed by a cleanup script) now ends the shift without the destruction fine or complaint.
- An unanswered crew invite no longer blocks that player from being invited again after it expires.
- Cancelling the body-bag load no longer makes the bag vanish from your hands.
- The default event list shipped an aggressive-animal call at the map origin (0, 0, 0). It's removed. Dispatch now skips any event with no animal, an invalid call type or a (0, 0, 0) location, and logs a console warning. The settings editor won't save such events.
- These settings had no effect and now work: `Config.Stealth.fallbackSpookChance`, `Config.AnimalLoading.loadRange`, `Config.BaitPlaceAnimation`, `Config.ReportingNPC.farScenario` and `animationDistance`, and each animal's `health` and `name` in `Config.Animals`. The reporting civilian now cycles through all of `Config.ReportingNPC.animations`, not just the first.
- The shift menu shows your partner's name instead of always saying "Solo", and the level progress bar measures progress toward the next level correctly.
- Switched-off settings no longer turn themselves back on after a restart.
- If the database returned nothing at startup, saved settings could be overwritten with the config file defaults. Startup now only adds settings that are missing.
- Decimal settings no longer show rounding noise like `0.90000000000009` in the editor.
- Tutorial text now says missed calls lead to automatic clock-out; they don't add complaints.

### Documentation
- `docs/INSTALLATION.md`: dart setup for QBCore and ox_inventory, and a new "Updating an existing installation" section.
- `docs/CONFIGURATION.md`: new "How Settings Work" section. Office and event changes now point to `/animalcontrol settings`, and the tranquilizer, crew and loading tables are updated.
- `docs/ADMIN.md`, `docs/FEATURES.md`, `docs/TROUBLESHOOTING.md`: updated for in-game settings and the new dart, crew, complaint and truck behavior.

### Migration Guide
- No SQL to run.
- **Before starting this version,** register `ammo_dart` and switch the dart gun to the new dart ammo, following "Updating an existing installation" in `docs/INSTALLATION.md`. Until you do, nobody can clock in.
  - **QBCore:** add `ammo_dart` to `qb-core/shared/items.lua`. Change `weapon_g2`'s `ammotype` to `AMMO_ANIMALCONTROL_DART` in `qb-core/shared/weapons.lua` and `qb-core/shared/items.lua`. Add the `ammo_dart` entry to `Config.AmmoTypes` and the `Config.WeaponRepairCosts.animalcontrol` line to `qb-weapons/config.lua`.
  - **ESX / QBX (ox_inventory):** set `ammoname = 'ammo_dart'` on `WEAPON_G2`, and add `ammo_dart` to the `Ammo` table in `ox_inventory/data/weapons.lua`.
  - Copy `itemimages/ammo_dart.png` into your inventory's image folder.
- Fully restart the server and have players reconnect, because the gun's weapon data changed.
- A saved ammo item of `ammo_pistol`, `pistol_ammo` or `ammo-9` switches to `ammo_dart` automatically on first start. A custom tranquilizer weapon needs its own ammo type and dart item.
- If your saved event list still has the old call at (0, 0, 0), dispatch now skips it. Remove it or move it in `/animalcontrol settings`.

## [1.3.0] - 2026-08-05

### Added
- **Live in-game settings editor — `/animalcontrol settings`** — nearly every setting now lives in the database and is editable in-game by admins, applying live with no restart: calls and dispatch, the full event catalog (with in-world scene placement), animals, the tranquilizer, stealth and behavior, delivery points, NPCs and blips, props, animations and tutorials. Values are checked before saving and bad ones rejected with a reason.
- **Model swaps** — new Compatibility section (`Config.ModelSwaps`): replace any prop, ped or animal model with one from your own packs. If a player's game build is missing a model, the server warns once naming the model — the bundled dart gun and the add-on kennel get their own clearer messages (`Config.ModelHints`).
- Animals can carry a display label (`Config.Animals[].label`) so near-identical models are distinguishable in the event builder's picker.
- New `/animalcontrol` command: `/animalcontrol settings` (admin), `/animalcontrol tutorials`, `/animalcontrol tutorialreset`. The old commands still work.

### Changed
- **Config files are now factory defaults.** On the first start after updating, everything in the config files — including your customizations — is imported into the database automatically. From then on the database is the source of truth: editing the config files does nothing on an existing install; use `/animalcontrol settings`.
- `Config.Tranquilizer.weaponHash` is now `weaponName` (a weapon name string). Changing the tranq weapon now also updates player-vs-player dart detection, which previously stayed locked to the default dart gun.
- A model missing from a player's game build is skipped instantly with a named warning instead of freezing the load. A missing ped in the reporting-NPC pool no longer randomly leaves a call with nobody to report it.

### Fixed
- **Every dispatched call was an aggressive-animal call.** A debug value shipped enabled and silently restricted the call mix; all four call types now appear again. The option is still available as "Force call type" in settings for testing.
- Changing a setting could break the clock-in menu camera (an extremely zoomed-in or empty view). Saved numbers now keep their exact values and previously saved settings repair themselves automatically on the next restart.

### Documentation
- `docs/CONFIGURATION.md`, `docs/INSTALLATION.md`, `docs/TROUBLESHOOTING.md`: updated for the `weaponName` rename.

### Migration Guide
- No SQL to run — the settings table is created automatically on first start and your existing config values are imported. Restart the server fully once after updating.
- If you customized `Config.Tranquilizer.weaponHash`, move the value to `weaponName` as a weapon name string (e.g. `'WEAPON_G2'`) before your first start on this version.

## [1.2.0] - 2026-07-29

### Added
- Cancel Current Task: while on an accepted call, the crew leader — or a solo worker — can cancel it from the office menu or by targeting the truck. The whole scene (animal, reporting civilian, bait, remains) cleans up for both crew members, the truck stays, and the worker goes back to waiting for calls. Cancelling doesn't count as a missed call, and calls already completed still pay at clock-out. Crew members can't cancel; they clock out to leave, exactly as before
- `Config.Calls.cancelCooldownSeconds` in `shared/config/job.lua` (default 60) — the next call is dispatched only after this wait; the wait follows the crew if leadership changes; set 0 to disable
- All new text ships translated in both included languages (English and Bosnian)

### Changed
- Docs: the cancel feature is documented; office configuration now has full field tables plus an "adding a second office" guide (call scenes are shared across offices — a second office needs no scene list of its own)

## [1.1.4] - 2026-07-07

### Changed
- `/animalcontrolbuilder` and its export tools now use the standard admin permission — the old `admin.animalcontroljob` permission is no longer needed, and standard QBCore/QBX/ESX admins pass automatically
- The crew invite dialog now opens through your server's configured input UI instead of always using ox_lib
- Added rate limiting to several job update events to protect against spam and abuse
- Corrected the ammo item note in `shared/config/animals.lua`: QBCore's pistol ammo item is `pistol_ammo`, not `ammo_pistol`
- Docs updated: admin permission setup corrected, fines and safety zone behavior documented

### Removed
- Removed config entries that had no effect in-game: `Config.Vehicle.spawnDistance`, `Config.SafetyZone.scenarioBlockRadius`, and `Config.SafetyZone.clearVehiclesRadius`

### Fixed
- Complaint suspension timers could be misread when loaded from the database on newer database library versions; they now count down correctly

## [1.1.3] - 2026-05-19

### Added
- `Config.Vehicle.livery` and `Config.Vehicle.extras` for custom vehicle models
- `Config.Payment.payoutAccount` and `Config.Payment.fineAccount` to configure which account shift pay is deposited to and which account fines are deducted from
- Bosnian (`bs`) locale

### Changed
- Installation guide now notes the required `qb-weapons` durability entry for `weapon_g2`

## [1.1.2] - 2026-05-13

### Added
- `Config.Blip.enabled` option to hide the office map blip

## [1.1.1] - 2026-05-12

### Added
- Uniform toggle button in the NUI shift menu (previously only available in the bridge context menu)
- UI locale support — NUI text now reads from `locales/*.json` so the shift menu, summary, and call notification can be translated
- oxide-logger integration for shift events, payouts, fines, ammo resupply, gear issue/return, progression changes, level-ups, timeouts, crew ownership transfers, auto-clock-outs, and admin commands

### Changed
- `Config.Debug` now defaults to `false`

### Fixed
- Progression load/save failures and zero-row updates now log warnings/errors instead of failing silently

## [1.1.0] - 2026-04-14

### Added
- Configurable outfit/uniform system with male and female outfit definitions (`shared/config/outfits.lua`)
- Uniform toggle in both NUI shift menu and bridge context menu with `Config.uniformsEnabled` (default: disabled)
- Server-side appearance backup and restore on uniform apply/remove
- Uniform auto-reverts on clock out, death, disconnect, and resource stop
- Migration script (`sql/migrate_unified_progression.sql`) for safe data migration from per-job progression tables
- Transaction reason strings on all payment and fine operations for audit trail

### Changed
- Migrated from `community_bridge` to `o-link` bridge across all server and client code
- Updated fxmanifest dependency from `community_bridge` to `o-link`
- Replaced all `Bridge.*` API calls with `olink.*` equivalents
- Updated lifecycle events to `olink:client:playerReady` / `olink:client:playerUnload`
- Unified progression storage from per-resource `animalcontroljob_progression` table to shared `job_progression` table with composite `(char_id, job)` key

### Fixed
- Vehicle fuel now set to full on spawn

### Migration Guide
- Back up your database before updating
- Run `sql/migrate_unified_progression.sql` once before starting the updated resource
- This copies data from `animalcontroljob_progression` into `job_progression` and removes the old table
- If you are updating multiple Oxide jobs that use this migration, only run the script once total

## [1.0.1] - 2026-04-05

### Fixed
- Crew member names now display character names instead of Steam names in the NUI and notifications

## [1.0.0] - 2026-03-25

### Added
- Initial release of the animal control gig job
- 4 job types: aggressive animal, stray capture, wildlife removal, and deceased pickup
- 55 preconfigured events across the city (13 aggressive, 15 stray, 5 wildlife, 22 deceased)
- Tranquilizer dart gun system with inventory-managed weapon and limited ammo per call (5 darts)
- Bait placement system with ghost preview, scent particle effects, and player retreat mechanics
- Stealth detection system with configurable native crouch detection or random chance fallback
- Animal behavior AI: aggressive combat targeting, stray flee/approach cycles, wildlife waypoint patrols, deceased ragdoll states
- Animal state machine: ALIVE, SEDATED, CAPTURED, LOADED, DEAD with transition logic
- Sedation wake-up timer (30 seconds) requiring re-tranquilization
- Escape radius detection per job type with complaint penalties
- Animal kill detection with fine ($150) and complaint
- Reporting NPC system with civilian models, reaction animations, and thank-you gestures on job completion
- World NPC flee behavior around aggressive animals and wildlife
- Shelter delivery system for live animal transport to `Config.ShelterDeliveryPoint`
- Deceased remains collection with body bag prop and carry animation
- Call dispatch system with configurable intervals (15-45s) and 30-second response window
- 5-tier progression system (Volunteer, Handler, Senior Handler, Field Supervisor, Chief Officer) with persistent database tracking
- 2-player crew co-op system with shared calls, vehicle sharing, and 15% co-op pay bonus
- Payment system with base pay, distance bonus, speed bonus, level multiplier, streak bonus, tips, gas fees, and taxes
- Complaint system with animal escape, animal kill, vehicle destruction, and death penalties; 30-minute timeout after 3 strikes
- Server-authoritative mission timers calculated from base time plus distance
- NUI frontend with mission timer, incoming call notification, shift menu with cinematic camera, shift summary screen, and tutorial tooltips
- 3D floating waypoint system (hold H to show) with distance-based scaling
- Dual menu style support: NUI (cinematic camera, crew headshots) and Bridge.Menu (lightweight context menu)
- Phone integration via community_bridge for shift emails and notifications
- Safety zones around animal scenes with speed zone tiers for NPC traffic
- Admin builder tool (`/animalcontrolbuilder`) for creating office locations and job events
- Tutorial commands (`/animalcontroltutorials` toggle, `/animalcontroltutorialreset` reset)
- 17 contextual tutorial tooltips for new player onboarding
- Database table `animalcontroljob_progression` for persistent progression
- Dart weapon damage restriction (only affects active job animals while on shift)
- Animal model registry with 14 animal types including dogs, cats, coyotes, mountain lions, panthers, and deer
- English locale (`locales/en.json`)
