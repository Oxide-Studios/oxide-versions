# Changelog

## [2.0.0] - 2026-10-27

### Added
- Cook: pour each chemical into its share and fill the pot, then keep the burner knob in the warm, react and finish heat bands. The server scores the cook, and purity comes from how well you did.
- Break: crack the 12-cell slab with the hammer, then scrape up shards in three sizes. A second hit on a cell turns it to dust.
- Bag: weigh shards on a scale and heat-seal each bag between 0.8 and 1.0 g.
- Lab tiers: the Lab Bench (`meth_bench`, level 4, two per player) and Cook Rig (`meth_rig`, level 7, one per player) join the folding table. Each is easier to keep in the heat band and smells less.
- Propane: labs run on a `propane_cylinder`. One cylinder lasts 20 cooks on a table, 10 on a bench and 6 on a rig. A part-used cylinder keeps its level when it is disconnected.
- Residue: every cook leaves residue that costs up to 15 purity until the lab is scrubbed. A lab at 25 residue or more (three cooks on a table) must be scrubbed before it can be packed up.
- Fume extractor (`meth_fume_extractor`, level 3): cuts the police alert chance at its lab to about a third and stops nearby players smelling the cook.
- A lab without an extractor smells while it cooks: players within 6 m notice it, and on-duty police within 12 m.
- Crystallizer: a new dark-gray cabinet with two glass doors that open replaces the game's chiller, with the same 18 slots. Trays slide out and show wet, drying and dried in their variant's colour. The optional heater (`meth_heater`, level 5) mounts on its side and dries trays 1.5× faster.
- Police seizure: an on-duty officer can seize a lab or crystallizer in 10 seconds. The equipment and its parts are destroyed, trays in a crystallizer go to the officer as evidence, and the owner is told.
- Chemical supplier: a ped sells the base chemicals, baggies, propane and lab gear for cash (gear from its meth level), and swaps any used propane cylinder for a full one for $60. Set it up under Store in `/meth settings` or in `shared/config/store.lua`.
- New additives Lithium Strips (level 8) and Methylamine (level 9), and new variants Desert Glass and Black Diamond, with their own colour and effects.
- Progression: XP by purity grade, first-time and best-purity bonuses, perks at levels 6 to 10, variant mastery, daily and weekly goals, master ranks with perk picks, and signature recipes that sell for up to 15% more through Oxide Drug Selling and lose reputation when police seize the owner's equipment. A station cap, gang influence and copying XP to a skills system are available and off by default.
- A Cook app with level and goals widgets on Oxide Phone and Oxide Tablet.
- Level-up banner and XP pop-ups. The stage header, prompts and pop-ups are panels players place on Oxide HUD, and `Config.UseHudAccent` uses the HUD's accent colour.
- Nearby players see the bottle being poured, the pot, flame, slab, shards, scale, bag, sealer and the hammer or scraper while someone works. The gas mask shows as a respirator that everyone sees.
- A status tag over each lab and crystallizer shows its propane, residue, extractor and drying trays.
- Using meth plays a smoking animation with a pipe and lighter.
- Admin commands `/meth setxp <amount> [id]` and `/meth unregister <signature>`. New exports `PriceFor` and `GetPlayerRank`.
- 15 new sections in `/meth settings`, in the new Labs, Progression and Economy categories.
- New models for the stove and knob, flames, slab, shards, pot liquid, bags, sealer, respirator, bench, rig, extractor, cylinder, heater, residue grime, the crystallizer and its doors, and the crystallizer trays.

### Changed
- The cook, break and bag minigames are replaced by the ones above, and the crystallizer has a new shelf scene. The server now works out purity, shards and bag weights.
- The burner phase of a cook lasts 40 seconds instead of 20. Propane burns when the cook starts; ingredients are taken when the burner is lit.
- XP: cook 30 (was 25), break 15 per tray (was 30 per session), bag 10 (was 15). Cook, dry and bag XP are multiplied by the purity grade (Dirty 0.5 to Pure 1.5), and break XP by how cleanly the slab broke.
- `/meth stats` opens the new Cook Profile instead of the short stats card.
- The police alert chance depends on the lab tier and the fume extractor. Alerts are still off by default.
- The "Cooked" total, now "Bagged" in the Cook Profile, counts sealed bags instead of shards.
- A lab can't be packed up with its extractor or propane fitted, or at 25 residue or more. A crystallizer can't be packed up with its heater fitted or trays inside.
- `/meth kit` also gives a propane cylinder.
- The portable stove model is replaced by the new stove with a separate knob, and the old stove model no longer ships.
- An additive's optimal range now widens or narrows the heat band.
- The variants' Value multiplier, which nothing used, is removed.
- The `SetSetting` export refuses a change while settings are still loading at startup, and refuses config-file-only keys such as `UseHudAccent` and `SceneAnims`, returning `false`. Before, a change made while settings loaded could be lost.
- Settings save errors can be translated (`settings_error_*` in `locales/en.json`).

