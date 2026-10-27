# Changelog

## [1.4.1] - 2026-10-26

### Changed
- The `SetSetting` export refuses a change while settings are still loading at startup, and returns `false` with the reason. Before, it accepted the change and then lost it.

### Fixed
- oxide-shops no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.
- `/shop settings` now says why a change wasn't saved (settings still loading, a number that isn't valid, or a database error) instead of "value rejected", and keeps the old value. A failed reset now shows an error too.

### Documentation
- `docs/EXPORTS.md`: when `SetSetting` refuses a change, and the reason it returns.

## [1.4.0] - 2026-10-23

### Added
- Works with Oxide HUD (free): the HUD hides while a player sits in a barber or tattoo chair, including the fades, and while an admin uses the tattoo camera editor. Needs o-link 1.12.1 or newer; without Oxide HUD nothing changes.

### Fixed
- Weapon registration takes the fee first. A refused charge no longer registers weapons for free, and weapons that fail to register get their share of the fee back.
- Tattoos are charged before they are applied or removed, and refunded if the change can't be saved.
- A refund or item that can't be given back (masks, pawn shop, clothing, barber, shop purchases, auction listings, bids and buyouts) is logged with the player and amount, and every admin online is alerted to return it by hand.
- Logging out now closes the tattoo parlor.

## [1.3.0] - 2026-09-29

### Added
- Owners can rename their shop. The **Overview** window of the management panel, at the shop or on the tablet, now has a **Rename Shop** button. Blank names are refused, and map color codes and other formatting characters are removed. The map blip updates for everyone right away, unless an admin gave the shop a custom blip label. The name stays if the shop is resold or reclaimed.
- Two new settings under General in `/shop settings`: **Allow owners to rename shops** (`Config.AllowPlayerRename`, on by default) and **Max shop name length** (`Config.MaxShopNameLength`, default `32`). Admins can still rename any shop in `/shop admin` when owner renaming is off.
- New rate limit `renameShop`: 5 seconds between renames per player.
- `itemimages/ox_inventory_items.lua`: the four shop supply items (`hair_product`, `tattoo_ink`, `mask_blank`, `fabric`), ready to paste into ox_inventory's `data/items.lua`.

### Changed
- Every button, placeholder, tooltip and empty-list message in `/shop settings` can now be translated in `locales/en.json` (the `settings.ui.*` keys).

### Fixed
- Pressing ESC to cancel an in-world placement no longer closes the whole `/shop settings` menu.
- If a setting can't be saved to the database, `/shop settings` now marks it as not saved and keeps the old value. Before, the server used the unsaved value until the next restart. **Reset to default** now reports a failed reset too.
- The `/shop settings` panel no longer stays on screen after the admin's character logs out.
- Renaming a shop in `/shop admin` now updates the owner's **My Shops** tablet widget straight away.

### Documentation
- `docs/FEATURES.md`: new **Renaming a Shop** section; Map Blips notes that renames update the blip.
- `docs/CONFIGURATION.md`: the two rename settings and the `renameShop` rate limit.
- `docs/ADMIN.md`: admins can rename any shop even with owner renaming off.
- `docs/INSTALLATION.md`: the ox_inventory step now points to `itemimages/ox_inventory_items.lua`.

## [1.2.0] - 2026-09-16

### Added
- Melee weapons no longer need a weapon license. When **Require a weapon license** is on, two new lists under Weapon Shop in `/shop settings` decide what sells without one: **Categories sold without a license** (ships with `Melee`) and **Items sold without a license** (ships with `weapon_knife`, `weapon_bat`, `weapon_crowbar`, `weapon_hammer`, `weapon_nightstick`). An item is exempt if either list matches it. Items added with **Add item**, wholesale or transfer have no category, so exempt those by name. Clear both lists to require the license for everything, as before. If you already had the requirement on, melee now sells without a license until you clear the lists.
- The shop map in `/shop admin` is now drawn from o-link's shared map and includes Cayo Perico. A new **Map mode** setting under General in `/shop settings` (`Config.MapMode`, default `inherit`) chooses `combined` islands, `separate` island views, or `disabled` for mainland only; `oxide-shops:mapmode <mode>` from the server console does the same. **Requires o-link 1.8.0 or newer.**

### Changed
- The message unlicensed buyers see now reads "You need a weapon license to buy this item" (`shop_weapon_license_required` in `locales/en.json`).

### Fixed
- Saved `/shop settings` values can no longer be overwritten with the file defaults if the database read fails during startup.
- Decimal settings no longer show rounding noise like `0.90000000000009` in the menu.

### Documentation
- `docs/CONFIGURATION.md`: the Weapon Shop section now documents **Require a weapon license**, **License type** and the two exemption lists; new **Shared map mode** section.
- `docs/FEATURES.md`: Weapon license bullet under Weapon Shops.

## [1.1.0] - 2026-09-09

### Added
- Weapon shops can now require a firearms license before a player is allowed to buy. Turn on **Require a weapon license** in `/shop settings` under Weapon Shop. It is off by default, so nothing changes until you enable it. **License type** sets which license is checked and defaults to `weapon` — the one QBCore and QBX already track. On ESX it has to match a type in your `user_licenses` table. If your framework has no license system at all, sales go through as before and a warning appears in your server console.
- Groundwork for the upcoming Oxide tablet: a **Shop Manager** tile, an **Auction House** tile, a **My Shops** home-screen widget listing each shop you own with its till, and optional notifications for sales, low stock and auction results. **The tablet has not been released yet, so none of this is active.** Until it is, shop management and the auction house open at the shop exactly as they always have, and nothing on your server changes.
- The matching Tablet settings appear in `/shop settings` so they are ready when the tablet arrives — **Tablet apps**, **Allow remote withdraw/deposit**, **Tablet notifications**, **Low stock warning at** and **Remote session timeout**. They have no effect until then. Remote **Withdraw** and **Deposit** will ship off by default: moving the till is the one shop action that puts cash straight into a player's pocket, so it stays at the shop counter unless you decide otherwise.

