# Changelog

All notable changes to oxide-blackmarket will be documented in this file.

## [1.9.4] - 2026-10-26

### Changed
- The `SetSetting` export refuses a change while settings are still loading at startup and returns `false`. Before, it accepted the change and then lost it.

### Fixed
- oxide-blackmarket no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.
- `/blackmarket settings` now says why a change wasn't saved (settings still loading, a number that isn't valid, or a database error) instead of "value rejected", and keeps the old value. A failed reset now shows an error too.

### Documentation
- `docs/EXPORTS.md`: when `SetSetting` refuses a change.

## [1.9.3] - 2026-10-23

### Fixed
- With dirty money set to an account (`mode = 'account'` in `Config.DirtyMoney` or `Config.SellPayout`), a purchase whose payment is refused no longer hands over the item, and a sale whose payout is refused gives the items back.
- A purchase refund, a sold item that can't be given back, or an arrest reward that can't be paid is logged with the player and amount, and every admin online is alerted to pay it by hand.

## [1.9.2] - 2026-10-04

### Changed
- The Burner Pager's item image now ships in `itemimages/blackmarket_pager.png`, with a paste-ready ox_inventory entry in `itemimages/ox_inventory_items.lua`.
- Every button and placeholder in `/blackmarket settings` can now be translated in `locales/en.json`.
- Attempts to open or change `/blackmarket settings` without admin rights are now written to the log.

### Fixed
- **Pager tips without a phone.** On servers with no phone resource, the tip now shows as a notification with the location, instead of telling the player to check an inbox that never gets the email. With a phone, the tip still arrives as an email.
- A `/blackmarket settings` change the database fails to save is now reported as failed, instead of applying until the next restart. Resetting a setting reports a failed save the same way.
- Pressing ESC to cancel an in-world placement no longer closes `/blackmarket settings` as well.
- `/blackmarket settings` now closes when your character logs out.

### Documentation
- `docs/FEATURES.md` and `docs/INSTALLATION.md`: how pager tips arrive with and without a phone, and where the item image now lives.

## [1.9.1] - 2026-09-22

### Changed
- **Dealer spawn locations are now server-only.** `Config.Locations` moved from `shared/config.lua` to the new `server/config.lua`, and the list is no longer sent to players' game clients - cheaters can't read it out of the downloaded resource files or out of the synced settings.
- Editing spawn locations in `/blackmarket settings` works exactly as before. If you already run the resource, your saved locations live in the database and carry over - nothing to redo.

### Fixed
- Settings you customized could be reset to the file defaults at startup if the settings table read came back empty. Startup now only adds settings that are missing, and never overwrites saved ones.
- Decimal settings in `/blackmarket settings` no longer come back as `0.90000000000009` after saving.

### Documentation
- `CONFIGURATION.md` and `INSTALLATION.md` updated for the new `server/config.lua` file.

## [1.9.0] - 2026-09-02