### Fixed
- A function inside a customized config no longer stops oxide-meth's settings from loading. The function is ignored and the server console names it.
- `/meth settings` says why a change wasn't saved instead of "value rejected". A failed reset shows an error instead of nothing.
- Screens no longer show "%%", as in "86%% Pure".
- Edits to `Config.SceneAnims` in the config file apply again after the first start.
- The hammer no longer lies on its side, and crystallizer trays sit on their shelves instead of sinking into or floating above them.
- Pours stream from the bottle's mouth.
- A table can no longer be packed up while someone is working at it.
- Packing up a crystallizer no longer destroys the trays inside; it waits until they are collected.
- Crystallizer trays update for everyone who can see them, not only players within 50 m.
- The purity penalty per level in `/meth settings` no longer shows "Level 0–9" and saves again.
- Placing equipment in third person works inside shipping containers and under low roofs.

### Documentation
- Installation, configuration, features, admin, exports and troubleshooting guides cover the rebuilt stages, labs, supplier, seizure and progression, and say which o-link version each optional part needs.

### Migration Guide
- Update oxide-drugselling to 2.2.0 at the same time. Without it, Desert Glass and Black Diamond baggies can't be sold, and signature baggies get no price bonus and earn no reputation.
- o-link 1.12.1 or newer is needed for the Oxide HUD panels, and 1.11.0 or newer for the Cook app on Oxide Phone. Everything else runs on older o-link.
- Add the 11 new items and their images: `meth_bench`, `meth_rig`, `meth_fume_extractor`, `meth_heater`, `propane_cylinder`, `lithium_strips`, `methylamine`, `crystal_meth_desert_glass`, `crystal_meth_black_diamond`, `meth_baggy_desert_glass`, `meth_baggy_black_diamond`. For ox_inventory, copy them from `items/ox_inventory_items.lua`.
- On qb-inventory (QBCore), set `useable = true` on `meth_bench`, `meth_rig`, `meth_baggy_desert_glass` and `meth_baggy_black_diamond`, and `unique = true` on `liquid_meth_tray`, `crystallized_meth_tray`, every `crystal_meth` item, every `meth_baggy` item and `propane_cylinder`.
- Seven new database tables are created on the first start, and are also in `sql/install.sql`. No SQL to run by hand.
- On the first start, saved settings still at their 1.7.0 defaults move once to the new ones: cook time, XP rewards, hammer and pour poses, use animation, stove and tray models. Values you changed are kept; a Break reward you changed is now paid per tray. The premium additives and variants, with their colours and effects, are added to your saved lists, and the Value multiplier is removed. Crystallizers move to the new cabinet and come back where they stood.
- Tables placed before this update have no propane. Connect a `propane_cylinder` before cooking, or turn Propane off under Lab Upkeep in `/meth settings`.
- Use this release's config files rather than your 1.7.0 copies. Several sections were replaced, `labs.lua`, `crystallizer.lua`, `production.lua` and `store.lua` are new, and a 1.7.0 `equipment.lua` names the old stove, which no longer ships.
- Merge the new keys into custom locales. Restart the server after adding the items.

## [1.7.0] - 2026-10-07

### Added
- Complete paste-ready ox_inventory item definitions in `items/ox_inventory_items.lua`, covering equipment, ingredients, additives, production items and variants.

### Changed
- Settings buttons, tooltips, item searches, empty lists and placement-zone tools use locale text.

### Fixed
- Nearby players no longer see a floating, cursor-controlled bottle while someone pours ingredients.
- A failed police-alert chance no longer starts the dispatch cooldown; the cooldown begins when an alert is sent.
- Gangs-only usage restrictions require gang membership instead of allowing players through an empty job list.
- Settings initialization no longer overwrites existing saved rows when a settings load returns empty.
- Failed database saves retain the previous active setting and report failure.
- Decimal settings no longer display long rounding tails.
- Escape cancels a world picker without closing the settings panel, and character logout closes the panel.

