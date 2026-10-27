# Changelog

## [1.15.0] - 2026-10-26

### Added
- **A ped can stand at a garage.** In `/police admin` → Fleet → Garages, type a ped's spawn name (for example `s_m_y_cop_01`) in **Ped model**, press **Save**, then press **Place Ped** and put it where it should stand and face. Officers aim at the ped instead of the invisible zone to take out or return a vehicle. The ped can't be hurt and comes back within a few seconds if it is killed or deleted. A player whose game doesn't have that ped gets the zone instead. Clear **Ped model** and save to go back to the zone.
- **Fleet categories.** Give a fleet entry a **Category** (K9, Patrol, SWAT or any name you like) in `/police admin` → Fleet → Vehicles. That screen and the garage menu officers use in game list vehicles under their categories in alphabetical order, with the rest under **Other**. Click a heading on the Vehicles screen to fold it away. Every unit of an entry shares its category, units added later get it, and copies keep it.
- **Cuffs can need a handcuffs item.** New settings in `/police settings` → Field Actions → Cuffs: **Cuffs require an item** (off by default) and **Cuffs item** (`handcuffs` by default). When it is on:
  - An officer without the item can't cuff anyone. Cuffing uses one.
  - The officer who uncuffs the suspect gets it back. The officer who books the suspect gets it on booking.
  - If the suspect leaves the server while cuffed, or an ambulance script frees them on revive, it goes back to the officer who cuffed them.
  - Cuffs a suspect breaks out of or picks with a lockpick are lost.
- `items/handcuffs.png` and a `handcuffs` entry in `items/ox_inventory_items.lua`.

### Fixed
- A suspect who logged out while cuffed and came back could not be uncuffed, searched, escorted, put in a vehicle or booked by any officer, and could not try to escape. All of these work again.
- Restarting `oxide-police` no longer leaves the commissary NPC behind as a person walking around, and police props and placement previews in the world are removed with it.
- `/police settings` now says why a setting could not be saved, for example that settings are still loading or that the database write failed, instead of "value rejected". A failed **Reset to default** now shows an error instead of nothing.

### When you update
- **Run `sql/migrations/010_garage_ped_model.sql` and then `sql/migrations/011_fleet_vehicle_category.sql`** on existing servers before starting this version. Until `011` is run, adding, buying, editing, copying and replacing fleet vehicles fails. Fresh installs need nothing. Existing garages keep their zones, and existing vehicles have no category, so they are listed under **Other**.
- To use **Cuffs require an item**, register `handcuffs` in your inventory first. QBCore and Qbox usually have it already. On ESX, copy it from `items/ox_inventory_items.lua`. See `docs/INSTALLATION.md`, step 5.
- Restart the whole server after updating; new files are added.

## [1.14.0] - 2026-10-24

### Added
- **Destroyed vehicle recovery is simpler.** A new setting, `/police settings` → Fleet → **Destroyed vehicle recovery** (`Config.DestroyedVehicleRecovery`), decides what happens when a fleet vehicle is wrecked or sunk:
  - **Paid from treasury** (default): it stays out of service until a boss presses **Replace** on the boss menu's Fleet screen. The boss picks how many to bring back and the treasury pays each one's replacement cost. If the treasury can't cover it, nothing is charged and the boss is told the cost and the balance.
  - **Free**: it is back in its garage straight away. Switching to Free also brings back every vehicle already destroyed.
  - Either way, nothing is ever charged automatically and no debt builds up. The vehicle comes back with the same plate, repaired and with a full tank.
- **Officers can have more than one vehicle out at a time**, as long as the garage has a free vehicle and a free bay. Each one has its own keys and map marker, and is returned, towed or written off on its own.

### Changed
- **Recover and Replace are now one Replace button**, shown only with Paid recovery. The free Recover button is gone.
- `/police writeoff <plate>` follows the recovery setting. With Free, the vehicle is back in its garage at once.

### Fixed
- Vehicles left out in the field are no longer deleted as wrecked about 30 seconds after a server restart when no player is near them.
- Blown-up cars and helicopters, and helicopters that crash, are now written off within seconds. Before, they could stay listed as out on the Fleet screen.

### When you update
- **Destroyed vehicles now cost their replacement cost to bring back.** To keep them free, set **Destroyed vehicle recovery** to **Free**: every vehicle already destroyed comes back at once. On Paid, they stay lost until a boss replaces them.
- There's no database migration in this version. The new setting is added as Paid from treasury and your other settings are kept. Restart the whole server after updating; new files are added.

## [1.13.1] - 2026-10-23

### Fixed
- A function left inside a menu-managed setting in an older customized config (for example `Config.Evidence.IsDropExempt`) no longer stops oxide-police from finishing its start. The console names each one and, for hooks that moved, the `Config.Hooks` entry to move it to.
- A commissary refund or prisoner job reward that can't be paid is logged with the player and amount, and every admin online is alerted to pay it by hand.

### When you update
- If the console names a function in your config, move it to the `Config.Hooks` entry it gives, or remove it. Until you do, that hook does nothing.

## [1.13.0] - 2026-10-18

### Added
- **Vehicle types.** Every fleet vehicle has a type: Car, Bike, Quad, Boat, Helicopter or Plane. Set it on each catalog entry in `/police settings` → Fleet catalog, and on each vehicle in `/police admin` → Fleet → Vehicles. That screen shows a type badge and marks entries whose units have mixed types.
- **`/police writeoff <plate>`** (admins and the server console) writes off a checked-out unit the server can't see lost, for example one sunk in a river or in an add-on map's water.
- **Works with Oxide HUD (free).** The vehicle radar, the bodycam REC pill and the controls hint become panels players can move or hide in `/hud`, with a preview in the HUD editor. The HUD hides while you watch a bodycam or CCTV camera, place a camera, wear a headbag, take mugshots or use the admin camera tools. `Config.UseHudAccent` (in `shared/config.lua`, default `true`) gives the controls hint the server's accent colour. Needs `o-link` 1.12.1 or newer. Without Oxide HUD nothing changes.
- The database tables are created automatically on first start.

### Changed
- **A vehicle's type is set by admins only**, and every unit spawns as its saved type. Boss purchases take it from the catalog, and the officer's game no longer detects or corrects it. A vehicle added in `/police admin` starts as its garage's kind (car, boat or helicopter); pick Bike, Quad or Plane yourself.
- **Catalog entries saved before this version have no type.** Until you set one:
  - the entry is bought as the type your fleet already has for that model, then the shipped type, then Car;
  - the console names each such entry at start;
  - `/police settings` marks it "Type not set";
  - the catalog can only be saved once every entry has a type.
- A saved type outside the six spawns as its garage's kind, with a console warning.
- **Units are written off only from what the server sees:** a wrecked engine, body or fuel tank, or the unit sitting in the sea, the Alamo Sea or a reservoir. An officer's report alone no longer writes one off. For rivers and add-on water, use `/police writeoff`.
- The unit count on the Vehicles screen reads `x4` instead of "Quantity Badge: 4".

