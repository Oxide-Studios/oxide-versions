# Changelog

## [2.2.0] - 2026-10-27

### Added
- Desert Glass and Black Diamond meth baggies from oxide-meth 2.0.0 can be sold.
- Signature recipe meth sells for more as the recipe's reputation grows, up to 15% with oxide-meth's default settings. oxide-meth sets the price through a new `export_multiplier` row in the meth entry of `shared/config/drugs.lua`. Signature baggies get their own line in the sell menu.
- Trap phone clients who order meth take Desert Glass and Black Diamond baggies too.
- The TrapFone text popup is a panel on Oxide HUD: move it or switch it off in `/hud`. `Config.UseHudAccent` (config file only) uses the HUD's accent colour for TrapFone highlights.

### Changed
- The meth `metaKey` passed to `Config.OnDrugSold` now starts with the signature id, or `none`, as in `none:blue_sky:High`. Update hooks that read the old `blue_sky:High`.
- The `SetSetting` export refuses a change while settings are still loading at startup and returns `false`. Before, a change made in those first moments could be overwritten as the saved settings loaded.
- Settings save errors can be translated (`settings.error.*` in `locales/en.json`).

### Fixed
- A function inside a customized config no longer stops oxide-drugselling's settings from loading. The function is ignored and the server console names it, along with the two hooks the resource does read, `Config.OnDrugSold` and `Config.OnPhoneExamined`.
- When `/drugselling settings` can't save a change to the database, it says "could not be saved, see the server console" instead of "value rejected". A failed reset shows an error; before, nothing appeared.
- A drug resource that runs without the price export its config row names (oxide-meth 1.7.0, for example) no longer empties the sell menu. That drug sells at its usual price and the server console warns once.
- Weed strains of one quality no longer merge into one sell line, and weed's strain sale credit now counts.

### Documentation
- Configuration, features, exports, installation and README cover the meth signature key, the oxide-meth price row, the premium baggies, the Oxide HUD popup with its o-link version, `Config.UseHudAccent`, and how the sell menu groups weed by strain and quality.

### Migration Guide
- `Config.Drugs` is file-only. If you customized `shared/config/drugs.lua`, make these changes in the `meth` entry:
  - in `items`: `meth_baggy_desert_glass = { label = 'Desert Glass Baggy', basePrice = 140 },` and `meth_baggy_black_diamond = { label = 'Black Diamond Baggy', basePrice = 140 },`
  - replace `metaKeyFormat` with `metaKeyFormat = '{signature}:{variant}:{purity}',`
  - in the `variant_multiplier` row's `variants`: `desert_glass = 1.7,` and `black_diamond = 1.85,`
  - as the last entry in `modifiers`: `{ type = 'export_multiplier', category = 'multiplier', exportResource = 'oxide-meth', exportName = 'PriceFor' },`
- If the meth row of **What clients ask for** (Orders in `/drugselling settings`) still lists the eleven 2.1.0 baggies, the two premium baggies are added to it once on the first start. Rows you edited are left alone.
- Works with oxide-meth 1.7.0: meth sells at its usual price, and the server console warns once that oxide-meth has no price export. Premium baggies and signature prices need oxide-meth 2.0.0.
- The HUD popup needs o-link 1.12.1 or newer. With older o-link it stays where it was.
- Merge the new `phone.ui.peek_sample_*`, `hud.panel_phone` and `settings.error.*` keys into custom locales. Restart oxide-drugselling. No SQL or new items.

## [2.1.0] - 2026-10-07

### Added
- Optional `xpUnits` on item rows in `shared/config/drugs.lua` lets larger packages award more sale XP. Items without it still count as one unit each.
- `Config.OnDrugSold` and sale logs include the progression units counted for XP, separately from the number of items sold.

### Changed
- Weed sales award 10 XP per gram used to make the package: 10 for a gram, 30 for an eighth, 60 for a quarter, 120 for a half, 240 for an ounce and 350 for a brick. Meth, cocaine and pills keep their existing XP.
- Sale XP rounds to a whole number. Invalid `xpUnits` values count as one and produce one server warning per item.

### Fixed
- Failed settings saves restore the previous active value and report failure. Resetting a setting also reports a failed save.
- Settings buttons, tooltips, item searches and zone tools use locale text.
- The settings editor closes when the character logs out.

### Documentation
- Updated configuration, exports and feature guides with package XP weights, sale-hook fields and progression examples.

### Migration Guide
- Merge weed `xpUnits` values `1`, `3`, `6`, `12`, `24` and `35` into your custom `shared/config/drugs.lua`, and set weed `progression.xpPerUnit` to `10`. Keeping your old rows preserves per-item XP instead.
- `Config.Drugs` remains file-only; database settings do not apply this configuration change for you.
- Merge the new `settings.ui.*` keys into custom locales.
- Restart oxide-drugselling after updating. No SQL or new items are required when upgrading from 2.0.0.

## [2.0.0] - 2026-09-28

