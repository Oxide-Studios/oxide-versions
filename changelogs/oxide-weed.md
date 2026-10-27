# Changelog

## [1.7.2] - 2026-10-26

### Changed
- The labels over plants, drying racks and water containers, and the tags in the drying rack, grow tent and freezer views, no longer have a coloured bar down one side.

### Fixed
- oxide-weed no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.
- `/weed settings` now says why a change wasn't saved (settings still loading, a number that isn't valid, the setting can't be edited, or a database error) instead of "value rejected", and keeps the old value. A failed reset now shows an error too.
- The grow tent scene no longer logs an error every time the player dies in it or the tent is removed.
- A grow tent can be placed on a floor built from props, such as an Oxide Containers unit. Before, the ground check accepted only map ground.

## [1.7.1] - 2026-10-23

### Fixed
- The Germinator stops a purchase when the cash payment is refused. Before, it still handed over the items.
- A refund that can't be paid (store, cloning, rank respec, strain registration) is logged with the player and amount, and every admin online is alerted to pay it by hand.

## [1.7.0] - 2026-10-18

### Added
- Works with Oxide HUD (free): the stage header, the controls (key prompts and hose bar), the level-up banner and the XP pop-ups can be moved or switched off in `/hud`. They share spots with other jobs' cards and hide with the HUD (pause, cinematics, death, character select), as do the gauges and labels over plants. Needs o-link 1.12.1 or newer; without Oxide HUD nothing changes.
- `Config.UseHudAccent` (`shared/config.lua`, default on): the weed HUD takes the Oxide HUD accent instead of green. Config file only.

### Changed
- The `SetSetting` export refuses `UseHudAccent` and `SceneAnims`, which are config-file only.

### Fixed
- Edits to `Config.SceneAnims` now apply after a restart; a copy saved by an earlier version is ignored.
- The smoking prompt can be translated (`smoke_prompt`, Spanish included), and it clears when smoking is cut short by a logout or restart.

## [1.6.0] - 2026-10-07

### Added
- **Water hose** — connect a hose to a water tank and water loose plants with Jet or Shower spray. Water and XP follow the amount actually delivered, and nearby players see the hose, nozzle and spray.
- **Grow tents** — the Grow Box holds two plants at level 2, the Grow Tent holds four at level 4, and the Large Grow Tent holds eight at level 6. Each has built-in lighting, weather protection and a close-up growing scene, with opening doors or flaps.
- **Carbon filters** — unlocked at level 5, filters suppress tent smell and reduce police alerts.
- Police can seize tents, their plants and filters. Configure eligible jobs, duty requirements and seizure time in `/weed settings`.
- **Custom streamed models** for every plant stage, wild plants, nursery pots, soil, lamps, heaters, drying racks, freeze dryers, water tanks, hoses, watering cans and tent plants.
- `Config.ModelSwaps` lets owners replace models unavailable on their game build. Missing models produce a clear server warning and are skipped.
- **Grower Profile** — `/weed stats` now opens Overview, Strains, Goals and Ranks, showing journal firsts, personal bests, lifetime totals, mastery, signature strains, perks and unlocks. XP gains and level-ups have their own feedback.
- Level perks add slower care decay at level 6, better trimming at 7, more seed chances at 8, kept male plants and another tent at 9, and better care and cheaper cloning at 10.
- Gelato, Wedding Cake and Durban Poison seed packs unlock at levels 7, 8 and 9. Each contains three seeds; these strains do not appear among wild plants.
- **Strain mastery** — Familiar, Skilled and Master unlock after 5, 15 and 40 harvests, with strain bonuses, badges and one-time XP rewards. Each strain's first-ever harvest also earns a bonus, capped at three strains per day.
- **Grower goals** — three daily and two weekly goals scale with progression. Players can swap one daily goal; sale goals work when oxide-drugselling is running.
- **Master ranks** — after level 10, every 4,000 XP earns another rank, up to five. Choose another tent, 15% faster drying, an extra bud, Wild Genes or free lab reports.
- The scientist can reset master perk choices for $25,000 dirty money. Extra tents must be packed before removing their capacity perk.
- **Signature strains** — level 10 growers can register mastered crossbreeds under unique, moderated names for $50,000 dirty money, with two registrations per character by default. Sales build street reputation and seizures reduce it; optional sale pricing adds up to 15%.
- **Grower app** for oxide-phone and oxide-tablet, with the same profile, goals and rank choices, plus level and daily-goal widgets. Neither device resource is required; signature registration and perk resets remain at the scientist.
- Optional weather effects on exposed loose plants, gang influence on harvests and brick production, and skills mirroring. These integrations ship disabled.
- Permanent `/weed tank`, `/weed setlevel` and `/weed unregister` admin tools. `/weed setxp` and `/weed setlevel` support a target player and server-console use.