### Fixed
- A modified game client can no longer write off a checked-out unit by reporting it destroyed, or choose the type a vehicle is bought or spawned as.
- A vehicle saved with the wrong type can now be corrected: set its Type on the Vehicles screen, and each unit spawns as that type from its next checkout.
- Logging out while watching a CCTV camera now closes the camera view.

### When you update
- **Set the Type of every catalog entry** in `/police settings` → Fleet catalog. Shipped models fall back to the right type, but a custom bike, boat, helicopter or plane that isn't in your fleet yet is bought as a car until you do.
- Check `/police admin` → Fleet → Vehicles for **Mixed types** or wrong types.
- There's no database migration in this version. Restart the whole server after updating; new files are added.

## [1.12.0] - 2026-10-04

### Added
- **Phone numbers on the MDT.** A person's profile shows their phone numbers, read from whichever phone resource your server runs. Nothing shows without a phone. This needs `o-link` 1.11.0 or newer.

### Fixed
- Fine emails are no longer attempted on servers without a phone resource, so the console no longer shows a warning on every fine.

### Documentation
- `docs/FEATURES.md`, `docs/INSTALLATION.md` and `docs/CONFIGURATION.md`: the phone numbers on the MDT person profile, the `o-link` version they need, and fine emails on servers without a phone.

## [1.11.0] - 2026-09-29

### Added
- **Copy a fleet entry to another department or garage.** In `/police admin` → Fleet → Vehicles, the copy button on a vehicle row recreates that entry — same number of units, label, model, rank, livery, extras, upgrades, addons, replacement cost and trunk loadout — in the department and garage you pick. Copies get new plates in that department's plate format, and copying is free, like adding a vehicle from the panel.
- **Performance upgrades for fleet vehicles.** Set engine, brakes, transmission, armor and turbo for each fleet entry in `/police admin` → Fleet → Vehicles. Every unit of that entry comes out of the garage with them.
- **Gun rack addon.** Bosses can buy vehicles with a gun rack (`Config.FleetAddons.gun_rack`, default `3500`), and admins fit or remove one with the **Rack** toggle in `/police admin` → Fleet → Vehicles. On-duty officers of the department that owns the vehicle get **Access Gun Rack** on it, which opens a small stash for that vehicle — 4 slots by default, set in `/police settings` → Fleet → **Fleet gun racks** (`Config.GunRack`).
- **Place the commissary NPC with a live preview.** `/police admin` → Detention → Prison Shop has a new **Place Interactively** button: move and turn a preview of the vendor, fine-tune it, then save.

### Changed
- Every button, placeholder and tooltip in `/police settings` can now be translated in `locales/*.json`.
- Attempts to open or change `/police settings` without admin rights are now written to the log.

### Fixed
- The Prison Shop's **Use My Position** button now saves the spot you are standing on. It used to open the aim-based placement instead.
- A `/police settings` change the database fails to save is now reported as failed, instead of applying until the next restart. Resetting a setting reports a failed save the same way.
- `/police settings` now closes when your character logs out.
- The treasury transaction for a trunk restock triggered by saving a vehicle's loadout now records who saved it.

### Database
- **Run `sql/migrations/008_fleet_performance_mods.sql` and then `sql/migrations/009_fleet_gun_rack.sql`** on existing servers before starting this version. They add performance upgrades and gun racks to your fleet table. Fresh installs need nothing — `install.sql` already includes both. Existing vehicles start with no upgrades and no gun rack.

### Notes
- **Gun racks accept any item, not only weapons.** The inventory bridge cannot limit a stash to weapons, so racks are kept small instead.
- A gun rack empties when its vehicle is returned, towed back or destroyed. Turn on `Config.GunRack.PersistOnReturn` to keep the contents with the vehicle. Removing a rack keeps what is in it — the items are there again once a rack is refitted.
- On inventories that cannot clear a stash (`core_inventory`, `hex_4_inventory`, `jpr-inventory`, `ps-inventory` and `qb-inventory` v1), racks are not emptied on return and a console warning names the vehicle.
- Performance upgrades reach a vehicle that is already out on the road the next time it is taken out of the garage.
- Copying between two departments needs someone who manages both, which in practice means an admin. A boss can copy between their own department's garages.
- **No changes to existing settings.** `Config.GunRack` arrives with its defaults, and the `gun_rack` price is added to your saved addon prices on the first start; your radar and megaphone prices are kept.

### Documentation
- `docs/CONFIGURATION.md`: new **Gun racks** section, plus the `Config.GunRack` and `Config.FleetAddons.gun_rack` settings.
- `docs/ADMIN.md` and `docs/FEATURES.md`: placing the commissary NPC, copying fleet entries, performance upgrades and the gun rack.
- `docs/INSTALLATION.md` lists the two new migrations, and `docs/BUILDING-A-DEPARTMENT.md` now points to that list instead of stopping at `006`.

## [1.10.0] - 2026-09-28

### Added
- **The MDT and boss menu can open inside the tablet.** With `oxide-tablet` running, officers get a **Police MDT** app and department heads get a **Police Command** app, and `/police mdt` or the tablet item opens the MDT inside the tablet too. Turn each off with `Config.MDT.useTablet` and `Config.Boss.useTablet`, or **Host in oxide-tablet** in `/police settings`. Without `oxide-tablet`, both open in their own window as before.
- **The MDT map now shows Cayo Perico.** A new **Map mode** setting under General in `/police settings` (`Config.MapMode`, default `inherit`) picks `combined` islands, `separate` island views with a region picker, or `disabled` for mainland only. `oxide-police:mapmode <mode>` from the server console does the same.
- Seized-phone reports from the drug selling resource can be attached to an MDT case by its case number, under the same department and duty rules as an entry typed in the MDT.
- Dispatch alerts in the MDT show the weather when the alert carries it.

### Changed
- The MDT map is now drawn from o-link's shared map instead of images bundled with this resource.

### Fixed
- Police payments showed "o-link" in bank transaction history. They now read Paycheck, Back pay, Police fine, Department payout, Department deposit or Commissary purchase.
- Saved `/police settings` values can no longer be overwritten with the file defaults if the database read fails during startup.
- Decimal settings no longer show rounding noise like `0.90000000000009` in the menu.
- Pressing ESC to cancel an in-world picker no longer closes `/police settings` as well.

### Notes
- **Requires o-link 1.8.0 or newer** for the MDT map.
- **Tablet hosting is on by default.** If you run `oxide-tablet` and want the MDT and boss menu in their own windows, turn off **Host in oxide-tablet** for each in `/police settings`.

### Documentation
- New `items/ox_inventory_items.lua` and `items/ox_inventory_weapons.lua` hold every item and the `WEAPON_RADAR` weapon ready to paste into `ox_inventory`, with images alongside. `docs/INSTALLATION.md`, `docs/ESX_INSTALL.md` and `docs/QBX_INSTALL.md` now point to them.
- `docs/CONFIGURATION.md`: new `shared/config/boss.lua` section, `Config.MDT.useTablet`, and a **Shared map mode** section.
- `docs/EXPORTS.md`: new `AttachCaseEvidence` export for adding an entry to a case from another resource.
- `docs/QBX_INSTALL.md`: stale references fixed and the `Config.Radar.HandheldWeapon` example corrected to `'WEAPON_RADAR'`.