### Added
- **Corner selling** — start anywhere with `/corner` or the radial menu's "Work corner". Buyers come to you on foot or by car until you walk away, run out, or sit idle. Set up in `Config.Corners`.
- **TrapFone trap phone** (`trap_phone` item, or bind a key to `/trapphone`) — clients text orders, you haggle on the keypad, then book a hand-off or a dead drop for an in-game time.
- Phone clients come from street sales, corner sales and referrals, each with their own personality, budget, trust and hours. Good product and punctual meets build trust; bad batches, lateness and no-shows lose it.
- Phone reputation tiers (Nobody up to Kingpin) raise the order rate, client slots and how far buyers will stretch on price.
- Hand-offs: the buyer walks over or pulls up at a street meet point near their home.
- Dead drops: leave the package at a drop spot, the client collects it and leaves cash (or pays instantly with `Config.Drops.payout = 'instant'`). Anyone who finds the package or cash can take it. Ten spots in south Los Santos ship as defaults; place your own with `/drugselling drops`.
- Phone battery (charges in vehicles or with the `trap_charger` item), `trap_sim` SIM swaps for a clean number, PIN lock, silent mode, no-signal areas (`Config.DeadZones`), and a destroy option that wipes the phone for good.
- Players can text each other between trap phones.
- **Heat** — selling raises heat on the area, the dealer, the corner and the phone number. High heat means more refusals, more police calls and more robberies. It cools over time and survives restarts. `Config.Heat`.
- **Witnesses** — nearby pedestrians can call the police on a sale, and a crowd makes buyers back off. `Config.Witnesses`.
- **Time of day** — buyers are more willing at night and scarcer in the morning. `Config.TimeOfDay`.
- **Seized phone evidence** — police who take a dealer's phone can extract it and read contacts, messages and booked meets, trace another number to a rough area, and send the report to a case. `Config.Evidence`.
- Evidence reports attach to oxide-police cases out of the box. Other MDTs can use `Config.OnPhoneExamined` or a bridge file (see `docs/EVIDENCE_BRIDGE_DEV.md`).
- **Stings** — police use the `sting_phone` item to post an undercover buy that reaches a hot dealer as a normal client. The meet or drop seizes the goods and alerts police. Officers also get a heat map. `Config.Stings`.
- **Bulk buyers** — at Plug and Kingpin reputation, big buyers order in person with two bodyguards, and may rob a dealer they don't trust. `Config.Bulk`.
- Party pills, capsules, bars, blues and percs can be sold, priced by oxide-pills.
- New `export_multiplier` pricing type lets a drug's own resource set its sale prices.
- In-game settings menu (`/drugselling settings`, admin only): edit every setting live, place drop spots and draw no-signal areas in the world, no restart needed.
- Admin commands `/drugselling corners`, `/drugselling drops`, `/drugselling kit [id]` (gives a trap phone, SIM, charger and sting phone) and `/drugselling heat`.
- `Config.ModelSwaps` replaces any model your game build doesn't have.
- Server exports for settings, heat, phone lookups and evidence reports — see `docs/EXPORTS.md`.
- `Config.OnDrugSold` and the `oxide-drugselling:drugSold` event now fire for every kind of sale, with a new `method` field (`street`, `corner`, `phone`, `drop`, `bulk`).
- A "Paid $X for N x item" notification after every paid sale.

### Changed
- **BREAKING**: `oxmysql` is now required, and so is o-link 1.9.1 or newer.
- **BREAKING**: settings now live in the database. The config files fill it on first start; after that, change settings with `/drugselling settings`. `Config.Drugs` stays file-only.
- **BREAKING**: sale money now goes to your framework's dirty money by default (`Config.DirtyMoney.mode = 'auto'`: marked bills on QBCore, the `black_money` item on QBX, the `black_money` account on ESX).
- New `Config.Selling.onDutyOnly` (on by default): only on-duty officers count toward `minPoliceOnline` and receive alerts.
- Restyled sell panel with keyboard controls, price steps around the fair price, stock counts, and a robbery result screen.
- Buyer cooldowns now apply per buyer, with a new "Try someone else" message.
- The negotiation accept chance, counter-offer lifetime and robber's weapon are now settings (`negotiateAcceptChance`, `counterExpiry`, `robberyWeapon`).

### Fixed
- Police alerts, buyer cooldowns, level requirements and robbery rolls can no longer be skipped with a modified game client.
- A sale can no longer be priced against a better-paying buyer standing nearby, or re-offered to the same buyer for a better roll.
- A modified client can no longer use sale animations to freeze or arm other players' entities.
- Drugs are returned if the payout fails, for example when the inventory is too full.
- Refunded items keep their strain and quality details.
- Robberies now report what was actually taken.

### Documentation
- New `docs/ADMIN.md` (commands and settings menu) and `docs/EVIDENCE_BRIDGE_DEV.md` (connecting another MDT). Every other doc was rewritten for 2.0.0.

