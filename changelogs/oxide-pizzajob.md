# Changelog

## [1.6.2] - 2026-10-26

### Changed
- In `/pizza settings` → General, the **Shift summary** option that shows the summary on screen is now called **Notification**, and the help for **Use phone** and **Shift summary** says what each one does. Saved settings keep working.
- Removed two settings that did nothing: **Spawn distance** (`Config.Vehicle.spawnDistance`) and the **Hand over** animation (`Config.Animations.deliver`).

### Fixed
- The pay summary names the accounts the pay and tips went to (`Config.Payment.payoutAccount` and `Config.Payment.tipAccount`). It always said Bank and Cash. This covers the clock-out notification, the pop-up and the pay summary sent to the phone, and the tutorial no longer says pay goes to the bank.
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.

### Migration Guide
- No SQL to run. An edited or translated `locales/en.json` keeps working, and lines added in this version show in English until you add them. To show the account name in the pay summary, copy `notify.clocked_out_deposited`, `notify.clocked_out_tips`, `summary.net_pay`, `summary.tips`, `summary.email_net`, `summary.email_tips` and the `account.*` lines from this version's file.

## [1.6.1] - 2026-10-23

### Fixed
- Daily streaks survive reconnects and restarts. A player who had already worked that day or the day before went back to a 1-day streak.
- On MySQL servers in strict mode, a player's progression could stop saving after they reconnected.
- A player whose saved job timeout is `0000-00-00 00:00:00` (left by earlier versions on some databases) can load their progression again.

## [1.6.0] - 2026-10-18

### Added
- Works with Oxide HUD (free): the delivery timer and tutorial cards can be moved or switched off in `/hud` (needs o-link 1.12.1 or newer).
- `Config.UseHudAccent` (`shared/config.lua`, default on), config file only.
- **Fastest believable trip** in `/pizza settings` → Payment (`Config.Payment.maxTravelSpeed`, 50 m/s).

### Changed
- Shops can be edited during shifts. Deleting one ends and pays its shifts.
- The job creates all its database tables itself; nothing to import.
- Refused `/pizza settings` attempts are logged once per player, per action, per minute.

### Fixed
- Pay is no longer lost on a disconnect, logout or character switch; it is paid at the next login. Going down still forfeits it.
- A restart now pays every running shift. It used to pay nothing.
- A payment the framework refuses is kept for the next login.
- `/pizzaexport` exports every current shop with its menu camera, not only the shops captured since the restart, so pasting it and pressing Reset to default can no longer wipe shops. It also handles names with apostrophes and a missing heading or level.

## [1.5.0] - 2026-10-04

### Added
- Shift messages arrive as texts from the shop on phones that support texts. Other phones get them as mail, as before. Texts need o-link 1.11.0 or newer.
- Gigs app support for Oxide Phone: the app shows the shops, the player's current shift and pay so far, and their level. Shift updates appear in the app instead of as texts; the pay summary still arrives as a text.
- New read-only server exports `GetInfo`, `GetShift` and `GetProgression`, plus `SetUpdateHandler` so another resource, such as a phone app, can show shift updates itself.

### Changed
- The settings editor's remaining English text (the Admin badge, Move up, Move down, Remove, Place in world, Teleport here, and the empty-list and entry labels) now comes from `locales/en.json` and can be translated.
- The server log now records refused `/pizza settings` attempts by non-admins, and whether each settings change came from the menu or another resource.

### Fixed
- `Config.usePhone` now works. Set it to `false` and the job sends nothing to the player's phone. Before, it was ignored.
- If a setting can't be saved to the database, the editor says it couldn't be saved and the old value stays. Before, the server used the new value even though it was never saved.
- A setting reset that fails now shows a message instead of doing nothing.
- Logging out with the settings editor open now closes it instead of leaving it on screen.