## [1.9.0] - 2026-09-01

### Added
- **Fleet vehicles can carry equipment in the trunk.** Each vehicle in `/police admin` → Fleet → Vehicles now has a **Trunk Loadout** — a list of items with a count and a price, sitting next to the radar and megaphone toggles, so every department equips its own cars. The equipment is loaded into the vehicle when the unit is bought and then stays with it: officers take what they need and put things back. Nothing is handed out at checkout, so there is no way to farm equipment by taking a car in and out. When a car comes back short, press **Refill** on the vehicle row — the department treasury is charged only for what is actually missing, at the prices you set, so a crew that loses its gear costs the department money instead of getting a free replacement set. Saving a loadout stocks the cars straight away, and if the treasury cannot cover it the save still succeeds and tells you the cost and the balance. Every unit of a vehicle shares its loadout, and units added later inherit it.
- **Departments can issue their own licence plates.** In `/police settings` → Fleet → **Fleet plate formats**, give each department a plate style — `LSP####` produces plates like `LSP4821` — so a unit reads as LSPD or BCSO at a glance instead of getting a random 8-character plate. Use `#` for a digit, `@` for a letter and `?` for either; everything else appears as typed. The editor shows a live example of each format and refuses anything the game cannot display. Plates already in service are never rewritten, because reports, BOLOs, evidence and impound records refer to them — only vehicles created after you set a format are affected.
- **The MDT's Gang Intel tab has been rebuilt.** It now opens on a gang registry with live member counts, territory held and active conflicts, and selecting a gang gives a full dossier: its territorial footprint broken down into held, contested and presence-only areas, its conflict record, and any war currently running with what is at stake and when it settles. Requires the gangs resource; the tab stays hidden without it.

### Changed
- **Minimum rank on a fleet vehicle is now a list of your department's ranks.** It used to be a free number box, and setting it above your highest rank made the vehicle unreachable by every officer while still looking correct in the admin panel — it simply never appeared in any garage. That value can no longer be entered.
- **Adding a second fleet entry with the same model, garage, rank and addons is now refused.** Fleet entries are identified by that combination rather than by name, so a duplicate used to merge into the existing entry and quietly discard the name you typed. Use the entry's quantity to add more units of it instead.

### Fixed
- **The MDT Gang Intel tab was hidden on every server.** The check that decides whether the gangs resource is available never passed, so the tab never appeared even with the resource installed and running.
- **The Gang Intel tab no longer opens empty when the gangs resource is stopped.** It was only hidden when the resource was absent entirely, so stopping it left a tab behind that could not load anything.
- **Adding a fleet vehicle now explains why it failed** instead of showing a generic "add failed" message.
- Close and refresh buttons in the admin and evidence side panels had no horizontal padding, leaving the icon and ESC chip flush against the button border.

### Database
- **Run `sql/migrations/007_fleet_trunk_stock.sql`** on existing servers to add trunk loadout storage to your fleet table. Fresh installs need nothing — `install.sql` already includes it. Existing vehicles start with no loadout until you give them one.

### Notes
- **Trunk loadouts need `o-link` 1.6.7 or newer**, and an inventory that can write to vehicle trunks. Base ESX has no trunk of its own, so on ESX this needs an inventory that adds one — `ox_inventory` is the usual choice. QBCore, QBX and Oxide Core ship one already. The full per-inventory list is in `docs/CONFIGURATION.md`. On an older `o-link` the equipment is written somewhere the trunk cannot show it, so update the bridge before you set a loadout up. Where the inventory does not support it, the loadout is skipped and a warning naming the vehicle is written to the server console — nothing is handed to the officer instead, because that would defeat the point of storing equipment in the car.
- **The department keeps its own record of what is in each trunk.** Inventory resources only keep vehicle storage for cars their framework owns, which a fleet vehicle is not, so what is in the trunk is saved against the fleet vehicle itself and put back whenever the unit spawns. It survives restarts, and it is why an officer who empties a car leaves it empty rather than getting a fresh set next time.
- **No changes to existing settings.** Fleet trunk loadouts and plate formats arrive switched off in effect: no vehicle has a loadout and no department has a plate format until you set one.

### Documentation
- `docs/CONFIGURATION.md` gained full sections on trunk loadouts and plate formats, including the two rules the game enforces on plates. `docs/ADMIN.md` and `docs/BUILDING-A-DEPARTMENT.md` were corrected — both stated that adding a vehicle from the admin panel debits the department treasury, which it does not; only a boss buying from their own Fleet screen is charged. The same pages also described a per-vehicle plate field that has never been editable.

## [1.8.0] - 2026-08-24

### Added
- **Edit settings in-game with `/police settings`.** Admins can now change nearly every setting from an in-game menu — radar, speed cameras, bodycam, evidence, custody, MDT, interactions, and more — and changes apply immediately, with no file edits and no restart. Settings are stored in the database (a shared `oxide_settings` table, created automatically). On the first start after updating, your current config-file values — including your customizations — are imported, so nothing changes by itself. From then on the database is the source of truth: the config files only act as factory defaults for the menu's reset buttons and for new settings added by future updates, so editing a file no longer changes a running server. A few keys stay file-only by design (command names and the hook functions — see Notes).

### Changed
- **Every command now lives under one `/police` command.** The old scattered commands are gone and chat autocomplete now lists every subcommand with its arguments: `/padmin` → `/police admin`, `/phire` → `/police hire`, `/pfire` → `/police fire`, `/mdt` → `/police mdt`, `/duty` → `/police duty`, `/jail` → `/police jail`, `/unjail` → `/police unjail`, `/addtime` → `/police addtime`, `/setsentence` → `/police setsentence`, `/purgeevidence` → `/police purgeevidence`, `/bodycams` → `/police bodycams`, `/cameras` → `/police cameras`, and `/speedcam_create` / `/speedcam_remove` → `/police speedcam create` / `/police speedcam remove`. Running `/police` alone lists what's available. All permission rules are unchanged.
- **The police radio is now the bundled radio resource.** Police no longer ships a radio of its own — the in-car/on-foot radio panel, the `police_radio` item, and the radio-channel editors in the admin panel and boss menu have been removed. The bundled **radio** resource owns the radio item, the panel, and the channel list; set up your police channels there (`/radio settings` → Channels), separated per department job name. When it is installed, police still puts officers on their department's channel at clock-on and shows badge numbers and downed officers on the channel roster. Without it there is no police radio; the megaphone is unaffected. The `police_radio` inventory item is no longer used by this resource and can be removed from your inventory config.

