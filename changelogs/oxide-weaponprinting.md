# Changelog

## [1.1.4] - 2026-09-02

### Fixed
- **Jammed weapons actually jam now.** A jam was announced on screen but the weapon kept firing normally, so jamming had no effect on gameplay at all.
- **Printed weapons wear out at the right rate.** Durability counted every frame the trigger was held rather than shots actually fired, so a printed weapon degraded far faster than the shot counts in your config — a burst could register dozens of "shots" from a handful of rounds. Both durability and the jam roll now count real rounds fired.
- **The 3D printer's built-in screen works again.** The page that draws the printer's on-prop display was missing from the download, leaving the screen blank on every printer. It now shows the idle, blueprint-loaded and printing-progress states as intended.

### Changed
- **The weapon bench item was renamed from `weapon_bench` to `weaponbench`.** Some inventories reject item names starting with `weapon_`, which stopped the bench working on those servers entirely. Its icon was renamed to match (`itemimages/weaponbench.png`).
- The printer manual now reads the item names from your config instead of assuming the defaults, so renaming any of the printer, bench, table, USB or filament items keeps the manual working.

### Documentation
- Corrected `weapon_bench` to `weaponbench` throughout `docs/CONFIGURATION.md`, `docs/FEATURES.md` and `docs/INSTALLATION.md`, and the admin command permission column in `docs/ADMIN.md` now reads "Admin" rather than naming a per-command permission that is no longer used.

### Migration Guide
- **Rename the bench item in your inventory.** In your framework's item list, change `weapon_bench` to `weaponbench` (keep the label, weight and other properties as they are). Any bench items players are already holding will need to be re-issued.
- Re-copy the `itemimages/` folder into your inventory's image directory so the renamed icon is picked up.
- If you set `Config.BenchItem` to your own item name, no change is needed.

## [1.1.3] - 2026-07-07

### Fixed
- **Players could load in unable to see placed printers, benches and tables** - if a player's game instance changed after loading (character select screens, instanced interiors, housing), their world data could be built for the wrong instance, leaving equipment invisible until relog. The server now detects the change and refreshes the player automatically, and joining during server boot no longer produces a half-empty world.
- **26 printed-part item images renamed to match their item names** - images for printed parts were named `3d_<part>.png` while the items are named `3d_printed_<part>`, so those part icons never displayed. Re-copy the `itemimages/` folder into your inventory's image directory; filenames now match item names exactly.

### Changed
- **Admin commands use the standard admin permission** - all ten admin commands (`/printer_kit`, `/give_parts`, `/wp_setlevel`, etc.) now pass for stock QBCore/QBX/ESX admins and show a "No permission" message on denial instead of silently doing nothing. See the updated Permissions section in `docs/ADMIN.md`.
- **Instance-scoped updates** - players in other game instances no longer receive printer, bench and table placements or in-use updates from outside their instance.
- **Removed the unused `Config.Debug` key** from `shared/config.lua` - it had no effect since logging moved to the console log output.
- **Documentation corrections** - weapon count corrected to 52, the QBCore item snippet in `docs/INSTALLATION.md` fixed (it was invalid Lua as written), the label toggle key documented as H, XP rewards documented as scaling with part cost, and the admin permission setup rewritten.

## [1.1.2] - 2026-05-27

### Fixed
- **Ghost Guns Stay Untraceable** - Assembled 3D-printed weapons now keep their blank serial number permanently. Previously the serial could reappear when the weapon was handed to another player or moved between inventories, making the gun traceable again.

## [1.1.1] - 2026-05-10

### Added
- **Independent Durability & Jam Toggles** - Durability and jam systems can now be enabled/disabled independently via `Config.Durability.enabled` and `Config.Jam.enabled` in `shared/config/durability.lua`
  - Disable `Config.Durability` when your inventory already handles weapon decay (ak47_qb_inventory, qs-inventory, codem-inventory, etc.) to avoid double-decay or nil-quality issues
  - When durability is disabled, jam chance falls back to a flat per-shot percentage (`Config.Jam.flatChance`) instead of the durability-tiered chance table
  - When durability is disabled, weapon metadata no longer writes a durability value

### Changed
- **Structured Logging** - All internal debug output now routes through the `oxide-logger` bridge (`olink.logger`). Servers running oxide-logger get categorized, traceable logs for player actions, printer/bench interactions, crafting, and database operations. No effect on servers without oxide-logger.

### Fixed
- **Quality Rounding** - Weapon quality metadata is now properly floored to an integer, preventing rare non-numeric quality values from causing issues with downstream inventory systems

## [1.1.0] - 2026-04-14