### Documentation
- `docs/EXPORTS.md`: `GetInfo`, `GetShift`, `GetProgression` and `SetUpdateHandler`.
- `docs/CONFIGURATION.md`, `docs/FEATURES.md`, `docs/INSTALLATION.md`, `docs/TROUBLESHOOTING.md`: shift messages as texts or mail, `Config.usePhone`, the Gigs app on Oxide Phone, o-link 1.11.0 for texts, Oxide Phone and qs-smartphone-pro in the supported phones, and the list of phone messages the job actually sends.

### Migration Guide
- No SQL to run. Restart the server fully once after updating (new server file and a rebuilt settings UI).

## [1.4.0] - 2026-09-28

### Added
- `IsOnShift` server export: `exports['oxide-pizzajob']:IsOnShift(source)` tells your other resources whether a player is on a pizza delivery shift, so they can stop someone working two jobs at once.

### Changed
- Missing-model warnings are now capped per player and in total, so one misbehaving client can't flood admins with notifications or fill the server log.

### Fixed
- Switched-off settings no longer turn themselves back on after a restart.
- If the database returned nothing at startup, saved settings could be overwritten with the config file defaults. Startup now only adds settings that are missing.
- Decimal settings no longer show rounding noise like `0.90000000000009` in the editor.
- Pressing ESC to cancel an in-world placement no longer closes the whole settings editor too.

### Documentation
- New `docs/EXPORTS.md` covering `IsOnShift`, `GetSetting` and `SetSetting`, with integration examples.
- `docs/CONFIGURATION.md`: new "How Settings Work" section. Adding a shop now points to `/pizza settings`.
- `docs/ADMIN.md`, `docs/TROUBLESHOOTING.md`: location builder exports and the cancel cooldown now point to `/pizza settings`. Pasting into the config file only works before the resource's first start.

## [1.3.0] - 2026-08-05

### Added
- **Live in-game settings editor — `/pizza settings`** — nearly every setting now lives in the database and is editable in-game by admins, applying live with no restart: pay, levels, timers, complaints, random events, vehicles, shop and delivery locations (with in-world placement previews), NPCs and blips, customer peds and reactions, uniforms, the pizza prop, animations and tutorials. Values are checked before saving and bad ones rejected with a reason.
- **Model swaps** — new Compatibility section (`Config.ModelSwaps`): replace any prop, ped or vehicle model with one from your own packs. If a player's game build is missing a model, the server warns once naming the model and pointing at the settings menu.
- New `/pizza` command: `/pizza settings` (admin), `/pizza tutorials`, `/pizza tutorialreset`. The old commands still work.

### Changed
- **Config files are now factory defaults.** On the first start after updating, everything in the config files — including your customizations — is imported into the database automatically. From then on the database is the source of truth: editing the config files does nothing on an existing install; use `/pizza settings`.
- A model missing from a player's game build is skipped instantly with a named warning instead of freezing the load. The delivery-customer ped pool no longer burns a long timeout per missing ped.

### Fixed
- Changing a setting could break the shift menu camera (an extremely zoomed-in or empty view). Saved numbers now keep their exact values and previously saved settings repair themselves automatically on the next restart.

### Migration Guide
- No SQL to run — the settings table is created automatically on first start and your existing config values are imported. Restart the server fully once after updating.

## [1.2.0] - 2026-07-29

### Added
- Cancel Current Task: while on deliveries, cancel from the shift menu or by targeting the delivery vehicle — a confirmation prompt appears, the remaining orders are forfeited, and you stay clocked in with your vehicle. Deliveries already completed still pay at clock-out
- `Config.Cancel.cooldownSeconds` in `shared/config/job.lua` (default 60) — how long a player waits before requesting new orders after cancelling. Tied to the character, so relogging doesn't skip it; set 0 to disable

### Fixed
- A customer walking up for a pizza handoff could be left standing at the door if the delivery was interrupted at the wrong moment — they are now always cleaned up

### Changed
- Docs: the cancel feature is documented; shop location configuration now has full field tables and an "adding another shop" walkthrough; new troubleshooting entries for stuck deliveries and the cancel cooldown

## [1.1.0] - 2026-07-07