### Documentation
- `docs/CONFIGURATION.md`: new Tablet section covering all five settings and why remote cash actions ship off.

## [1.0.3] - 2026-09-04

### Changed
- The **Purchase Shop** option now shows the asking price on the option itself, and opens a confirmation with the shop name and price before any money is taken. Previously the purchase went through the instant the option was clicked.
- The shop templates and wholesale catalog that ship with the resource now list `firstaid` where they previously listed `medkit`. Servers that already have these saved in the database keep their current item — rename it in `/shop settings` if you want to match.

### Fixed
- Trying to buy a shop you can't afford now tells you the asking price and how much you are short, instead of only "You don't have enough money". The wording is editable in `locales/en.json`.
- Settings toggles switched **off** in `/shop settings` no longer revert to their built-in default on the next server restart.

## [1.0.2] - 2026-08-05

### Fixed
- The barber shop scene camera and admin-saved vehicle preview cameras (auction house spots) could open extremely zoomed-in or empty after their settings were saved. Saved camera values now apply correctly, and previously saved settings repair themselves automatically on the next server restart.
- Emptying a list in `/shop settings` no longer breaks the editor.
- Numbers saved in `/shop settings` are now validated more strictly: corrupt values can no longer be saved or persist in the database.

### Documentation
- `README.md` and `docs/INSTALLATION.md`: accuracy fixes from the docs review.

## [1.0.1] - 2026-07-26

### Fixed

- The admin panel map (`/shop admin`) was stuck on "Loading map…" unless oxide-vending was also installed on the server. The map images now ship inside oxide-shops, so the map works on its own.
- Mask shop catalog previews showed blank images on servers installed from the release package; the image files are now included and load correctly.

## [1.0.0] - 2026-07-13

- Initial public release of `oxide-shops`.
- 61 ready-to-use shops placed at familiar GTA locations out of the box: 22 general stores (24/7 supermarkets, LTD Gasoline, Rob's Liquor, hardware stores), 11 Ammunation gun stores, 15 clothing stores, 7 barbershops, 4 tattoo parlors, 1 pawn shop, and 1 auction house.
- Retail stores where players buy from a stocked catalog of 28 everyday items (food, drink, tools, medical supplies, equipment), with a configurable sales tax on every purchase (`Config.TaxRate`, default 5%).
- Weapon stores stocking 40 firearms, melee weapons, throwables, and body armor, shown with a rotating 3D weapon preview and on-screen weapon stats. Players can register their unregistered firearms for a flat fee (`Config.WeaponRegistrationFee`, default `$500`).
- Pawn shops that buy players' items at dynamic prices which drop as more of an item is sold and recover over time.
- Barbershops with a seated styling scene, charging per category the customer actually changes: haircut, hair color, facial hair, eyebrows, and eye color.
- Tattoo parlors with a seated application scene and 10 GTA tattoo collections, with separate apply and removal pricing.
- Mask shops and clothing stores; clothing pricing is set per slot across 14 outfit and accessory slots.
- Auction house where players list items or vehicles for timed auctions of 1, 6, 12, or 24 hours, with bidding, instant buyouts, and listing/completion fees. Each player can keep up to 10 active listings. Winners and sellers who are offline when an auction ends receive their items, refunds, and payouts automatically on their next login.
- Player-owned shops: players can buy a shop and run it as a business — own up to 3 shops (`Config.MaxShopsPerPlayer`), set their own retail prices, keep a shop balance, deposit and withdraw funds, and restock inventory from a wholesale supplier for profit.
- Automatic restocking that tops a player-owned store back up to a per-item target from wholesale on a timer (`Config.AutoRestock`), drawing down the shop's balance until it runs out.
- Shop resale between players (`Config.AllowResale`): an owner can list their shop for sale from the management screen; the next player to buy it at the shop takes over ownership, and the seller is paid the sale price minus a fee (`Config.ResaleFeePercent`), including a payout that waits for them if they are offline.
- Player-owned service shops (barber, tattoo, mask, clothing) consume supplies as they serve customers — `hair_product`, `tattoo_ink`, `mask_blank`, and `fabric` — and refuse service when empty until the owner restocks from wholesale. Static (server-run) shops are unlimited.
- Per-shop access control: restrict any shop to a specific job, a minimum job rank, and on-duty players only, and optionally hide its map blip.
- `/shop admin` opens an admin panel to create shops from a template picker, edit and delete them, reclaim a player-owned shop, adjust balances, change prices and stock, place shop peds, blips, and barber/tattoo chairs in the world, and set the camera framing for each tattoo.
- `/shop settings` opens a live in-game settings editor. Most configuration is stored in the database and can be changed on the fly without editing files or restarting the server.
- Works on QBCore, ESX, and QBX (and custom frameworks) through the o-link bridge, with no framework-specific setup.
- Full localization: every notification and menu label is loaded from `locales/en.json` — copy it to another language code and translate to localize the resource.
- Ships its database schema in `sql/install.sql` (shops, inventory, transaction history, tattoos, and auction tables), created automatically on first start.