### Changed
- Rebuilt the growing and processing scenes, world displays, minigames, store and scientist menus.
- Drying racks hang buds upside down from four bars. Freeze dryers have opening doors, sliding trays and individual bud loading and collection; cancelling a scene keeps its contents.
- Soil fills the nursery pot as fertilizer is poured. Planting and care cameras frame the whole pot, harvested buds fit the new plants, heaters face the table, and wild plants follow sloped ground.
- Harvest XP reflects quality, drying XP is paid on collection, and journal firsts and personal bests earn bonuses. Repeated loading and lamp actions no longer farm XP; care XP requires a plant below 90%.
- Store level requirements now match equipment and strain usage requirements.
- `Config.MaxPlantsPerPlayer` defaults to 40 and counts loose and tent plants together; `0` disables the limit. Optional level-based loose-plant limits ship disabled, and existing plants are kept.
- Tote capacity is measured in grams. The new factory default is 50,000 g; legacy kilogram settings convert without changing their saved capacity.
- Wild Genes now uses a configurable 3.0 range multiplier. Saved unchanged 1.25 defaults migrate; owner tuning remains.

### Fixed
- Watering-can item use and plant-care watering now spend and deliver the same amounts. Water is saved to the correct inventory slot, and a failed save gives no free water.
- Picked-up tanks retain their water on inventories that support item metadata. Placement claims each stored tank once, and pickup blocks simultaneous filling, refilling and hose use.
- Tote contents use the inventory bridge. Supported inventories prevent picking up a non-empty tote.
- All planting paths enforce seed, strain, level and usage restrictions. Watering, fertilizing and equipment actions check the player's routing bucket, and harvests cannot be claimed twice.
- Tent options respect ownership and access, while ownerless tents can still be packed or fitted with a filter.
- Kept male plants regrow pollen after an hour without producing another pot. The timer and once-only XP survive restarts.
- XP awards consistently check level-ups, and admin XP reductions update the player's progression immediately.
- Goal draws, swaps, progress and rewards cannot race each other. Completed goals pay once across reset boundaries and reloads.
- Failed payments refuse lab reports, cloning, signature registration and perk resets.
- Freeze-dryer buds rest on the tray floor throughout loading, drying and collection. Saved unchanged default positions repair automatically; custom fitting remains.
- Nearby players see held tools and pouring props in the correct place. Cursor-controlled scene props stay local instead of floating around other players.
- A failed police-alert chance no longer starts the dispatch cooldown, and gangs-only usage restrictions no longer allow players through an empty job list.
- Settings initialization preserves saved rows, failed saves retain the previous active value, cancelling a world picker keeps the settings menu open, and character logout closes it.

### Documentation
- Updated installation, configuration, admin, exports and troubleshooting guides, the grower's manual, and English and Spanish text.
- Added integration guidance and complete paste-ready ox_inventory item definitions.