### Added
- Per-level vehicle liveries and extras: each level in `shared/config/levels.lua` now supports `vehicleLivery` and `vehicleExtras` for the company vehicle
- Payment routing options in `shared/config/job.lua`: `payoutAccount`, `tipAccount` and `fineAccount` choose which account (bank or cash) shift payouts, tips and fines use — defaults match the old behavior
- Config, locale and SQL files are now viewable in escrowed builds
- The delivery waypoint distance text and keybind label are now translatable (`nui.dist_km`, `nui.dist_m`, `keymap.show_waypoint` in `locales/en.json`)

### Changed
- The welcome email now arrives with your first order batch and names your first delivery destination (translators: `email.welcome` gained a `%s` placeholder)
- The vehicle-destruction fine can no longer take more money than the player has; notifications now report the amount actually charged
- Builder commands (`/pizzacreate`, `/pizzavehicle`, `/pizzadeliv`, `/pizzaexport`, `/pizzacancel`) now use the standard admin permission — the old `admin.pizzajob` permission is no longer needed
- Stronger server-side checks against spoofed delivery completions and fake vehicle-destroyed reports

### Fixed
- Complaint suspension timers could be misread after a server restart on newer database library versions; they now count down correctly
- Exiting a vehicle while carrying a pizza now correctly restores the Store Pizza prompt and re-blocks vehicle entry until stored
- Restarting the resource mid-session now reliably restores the pizzeria NPC

## [1.0.2] - 2026-05-13

### Added
- `Config.Blip.enabled` option to hide the pizzeria map blip

### Fixed
- Adjusted menu camera coords and headings for the Downtown, Del Perro, and Mission Row locations so the pizzeria is properly framed in the shift menu

## [1.0.1] - 2026-04-16

### Fixed
- Supervisor target zones no longer duplicate when the resource is restarted
- Uniform system now snapshots appearance before applying the outfit, fixing cases where clothing bridges corrupted the restore data
- Removed dependency on custom `GetIsPlayerDowned` bridge function that prevented the resource from working with the original community_bridge

## [1.0.0] - 2026-03-19

### Added
- Vue 3 + Vite + Tailwind frontend with delivery HUD, shift menu, waypoint overlay, and shift summary screen
- Three order types: Standard (1.0x), Priority (1.5x), and Catering (3.0x) with level-gated access
- 5-tier progression system (Trainee, Driver, Reliable, Senior, Veteran) with persistent database tracking
- Random event system: Customer Not Home (8%), Rush Order (10%), Wrong Address (5%)
- Customer reaction system with 4 tiers (Excellent, Good, Poor, Bad) based on delivery performance
- Pizza box prop with carry animation, vehicle storage interaction, and automatic attach/detach on vehicle enter/exit
- Delivery timer system with distance-based time limits and 15% late penalty
- 3D floating waypoint overlay (hold H) with distance scaling and distance text
- Daily streak bonus system (up to 7 consecutive days, $10/day)
- Complaint and suspension system (3 complaints triggers 30-minute timeout)
- Lump-sum shift payout with gas fee (5%) and tax (10%) deductions
- Tips paid instantly as cash, base earnings deposited to bank on clock-out
- Vehicle damage charge system based on body health at clock-out
- Uniform system with separate male/female outfit configurations
- NUI shift menu with cinematic camera and sway animation per location
- Bridge.Menu fallback context menu for servers preferring simplicity
- Shift summary delivered via NUI popup or SMS (configurable)
- Phone integration for shift event notifications via `community_bridge` phone bridge
- Tutorial system with 8 contextual tooltips and KVP persistence
- Player commands: `/pizzatutorials` (toggle), `/pizzatutorialreset` (reset progress)
- Admin coordinate builder: `/pizzacreate`, `/pizzavehicle`, `/pizzadeliv`, `/pizzaexport`, `/pizzacancel`
- Server-side anti-teleport validation based on delivery distance
- Rate limiting on all server callbacks and events
- Automatic cleanup on disconnect, character unload, death, and resource restart
- 3 pre-configured shop locations: Downtown, Del Perro, and Mission Row
- Localization support via `locales/en.json`