### Database
- **Existing radio channels convert automatically.** On the first start after updating — with the bundled radio resource installed and running — your department radio channels are copied into that resource's channel list (labels, department, minimum rank, and clock-on default are kept), and the old `police_radio_channels` table is renamed to `police_radio_channels_migrated` as a backup. This runs once and never again. If the radio resource is not running at that start, nothing is lost: a console warning is printed and the conversion runs on a later restart once it is. Fresh installs simply no longer create the radio table. **No SQL to run by hand** — the settings table and the conversion are both automatic.

### Notes
- **If you use customized copies of the config files, a few keys moved — copy your custom values to the new names.** The old names are no longer read: `Config.Evidence.IsDropExempt` → `Config.Hooks.EvidenceIsDropExempt`, `Config.Evidence.CheckPlayerGloves` → `Config.Hooks.EvidenceCheckPlayerGloves`, `Config.Mugshot.BuildEmbed` → `Config.Hooks.MugshotBuildEmbed`, `Config.Mugshot.SendPhoto` → `Config.Hooks.MugshotSendPhoto`, and `Config.Evidence.Weapons` → `Config.EvidenceWeapons`. Also `Config.Custody.RemoveAfterRelease` is now a plain list (`{ 'prison_bread' }` instead of `{ ['prison_bread'] = true }`), and each entry in `Config.SpeedCam.Models` is now `{ label = '...' }` instead of a bare label string. Keeping an old customized `evidence.lua` without these updates will break evidence drops.
- **Changing your server language later needs one extra step.** The labels inside `SpeedCam`, `FleetCatalog`, and `PlaceableObjects` are stored in the database in whatever language the server used on first start. If you change `ox:locale` afterwards, delete those three rows from `oxide_settings` and restart — the exact SQL is in `docs/CONFIGURATION.md` (this also discards saved menu edits to those three sections).

## [1.7.0] - 2026-08-10

### Added
- **Take a seated suspect out of a vehicle by third-eyeing the vehicle.** With the `vehicle` action enabled in `Config.OfficerTargetMenu.Actions` (`shared/config/interactions.lua`), "Take Out of Vehicle" now also appears when aiming at a vehicle that has a restrained occupant inside. Before, the option was attached only to the suspect themselves — and aiming at someone seated in a car hits the vehicle's body or glass, so the option never showed and the F2 action menu was the only reliable way to get them out. The on-player option is unchanged, the same duty rules apply, and with several restrained occupants each use takes out the next one. Works with zip-tied suspects too, so the civilian `requireDuty = false` setup behaves the same.

### Documentation
- The upgrade steps in BUILDING-A-DEPARTMENT.md now list all six migration files (`001` through `006`), and mentions of bodycam video-resolution and CCTV recording/thermal settings that don't exist in the config were removed.

### Notes
- **No database changes** — existing servers update in place. Nothing new to configure: the vehicle option follows your existing `Actions.vehicle` setting, which is off by default.

## [1.6.0] - 2026-07-27

### Added
- **Department bosses can set up their own department.** A new `Config.AllowBossSetup` setting in `shared/config.lua` (off by default) lets a department boss open the admin panel (`/padmin`) without server admin permission. The boss sees only their own department and can manage its stations and interaction points, garages, doors, CCTV cameras, speed cameras, and jail policy. They can never create, delete, or rename a department, touch a treasury, or reach the server-wide sections (revive point, prisoner jobs, prison shop) — those stay admin-only, and everything a boss changes is enforced server-side and written to the log.
- **Officers can adjust jail time when booking.** Both the MDT Charges tab and the in-station booking panel now have an editable sentence field, for time served or a plea agreement. The criminal record keeps the catalog values — only the served sentence changes, and every adjustment is logged with the officer and shown as "adjusted" in the department activity feed. On by default via `Config.MDT.allowJailOverride` in `shared/config/mdt.lua`; restrict it to higher ranks with `permissions.overrideJail`, and cap it with `limits.maxJailPerCall`. Set `allowJailOverride = false` to remove the field.
- **Charge catalog import and export (CSV).** The MDT's charge catalog editor now has Export and Import buttons. Export copies the whole catalog as CSV to back up or move between servers; Import accepts a pasted or uploaded CSV (columns: code, title, category, description, fine_amount, jail_time) with a preview of what will be added, updated, or skipped before anything is written. Rows with an existing code update that charge. The import limit is configurable (`limits.maxImportRows` in `shared/config/mdt.lua`, default 500).
- **Individual MDT tabs can be turned off.** A new `Config.MDT.modules` table in `shared/config/mdt.lua` lets you hide any MDT tab (warrants, BOLOs, videos, fines, etc.) for every officer. A hidden tab disappears from the navigation and its server functions are refused too, so it can't be reached by other means. Everything defaults to on — leaving the config alone changes nothing.
- **Spent casings can carry the firing weapon's serial number.** With `Config.Evidence.includeWeaponSerial` in `shared/config/evidence.lua` (on by default), a casing records the serial of the gun that fired it — read server-side from a weapon the shooter is actually carrying, so a modified game client cannot fake or hide it. When that serial matches a firearm in the weapon registry, the evidence lab shows who the gun is registered to. Turn it off if your server doesn't use weapon registration.
- **Evidence lockers can seal when a case closes.** With `Config.Evidence.locker.sealOnCaseClose` in `shared/config/evidence.lua` (off by default), closing a case seals its evidence locker: contents stay listed for reference, but nothing can be added or removed until the case is reopened. Admins can permanently empty a sealed locker with the new `/purgeevidence <case number>` command — the destroyed contents are written to the log first.
- **Other jobs can work on wheel clamps.** A new `externalJobs` table in `shared/config/field_tools.lua` lets you allow non-police jobs (for example mechanics) to remove — and optionally fit — wheel clamps, with per-job "must be on duty" and "may also apply" switches. Allowed jobs get target options on clamped vehicles plus a fallback `/removeclamp` chat command (rename or disable it with `externalCommand`). Removal by these jobs never hands over the clamp item — that stays police equipment. Empty by default, so clamps remain police-only unless you add jobs.
- **Kneeling surrender pose.** Holding the hands-up key now drops the player into a kneeling surrender instead of the standing pose; a tap still gives the standing pose. On by default; tune or disable it with the new `KneelEnabled`, `HoldThreshold`, and `KneelSticky` settings under `Config.HandsUp` in `shared/config/interactions.lua`.

### Fixed
- **Forensic details are no longer readable by game clients.** The information attached to dropped evidence — whose DNA is on it, whose fingerprints, weapon serials — was synced to every player's game client, where cheat software could read it off the map. Clients now only receive what they need to draw the evidence marker (position and type); the forensic details stay on the server until evidence is properly collected.

### Notes
- **No database changes** — existing servers update in place.
- The new settings live in `shared/config.lua`, `shared/config/mdt.lua`, `shared/config/evidence.lua`, `shared/config/field_tools.lua`, and `shared/config/interactions.lua`. If you use **customized** copies of those files, copy the additions over to get them — fresh installs already include them.

## [1.5.1] - 2026-07-26

### Fixed
- **Bodycam recordings now work on servers installed from the release package.** The bodycam's video renderer was loaded from a folder that isn't included in the download, so recordings could silently produce no video. The renderer now ships inside the app bundle.