### Migration Guide
- Add `water_hose`, `grow_box`, `grow_tent`, `grow_tent_large`, `carbon_filter`, `gelato_seed_pack`, `wedding_cake_seed_pack` and `durban_poison_seed_pack` to your inventory. All except `carbon_filter` must be usable.
- Copy the supplied icons from `item images/` into your inventory's image folder, including replacements for both water-container icons. ox_inventory definitions are in `item images/ox_inventory_items.lua`.
- On qb-inventory, set `unique = true` for `watering_can`, `water_container_small` and `water_container_large` so each item keeps its own water.
- Restart the whole server after updating. The new stream files require a full restart; restarting only oxide-weed is insufficient. Placement requires o-link 1.9 or newer.
- On boot, oxide-weed creates `weed_hoses`, `weed_tents`, `weed_carried_tanks`, `weed_grower`, `weed_strain_mastery`, `weed_grower_goals` and `weed_grower_ranks`.
- Boot migrations add `growth_state`, `tent_id`, `tent_slot`, `grower_bonus` and `pollen_ready_at` to `drug_plants`, plus `registered_by`, `registered_at`, `reputation`, `name_key` and a unique name index to `drug_strains`. The shared `drugs_progression` table is unchanged.
- Normal upgrades need no manual SQL when the database user has CREATE and ALTER permissions. For manual installation use `sql/install.sql`; for manual upgrades use `sql/phase_7_growth.sql`, `sql/migrate_water_hose.sql`, `sql/migrate_grow_tents.sql`, `sql/migrate_carried_tanks.sql`, `sql/migrate_grower.sql`, `sql/migrate_pollen_regrow.sql`, `sql/migrate_strain_mastery.sql`, `sql/migrate_grower_goals.sql`, `sql/migrate_master_ranks.sql` and `sql/migrate_signature_strains.sql`.
- Saved settings remain authoritative. Unchanged vanilla models, scene layouts and factory XP values migrate to their new defaults; custom choices remain. Review custom drying-rack and freeze-dryer layouts.
- Drying-rack `trayCount`, `budsPerTray` and `trays` settings migrate to `barCount`, `budsPerBar` and `bars`. Watering amounts now come from `PlantSettings.water.sources`; obsolete `WateringCan.waterCost` and `fillAmount` settings are removed.
- `FreezeDryer.requiredLevel` moves to `Equipment.freeze_dryer.requiredLevel`, preserving a higher saved requirement.
- New tent, filter, premium-strain and store defaults are seeded once; later removals remain removed. The hose is added during the legacy tank-model migration. Servers with custom tank models should add it to their saved store list themselves.
- Existing XP, levels and lifetime totals remain. New journal and mastery history starts with this update; old actions are not reconstructed.
- Update oxide-drugselling and merge its weed `xpUnits` configuration for sale XP by weight. Signature price bonuses also require the `PriceFor` modifier described in `docs/CONFIGURATION.md`.
- Custom integrations reading `GetPlayerProgression` must use translated unlock `label` values in place of `labelKey`; unlocks also include `kind`. Merge the new locale keys into custom translations.

## [1.5.0] - 2026-09-02

### Added
- **Gang territory affects your grow** — plants growing inside a gang territory use that territory's `yield` and `growth` modifiers, so a good patch of turf grows faster and harvests bigger. Needs the oxide-gangs territory system; modifiers are set per territory in `oxide-gangs/shared/config/territories.lua`. Without it (or outside any territory) nothing changes.
- The plant menu's time-remaining readout accounts for the territory speed bonus, so it matches what you actually see on the plant.

### Fixed
- Settings you switched **off** in `/weed settings` reverted to their file defaults on every restart. Saved off-toggles now stick.

## [1.4.1] - 2026-08-05

### Fixed
- Saving a settings section in `/weed settings` could break that scene's camera — the brick press, freeze dryer and drying rack views turned extremely zoomed-in or empty and stayed broken until the setting row was deleted from the database. Numbers you save now keep their exact values, previously saved settings repair themselves automatically on the next server restart, and a corrupt value can no longer be saved at all.

## [1.4.0] - 2026-07-27

### Added
- **Police alerts** — planting, harvesting, drying and brick pressing can now tip off police with a configurable chance (wild plants never alert). Ships disabled; enable it in the new Dispatch section of `/weed settings` (or `Config.Dispatch` in `shared/config.lua`). Set one master chance or override per stage, with a per-site cooldown so one grow doesn't spam calls. Works with oxide-dispatch or any dispatch resource o-link supports; with no dispatch resource installed, online officers still get a notification and a map blip. Alerts use the plant or table's real location, and the default message deliberately doesn't name the drug.
- **Placement zone modes** — new `Config.ZoneMode` chooses how placement zones work: `blacklist` (the previous behavior — block planting and placement inside drawn zones, still the default), `whitelist` (allow planting and placement only inside drawn zones), or `off`. The settings menu's Safe Zones section is now **Placement Zones**, with Zone mode, Blocked areas and Allowed areas. Careful: whitelist mode with no allowed areas drawn blocks placement everywhere — the menu warns you when that's the case.
- **Item usage whitelist** — restrict any usable item in the resource (seeds, equipment, watering can, scissors, rolling items, joints and blunts, lamps, heaters, the manual) to specific jobs or gangs, with optional minimum rank and on-duty requirements. Ships empty (nothing restricted); edit it in the new Usage Whitelist section of `/weed settings` or `Config.UsageWhitelist` in `shared/config.lua`.
- **Bystanders now see the work** — players at the brick press, drying rack, freeze dryer or crossbreeding station play a working animation that nearby players can see, instead of standing frozen (`Config.SceneAnims` in `shared/config.lua`; each animation can be turned off).
- **Item browser in the settings menu** — item fields (yields, store stock, seeds, recipes and more) now have a browse button that opens a searchable catalog of your framework's items, with icons, instead of typing item names by hand.

