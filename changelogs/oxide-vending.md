# Changelog

All notable changes to oxide-vending will be documented in this file.

## [1.7.1] - 2026-10-23

### Fixed
- A failed business registration refunds the fee to the account it was paid from. It always went to the bank before.
- A refund that can't be paid (business registration, vending machine purchases) is logged with the player and amount, and every admin online is alerted to pay it back by hand.

## [1.7.0] - 2026-10-18

### Added
- Business dashboard as an app on Oxide Tablet when it runs; `/vdb` opens it (`Config.Dashboard.UseTablet` to turn it off).
- The business dashboard map covers Cayo Perico. `Config.MapMode` (combined, separate or disabled; default follows o-link) is used on the first start; change it later with `oxide-vending:mapmode <mode>` from the console, or as an admin in game.
- Optional weather effect on NPC sales with Oxide Weather (off by default, `config/weather.lua`): rain lowers sales, and heat raises drink sales. Edit the file and restart; other scripts can adjust it while the server runs with the `SetSetting` export.
- Works with Oxide HUD (free): the machine placement hint can be moved or switched off in `/hud` and follows the accent (`Config.UseHudAccent`). Needs o-link 1.12.1 or newer.

### Changed
- The dashboard map now comes from o-link and loads only what is on screen; the resource download is far smaller. **Needs o-link 1.8.0 or newer**, or the dashboard won't load.
- The map and its status counts show only machines in the region on screen.
- Business account deposits and withdrawals take whole dollars only.
- The placement hint can be translated.

### Fixed
- Business banking: on some frameworks, fractional deposits and withdrawals could create money, and a deposit could credit the business without taking the player's money. A withdrawal the framework refuses now goes back into the business account.
- The dashboard's wholesale orders and price changes accepted fractional numbers that the menu refuses.
- Players saw raw text instead of messages for the business account menu, a failed bank deposit and a failed restock.
- Since 1.4.0, `sql/reset.sql` rebuilt `vending_businesses` without the `balance` column, so business balances weren't saved after a reset. It now matches `sql/install.sql`. If you ran the old `reset.sql`, run once: `ALTER TABLE vending_businesses ADD COLUMN balance BIGINT NOT NULL DEFAULT 0;`
- Employees without the View balance permission could see the business balance and transactions.

### Removed
- `Config.Notifications` (never used; you can delete it).
- The bundled map tiles and an unused 28 MB map image (o-link supplies the map).

## [1.6.0] - 2026-09-02

### Added
- Nine new server exports for integrators: `GetTotalWarehouseItems`, `GetWarehouseByBucket`, `PurchaseWarehouse`, `UpdateWarehouseCode`, `ValidateAccessCode`, `CreatePickupBox`, `GetPickupBoxesByBusiness`, `GetPickupBox`, and `GetNPCSalesStats`. See `docs/EXPORTS.md`.

### Fixed
- **Admin commands work for your existing admins again.** All twelve `/vending*` commands were gated behind a per-command permission most servers never set up, so admins got no response at all. They now use the standard admin check — if you already have QBCore, QBX or ESX admins, they pass with no extra setup, and a non-admin who tries one gets a clear "no permission" message instead of silence.
- The vending radial menu no longer depends on a specific menu resource, so the Dashboard / My Machines / Employees / Place Machine entries appear on more server setups.
- `/vendingdebug daily` compared local dates against UTC-stored records, so its "today" and "yesterday" readouts could be a day off. Both now use UTC.
- The startup warning for missing progression tables pointed at `sql/progression.sql`, which doesn't exist — it now names `sql/install.sql`.

### Documentation
- Rewrote all seven guides (`ADMIN.md`, `CONFIGURATION.md`, `FEATURES.md`, `INSTALLATION.md`, `NPC_SALES.md`, `PROGRESSION.md`, `TROUBLESHOOTING.md`) in plain language, with the admin permission setup spelled out line by line in `docs/ADMIN.md`.