## [1.5.0] - 2026-07-07

### Added
- **Disabled fleet vehicle groups can now be re-enabled or permanently deleted.** In the admin panel's Vehicles screen, disabling a group no longer removes it from the list — it stays dimmed with a "Disabled" badge. Admins can re-enable it, or permanently delete it, which removes every unit from the database and despawns any of that group's vehicles still in the world. Deletion is blocked while any unit is checked out.

### Changed
- **The trash button on a vehicle group is now an explicit "Disable".** The confirmation dialog explains the group stays in the list and can be re-enabled or deleted forever, replacing the old "This action cannot be undone." wording.
- **The duty toggle moved into the police radial menu.** When `Config.RequireStationClockIn` is off, going on or off duty is now the first item in the police actions menu (default key `Config.ActionMenuKey = 'F2'`) instead of a separate radial entry, and the menu is now titled "Police". Off-duty officers can open the menu to clock in; all other actions still require being on duty.

### Notes
- No database changes — existing servers update in place.

## [1.4.7] - 2026-07-03

### Changed
- **Patrol vehicles now stay in the world.** A checked-out fleet vehicle no longer disappears when the officer clocks out, disconnects, or the server restarts — it stays parked wherever it was left and, after a restart, comes back in the same spot. Before, clocking out or a restart made the car vanish, and the vehicle count was quietly returned to the pool. Cars now have to be physically brought back to a garage before they count as available again.
- **Any on-duty officer can return a department vehicle.** Returning is now a physical act: bring any of your department's vehicles to one of that department's garage bays and use the return option. It no longer has to be the car you personally checked out — handy when a colleague left a unit sitting in the field. To return a vehicle it must be parked at a garage bay; trying to return it anywhere else, or returning another department's vehicle, now tells you why.
- **A busy garage now says so.** If every bay at a garage is occupied, checking out another vehicle is refused with a clear "return a vehicle first" message instead of spawning it on top of an existing car.

### Added
- **Bosses can tow back lost units.** The Fleet screen in the boss menu has a new **Tow** button next to each vehicle type that has units out in the field. It opens a list of every unit that is out — showing its plate, who checked it out, whether that officer is currently online, and the street it was last seen on — so you can pick exactly which vehicle to bring back. Towing returns the unit to the garage without anyone driving it, useful when the officer who took it is long offline. Each tow charges the department treasury `Config.FleetRecoveryFee` (in `shared/config.lua`, default `750`; set it to `0` to make towing free).

### Fixed
- **Firearm registration now works with ox_inventory.** On servers using ox_inventory, the firearm registration desk always said "You have no unregistered firearms to register" even when the player was carrying weapons with serial numbers. Two causes: ox_inventory names weapons in capital letters, which the desk did not recognize, and ox_inventory marks every weapon it creates with its own "registered" note, which the desk mistook for a completed police registration. The desk now recognizes ox_inventory weapons and only treats a weapon as registered when it was actually registered at a police desk (or bought from a licensed weapon shop). Illegally printed "ghost guns" still cannot be registered.
- **Seized weapons are recorded on ox_inventory servers.** For the same capital-letters reason, weapons submitted as evidence on ox_inventory servers were not being accepted as evidence items and their serials were not being logged in the MDT weapon registry. Both now work.
- **Works on servers running state bag strict mode.** All synced player and vehicle state is now written by the server instead of by players' game clients. If your server has strict mode turned on (a FiveM setting that rejects client-written state), everything — cuffing with ESX inventories, fleet vehicle damage tracking, and the megaphone — now works correctly. Nothing changes on servers without strict mode.

### Database
- When upgrading, run the new file in `sql/migrations/` (`006_server_fleet_persistence.sql`) once. It corrects the stored vehicle type on any boat and helicopter fleet vehicles so they spawn correctly with the new persistent-vehicle system. Fresh installs already include it, and it is safe to run on an existing database.

### Notes
- **Nothing to turn on** — persistent patrol vehicles and physical returns are the standard behavior now.
- The one new setting, `Config.FleetRecoveryFee`, lives in `shared/config.lua`. If you use a **customized** copy of that file, copy the addition over to get it — fresh installs already include it.

## [1.4.6] - 2026-06-29

### Added
- **Open the MDT from a tablet item.** You can now make the Mobile Data Terminal open by *using* an inventory item instead of typing the `/mdt` command. Turn it on with `Config.MDT.useItem = true` in `shared/config/mdt.lua` — when on, officers open the MDT by using a `police_tablet` item and the `/mdt` command is switched off. The item name can be changed with `Config.MDT.itemName`. This is off by default, so `/mdt` keeps working unless you turn it on. If you run a non-Oxide framework, add the `police_tablet` item to your inventory first — see the "Register items" step in `docs/INSTALLATION.md`.
- **Use the MDT while off duty.** A new setting lets hired officers open and use the MDT without clocking in. Turn it on with `Config.MDT.allowOffDuty = true` in `shared/config/mdt.lua`. Off by default, so the MDT still requires being on duty unless you turn it on. The minimum-rank setting for opening the MDT still applies either way.
- **Smoother on-screen speed radar.** A new `Config.Radar.Overlay.updateInterval` setting in `shared/config.lua` lets you limit how often the floating speed readout above a vehicle refreshes, in milliseconds. Left at the default `0` it updates every frame, exactly as before.

### Fixed
- **Admin commands use your server's standard admin permission.** The admin panel (`/padmin`) and admin commands are gated by your server's normal `admin` permission. An old, unused setting that pointed to a custom `oxide.police.admin` permission has been removed, and the guides have been corrected. Before, the guides told you to grant a permission the resource no longer checks, so admin tools could appear to "do nothing" for staff who only had that custom permission. Grant your admins the standard `admin` permission — the same one used elsewhere on your server.
- **Boss menu actions no longer confirm twice.** Hiring, firing, promoting and demoting officers, buying and managing fleet vehicles, and treasury deposits, withdrawals and payroll settlement now show a single confirmation message instead of two.
- **Breathalyzer color thresholds now follow your config.** The "over the limit" and "caution" readings on the breathalyzer screen now use `Config.Breathalyzer.overLimit` and `Config.Breathalyzer.cautionLimit` in `shared/config/breathalyzer.lua`. Before, the screen always used the built-in 80 / 50 values no matter what you set.
- **Tackle cooldown and on/off switch now work.** `Config.Tackle.Cooldown` is now enforced between tackles, and setting `Config.Tackle.Enable = false` now fully turns tackling off (the keybind is no longer registered). Both live in `shared/config/interactions.lua`. Before, these settings were ignored.
- **Speed radar range follows your config.** The front and rear radar detection distances now use `Config.Radar.OffsetFront` and `Config.Radar.OffsetRear` in `shared/config.lua`. Before, the radar always used fixed distances and ignored those settings.
- **CCTV cameras can see into the distance.** Camera feeds now draw far-away buildings and objects out to `Config.Cctv.viewerMaxDistance` in `shared/config/cctv.lua`. Before, distant scenery could be missing from the feed.
- **Zip-tie duty requirement now applies.** If you set `Config.FieldActions.Ziptie.requireDuty = true` in `shared/config/field_actions.lua`, only on-duty officers can apply zip ties. Before, this setting had no effect.
- **Admin panel pop-ups appear on top.** Confirmation boxes, input boxes, and notifications inside the admin panel now display above the panel instead of being hidden behind it.