### Changed
- Wild plant zones and placement zones now store a named vertical band (`minZ`/`maxZ`) instead of a `thickness` value. Old config files and previously saved zones are converted automatically — nothing to edit.
- The zone editor in the settings menu was rebuilt: collapsible per-zone cards with Rename, Redraw, Teleport and Remove, and re-editing a zone opens at its stored heights.

### Fixed
- Placing the store or scientist NPC with the settings menu's Place button stored the position too low, sinking the NPC into the ground — placed NPCs now stand at the right height.
- Emptying a list in the settings menu no longer breaks the editor.

### Documentation
- New Police Alerts sections in `docs/ADMIN.md`, `docs/CONFIGURATION.md` and `docs/FEATURES.md`; Placement Zones and the Usage Whitelist documented throughout; `docs/TROUBLESHOOTING.md` adds "Nothing can be planted or placed anywhere", "A player can't use an item they're holding", "Police alerts never fire" and "Police alerts fire too often". Spanish locale updated alongside English.

### Migration Guide
- No SQL to run. Old zone shapes are read automatically and rewritten on your next settings save.
- Police alerts and the usage whitelist ship disabled/empty — nothing changes until you turn them on in `/weed settings`.
- Restart the server once after updating (new files and a rebuilt UI), not just a live resource refresh.

## [1.3.0] - 2026-07-14

### Added
- **Live in-game settings editor — `/weed settings`** — every setting now lives in the database and is editable in-game by admins. Changes save instantly and apply to all players live, with no restart. Values are checked before saving and bad ones are rejected with a reason. 17 sections cover everything: general, dirty money, plant care, plants, strains, genetics, progression, equipment, drying, drying rack, freeze dryer, store, scientist, wild plants, safe zones, lamps and smoking. Editing the store or scientist moves the NPC on the spot; editing wild plants regrows them against the new zones.
- **In-world editing** — wild plant zones and safe zones are drawn with a fly-around polygon tool, and coordinate settings get a Place button. Requires an up-to-date `o-link`; the menu tells you if yours is too old.
- New server exports `GetSetting(key)` and `SetSetting(key, value)` for other resources — see `docs/EXPORTS.md`.
- All 8 database tables are now created automatically on first start — fresh installs no longer run any SQL. `sql/install.sql` stays in the download for reference.

### Changed
- **Config files are now factory defaults.** On the first start after updating, everything in `shared/config.lua` and `shared/config/*.lua` — including your customizations — is imported into the database automatically. From then on the database is the source of truth: editing the config files does nothing on an existing install. The files still seed fresh installs, supply defaults for settings added by future updates, and back the per-setting "Reset to default" button.
- **All commands renamed to `/weed <subcommand>`** — `/weedstats` → `/weed stats`, `/weed_kit` → `/weed kit`, `/weed_seeds` → `/weed seeds`, `/weed_givebuds` → `/weed givebuds`, `/weed_grow` → `/weed grow`, `/weed_water` → `/weed water`, `/weed_dry` → `/weed dry`, `/weed_fasttick` → `/weed fasttick`, `/weed_setxp` → `/weed setxp`, `/weed_info` → `/weed info`, `/weed_sellkit` → `/weed sellkit`, `/weed_clearplants` → `/weed clearplants`, `/weed_clearlamps` → `/weed clearlamps`, `/weed_clearequipment` → `/weed clearequipment`, plus the new `/weed settings`. The old names no longer exist. Typing `/weed` alone lists the subcommands.
- Admin subcommands run in-game only; running one from the server console now prints a message instead of silently doing nothing.
- Command feedback messages are now localized (English and Spanish included).
- The `/weed fasttick` testing speed-up is command-only and can never be accidentally saved as a permanent setting.