### Documentation
- Updated installation guidance with the complete inventory template and configuration guidance with the corrected dispatch cooldown behavior.

### Migration Guide
- No SQL or new item names are required when upgrading from 1.6.0. Existing database settings remain.
- For a fresh ox_inventory installation, copy entries from `items/ox_inventory_items.lua` into `ox_inventory/data/items.lua`, keeping one definition per item, and copy supplied item images into `ox_inventory/web/images/`.
- Merge the new `settings_ui_*` keys into custom locales.
- Restart oxide-meth after updating. Restart the whole server if you change inventory item definitions.

## [1.6.0] - 2026-09-02

### Added
- **Gang territory affects your lab** — a cook run inside a gang territory uses that territory's `yield` modifier for how much product comes out, and crystallizers use its `growth` modifier for how fast trays dry. Needs the oxide-gangs territory system; modifiers are set per territory in `oxide-gangs/shared/config/territories.lua`. Without it (or outside any territory) nothing changes.
- A cook never returns nothing: even on a territory with a below-1.0 yield modifier you always get at least one unit for your ingredients.

### Fixed
- Settings you switched **off** in `/meth settings` reverted to their file defaults on every restart. Saved off-toggles now stick.

## [1.5.0] - 2026-08-05

### Added
- **Gas mask for female characters** — new **Female drawable** and **Female texture** fields in the Gas mask section of `/meth settings` (`Config.GasMask.femaleDrawable` / `femaleTexture`). Clothing packs often number female masks differently than male ones; set these to your pack's female numbers. Left at `-1`, female characters use the same numbers as everyone else — nothing changes.

### Fixed
- Saving the Breaking or Bagging section in `/meth settings` broke that scene's camera — an extremely zoomed-in, empty-looking view — and it stayed broken until the setting row was deleted from the database. Numbers you save now keep their exact values, previously saved settings repair themselves automatically on the next server restart, and a corrupt value can no longer be saved at all. All four scene cameras (cooking, drying, breaking, bagging) are covered.
- Placed equipment with corrupt coordinates in the database is skipped with a logged warning instead of silently breaking the scenes anchored to it.

### Documentation
- `docs/CONFIGURATION.md` and `docs/TROUBLESHOOTING.md`: new guidance for masks that show on one gender but not the other with custom clothing packs.

### Migration Guide
- No SQL to run. Restart the server once after updating — previously saved settings repair themselves automatically at boot. If your break/bag camera was broken, it fixes itself on that restart; no database edits needed.

## [1.4.0] - 2026-07-27

### Added
- **Police alerts** — cooking, breaking, bagging and drying can now tip off police with a configurable chance. Ships disabled; enable it in the new Dispatch section of `/meth settings` (or `Config.Dispatch` in `shared/config.lua`). Set one master chance or override per stage, with a per-site cooldown so one lab doesn't spam calls. Works with oxide-dispatch or any dispatch resource o-link supports; with no dispatch resource installed, online officers still get a notification and a map blip. Alerts use the equipment's real location.
- **Placement zone modes** — new `Config.ZoneMode` chooses how placement zones work: `blacklist` (the previous behavior — block placement inside drawn zones, still the default), `whitelist` (allow placement only inside drawn zones), or `off`. The settings menu's Safe Zones section is now **Placement Zones**, with Zone mode, Blocked areas and Allowed areas. Careful: whitelist mode with no allowed areas drawn blocks placement everywhere — the menu warns you when that's the case.
- **Item usage whitelist** — restrict any usable item in the resource (tables, crystallizer, gas mask, bagged meth, the manual) to specific jobs or gangs, with optional minimum rank and on-duty requirements. Ships empty (nothing restricted); edit it in the new Usage Whitelist section of `/meth settings` or `Config.UsageWhitelist` in `shared/config.lua`.
- **"Explosion on overheat" toggle** — new option in the Cooking section (`Config.Cooking.explosionOnOverheat`). Turned off, hitting 100°C still ruins the batch and eats the ingredients, but nothing blows up. Default is on (unchanged behavior).
- **Bystanders now see the work** — players cooking, breaking, bagging or managing drying trays play a working animation that nearby players can see, instead of standing frozen (`Config.SceneAnims` in `shared/config.lua`; each animation can be turned off).

