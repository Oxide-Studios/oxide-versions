# Changelog

## [1.3.0] - 2026-09-02

### Added
- **Gang territory affects production** — every production stage inside a gang territory uses that territory's `yield` modifier for its output, extractors use its `growth` modifier for soak time, and wild coca plants there regrow faster by the same modifier. Needs the oxide-gangs territory system; modifiers are set per territory in `oxide-gangs/shared/config/territories.lua`. Without it (or outside any territory) nothing changes.
- A stage never returns nothing: even on a territory with a below-1.0 yield modifier you always get at least one unit for your inputs.

### Fixed
- Settings you switched **off** in `/cocaine settings` reverted to their file defaults on every restart. Saved off-toggles now stick.

## [1.2.1] - 2026-08-05

### Fixed
- Saving a settings section in `/cocaine settings` could break the cooking or packaging scene camera — an extremely zoomed-in or empty view that stayed broken until the setting row was deleted from the database. Numbers you save now keep their exact values, previously saved settings repair themselves automatically on the next server restart, and a corrupt value can no longer be saved at all.

## [1.2.0] - 2026-07-27

### Added
- **Police alerts** — stomping, cooking, brick pressing and packaging can now tip off police with a configurable chance. Ships disabled; enable it in the new Dispatch section of `/cocaine settings` (or `Config.Dispatch` in `shared/config.lua`). Set one master chance or override per stage, with a per-site cooldown so one lab doesn't spam calls. Works with oxide-dispatch or any dispatch resource o-link supports; with no dispatch resource installed, online officers still get a notification and a map blip. Alerts use the station's real location, and the default message deliberately doesn't name the drug.
- **Placement zone modes** — new `Config.ZoneMode` chooses how placement zones work: `blacklist` (the previous behavior — block placement inside drawn zones, still the default), `whitelist` (allow placement only inside drawn zones), or `off`. The settings menu's Safe Zones section is now **Placement Zones**, with Zone mode, Blocked areas and Allowed areas. Careful: whitelist mode with no allowed areas drawn blocks placement everywhere — the menu warns you when that's the case.
- **Item usage whitelist** — restrict any usable item in the resource (station kits, bagged product, crack, the manual) to specific jobs or gangs, with optional minimum rank and on-duty requirements. Ships empty (nothing restricted); edit it in the new Usage Whitelist section of `/cocaine settings` or `Config.UsageWhitelist` in `shared/config.lua`.
- **Bystanders now see the work** — other players watching a stomp now see the leaf pile change and the alkali/water pours, a running brick press animates for everyone nearby, and players at the stove or packaging table play a working animation instead of standing frozen (`Config.SceneAnims` in `shared/config.lua`; each animation can be turned off).
- **Item browser in the settings menu** — item fields now have a browse button that opens a searchable catalog of your framework's items, with icons, instead of typing item names by hand.

### Changed
- Wild coca zones now store a named vertical band (`minZ`/`maxZ`) instead of a `thickness` value. Old config files and previously saved zones are converted automatically — nothing to edit.
- The zone editor in the settings menu was rebuilt: collapsible per-zone cards with Rename, Redraw, Teleport and Remove, and re-editing a zone opens at its stored heights.

### Fixed
- Emptying a list in the settings menu no longer breaks the editor.

### Documentation
- New Police Alerts sections in `docs/ADMIN.md`, `docs/CONFIGURATION.md` and `docs/FEATURES.md`; Placement Zones and the Usage Whitelist documented throughout; `docs/TROUBLESHOOTING.md` adds "Police alerts never fire" and "Police alerts fire too often".

### Migration Guide
- No SQL to run. Old zone shapes are read automatically and rewritten on your next settings save.
- Police alerts and the usage whitelist ship disabled/empty — nothing changes until you turn them on in `/cocaine settings`.
- Restart the server once after updating (new files and a rebuilt UI), not just a live resource refresh.

## [1.1.1] - 2026-07-24

### Fixed
- Harvesting a wild coca bush no longer fails silently — players now get a clear message when a bush can't be harvested or they moved too far from it (two new strings in `locales/en.json` for translators)
- Bushes on sloped terrain could refuse to harvest even when standing right next to them; distance is now checked horizontally, so hillside bushes work
- Harvesting bushes back-to-back was blocked by an overly long anti-spam window — starting a harvest is now limited to once per 2 seconds instead of 5
- Saving wild coca zone settings twice in quick succession could leave players seeing bushes in their old spots; clients now automatically re-fetch the new positions

## [1.1.0] - 2026-07-14