### Documentation
- All docs rewritten around the settings editor: `docs/CONFIGURATION.md` explains the database-backed model and how to reset everything to defaults, `docs/INSTALLATION.md` drops the SQL step, `docs/ADMIN.md` documents the `/weed` command and the editor, and `docs/TROUBLESHOOTING.md` covers "I edited a config file and nothing changed" and "the settings menu won't open".

### Migration Guide
- No SQL to run. On the first start after updating, the `oxide_settings` table is created automatically and your existing config values — customizations included — are imported into it.
- From then on, change settings with `/weed settings` in-game; config file edits no longer apply on an existing install.
- Update `o-link` to the latest version to enable the in-world Place and zone-drawing buttons. Everything else works on an older `o-link`; the menu just shows a notice.

## [1.2.7] - 2026-07-07

### Fixed
- **Players could load in unable to see any placed plants or equipment** — if a player's game instance changed after loading (character select screens, instanced interiors), their world data could be built for the wrong instance, leaving plants, lamps, equipment, heaters, racks and freeze dryers invisible until relog. The server now detects the change and refreshes the player automatically, and joining during server boot no longer produces a half-empty world.
- **Smoking stress relief and hunger cost now actually apply** — `Config.Smoking.needsEffects.stressRelief` (CBD strains reduce stress) and `hungerCost` (THC gives the munchies) were documented but had no effect. They now work on servers with a needs system.

### Changed
- Admin commands now accept standard QBCore/QBX/ESX admins with no `server.cfg` changes
- Players in other game instances no longer receive plant and equipment updates from outside their instance
- Removed the unused `sell` XP reward from `shared/config/progression.lua` — selling XP is granted by `oxide-drugselling`, not here
- SQL files are now viewable in escrowed builds

### Documentation
- All docs and the README rewritten in plain language, with corrected XP values, notes that the drying rack and brick press must be added to a store via `shared/config/store.lua`, and framework support stated up front

## [1.2.6] - 2026-05-27

### Fixed
- Interaction prompts no longer show on top of the plant harvesting minigame. On proximity-based interaction setups (where options appear automatically as you walk up to something), the plant's harvest prompt could stay on screen during the minigame. Targeting is now hidden while you harvest a planted or wild plant and restored as soon as you finish.

## [1.2.5] - 2026-05-17

### Fixed
- **Drying rack Collect button not appearing at 0:00** — same fix as the 1.2.3 freeze dryer fix, applied to drying racks. Trays now report dried buds as soon as the timer hits zero instead of waiting for the next background tick.
- **Duplicate plant / lamp / heater / equipment / wild plant models** — under load (many entities spawning at once, or a stage change firing mid-spawn) the client could create duplicate objects or leak entities. Spawning is now race-safe.
- **Plant model occasionally jumping back to an earlier growth stage** — growth is now monotonic and only ever advances.
- **Background loops silently stopping** — drying, freeze dryer, plant growth, smoking effects, and wild plant respawn are no longer wrapped in the logger's SafeCall and run reliably regardless of `oxide-logger`'s state. Errors still print to the server console.

### Changed
- `/weed_water` and `/weed_grow` admin commands now refresh the plant's quality and push the updated stats to clients (previously the menu showed stale values).
- Water and fertilizer percentages on the plant menu now show as whole numbers (e.g. `45%`) instead of decimals.
- Plant trait names on the plant menu now run through your locale file.
- `/weed_fasttick` now takes a multiplier: `/weed_fasttick 12` runs plants 12x faster, `/weed_fasttick 1` restores normal speed. Running it with no args still toggles 12x on/off. The multiplier affects both growth time and water/fertilizer decay.

## [1.2.4] - 2026-05-13

### Changed
- `Config.ImageBaseUrl` moved from `shared/config.lua` to `o-link/config.lua` so the CDN applies to all Oxide resources at once. Copy your value over if you had one set.

## [1.2.3] - 2026-05-12

### Added
- New `Config.ImageBaseUrl` option — set this to a CDN URL (e.g. `https://r2.qbox.re/myserver/inventory/`) and any item image the inventory bridge can't resolve locally will fall back to that URL. Leave `nil` to keep current behavior.