### Notes
- The new and corrected settings live in `shared/config/mdt.lua`, `shared/config.lua`, `shared/config/breathalyzer.lua`, `shared/config/cctv.lua`, `shared/config/interactions.lua`, and `shared/config/field_actions.lua`. If you use **customized** copies of any of these files, copy the additions over to get them — fresh installs already include them.

## [1.4.5] - 2026-06-23

### Added
- **Officers can belong to more than one department.** A character can now be hired into several departments at once — for example the city police and the sheriff's office — each keeping its own badge number, callsign, rank, and pay. The department they count as "working" at any moment is whichever police job they currently have selected; duty, the boss menu, the MDT, and field tools all follow that active department. This is off by default. Turn it on with `Config.AllowMultiDepartment = true` in `shared/config.lua`. With it off, hiring someone into a second department still moves them out of the first, exactly as before.
- **Works with g-multijob.** Oxide Police now plugs into the g-multijob multi-job menu, so officers can switch between their police job and their other jobs from that menu. Hiring or firing an officer automatically adds or removes their police job in the menu, and switching to a non-police job takes them off police duty until they switch back. It turns on by itself when g-multijob is installed (`Config.Compatibility['g-multijob']` in `shared/config/compat.lua`) and needs `Config.AllowMultiDepartment = true`. Full setup steps are in the g-multijob section of `docs/ESX_INSTALL.md`.

### Changed
- **`/pfire` can target one department.** When a player belongs to more than one department, you can now fire them from a single department and leave the others untouched: `/pfire <serverID> <departmentName>`. Without a department name it still fires them from the department they are currently working.

### Fixed
- **Station no longer thinks you're on duty when you're not.** If you were taken off duty by something other than clocking out yourself — being fired or moved while on duty, or switching to another job — the station's clock-in point could still treat you as on duty and refuse to let you clock back in. It now always matches your real duty status.

### Database
- When upgrading, run the new file in `sql/migrations/` (`005_multi_department.sql`) once. It lets a character be stored in more than one department. Fresh installs already include it, and it is safe to run on an existing database.

### Notes
- **Both new features stay off until you turn them on.** If you change nothing, the resource behaves exactly as it did before.
- The new settings live in `shared/config.lua` (`Config.AllowMultiDepartment`) and `shared/config/compat.lua` (the `g-multijob` block). If you use **customized** copies of those files, copy the additions over to get them — fresh installs already include them.

## [1.4.4] - 2026-06-22

### Added
- **Turn off bullet casings for on-duty officers.** A new setting lets you stop on-duty officers from leaving bullet casing evidence when they fire — so range training or a duty firefight doesn't litter the area with collectable casings. It is off by default, meaning officers leave casings just like everyone else until you turn it on. Set `Config.Evidence.OfficersDropBullets = false` in `shared/config/evidence.lua`. Off-duty officers and civilians always leave casings.
- **Skip evidence drops during your own minigames or modes.** A new check lets you stop a player from leaving any evidence — bullet casings, blood, and vehicle fingerprints — while they are in a state you control, such as a paintball or laser-tag minigame. You write the check using your other resource's export. See `Config.Evidence.IsDropExempt` in `shared/config/evidence.lua` and the "Excluding players from evidence drops" section in `docs/CONFIGURATION.md`.

### Notes
- Both new settings live in `shared/config/evidence.lua`. If you use a **customized** copy of that file, copy the additions over to get them; fresh installs already include them.

## [1.4.3] - 2026-06-15

### Fixed
- **MDT rank requirements now actually apply.** The minimum-rank settings for the Mobile Data Terminal — opening the MDT, processing an arrest, and issuing a fine — are now enforced. Before, these settings were ignored, so any on-duty officer could do all three no matter what minimum rank you set. Officers below the rank you choose are now turned away. The defaults allow every rank, so nothing changes unless you raise these values in `shared/config/mdt.lua`.
- **Jail sentences from the MDT use your server's month length.** Sentences handed down through the MDT now use the same month length as the `/jail` command (`Config.Custody.SecondsPerMonth` in `shared/config/custody.lua`). Before, MDT sentences were always locked to 60 seconds per month, so if you had changed the month length, an MDT arrest and the `/jail` command could give different sentence lengths. At the default setting nothing changes.
- **Escorting a suspect into a vehicle.** Putting a suspect you are escorting into a vehicle now properly ends the escort hold on the officer, instead of occasionally leaving the officer stuck in the escort animation.

### Notes
- An unused permission setting (`manageCatalog`) was removed from `shared/config/mdt.lua`. If you use a customized copy of that file, you can leave the line in or delete it — it has no effect either way.

## [1.4.2] - 2026-06-10

### Fixed
- **Officer actions now lock you in place properly.** Restraining or releasing a suspect with zip-ties, searching a suspect, clamping or unclamping a tire, applying or removing mouth tape or a tracking band, and impounding a vehicle now correctly play the action animation and stop you from moving or fighting until the progress bar finishes. Previously, on some setups, the animation could be skipped and you could still move during the action.
- **Rank-restricted radio channels.** Radio channels with a minimum rank now correctly recognize each officer's rank. Previously every officer was treated as the lowest rank, so restricted channels (such as a command channel) were unavailable to everyone — including officers who should have access.

### Documentation
- **New setup guide for media uploads.** The documentation now includes a full step-by-step walkthrough for setting up FiveManage — the free service that stores mugshot photos, bodycam clips, and bait car dashcam clips. See "Setting up FiveManage media uploads" in `docs/CONFIGURATION.md`; the installation and troubleshooting guides now link to it too.

## [1.4.1] - 2026-06-06

### Fixed
- **Department member counts.** The admin panel now shows the correct number of officers in each department instead of always showing 0.
- **On-screen text.** Fixed placeholder and incorrect wording in several screens — the evidence terminal, the boss menu, the admin panel, and the electrical repair minigames now show proper labels and text.

### Notes
- The wording fixes are in the language file (`locales/en.json`). If you use a **customized** language file, copy the updated lines over to get the corrected text.

## [1.4.0] - 2026-06-06