### Changed
- **o-link Migration** - Converted entire resource from `community_bridge` to `o-link` bridge across all 16 files
  - Dependency updated from `community_bridge` to `o-link` in fxmanifest
  - All `Bridge.*` API calls replaced with `olink.*` equivalents
  - Lifecycle events switched to `olink:client:playerReady` / `olink:client:playerUnload`
  - Character metadata now uses `olink.character.GetMetadata` / `SetMetadata`
- **Rate Limit Tuning** - Reduced all action cooldowns for snappier gameplay
  - Placement/removal actions: 3000ms → 1000ms
  - Print/craft/collect actions: 2000–3000ms → 1000ms
  - Blueprint/USB browsing: 1000ms → 500ms
- **Inventory Guard Refactor** - Collapsed separate `CanCarryItem` + `AddItem` calls into single `AddItem` return-value checks for printers, benches, and tables, eliminating race conditions between check and action
- **Progress Bar API** - Updated field names to match o-link conventions (`animation` → `anim`, `flags` → `flag`, `disableControls` → `disable`)

## [1.0.1] - 2026-04-02

### Added
- **Item Printing (Preview)** - Printers can now produce non-weapon items directly, no bench assembly required
  - New `Config.ItemPrints` table in `shared/config/items.lua` for defining printable items
  - Each entry supports: `label`, `item`, `model` (optional), `filamentCost`, `printTime`
  - Items are delivered as-is on collection — no quality, durability, or serial metadata
  - Ghost prop preview works when a `model` is provided, gracefully skipped when nil
  - All existing printer systems apply: filament cost, print time, level gating, XP, upgrades
  - `/givewepblueprint` command now supports both weapon and item blueprint keys
  - NUI distinguishes items (cube icon) from weapons (scroll icon) in the blueprint grid and USB picker

### Limitations
- This is a **preview feature** — full item printing support with metadata, categories, and dedicated UI is planned for a future release
- Item prints are single-output only (one print = one finished item, no multi-part recipes)
- No quality grades, durability, or custom metadata on collected items
- Items skip weapon benches entirely — collected directly from the printer
- `Config.ItemPrints` keys must not collide with `Config.Blueprints` keys (weapons take priority if duplicated)

### Setup
1. Define items in `shared/config/items.lua`
2. Add level gates in `shared/config/progression.lua` under `blueprintLevels`
3. Optionally add ghost prop scale/offset in `shared/config/ghostprops.lua`
4. Give test USBs: `/givewepblueprint [playerid] [itemkey]`


## [1.0.0] - 2026-03-27

### Added
- **3D Printer System** - Placeable 3D printers with ghost-prop placement preview, DUI status screen, and blueprint loading via USB drives
- **Blueprint System** - 60+ weapon blueprints across pistols, SMGs, rifles, shotguns, snipers, and machine guns with per-part filament costs and print times
- **Part Printing** - Print individual weapon components (slides, barrels, receivers, stocks, magazines, chassis, etc.) with real-time progress tracking and collection
- **Printer Upgrades** - 5 upgrade categories (Nozzle, Storage Drive, Cooling Fan, Build Plate, Power Supply) with up to 3 tiers each, PSU requirements, and level gates
- **Weapon Assembly** - Placeable weapon benches for assembling printed parts into finished weapons with crafting animations and 3D weapon preview
- **Quality System** - 5-tier quality grading (Poor, Decent, Standard, Quality, Superior) based on player level, build plate upgrades, and experience bonuses
- **Weapon Durability** - Shot-based degradation, escalating jam chances at low durability, unjam animations, and weapon destruction at 0%
- **10-Level Progression** - XP from loading USBs, completing prints, and assembling weapons with tiered weapon and upgrade unlocks
- **Experience Bonuses** - Accumulated prints and assemblies grant progressive speed and quality bonuses up to a configurable maximum
- **USB Malfunction** - Configurable chance for USB loading to fail, reduced by player level and cooling fan upgrades
- **Bad Luck Protection** - Configurable chance and penalty for failed print attempts
- **Placeable Tables** - General-purpose placeable tables with ownership and per-player limits
- **Floating Labels** - Toggle-able floating info labels above printers and benches showing status, progress, and upgrade info
- **Camera System** - Smooth camera transitions when interacting with printers and benches
- **Safe Zones** - Configurable polygon zones where equipment placement is blocked
- **Ownership System** - Configurable owner-only pickup for all placed equipment
- **Per-Player Limits** - Configurable maximum count of printers, benches, and tables per player
- **Rate Limiting** - Server-side rate limiting on all player actions
- **Admin Commands** - Full suite of admin commands for testing: `/printer_kit`, `/bench_kit`, `/upgrade_kit`, `/give_parts`, `/givewepblueprint`, `/wp_setlevel`, `/wp_setxp`, `/wp_info`, `/wp_testkit`, `/table_kit`
- **Custom Props** - 30 custom streamed props for weapon parts and 3D printer model
- **Database Persistence** - All placed printers, benches, tables, and active prints persist across server restarts