### Added
- **Live in-game settings editor — `/cocaine settings`** — every setting now lives in the database and is editable in-game by admins. Changes save instantly and apply to all players live, with no restart. Values are checked before saving and bad ones are rejected with a reason. 12 sections cover everything: recipes and amounts, minigame tuning (stomp, cook temperature game, bagging, brick press), cutting agents, extractor timers, wild coca zones and yields, progression, usage effects, station props, safe zones and world labels. Edited stations respawn for everyone live, waiting for any in-progress minigame to finish first; edited wild coca zones regrow the bushes on the spot.
- **Zones drawn in-world** — wild coca spawn zones and no-placement zones use a fly-around polygon tool, with a Teleport button on every zone row. Requires an up-to-date `o-link`; the menu tells you if yours is too old.
- New server exports `GetSetting(key)` and `SetSetting(key, value)` for other resources — see `docs/EXPORTS.md`.
- All 5 database tables are now created automatically on first start — fresh installs no longer run any SQL. `sql/install.sql` stays in the download for reference.

### Changed
- **Config files are now factory defaults.** On the first start after updating, everything in `shared/config.lua` and `shared/config/*.lua` — including your customizations — is imported into the database automatically. From then on the database is the source of truth: editing the config files does nothing on an existing install. The files still seed fresh installs, supply defaults for settings added by future updates, and back the per-setting "Reset to default" button.
- **Commands renamed to `/cocaine <subcommand>`** — `/cokestats` → `/cocaine stats`, `/cokekit` → `/cocaine kit`, `/setcokelvl` → `/cocaine setlevel`, plus the new `/cocaine settings`. The old names no longer exist. Typing `/cocaine` alone lists the subcommands.
- Admin command messages are now localized via `locales/en.json` (they were hardcoded English).
- The settings menu refuses to open while you're mid-stomp, mid-cook or mid-bagging, so a station is never changed out from under you.

### Documentation
- All docs rewritten around the settings editor: `docs/CONFIGURATION.md` explains the database-backed model and reorganizes every setting by menu section, `docs/INSTALLATION.md` drops the SQL step, `docs/ADMIN.md` documents the `/cocaine` command and the editor, `docs/TROUBLESHOOTING.md` covers "I changed a setting in a config file and nothing happened" and "the settings menu won't open", and `docs/EXPORTS.md` documents the new exports.

### Migration Guide
- No SQL to run. On the first start after updating, the `oxide_settings` table is created automatically and your existing config values — customizations included — are imported into it.
- From then on, change settings with `/cocaine settings` in-game; config file edits no longer apply on an existing install.
- Update `o-link` to the latest version to enable in-world zone drawing. Everything else works on an older `o-link`; the menu just shows a notice.
- A full restart of the resource is required once after updating (new files and a rebuilt UI), not just a live refresh.

## [1.0.1] - 2026-07-07

### Fixed
- Players whose game instance changed after loading (character select screens, instanced interiors) could miss all placed equipment — pools, extractors, cook tables, presses — until relog; the server now detects the change and refreshes them automatically
- Joining during server boot no longer produces a half-empty equipment snapshot

### Documentation
- `docs/ADMIN.md` now lists the correct `/cokekit` contents (4x Baking Soda plus 5x each of the other seven cutting agents)

## [1.0.0] - 2026-06-28

### Added
- Full cocaine production pipeline: harvest, stomp, soak, cook, bag, press
- Wild coca bush harvesting across configurable polygon zones with per-bush yield and respawn timers
- Free-walking pool stomp minigame with alkali and water pours and a four-stage leaf prop
- Passive soak extractor with restart-safe timers persisted to the database
- Interactive stove cook with a 6-key cycling temperature minigame and optimal-range purity scoring
- Overheat-explosion and cold-ruin failure states on the cook
- Crack as a second cook ("stepping on it") from powder cocaine
- Cutting agents on the crack cook: every batch takes one or two cuts that stretch it into more product at the cost of purity
- Eight configurable, level-gated cutting agents (baking soda, cornstarch, baby laxative, creatine, caffeine powder, benzocaine, lidocaine, levamisole)
- Live in-world preview of the selected cut floating above the stove while picking
- 5 purity grades (Pure, High, Mid, Low, Dirty) carried through every stage via item metadata
- Spoon-and-baggy bagging minigame with per-grade and mixed-grade (lowest-grade) bagging
- Brick press for compressing bulk cocaine into pressed kilos, gated to level 5
- Usable bagged cocaine and crack with sniff/smoke styles, purity-scaled stimulant effects, stress relief, and comedown
- 10-level progression with XP rewards for all production activities; leveling reduces the cook purity penalty and unlocks the brick press
- In-game illustrated cocaine manual that teaches the full system
- Four placeable stations (Coca Pool, Coca Extractor, Cook Table, Brick Press) with equipment ownership enforcement
- Safe-zone polygon system blocking equipment placement, and vehicle-use restriction
- Server exports: `GetPlayerLevel`, `GetPlayerProgression`, `AddXP`, `AddSold`
- Admin commands `/cokekit` and `/setcokelvl`, and the player command `/cokestats`
- Rate limiting on all server callbacks and events, session locking, and routing-bucket support
- Shared `drugs_progression` and `drug_equipment` tables compatible with other Oxide drug resources
- Custom streamed props for the kiddie pool, its leaf stages, and the seven cutting agents
- Full localization support via `locales/en.json`
- QBCore, QBX, and ESX support through `o-link`