### Added
- **Firearm registration desk.** Civilians can register the guns they carry at a station for a small fee, which is paid into the department's funds. Only legal, serial-numbered guns that haven't been registered before can be registered — illegal guns can't. You add these desks from the admin menu, under a station's services.
- **Weapon registry and a new Weapons tab in the MDT.** Every registered gun is tracked by its serial number, so officers always have a record of who it belongs to — even after the gun is sold or destroyed. Officers can search by serial number or owner name and mark a gun as stolen, seized, or revoked. The registry fills up on its own from the registration desk and from guns booked into evidence.
- **More than one of each point per station.** Clock-in, armory, boss menu, evidence locker, evidence lab, and personal locker can each be placed more than once at the same station.
- **Prisoner sentence commands.** `/addtime` adds or removes time from a prisoner's sentence, and `/setsentence` sets it to a specific length. Both work even when the prisoner is offline, and release the prisoner if the time reaches zero. Usable by admins, or by officers of a high enough rank.
- **New developer exports** for the weapon registry, for third-party integrations. See `docs/EXPORTS.md`.

### Changed
- **Evidence lockers now work per case.** Each case has its own evidence locker instead of one shared locker per station. Officers pick a case before opening, processing, or logging evidence.
- **Officers go on and off duty at a station by default.** The duty command and shortcut are turned off in favor of clocking in at a station. You can switch this back if you prefer (see Notes).
- **Automatic hiring from job changes.** Setting a player's job to one of your departments now hires them, transfers them, or updates their rank automatically. Switching a player to a different, unrelated job no longer removes them as an officer, so multi-job setups work cleanly.
- **Per-action duty rules.** Each officer interaction can now be set to require being on duty, or not, on its own. This lets you allow certain actions (such as restraining with zip-ties) for off-duty or civilian players while keeping the rest for on-duty officers.
- **Impounding unowned vehicles.** If you allow impounding vehicles that nobody owns, they are now simply removed instead of causing an error.

### Fixed
- **Evidence screen title.** The top of the evidence screen showed placeholder text instead of a proper title.
- **Dispatch alerts.** Alerts on the MDT dispatch screen no longer flicker while the list refreshes.

### Database
- When upgrading, run the new file in `sql/migrations/` (`004_weapon_registry.sql`). It adds the weapon registry and the firearm registration desk. Fresh installs already include it. Run it once.

### Notes
- **Important — existing evidence.** Because evidence lockers now work per case, anything left in the old per-station lockers will no longer be reachable in game after updating. The items are not deleted, but there is no automatic move and no in-game way to reach them. If you have evidence stored at stations, take it out before updating.
- These updates add a few new settings. If you use **customized config files**, review the additions below — fresh installs already include them.
- Station-only duty is now on by default in `shared/config.lua`. To let officers toggle duty from anywhere instead, set:
  ```lua
  Config.RequireStationClockIn = false
  ```
- Add the firearm registration settings in `shared/config/field_actions.lua`, in the station services section:
  ```lua
  firearmRegistrationEnabled = true,
  firearmRegistrationFee = 250, -- taken per gun, paid into the department's funds
  ```

## [1.3.1] - 2026-05-27

### Added
- **Station-only duty option.** A new setting lets you require officers to clock on and off duty only at a station's clock-in point. When turned on, the `/duty` command and the radial menu duty shortcut are switched off. Off by default, so nothing changes unless you enable it.
- **More officer target-menu actions.** The on-duty officer target menu can now also include hard cuff, carry, headbag, put in / take out of vehicle, gunshot residue (GSR) check, and issue ticket. Each one can be turned on or off on its own. All of the new actions start **off** — cuff, search, and escort stay on as before.

### Changed
- **Cleaner suspect vehicle options.** When dealing with a cuffed suspect, the menu now shows only the option that applies — "Put in Vehicle" when they're standing, or "Take out of Vehicle" when they're seated — instead of both at once. These options only appear for a cuffed suspect.
- **Uniform editor improvements.** Creating and editing station uniforms is smoother, with male and female outfits handled separately.

### Fixed
- **"Take out of Vehicle" now shows up reliably.** After putting a cuffed suspect into a vehicle, the option to take them back out now appears as expected. On some menu systems it could previously stay hidden.
- **Fleet liveries and extras apply correctly.** Admin-set liveries and forced extras on fleet vehicles are no longer overwritten by older saved vehicle data when a vehicle spawns.

### Notes
- These updates add new settings. If you are upgrading and use **customized config files**, add the lines below (the defaults keep your current behavior). Fresh installs already include them.
- Add to `shared/config.lua` to enable the station-only duty option:
  ```lua
  -- When true, officers can only go on/off duty at a station's clock-in zone.
  -- Disables the /duty command and the radial duty toggle.
  Config.RequireStationClockIn = false
  ```
- Add the new actions inside `Config.OfficerTargetMenu.Actions` in `shared/config/interactions.lua` (all default off):
  ```lua
  hard_cuff = { enable = false, icon = 'fas fa-lock' },
  carry     = { enable = false, icon = 'fas fa-user-plus' },
  headbag   = { enable = false, icon = 'fas fa-user-secret' },
  vehicle   = { enable = false, icon = 'fas fa-car-side' },
  gsr       = { enable = false, icon = 'fas fa-hand-dots' },
  ticket    = { enable = false, icon = 'fas fa-receipt' },
  ```

## [1.3.0] - 2026-05-18

### Added
- **Vehicle extras editor in the admin panel.** Admin → Vehicles now has an extras editor alongside the existing livery field. Force individual extras on or off per fleet vehicle — useful for hiding player-business liveries or decals on custom replacement models.
- **Duty toggle from anywhere.** The `/duty` command is now available to all hired officers (no longer debug-only) and a global radial menu shortcut is added when on the force. Officers can clock in / out without visiting a station's clock-in zone.
- **New MDT server exports** for third-party integrations: `CreateBolo`, `GetActiveBolos`, `ClearBolo`, `SearchPerson`, `GetPersonProfile`, `SearchVehicle`. See `docs/EXPORTS.md`.

### Fixed
- Officers were being auto-fired when external multi-job scripts swapped their active job between names. Officer records are now only cleared when the framework job is explicitly set to `unemployed`.

## [1.2.0] - 2026-05-13

### Added
- **License management in the MDT.** Officers can grant and revoke driver, weapons, business, pilot, and boating licenses from a person's profile. License types are configurable in `shared/config/licenses.lua`.
- **`/cuff` and `/uncuff` commands** for on-duty officers to cuff or uncuff the nearest player.
- **Officer target menu.** On-duty officers get cuff, search, and escort options when targeting another player through your server's target system. Toggle and tweak distance/actions in `shared/config/interactions.lua` (`Config.OfficerTargetMenu`).
- **Tablet animation** while using bodycam, CCTV, and the MDT — officers visibly hold a tablet instead of standing idle. Toggle per feature in `shared/config/bodycam.lua`, `shared/config/cctv.lua`, and `shared/config/mdt.lua`.

### Changed
- Fleet vehicles now spawn client-side for smoother sync and fewer race conditions on plate/key assignment.