### Added
- **Gang standing at the black market** - two new settings under the new **Gangs** section of `/blackmarket settings`, both needing the oxide-gangs territory system installed (they're ignored otherwise).
- `Config.GangTurfRepMultiplier` (default `1.5`) multiplies the reputation earned when you sell to a dealer standing in territory your own gang owns. Set it to `1.0` to turn the bonus off.
- `Config.GangReportInfluence` (default on) makes black market sales count toward your gang's influence in that territory, the same way street drug sales do. The gang system's own anti-farm limits still apply.

### Fixed
- Settings you switched **off** in `/blackmarket settings` reverted to their file defaults on every restart. Saved off-toggles now stick.

## [1.8.0] - 2026-08-30

### Added
- **Pay sellers in a different currency than buyers pay with** - the new `Config.SellPayout` setting controls what players are paid in when selling to a dealer and when finding cash in a dead dealer's loot bag. For example, charge regular cash on purchases but pay out dirty money on sales.
- It takes the same options as `Config.DirtyMoney` - a money account or an inventory item, including marked bills that carry their value in metadata. The default (`inherit`) keeps paying out with your `Config.DirtyMoney` setup, so existing servers are unchanged until you change it.
- Editable live in `/blackmarket settings` under **Dirty Money** - changes apply to the next sale, no restart needed.
- With split currencies, purchase refunds always return the buy currency the player was actually charged, and the shop's Balance readout keeps showing the buy currency - it won't go up after a sale, since the payout went to the other currency.
- The startup catalog check now also verifies the sell-payout item name when item mode is used.

## [1.7.1] - 2026-08-05

### Fixed
- Numbers and dealer locations saved in `/blackmarket settings` are now validated more strictly: corrupt values can no longer be saved or persist in the database, and previously saved settings are re-checked and repaired automatically at each restart.

## [1.7.0] - 2026-07-26

### Added
- **Reserve a tier for certain jobs or gangs** - every reputation tier now has an optional job list and gang list (`Config.Tiers`), so part of a dealer's stock can be sold only to the groups you name. Reputation is still required; the lists are an extra condition on top of it.
- Filling both lists lets in anyone holding a listed job **or** a listed gang. Leaving both empty (the default) keeps a tier open to everyone, so existing setups are unchanged.
- Restricting one tier hides only that tier's items - the tiers above it stay available to players who qualify for them.
- Both lists are editable live in `/blackmarket settings` under **Reputation** (Advanced tab) - changes apply the next time a player opens a dealer, no restart needed.
- Gang lists have no effect on ESX, which has no gang system - use the job list there instead.

### Fixed
- **Emptying a list in the settings menu no longer breaks it** - after clearing every entry from a list and saving, reopening the menu left that list unable to accept new entries until a restart. This affected text lists (such as the police job list) and placed-point lists (dealer spawn points, arrest drop-offs) - on the latter it also blocked placing new points back in the world.

## [1.6.1] - 2026-07-05

### Fixed
- **Shop item edits not taking effect after a restart** - editing a dealer's buy catalog in the settings menu updated the shop's appearance, but purchases still handed out the items from before the edit until the next time you saved in-game. Your saved catalog now loads correctly on every start, so what a player buys always matches what the shop shows.
- **Resetting a setting now applies immediately** - resetting the shop inventory or dealer spawn settings to default from the settings menu takes effect right away instead of waiting for a restart.

## [1.6.0] - 2026-07-01

### Added
- **Multiple dealers at once** - the map now hosts several dealers at the same time (3 by default, `Config.DealerCount`). Each dealer has his own spot, his own stock, and moves on his own timer, so buying one dealer out doesn't empty the others.
- **In-game settings menu** - `/blackmarket settings` (admin only) opens a full settings editor: change almost any setting live, add or move dealer spawn points and arrest drop-offs by placing them in the world, and edit the buy/sell catalogs with a searchable item picker. Changes save to the database and apply immediately - no restart needed.
- **Settings stored in the database** - configuration now lives in a shared `oxide_settings` table (created automatically on first start; `sql/install.sql` is included for manual setups). `shared/config.lua` and `shared/items.lua` are now the factory defaults: they are imported on the first start and fill in new settings after updates. Set them up before your first start, then manage settings in-game or in the database.
- **Living economy**:
  - Sell prices react to supply - flooding the market with an item drops its sell price for everyone, and the price recovers over time (`Config.Market`).
  - Dealers restock toward their maximum stock on a timer (`Config.RestockEnabled`, `Config.RestockInterval`, `Config.RestockPercent`).
  - Optional per-player daily sell caps to curb money printing (`Config.DailySellCap`, off by default).
- **Optional oxide-police integration** - with `Config.ArrestPoliceIntegration = true`, the department cut from an arrest is paid into the arresting officer's real oxide-police department treasury. Everything works without oxide-police installed.
- **Shop categories and search** - the buy and sell lists are grouped under category headings and have a search box. Every entry in `shared/items.lua` now has a `category` field.
- **Startup catalog check** - on start the resource checks every item name in your buy/sell catalogs (and your dirty-money item) against your inventory system and warns about any name it doesn't recognize, so typos surface immediately instead of failing silently at the counter.

### Changed
- **Arresting a dealer is now a transport job** - instead of an instant arrest, the officer cuffs the surrendered dealer, escorts him on foot, loads him into a vehicle, and drives him to a drop-off point (`Config.ArrestDropOffs`) to collect the reward. The dealer breaks free if the officer wanders off (`Config.EscortBreakFreeDistance`) or the transport takes too long (`Config.EscortMaxDuration`). Who gets paid on delivery is configurable (`Config.ArrestRewardRecipient`: officer, society, or both).
- **Pager intel reworked** - the reply now covers the nearest dealer and arrives as an anonymous phone email whose precision scales with reputation (`Config.PagerIntel`): general area at Tier 2, street and district at Tier 3, street plus a GPS waypoint at Tier 4. The player plays a texting animation with a progress bar while waiting, and the reply delay is configurable (`Config.PagerResponseDelay`).
- **Admin commands consolidated** - the old commands are replaced by `/blackmarket` subcommands: `/blackmarket relocate` (moves all dealers), `/blackmarket status`, `/blackmarket goto` (teleports to the nearest dealer), `/blackmarket setrep`, `/blackmarket checkrep`, and the new `/blackmarket settings`.
- Shop UI rebuilt with a new look - same buy/sell flow as before.
- A surrendered dealer now waits 15 seconds (was 10) before trying to break free, and no longer tries once he's been cuffed.
- New required dependency: `oxmysql` - add `ensure oxmysql` before this resource.

### Removed
- `/relocatedealer`, `/dealerstatus`, `/godealer`, `/setblackmarketrep`, `/checkblackmarketrep` - replaced by the `/blackmarket` subcommands above. Update any admin macros or documentation that used the old names.

## [1.5.1] - 2026-06-18

### Fixed
- **Blueprint USBs selling the wrong product** - shop entries that share an item name (the printer USB blueprints) were treated as one product: buying any USB charged the SNS Pistol USB price, delivered the SNS Pistol blueprint, and all USBs drew from one stock pool. Each shop entry now has its own price, stock, and item data.
- Loot bag items now keep their item data - looted weapons stay serial-free and looted blueprint USBs keep their blueprint.

### Changed
- Admin commands (`relocatedealer`, `dealerstatus`, `godealer`, `setblackmarketrep`, `checkblackmarketrep`) now use the standard admin check instead of a restricted ACE, so they work for your framework's admins and tell a non-admin they lack permission instead of silently doing nothing.
- `setblackmarketrep` now ignores negative or fractional values - reputation can only be set to a whole number of zero or more.
- `Config.Debug` now ships disabled. Turn it on while setting up or troubleshooting.

## [1.5.0] - 2026-05-21

### Fixed
- **Dealer not interactable for players who joined or teleported in late** - players outside scope when the dealer spawned or relocated couldn't open the trade menu. The dealer is now identified consistently for every player who reaches them.
- **Dealer not wandering after spawn** - the dealer would freeze in place until interacted with. They now begin wandering as soon as someone is nearby.
- **One-shot kills not registering** - a single fatal hit on the dealer sometimes failed to trigger death detection (no loot, no hit squad, body left on the ground). All death types are now detected reliably.
- **Hit squad never arriving** - the retaliation squad would spawn and sit there. They now actually drive to the player and engage.
- **Hit squad spawning on top of the player** - spawn distance is now consistent regardless of where the dealer dies.
- **Hit squad teleporting members into the driver seat mid-firefight** - killing the driver during on-foot combat no longer warps another member into the car. Driver swaps only happen when the squad actually needs to drive.

### Added
- `Config.HitSquadMaxChaseDuration` - releases the squad after this many ms in vehicle mode without reaching the player (safety net for bad pathing). Defaults to `90000`.

### Changed
- `Config.HitSquadDriveSpeed` default lowered from `80.0` to `30.0` (m/s). The old default was ~180 mph and caused the AI to constantly lose control on corners.
- `Config.HitSquadSpawnDistMin` / `HitSquadSpawnDistMax` defaults lowered from `350` / `450` to `200` / `300`. Closer spawns reliably trigger chase behavior.

## [1.4.1] - 2026-05-12

### Fixed
- **Dealer becoming invincible / unkillable / unarrestable** - the dealer ped used a huge entity culling radius (50,000) which forced every player on the server into the entity's scope at once. Under OneSync this thrashed ownership migration between players and could leave the dealer in a state where damage and arrest attempts had no effect. The dealer now uses the default culling radius with orphan-mode persistence, so ownership migrates cleanly between nearby players and the dealer remains damageable and arrestable as intended.
- Dealer initialization no longer reports success when the dealer ped fails to spawn - the failure is now surfaced so you can see it.

### Added
- **Structured logging via oxide-logger** - money movements, transactions, dealer spawns/kills/arrests, loot collection, reputation changes, admin commands, and security rejections (proximity exploits, invalid input, unauthorized arrest attempts) are now logged through the optional `oxide-logger` resource. Install `oxide-logger` to get a full audit trail in your console, file logs, or Discord webhooks. No effect if `oxide-logger` is not installed.
- **Dirty money metadata mode** - new `Config.DirtyMoney.useMetadata` option lets you store dirty money as the value of a metadata field (default `worth`) on the configured item rather than stack count. Useful for inventory systems that use marked-bills patterns. Bills are consumed greedily by highest value first, and any change is returned as a new bill.

### Changed
- Removed verbose `[oxide-blackmarket]` `print` lines from the arrest flow that were appearing in the console regardless of `Config.Debug`. All debug output is now gated by `Config.Debug` or the oxide-logger configuration.

## [1.4.0] - 2026-04-14

### Changed
- Optimized OneSync health sync loop in dealer death processing - reduced polling iterations from 20x100ms to 8x250ms while maintaining the same timeout window
- Converted framework/integration usage from `community_bridge` to `o-link`

## [1.3.0] - 2026-03-26

### Added
- `Config.EnableTiers` option to disable the reputation/tier system entirely - when set to `false`, all shop items are available to all players, reputation is not gained or lost, tier notifications are suppressed, pager works without tier requirements, and tier/rep display is hidden from the shop UI

## [1.2.2] - 2026-03-03

### Changed
- **Dirty money now supports inventory items** - new `Config.DirtyMoney` table replaces `Config.MoneyAccount`, allowing dirty money to be handled as either a framework account (`'account'` mode for ESX) or an inventory item (`'item'` mode for QBCore/QBX)
- Transactions now properly fail with user feedback when inventory is full in item mode (buying, selling, and loot collection)
- Selling items refunds the sold items back if dirty money can't be added to inventory

### Removed
- `Config.MoneyAccount` - replaced by `Config.DirtyMoney` table

## [1.2.1] - 2026-02-21

### Added
- **Configurable money account type** - new `Config.MoneyAccount` setting lets you choose which account type is used for all blackmarket transactions (buying, selling, loot drops)
  - ESX: set to `'black_money'` for native dirty money
  - QBCore/QBX: set to `'crypto'`, `'black_money'`, or whatever your server uses
  - Defaults to `'cash'` - existing behavior is unchanged out of the box
- Police arrest rewards always pay in regular cash regardless of `Config.MoneyAccount`

### Changed
- Shop UI label updated from "Cash" to "Balance" to reflect configurable account types
- "Not enough cash" notification updated to "Not enough funds"

## [1.2.0] - 2026-02-16

### Added
- **Multi-framework support** via [Community Bridge](https://community-bridge.com) - now works with QBCore, ESX, QBox, and more out of the box
- **Universal dispatch alerts** - compatible with 17+ dispatch systems (ps-dispatch, cd_dispatch, etc.) via configurable `Config.DispatchAlerts`
- **Universal phone support** - pager emails work with any phone system supported by Community Bridge
- **Universal target support** - works with qb-target, ox_target, and any target system supported by Community Bridge
- **Universal inventory support** - item images resolve automatically per inventory system, no manual path config needed
- **Universal progress bar support** - loot bag searching uses Bridge progress bar (compatible with ox_lib, qb, etc.)
- **Transaction rate-limiting** - prevents rapid-fire buy/sell exploit attempts
- **Server-side killer tracking** - dealer killer is now identified via `weaponDamageEvent` on the server, preventing client-side spoofing
- **Distance-based performance scaling** - dealer monitoring thread adjusts polling rate based on player distance (2s at 100m+ vs 100ms nearby)
- **Automatic area name resolution** - pager messages now resolve location names from GTA natives instead of a hardcoded list
- **Proper cleanup on disconnect** - all client state (hit squad, loot bags, NUI, blips) is fully cleaned up on character switch or disconnect
- **Pager cooldown cleanup** - server-side pager cooldowns are removed when players disconnect (prevents memory leaks)
- New dealer spawn locations (Cypress Flats, Davis, Little Seoul, Mirror Park, Banham Canyon, Sandy Shores, Grapeseed, Grand Senora Desert)

### Fixed
- Loot proximity check now validates distance **before** removing loot from storage, preventing edge case where loot is lost if player is too far
- Hit squad now targets the actual killer (server-tracked) instead of relying on client-side detection which could miss one-shot kills
- Reputation tier change events now properly fire to the blip system on every tier transition
- Admin commands (`/setblackmarketrep`, `/checkblackmarketrep`) now validate that the target player is online before executing
- ESC key detection loop no longer runs at full framerate when shop is closed
- Selling items now properly triggers tier-up notifications via `AddReputationWithNotify`

### Changed
- **Dependency changed from `qb-core` to `community_bridge`** - this is the only required dependency now
- Removed `@oxmysql/lib/MySQL.lua` dependency - handled by Community Bridge
- Removed `Config.TargetSystem`, `Config.InventorySystem`, `Config.InventoryImagePath`, `Config.PhoneSystem` settings - Community Bridge auto-detects these
- Removed hardcoded `Config.AreaNames` table - area names are now resolved dynamically from coordinates
- Removed `Config.PoliceDispatchEvent` and `Config.PoliceAlertChance` - replaced by `Config.DispatchAlerts` table with per-alert-type configuration
- Dispatch alerts (transaction + proximity) are now configurable with individual chance, message, code, icon, and blip settings
- Cuff sound effect replaced with native GTA sound (removed `InteractSound` dependency)
- Death report distance threshold increased from 50m to 200m to allow any nearby client to report (server validates independently)

## [1.1.0] - 2026-01-27

### Added
- Target system wrapper for unified interaction with qb-target and ox_target
- `Config.TargetSystem` setting to switch between targeting systems
- `Config.InventorySystem` setting to support both qb-inventory and qs-inventory
- `Config.InventoryImagePath` for configurable item image paths
- `Config.PoliceJobs` setting to configure which jobs can arrest dealers
- Unique loot ID generation for secure loot tracking

### Fixed
- Dealer death processing now prevents duplicate loot drops
- Item name validation in purchase and sell events

### Changed
- Dealer and loot bag interactions now use unified target system wrapper

## [1.0.1] - 2026-01-24

### Added
- LB Phone support for Tier 4 pager notifications
- `Config.PhoneSystem` setting to switch between 'qb-phone' and 'lb-phone'

### Fixed
- Police dispatch blip now properly removes after 60 seconds or when dealer despawns/dies

## [1.0.0]

### Features
- Dynamic black market dealer system with single dealer instance
- Reputation-based progression (5 tiers: Unknown, Street, Connected, Trusted, Inner Circle)
- Buy/sell shop with tier-locked inventory
- Police surrender/arrest mechanics
- Hit squad retaliation on dealer death
- Pager-based dealer discovery
- Server-side secured loot drops
- Proximity-based blip discovery
- Dealer relocation every 45 minutes