### Migration Guide
1. Install `oxmysql` and update `o-link` to 1.9.1 or newer. In server.cfg, start both before `oxide-drugselling`.
2. Database tables are created automatically on first start. To create them by hand instead, run `sql/install.sql`.
3. Register the new items `trap_phone`, `trap_sim`, `trap_charger` and `sting_phone` in your inventory. ox_inventory users can paste `items/ox_inventory_items.lua`, and the icons are in `items/`. See `docs/INSTALLATION.md`.
4. Replace your 1.2.0 config files with the new ones and copy your own edits across **before** the first start. After that, the files only fill in settings the database doesn't have yet — use `/drugselling settings`.
5. To keep paying sales in clean cash, set `Config.DirtyMoney` to `mode = 'account'`, `account = 'cash'` before the first start.
6. If your police have no duty toggle and you use `minPoliceOnline`, turn off `onDutyOnly`, or no officers will count.
7. If you translated `locales/en.json`, start again from the new file. Many keys were added and a few were removed.

## [1.2.0] - 2026-09-02

### Added
- **Gang territory affects street sales** — selling inside a gang territory applies that territory's `price` modifier to what the buyer pays, and its `buyerDensity` modifier to how often buyers refuse outright. Needs the oxide-gangs territory system; modifiers are set per territory in `oxide-gangs/shared/config/territories.lua`. Without it (or outside any territory) nothing changes.
- Sales feed your gang's influence in the territory you sold in, so working a corner builds standing there. The gang system's own anti-farm limits apply.
- The price the player is shown on a completed sale is the price actually paid, including any territory bonus.

## [1.1.2] - 2026-06-29

### Added
- Cocaine, crack baggies, and pressed cocaine bricks can now be sold through the street selling system, priced by purity (Pure, High, Mid, Low, and Dirty grades). Selling them rewards experience in oxide-cocaine, the same way selling weed and meth ties into their own resources.
- New `Config.OnDrugSold` hook in `shared/config.lua` that runs after every completed sale. Drop in your own code (trigger an event, call another resource, send a webhook, hand out rewards) — leave it empty to do nothing.
- New `oxide-drugselling:drugSold` server event that fires after every sale, for server owners who would rather react from their own resource than edit the config. Both give you the same sale details: who sold, the drug and item, the quantity, the final price, and the payout mode.

### Fixed
- The background task that clears out abandoned sell sessions now reports problems instead of failing silently, so cleanup keeps running reliably.

## [1.1.1] - 2026-05-12

### Added
- Structured audit logging via `oxide-logger` — sales, robberies, police dispatches, and progression XP awards are now recorded with player and item identifiers (visible in console, file, and Discord sinks when oxide-logger is installed)

### Fixed
- Other players can now see the NPC walk up to the seller and play the negotiate/accept/reject/robbery animations during a sale. Previously the entire interaction was only visible to the selling player while bystanders saw the NPC continue wandering normally.

## [1.1.0] - 2026-04-14

### Added
- Frontend locale loader now reads the active language from the `ox:locale` convar via a `getLocale` NUI callback and loads the matching `locales/<lang>.json`, with graceful fallback to English

### Changed
- Migrated from `community_bridge` to `o-link` for all cross-resource communication (framework, inventory, money, dispatch, target, notify)
- Updated `fxmanifest.lua` dependency from `community_bridge` to `o-link`
- Player is now hidden locally only (`SetLocalPlayerInvisibleLocally`) during cinematic sell camera instead of using `SetEntityVisible` which was visible to other players
- Simplified `safeExport` helper to use a pcall wrapper instead of colon-style proxy invocation
- Updated all documentation to reference `o-link`

### Fixed
- Fixed player ped being invisible to other players during sell camera interactions
- Fixed partial item removal not being refunded when a transaction fails mid-removal
- Fixed `source` not being captured to a local in the `playerDropped` handler

### Removed
- Removed unused `@oxmysql/lib/MySQL.lua` from server_scripts

## [1.0.0] - 2026-04-07

### Added
- NPC street selling system with ox_target integration for ambient pedestrians
- Vue 3 sell panel UI with product selection, variant grouping, quantity controls, and price slider
- Pricing pipeline engine with 5 modifier types: grade multiplier, score multiplier, variant multiplier, trait bonus, metadata bonus
- Negotiation system with accept, counter-offer (95% fair price, 60s expiry), and reject outcomes
- Gang NPC robbery mechanic with configurable item loss and cash percentage
- Police dispatch system with independent trigger chance on any interaction
- Dirty money system with account and item payout modes
- Progression bridge for XP awards and level requirements via exports on source drug resources
- Graceful degradation when source drug resources (oxide-weed, oxide-meth) are not running
- Weed drug type with 6 sellable items, score-based pricing, 7 trait bonuses, and generation metadata bonus
- Meth drug type with 11 variant-specific items, grade-based pricing, and variant multipliers
- NPC blacklist for cops, paramedics, military, rangers, and security models
- Gang model list for Families, Ballas, Lost MC, Mexican gangs, Salvadorians, and Koreans
- Cinematic camera system with wall-collision raycast during NPC interactions
- NPC walk-to-player behavior with distance monitoring and timeout safety
- Rate limiting on all server callbacks (3-5 second windows)
- Player and NPC cooldown systems to prevent spam
- Server-side transaction validation with rollback on partial item removal failure
- Localization support via ox_lib locale system (`locales/en.json`)