### Database
- Run `sql/migrations/003_random_plates.sql` to upgrade existing installations. Rewrites old `PREFIX-###` fleet plates to random 8-character alphanumeric plates so plate-keyed key systems work correctly (GTA's plate font has no dash glyph).

## [1.1.1] - 2026-05-13

### Fixed
- **Custom / addon fleet vehicles now get keys.** Garage spawns are now server-side, so plates and vehicle keys line up correctly for streamed addon police models, not just stock GTA cars.

### Changed
- Fleet vehicles store their entity type (car, bike, boat, heli, plane), detected automatically when you add the vehicle. Motorcycles, helis, and boats now spawn as the correct type.

### Database
- Run `sql/migrations/002_vehicle_type.sql` to upgrade existing installations.

## [1.1.0] - 2026-05-11

### Added
- **Video Storage** tab in the MDT (replaces the Bodycam tab). Now stores bodycam clips and bait car dashcam recordings together, with a source filter for switching between them. Search now matches by name, plate, or recorder.
- **Bait car integration** — dashcam clips captured by `oxide-baitcar` are uploaded to the same library and visible to officers in the MDT.
- **Citizen email notifications** for traffic enforcement. Speed camera violations and manually issued citations now email the offender's in-game inbox with the invoice number and amount. Toggle per type in `shared/config.lua` (`Config.CitizenEmail`).
- **Hidden characters in the MDT.** Admins can hide specific character IDs from MDT search, profiles, and vehicle owner lookups. Optional rank-based bypass lets senior officers still see hidden records. Configure in `shared/config/mdt.lua` (`Config.MDT.hidden`).
- **Offline jail time reduction.** When enabled, sentences continue counting down for offline players and records auto-release when the timer expires. Configure in `shared/config/custody.lua` (`Config.Custody.ReduceOfflineTime`, `Config.Custody.SecondsPerMonth`).
- **Admin `/jail` and `/unjail` commands** for placing or releasing a player by ID with permission checks and notifications.

### Database
- Run `sql/migrations/001_video_storage.sql` to upgrade existing installations. Adds `source` and `metadata` columns to `police_bodycam_recordings` so the table can hold both bodycam and bait car clips.

## [1.0.0] - 2026-05-08

### Added
- Initial public release of `oxide-police`.
- Full department builder with runtime in-game admin panel (`/padmin`): departments, stations, grades, badges, salaries, jail and stripping rules, speedcam ignored jobs.
- Per-station configuration: clock-in zone, armory, garage / fleet, evidence locker, helipad, mugshot booth, jail cells, holding cells, station bell, trash bin.
- MDT with dashboard, case management, charges catalog, fines, warrants, BOLOs, criminal records, dispatch overlay, live officer locations, plate / person search.
- Custody pipeline: cuffs (front/rear), escort, search, vehicle put-in / take-out, mugshot capture (with `screenshot-basic` upload), booking flow, jail sentencing, configurable prison stripping (locker / wipe / whitelist), prison revive cutscene, automatic release on timer.
- Prison economy: mining, cleaning, and electrical prisoner jobs with money / item rewards; admin-configured prison shop with paid catalog and a free meal at configurable meal windows.
- Field tools: handheld speed radar (`WEAPON_RADAR` streamed addon weapon), vehicle radar with front/rear lock, placeable admin-configured speed cameras with configurable fines.
- Evidence registry: blood, bullet, and fingerprint drops with auto-cleanup, GSR check, vehicle fingerprint capture, weapon ballistics catalog, evidence camera item, per-station evidence locker.
- Bodycam viewer (`/bodycams`) and CCTV viewer (`/cameras`) with optional same-department filtering and shared `Island_CCTV_ChannelFuzz` overlay.
- Bodycam clip recording: officers watching another officer's feed can capture clips that upload to Fivemanage (using `FIVEMANAGE_MEDIA_API_KEY`) or post to a Discord webhook. Auto-records once per watch session and exposes a manual hotkey (`+bodycamrec`, default `F6`). Configured via `Config.Bodycam.recording.*` in `shared/config/bodycam.lua` (provider, max duration, framerate, bitrate, audio capture, hotkey, thumbnail capture, Discord webhook + size cap, auto-record toggle, manual cooldown).
- MDT bodycam roster: list, attach to case, delete, and viewer with playback. Boss-configurable per-department `bodycam_view_all_min_rank` overrides the same-department roster filter for senior ranks. Recordings are stored in `police_bodycam_recordings` with the recorder, wearer, department, trigger type (`auto` / `manual`), duration, provider, video URL, and optional thumbnail / case attachment.
- Armory shop NUI with Loadouts / Items tabs (modeled on the prison shop). Officers can buy whole loadouts or individual items; pricing is per-item inside a loadout (admins set prices in `/padmin` → Loadouts; the bundle total is derived). Funding source is per department, toggled by the boss in the boss menu (`armory_treasury_funded` column). Armory pulls are commitment-based: items stay in the officer's inventory across clock-out and disconnect (no auto-strip, no manual return).
- Department treasury with automatic payroll on `Config.SalaryInterval`. With `Config.PlayerManagement = true` (default) salaries debit the department treasury and unpaid ticks accrue in `police_unpaid_payroll` for boss settlement; set to `false` to revert to direct framework salary payouts.
- Field actions: tackle (`+policetackle`), hands-up (`+handsup`), headbag, ziptie + scissors, mouth tape, tracking band, wheel clamp, vehicle impound, ticketing.
- Placeable scene props with carry mode: cones, barriers, road poles, spike strips (configurable in `shared/config/objects.lua`).
- Police radio (`+oxidepoliceradio`) with channel browser, `pma-voice` / `yaca-voice` integration, and per-officer dead state via `SetRadioDead` export.
- Megaphone item with handheld and vehicle PA modes (`+oxide_police_vehicle_pa`).
- Door lock control with `ox_target` zones and a hotkey (`Config.Doors.ToggleKey`, default `L`) for the nearest door.
- 100+ pre-loaded charges across traffic, public order, property, violent, weapons, drug, financial, and miscellaneous categories.
- Fleet catalog ships with Patrol, Pursuit, Tactical, and Motorcycle categories; category labels are locale-driven (`locales/en.json`) so translators can retitle them without editing `shared/config/fleet_catalog.lua`.
- QBCore compatibility shim: `police:GetCops`, `police:GetDutyPlayers`, `police:server:IsPoliceForcePresent`, `police:server:isPlayerDead`, `police:IsSilencedWeapon`, `police:server:UpdateCurrentCops`, `police:SetCopCount` broadcast for QB robbery scripts, and `exports['qb-policejob']:IsHandcuffed()` via `provide('qb-policejob')`.
- ESX compatibility shim: optional ESX job sync (`Config.Compatibility.es_extended.jobSync`), `ox_inventory` busy state synced with cuffs, common ESX policejob callbacks, and `SyncEsxJobs` export for manual sync.
- Global compatibility toggles `Config.Compatibility.enabled` and `Config.Compatibility.debug` in `shared/config/compat.lua`.
- Server exports for cross-resource integration: duty / officer queries, department / treasury management, MDT warrant and BOLO checks, criminal records, jail apply, mugshot capture and lookup, custody control, evidence GSR / fingerprint, vehicle fingerprint lookup. Full reference in `docs/EXPORTS.md`.
- Locale-driven UI strings via `locales/en.json` for the MDT, admin panel, boss panel, custody, prison shop, and notifications.