### Fixed
- **Freeze dryer Collect button not appearing at 0:00** — once a tray's timer hit zero, the UI could still report 0 dried buds until the next background tick (up to 30s). Slots now promote to dried as soon as they're due.
- **Freeze dryer timers drifting from the server clock** — remaining time is now anchored to the server's clock, so timers stay accurate after long sessions or if the UI was opened mid-cycle.
- **Freeze dryer panel out of sync** — the panel now re-syncs every few seconds and on demand, so missed state updates self-heal instead of requiring a reopen.
- **Freeze dryer bud models not updating from wet to dried** — placed buds now correctly switch from semi-transparent (wet) to solid (dried) without needing to reopen the panel.

### Changed
- Freeze dryer tray progress bar now fills smoothly as buds dry instead of only jumping when a slot finishes.
- Cleaned up unused commented-out items (growing manual, rolling papers, cigarillo wraps, lighter) from the default Germinator store config. Re-add them in `shared/config/store.lua` if you want them sold.

## [1.2.2] - 2026-05-09

### Added
- Structured logging through `oxide-logger` for every audit-worthy event — plant placement / harvest / death, genetics (strain registration, gender check, pollen, crossbreed), money flows, store purchases, scientist lab reports & cloning, drying / freeze dryer / drying rack state changes, packaging / bagging / brick press output, smoking sessions, ownership rejections, admin commands, and DB failures. Configure Discord or file sinks in `oxide-logger`'s config to receive the audit trail.
- Player stats card now fully localized — level, XP, "MAX" indicator, and progression milestone labels (Planting & Growing, Street Selling, Lab Reports, Crossbreeding, Seed Cloning, Freeze Dryer) read from your locale file.

### Fixed
- **Brick press metadata bug** — bricks produced before this version had `strain_id` set to the strain name and `strain_name` set to a traits table, breaking strain lookup and downstream genetics on pressed bricks.
- Seed cloning at the scientist now refunds the player if the clone fails (e.g. inventory full). Previously the player was charged but received nothing.
- Watering and fertilizing plants, refilling the watering can, filling water containers, and rolling joints/blunts now emit audit events — these inventory and state mutations were previously silent.
- Database query failures during boot (plants, lamps, equipment, drying slots, heaters) now log a warning instead of failing silently.

### Changed
- Removed the legacy `WeedDebug` print helper. Debug output now flows through `oxide-logger` and respects its configured log level / sinks. Setting `Config.Debug = true` no longer prints to the server console — install `oxide-logger` and set its level to `debug` for verbose tracing.

## [1.2.1] - 2026-05-07

### Added
- Per-snap-point rotation for heater placement on drying racks — `Config.Drying.heater.snapOffsets` entries can now be `{ offset = vec3(...), rotation = vec3(...) }` to set a rotation delta applied on top of the rack's rotation. Useful for custom drying rack models where the heater needs to face a different direction at each snap. Bare `vec3(...)` entries continue to work unchanged.

## [1.2.0] - 2026-04-14

### Added
- Server-side vehicle check — weed items (seeds, equipment, lamps, heaters, scissors, watering can) are now blocked while inside a vehicle
- Frontend locale loader now reads the active language from the `ox:locale` convar via a `getLocale` NUI callback and loads the matching `locales/<lang>.json`, with graceful fallback to English
- Spanish locale file (`locales/es.json`) for the Vue frontend

### Changed
- Migrated from `community_bridge` to `o-link` for framework abstraction — all cross-resource calls now use `olink.*` APIs

### Fixed
- Player model flickering and visibility during camera sequences (brick press, drying rack, crossbreed, freeze dryer, supply store, plant harvest, wild plant harvest) — replaced dual-call visibility toggling with tick-enforced `SetLocalPlayerInvisibleLocally`
- Smoking stress relief causing errors on frameworks that don't provide a stress removal function
- Weed table placement arrow pointing backwards — added `facingOffset = 180` to the weed_table config

## [1.1.0] - 2026-04-07

### Added
- **oxide-drugselling** — NPC street selling has been extracted into a standalone resource that supports all drug types (weed, meth, and custom). oxide-drugselling is included with your purchase and available on your Cfx.re Keymaster dashboard as a separate download. See its documentation for setup instructions.
- New server export `AddSold(source, amount)` — allows external resources (like oxide-drugselling) to track lifetime sold count on the player's weed progression
- New admin command `/weed_sellkit` — gives a random assortment of sellable weed products with proper strain metadata for testing selling