### Changed
- Placement zones now store a named vertical band (`minZ`/`maxZ`). Old config files and previously saved zones are converted automatically — nothing to edit.
- The zone editor in the settings menu was rebuilt: collapsible per-zone cards with Rename, Redraw, Teleport and Remove, and re-editing a zone opens at its stored heights.

### Fixed
- Emptying a list in the settings menu no longer breaks the editor.

### Documentation
- New Police Alerts sections in `docs/ADMIN.md`, `docs/CONFIGURATION.md` and `docs/FEATURES.md`; Placement Zones and the Usage Whitelist documented throughout; `docs/TROUBLESHOOTING.md` adds "Police alerts never fire" and "Police alerts fire too often".

### Migration Guide
- No SQL to run. Old zone shapes are read automatically and rewritten on your next settings save.
- Police alerts and the usage whitelist ship disabled/empty — nothing changes until you turn them on in `/meth settings`.
- Restart the server once after updating (new files and a rebuilt UI), not just a live resource refresh.

## [1.3.0] - 2026-07-14

### Added
- **Live in-game settings editor — `/meth settings`** — every setting now lives in the database and is editable in-game by admins. Changes save instantly and apply to all players live, with no restart. Values are checked before saving and bad ones are rejected with a reason. Sections cover everything: general, safe zones, equipment, items & stages, cooking, drying, breaking, bagging, additives, variants & purity, progression, usage & effects, gas mask & fumes, and animations. Edited equipment props respawn live; equipment mid-minigame is left alone and updates when the player finishes.
- **Safe zones drawn in-world** with a fly-around polygon tool. Requires an up-to-date `o-link`; the menu tells you if yours is too old.
- New server exports `GetSetting(key)` and `SetSetting(key, value)` for other resources — see `docs/EXPORTS.md`.
- All database tables are now created automatically on first start — fresh installs no longer run any SQL. `sql/install.sql` stays in the download for reference.

### Changed
- **Config files are now factory defaults.** On the first start after updating, everything in `shared/config.lua` and `shared/config/*.lua` — including your customizations — is imported into the database automatically. From then on the database is the source of truth: editing the config files does nothing on an existing install. The files still seed fresh installs, supply defaults for settings added by future updates, and back the per-setting "Reset to default" button.
- **Commands renamed to `/meth <subcommand>`** — `/methstats` → `/meth stats`, `/methkit` → `/meth kit`, `/meth_sellkit` → `/meth sellkit`, `/setmethlvl` → `/meth setlevel`, plus the new `/meth settings`. The old names no longer exist. Typing `/meth` alone lists the subcommands, and feedback messages are now localized.
- Shrinking the drying tray capacity in settings is blocked while a higher tray slot is in use, so nobody's drying meth can be orphaned.

### Documentation
- All docs rewritten around the settings editor: `docs/CONFIGURATION.md` explains the database-backed model and how to reset to defaults, `docs/INSTALLATION.md` drops the SQL step, `docs/ADMIN.md` walks through the editor and safe-zone drawing, `docs/TROUBLESHOOTING.md` covers "I changed a setting in config.lua and nothing happened" and "the settings panel won't open", and `docs/EXPORTS.md` documents the new exports.

### Migration Guide
- No SQL to run. On the first start after updating, the `oxide_settings` table is created automatically and your existing config values — customizations included — are imported into it.
- From then on, change settings with `/meth settings` in-game; config file edits no longer apply on an existing install.
- Update `o-link` to the latest version to enable in-world safe-zone drawing. Everything else works on an older `o-link`; the menu just shows a notice.

## [1.2.2] - 2026-07-07

### Fixed
- Players whose game instance changed after loading (character select screens, instanced interiors) could never see placed labs, equipment or drying trays until relog — the server now detects the change and refreshes them automatically. Joining during server boot no longer produces a half-empty world.

### Changed
- `/methkit`, `/meth_sellkit` and `/setmethlvl` now use the standard admin permission and show a "No permission" message on denial. The recommended setup is `add_ace group.admin admin allow` (see `docs/ADMIN.md`); old per-command grants such as `command.methkit` no longer apply.
- Equipment placement and removal updates are now only sent to players in the same game instance.

## [1.2.1] - 2026-06-11

