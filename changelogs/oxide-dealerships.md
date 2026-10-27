# Changelog

## [1.1.0] - 2026-10-23

### Added
- **Works with Oxide HUD (free).** Players can move the test-drive timer or switch it off in `/hud`, with a sample in the HUD editor, and it hides with the HUD. The HUD also hides during the catalog preview scene and while an admin draws a lot zone. Needs o-link 1.12.1 or newer; without Oxide HUD nothing changes.
- **`Config.UseHudAccent`** (`shared/config.lua`, default on): the test-drive timer uses the Oxide HUD accent instead of amber. The red last ten seconds don't change.

### Fixed
- **A refused payment no longer pays the buyer.** When a vehicle payment was refused, the buyer was paid the price as a refund for money that was never taken. The purchase now just stops.
- **Failed refunds are flagged.** A refund or trade-in payout that can't be paid is logged with the player and amount, and every admin online is alerted to pay it by hand.

## [1.0.5] - 2026-10-04

### Fixed
- **Missed-payment invoices reach the bank.** When a financed vehicle loan defaults, the missed-payment invoice is now created in `oxide-banking` and can be paid. Collected payments go into a `dealership` business account, which is created automatically.
- **No emails without a phone.** On servers without a phone resource, the dealership no longer tries to send emails, so the console no longer shows o-link fallback warnings.

## [1.0.4] - 2026-09-02

### Fixed
- **Test drive and purchased vehicles appear reliably.** Both are now created by the server directly into the buyer's world instead of being spawned by their game client, closing the gap where a car could be paid for or a test drive announced with no vehicle ever showing up.
- **Test drive cars can no longer be left behind.** The server now removes the test drive vehicle on every ending — timer expiry, manual end, resource restart, or the driver disconnecting — instead of relying on the driver's game to clean it up.
- **You're told when delivery fails.** If a bought car can't be placed on the lot you now get "Your vehicle couldn't be delivered to the lot — pull it from your garage", and a test drive that can't start says so instead of showing a misleading "test drives are disabled" message. A failed delivery never costs you the car — the purchase is already yours.

### Documentation
- `docs/CONFIGURATION.md`: `Config.Debug` draws the dealership lot-zone outlines in-game; it does not add server console output.
- `docs/TROUBLESHOOTING.md`: corrected the note about upgrade SQL files — `sql/install.sql` is the complete schema and there are no separate migration files.

## [1.0.3] - 2026-06-23

### Fixed
- **Test drive vehicles now spawn reliably.** On some servers a test drive would announce it started but no car would appear — sometimes even showing a "test drives are disabled here" message while they were switched on. The test drive car now spawns dependably and puts the player behind the wheel.
- **Purchased vehicles spawn more dependably.** Cars bought from a dealership now appear reliably across different server setups; on some configurations a purchase could go through without the vehicle spawning in.

## [1.0.2] - 2026-06-22

### Fixed
- **Turning off test drives now actually disables them.** Setting a shop's test drives to "off" (from the admin dashboard or the `/dealership testdrive <shopId> off` command) previously left them switched on if they were already enabled. The off setting now takes effect and the greeter hides the Test Drive button as expected.

## [1.0.1] - 2026-06-15

### Fixed
- **Greeter appearance now saves when you move it.** Picking a new greeter character or idle animation in the admin dashboard and then repositioning the greeter now applies your choice in the same step.
- **Corrected the "Used Car Dealer" greeter option.** This appearance choice pointed at an invalid character; it now shows the right one.

## [1.0.0] - 2026-06-10

### Added
- **NPC and player-owned dealerships.** Four NPC dealerships (cars, boats, aircraft, trucks) are ready on install, and players can buy and run for-sale lots themselves.
- **3D browsing catalog.** Customers browse the lineup at the greeter, rotate each vehicle in a cinematic preview, and try preset paint colors before buying.
- **Test drives.** Players take a vehicle for a timed spin and it returns automatically when the timer ends or they step out.
- **Flexible payment methods.** Buy with cash, bank, or any custom currency a lot chooses to accept, including inventory items such as a VIP token.
- **In-house financing.** Put money down and pay off the rest over time, with payments pulled automatically from the bank, missed-payment tracking, and repossession after repeated misses.
- **My Contracts panel.** `/myloans` lets players see active loans, make a payment, or pay one off in full.
- **Vehicle trade-ins.** Customers hand over a vehicle for instant value, taken as cash or applied toward a new purchase.
- **Used inventory.** Repossessed and traded vehicles re-list on the used lot keeping their real plate.
- **Management tablet.** Owners and staff open a windowed dashboard (`/managedealership`) at the lot to run the business in-game.
- **Stock and pricing.** Owners list models, order stock at wholesale, and set their own retail prices.
- **Employees and commission.** Owners hire managers and salespeople who earn a commission cut on sales they close while on the lot.
- **In-person contract sales.** A salesperson picks a vehicle and a nearby customer, sets the terms, and sends a contract the buyer signs.
- **Promo codes.** Admins create global or shop-scoped discount codes; owners can optionally run their own.
- **Showroom displays.** Display vehicles can slowly rotate on a turntable, and a hold-key card shows a car's stats, price, and finance teaser.
- **Job-restricted lots.** Limit a dealership's self-serve greeter to specific jobs.
- **In-character emails.** Receipts, finance agreements, employment notices, and financing reminders arrive as phone emails when a phone resource is connected.
- **Admin dashboard and commands.** A full in-game admin panel (`/dealership admin`) plus 19 `/dealership` commands create and manage every dealership without editing config files.
- **Optional credit-based financing.** With `oxide-banking` connected, interest rates follow a player's credit score and a missed-payment invoice is sent on default.
- **`IsCatalogOpen` client export.** Other resources (such as a custom HUD) can check whether a player has the browsing catalog open, for example to hide the HUD while browsing.