### Removed
- Built-in NPC street selling system — all selling config (`shared/config/selling.lua`), client/server selling logic, sell panel UI, and locale strings have been removed from oxide-weed. Install oxide-drugselling as the replacement.
- `Config.Progression.xpRewards.sell` — selling XP is now configured in oxide-drugselling's drug type config

## [1.0.3] - 2026-03-23

### Fixed
- Heat lamps and plants not disappearing after pickup/harvest due to silent entity deletion failures — added fallback cleanup methods
- "Attempt to index a nil value" error when interacting with a lamp or plant that was already removed server-side
- Ghost plant models from earlier growth stages persisting after stage transitions — orphaned entities are now retried for deletion and have their interactions stripped immediately
- Plant water and fertilize interactions crashing if the plant data was removed while the model was still visible
- Picking up lamps/plants multiple times in quick succession causing desync — interactions are now blocked while a removal is pending
- Server now notifies the client on lamp removal failure (inventory full, ownership, rate limit) so the interaction unblocks correctly

## [1.0.2] - 2026-03-22

### Added
- Configurable needs effect toggles for smoking (`needsEffects.stressRelief`, `needsEffects.hungerCost`, `needsEffects.healing`) — disable individually for frameworks without stress, hunger, etc.

### Changed
- Increased default smoking effect duration from 45-90s to 5-10 minutes (`densityDurationMin`/`densityDurationMax`)

### Fixed
- Removed unused config values (`baseDuration`, `maxSlots`, `moldOffset`, `coughChance`, dead selling animations)

## [1.0.1] - 2026-03-17

### Added
- **Drying Rack** - Standalone 4-tray drying rack with 32 slots, unlocks at level 3. Interactive tray-by-tray loading and collection via 3D NUI
- **Brick Press** - Compress 10 dried buds of the same strain into a weed brick ($800 base sell price), unlocks at level 8
- **Weed Brick** - New sellable item with full quality/trait/generation pricing support
- **Safe Zones** - Config-driven polygon zones where planting and equipment placement are blocked, with server-side enforcement and client-side red outline feedback. Define zones using the config file
- New XP rewards: drying rack load (5 XP/bud), drying rack collect (8 XP/bud), brick press (20 XP)
- New items: `drying_rack`, `brick_press`, `weed_brick` (must be registered in your framework)
- New item images: `brick_press.png`, `weed_brick.png`
- Grower's manual updated with drying rack, brick press, and revised equipment progression guidance

### Changed
- Equipment placement now auto-faces away from the player with a directional arrow indicator on the ground
- Equipment with non-standard model orientation can define `facingOffset` in config to correct the facing
- Drying section of grower's manual rewritten to cover all three drying methods (table, rack, freeze dryer)
- Supply shop manual section updated to reference new equipment tiers
- Point-in-polygon logic extracted to shared utility for use by both safe zones and wild plants

### Fixed
- Heaters not affecting drying rack drying time (was calling non-existent function instead of `WeedDrying.GetDryingMultiplier`)

## [1.0.0] - 2026-03-15

### Added
- Full cannabis cultivation system with 4-stage plant growth
- 10-trait genetics system with weighted quality calculation
- Plant gender mechanics with male/female determination at growth stage 2
- Crossbreeding system combining pollen and seeds to produce hybrid strains
- Strain registry with procedural name generation (1600+ unique names)
- Drying rack system with 14 slots per weed table
- Freeze dryer with 18 trays and 144 total slots (requires level 7)
- Heater system to accelerate drying (up to 3 per rack, 1.5x multiplier each)
- Trimming with scissors producing pruned buds and cannabis leaves
- Bagging via drag-and-drop NUI at the weed table
- Tiered packaging: 1g, eighth, quarter, half, ounce
- Joint and blunt rolling with trait-based smoking effects
- NPC street selling with price negotiation, counter-offers, and risk mechanics
- Wild plant foraging in configurable polygon zones with respawn timers
- Supply store NPC (The Germinator) with categorized inventory
- Scientist NPC (Best Buds Laboratories) for lab reports and seed cloning
- 10-level progression system with XP rewards for all activities
- Water container and watering can equipment for efficient hydration
- Storage tote with stash inventory access
- Growing manual item with comprehensive NUI guide
- Admin commands for testing and management
- Full localization support via `locales/en.json`
- Rate limiting on all server callbacks and events
- Ownership enforcement for placed items
- Routing bucket support for instanced gameplay