### Fixed
- Variant baggies produced through bagging now give their variant's own effects when used — previously they used the standard Crystal effects
- Using meth now reduces stress on frameworks with a stress system (QBCore/QBX)
- Shards and baggies now carry their variant in item metadata

## [1.2.0] - 2026-04-14

### Added
- Meth usage system: all bagged meth variants are now usable items with configurable stimulant effects (screen visuals, intensity pulses, FOV surges, speed boost, stamina regen, armour, healing)
- Per-variant and per-purity effect scaling with full configuration in `shared/config/usage.lua`
- Usage cooldown and active-session checks to prevent stacking
- Client-side usage cleanup on resource stop and player disconnect
- Session cancellation when players press Escape during cooking, breaking, or bagging minigames — properly releases server-side session locks
- Vehicle usage restriction: gas mask and equipment items can no longer be used while in a vehicle
- Configurable fume effects during cooking (`Config.FumeEffects`) — linger duration, max strength, ramp-up rate, and masked fade rate are now exposed in config
- Race condition guard on equipment spawning to prevent duplicate entity creation during yielding
- Frontend locale loader now reads the active language from the `ox:locale` convar via a `getLocale` NUI callback and loads the matching `locales/<lang>.json`, with graceful fallback to English

### Fixed
- Player visibility in drug cameras: removed redundant `SetEntityVisible` calls that caused the local player to appear invisible to other players during minigames
- Bagging NUI close now properly cancels pending variant selection and releases server session
- Equipment spawning no longer leaves phantom entries when all entity models fail to create

### Changed
- Migrated from `community_bridge` to `o-link` — all `Bridge.*` calls replaced with `olink.*` equivalents, dependency updated in fxmanifest
- Replaced `CanCarryItem` pre-checks with `AddItem` return value validation and proper rollback on failure (cooking, breaking, bagging, drying)
- Meth table facing offset changed from 0 to 180 degrees

## [1.1.0] - 2026-04-09

### Added
- All frontend UI strings are now in `locales/en.json` — cooking manual, stats card, crystallizer panel, additive panel, and HUD can all be translated or customized without rebuilding the frontend

### Fixed
- ESX compatibility: stress removal no longer errors on frameworks without a stress system
- Replaced invalid `switch@trevor@trev_hunt_intro` animation with a valid use animation

### Changed
- Reworked drug high effects to feel like a stimulant instead of drunkenness — removed wobbly vision, drunk camera sway, and drunk walk; replaced with vivid colors, jittery hand shake, and periodic intensity surges with FOV pulses

## [1.0.0] - 2026-04-07

### Added
- Full meth production pipeline: cook, crystallize, break, bag
- Interactive temperature minigame with 6 cycling key controls and optimal range scoring
- 4 base ingredients: hydrogen peroxide, ammonia, hydrochloric acid, sodium benzoate
- 10-additive chemistry system with pour and drag interactions
- 10 named variants (Glass, Blue Sky, Purple Haze, Lean Crystal, Green Rush, Dirty Sprite, Street Mix, Rocket Fuel, Club Special, Ice) with unique items and value multipliers
- Purity scoring system with temperature-based base score, additive modifiers, and level-based penalties
- 5 purity grades: Pure, High, Mid, Low, Dirty
- Crystallizer equipment with 18 drying slots (2 columns x 9 stacked) and 5-minute crystallization time
- Breaking system with 4-stage visual tray progression and variant-specific shard output (3-6 per tray)
- Bagging system with scale weight mechanics (0.3-1.0g shards, 1.0g target, 0.8g minimum seal)
- 10-level progression system with XP rewards for all production activities
- Shared `drugs_progression` table compatible with other Oxide drug resources
- Gas mask usable item for cooking protection
- Interactive cooking manual NUI with full production guide
- Meth table and crystallizer placeable equipment
- Equipment ownership enforcement with `Config.RequireOwnership`
- Safe zone polygon system blocking equipment placement
- Admin commands: `/methkit`, `/meth_sellkit`, `/methstats`
- Server exports: `GetPlayerLevel`, `GetPlayerProgression`, `AddXP`, `AddSold`
- Rate limiting on all server callbacks and events
- Session locking to prevent concurrent production activities
- Full localization support via `locales/en.json`
- Routing bucket support for instanced gameplay
- Database persistence for equipment, drying slots, and progression