### Migration Guide
- No SQL to run. Restart the server once after updating.
- If you previously added per-command permission entries to `server.cfg` to make `/vending*` work, you can remove them — they're no longer used. Admins now just need `add_principal identifier.license:<their-license> group.admin` and `add_ace group.admin admin allow`.

## [1.5.0] - 2026-04-14

### Changed
- Updated all progress bar calls to use the new o-link API format (`disable`, `anim`, `flag` instead of `disableControls`, `animation`, `flags`)
- Added thread yields (`Wait(0)`) between each data loading step during startup to prevent server hitches on resource start
- **Placement system rewritten** to match the standard used by other oxide resources: camera raycast preview with a green/red validity outline, scroll wheel to rotate, `E` to place, `Backspace` to cancel, and `olink.helptext` for on-screen controls. Replaces the two-stage "follow the player" flow.
- **Migrated from community_bridge to o-link** — All framework abstraction now uses o-link instead of community_bridge. Dependencies updated to `oxmysql`, `ox_lib`, and `o-link
- **NUI locale loader refactored** to match the canonical pattern used by oxide-weed / oxide-meth / oxide-drugselling / oxide-weaponprinting. The frontend now fetches `locales/{lang}.json` dynamically from the NUI side via a new `getLocale` client callback (returning the `ox:locale` convar), with automatic English fallback. Replaces the previous Lua-push pattern (`SendNUIMessage({action='setLocales', ...})`) which filtered and sent `nui.*` keys at startup. The `t()` helper now supports both `%s` and `%d` positional formatting

### Removed
- `Config.Placement.FollowOffset` and `Config.Placement.RotationSensitivity` (no longer used by the new placement system)
- Unused `placement.lock_position`, `placement.rotate_place`, and `notify.placement_started` locale keys (replaced by `placement.controls`)
- Unused `DrawText3D` helper from `client/utils.lua`

## [1.4.0] - 2026-03-18

### Added
- `Config.Business.MaxBusinesses` option to set a server-wide limit on total businesses (0 = unlimited)

### Changed
- **Migrated to community_bridge** — All framework abstraction is now handled by community_bridge instead of the built-in bridge system. Dependencies simplified to just `oxmysql`, `ox_lib`, and `community_bridge` for all frameworks
- **Business balance is now built-in** — Stored directly on the `vending_businesses` table instead of using `oxide-banking`. Existing servers must run:
  ```sql
  ALTER TABLE vending_businesses ADD COLUMN balance BIGINT NOT NULL DEFAULT 0;
  ```
- Dashboard map rewritten with tiled PNG rendering (20 tiles in a 4x5 grid) replacing the single 6MB `gta-map.png` image. Default zoom starts further out (0.5x) with a range of 0.25x to 5.0x for crisp rendering at all zoom levels

### Removed
- Built-in `bridge/` directory and all framework-specific bridge files
- `Config.BridgeOverride` setting (framework detection handled by community_bridge)
- `Config.StandaloneMoney` and `Config.StandaloneInventory` function tables (handled by community_bridge)
- `Config.UseBanking` and `Config.BusinessAccountPrefix` settings — business balance is now built-in on the `vending_businesses` table, replacing the `oxide-banking` integration
- `oxide-banking` optional dependency — no longer needed
- Framework-specific dependencies (qb-core, qb-target, qb-inventory, qb-radialmenu, qb-input, es_extended, ox_target, ox_inventory) — these are now managed through community_bridge

## [1.3.0] - 2026-02-12

### Added
- **Multi-Framework Support** — Full support for QBCore, ESX, and Standalone (ox_lib + ox_inventory + ox_target) servers via a new framework bridge system with automatic detection. Configure manually with `Config.BridgeOverride` or let it auto-detect.
- **Multi-Inventory Support** — Auto-detects `ak47_qb_inventory`, `ak47_inventory`, `ox_inventory`, and `qb-inventory`. No configuration needed — the bridge picks the active inventory system automatically per framework.
- **Account-Wide Business Restriction** — New `Config.Business.OnePerAccount` option to limit players to one business across all characters sharing the same license.
- **Vehicle Placement Check** — Players can no longer place vending machines while inside a vehicle.
- **`GetNPCSalesStats` Export** — External scripts can now query NPC sales simulation stats via `exports['oxide-vending']:GetNPCSalesStats()`.

### Changed
- **Locale Format** — Locales migrated from `locales/en.lua` to `locales/en.json`. Custom translations will need to be converted to JSON format.
- **Transaction Safety** — Business registration fee is now charged before creation with automatic refund on failure. Revenue collection verifies database success before updating in-memory caches. Warehouse withdrawals roll back on inventory failures.
- **Banking Validation** — Withdrawal and deposit amounts are now validated server-side as positive integers.
- **QBx Streamlined** — Removed redundant QBx-specific code paths in bridge files; QBx now routes through the same QBCore code paths.
- **Inventory Detection** — `Config.UseOxInventory` is no longer needed. `ox_inventory` is now auto-detected on all frameworks including ESX.
- **Documentation** updated for multi-framework setup, bridge configuration, and framework-specific troubleshooting.

### Removed
- `locales/en.lua` — Replaced by `locales/en.json`.

## [1.2.0] - 2026-01-30

### Added
- **Warehouse System**: Business inventory management for bulk storage
  - Purchase and manage a warehouse building for your business
  - Store items in bulk before distributing to vending machines
  - Warehouse state restoration on player reconnect
  - Security measures and access controls
- **Pickup Location Feature**: Wholesale orders can now be picked up at designated locations
- **Contract Signing**: Business registration now requires signing a contract for immersion

### Changed
- Enhanced vending machine pricing system with improved validation
- Updated database schema to support warehouse and pickup features
- Improved NPC sales item configuration and behavior
- Better user notifications for warehouse operations

### Fixed
- Warehouse callback issues and removed dead code
- Various warehouse state restoration edge cases

## [1.1.1] - 2026-01-22

### Fixed
- Analytics tab: "No data" placeholders now properly positioned within chart containers instead of appearing behind or out of place

## [1.1.0] - 2026-01-17

### Added
- **Machine Competition System**: NPCs now prefer cheaper machines when multiple same-type machines are nearby
  - Configurable competition radius (default 500m)
  - Same machine types compete (drinks vs drinks, snacks vs snacks)
  - Per-item price comparison - cheaper items get more NPC sales
  - Probabilistic model - expensive machines still get some sales, just fewer
  - Cross-business only - your own machines don't compete with each other
- New `Config.Competition` settings in `config/npc_sales.lua`:
  - `Radius` - Competition detection radius
  - `MinMultiplier` / `MaxMultiplier` - Bounds for price competitiveness effect
  - `CheapestBonus` - Extra sales bonus when you have the cheapest price
  - `SameOwnerCompetes` - Toggle for same-business competition
- Dashboard Analytics: "Market Competition" section showing:
  - Summary cards: Nearby Competitors, Price Competitiveness %, Best Price Items, Overpriced Items
  - Per-machine breakdown with competitor names and distances
  - Price comparison table with your price, avg competitor price, rank, and suggested prices
  - Color-coded rows: green for cheapest, red for most expensive
- Server-side competition helpers: `GetCompetitors()`, `CalculateCompetitionMultiplier()`, `UpdateCompetitionAnalysis()`
- New server callback: `oxide-vending:server:getCompetitionAnalysis`

## [1.0.0] - 2026-01-16

### Changed
- Removed statistics/transaction history menus from oxide-menu (use NUI dashboard instead)
- Debug print statements now properly gated behind `Config.Debug` flag
- Cleaned up LoadBusinesses and LoadMachines startup logging

### Removed
- Unused server callbacks: `isOwner`, `getBusinessDashboard`, `getBusinessTransactionHistory`
- Unused server functions: `SyncMachineToNearby`, `RemoveMachineFromNearby`, `GetTotalStockCount`
- Unused client helper: `UpdateMenuItem`
- Hardcoded debug print statements in dashboard NUI callback

### Fixed
- Transaction history in NUI now correctly shows NPC sales with same styling as player sales
- Combined "Player Sales" and "NPC Sales" filters into single "Sales" filter for immersion
