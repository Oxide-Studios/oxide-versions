# Changelog

## [1.2.2] - 2026-10-26

### Fixed
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.
- `/ems settings` now says why a change was not saved, for example "settings are still loading, try again shortly" or "could not be saved, see the server console", instead of "value rejected". The old value stays, and a failed Reset to default now shows an error.
- A settings change made before the settings finish loading at startup, from `/ems settings` or the `SetSetting` export, is now refused instead of being lost.

### Documentation
- `docs/FEATURES.md` and `docs/TROUBLESHOOTING.md` list every wreck the server writes off: blown up, burnt out or crashed beyond repair, or engine and body both gone. Troubleshooting also notes the restart write-off fixed in 1.2.1.

### When you update
- There's no database migration in this version.

## [1.2.1] - 2026-10-24

### Fixed
- Units left out in the field are no longer deleted as wrecked about 30 seconds after a server or resource restart when no player is near them.
- Blown-up units, and helicopters that crash, are now written off within seconds. Before, a blown-up ambulance took about half a minute and a blown-up helicopter could stay listed as out.

### When you update
- There's no database migration in this version. A new server file is added, so restart the whole server after updating, or run `refresh` and then `restart oxide-ems`.

## [1.2.0] - 2026-10-18

### Added
- **Vehicle types Bike, Quad and Plane**, alongside Car, Boat and Helicopter. Set each catalog entry's type in `/ems settings` → Fleet, and each vehicle's type in `/ems admin` → Fleet. The Fleet screen now has a Type dropdown and marks groups whose vehicles have mixed types.
- **`/ems writeoff <plate>`** (admins and the server console) writes off a unit the server can't see lost, for example one sunk in a river or in an add-on map's water.
- **Works with Oxide HUD (free).** Players move the downed card and the placement hint in `/hud`, the HUD editor previews them, and their key badges take the server's accent colour (`Config.UseHudAccent` in `shared/config.lua`, default `true`). Needs `o-link` 1.12.1 or newer. Without Oxide HUD nothing changes.
- The database tables are created automatically on first start.

### Changed
- **Hires from the management desk are job offers.** The player has 60 seconds to accept or decline, and the boss is told the result. `/ems hire` still hires straight away.
- **A vehicle's type is set by admins only**, and every unit spawns as its saved type. The medic's game no longer reports or corrects it. A catalog entry with an unknown type can no longer be bought; before, it silently became a car.
- **Units are written off only from what the server sees:** a wrecked engine, body or fuel tank, or the unit sitting in the sea, the Alamo Sea or a reservoir. A driver's report alone no longer writes one off. For anywhere else, use `/ems writeoff`.
- Taking a vehicle out means standing within 10 m of the garage's interaction point.
- **Wear Civilian Clothes** works only while you're wearing a department uniform.
- A rank change applies at once to an on-duty medic: garage, vehicle and uniform rank checks, and the next salary.
- With a banking account, the treasury can no longer go below zero, even if your banking resource allows overdrafts.
- How long unpaid back pay stays owed now follows the value set in `/ems settings`, not the config file's.
- Refused admin and boss actions, payouts and refunds are written to the server log.

### Fixed
- The treasury can no longer be overdrawn by withdrawals, payroll runs or fleet purchases landing at the same moment.
- Two medics checking out at the same moment can no longer get the same unit or bay. A unit is returned only once, and a wrecked unit can no longer come back as available.
- Two fleet recoveries at once no longer charge for the same units, and back pay can no longer be paid twice.
- Two hires at once can no longer put one character in two departments or give two medics the same badge number.
- On QBCore, Qbox and ESX, pressing an item twice quickly no longer uses it twice; one bandage used to heal twice.
- Fleet units are recognised from the server's own records. A modified game client could disguise another car as a unit, or wipe a parked unit's health so the server wrote it off.
- A modified game client can no longer report impossible fuel or health for a unit, or save an oversized vehicle look.
- A treasury withdrawal the bank refuses goes back into the treasury, and the boss sees "The bank refused the payment. The treasury was refunded." Before, it reported success and the money was lost.
- Back pay that can't be delivered stays owed and its money goes back to the treasury, instead of being lost.
- Aiming the admin panel's entity picker at a person no longer risks crashing the game.
- Medics examining hypothermia or smoke inhalation from `oxide-medical` see the condition's name instead of a raw text key.

### When you update
- **Run `sql/migrations/0006_vehicle_types.sql` once.** Until you do, vehicles an admin added to a boat or helicopter garage that nobody has taken out are still saved as cars. The migration saves them as boats or helicopters, or as planes when the same model already flies as a plane in your fleet. Don't run it twice: a second run would undo an admin's choice of Car in such a garage.
- **Check your fleet types.** 1.1.1 had no Bike, Quad or Plane, so look for:
  - motorbikes and quads saved as cars;
  - planes listed as `helicopter` in the catalog.
  
  Fix them in `/ems settings` → Fleet and `/ems admin` → Fleet.
- Restart the whole server after updating; new files are added.
- If you translated `locales/en.json`, copy in the new keys.

## [1.1.1] - 2026-10-10

### Fixed
- Clocking in at a facility now requires standing at one of its clock-in points. A modified game client could clock in from anywhere before. With **Require facility clock-in** turned off, `/ems duty` still works anywhere.
- Treasury deposits and withdrawals, admin funding, salaries, fleet purchases, supply prices and grade salaries only accept a real amount of money: a whole number above zero. A modified game client could send an invalid number that slipped past the old check and broke the treasury balance or a medic's bank balance.
- A deposit only reaches the treasury once the money has actually left the depositor's bank.
- A fleet purchase is limited to 50 vehicles at a time. An oversized order of a free catalog vehicle could stall the server.
- Bosses can change only their own badge number and callsign, and those of medics ranked below them. Before, they could change anyone's in the department, including higher ranks.
- Pressing ESC to cancel an in-world placement no longer closes the whole `/ems settings` menu.
- The `/ems settings` menu now closes when the character logs out instead of staying on screen.
- A setting the database fails to save keeps its old value and shows as rejected in `/ems settings`, instead of looking saved until the next restart. The `SetSetting` export returns `false` when the save fails.
- Salaries, back pay and treasury deposits and payouts show a description in players' bank history ("Paycheck", "Back pay", "Department deposit", "Department payout").

### Changed
- With wage garnishment turned on in `oxide-banking`, EMS salaries now count as wages and can be garnished for loans in collections.

### Added
- `itemimages/ox_inventory_items.lua`: paste-ready `ox_inventory` definitions for the seven EMS and medical items.
- The `/ems settings` editor's buttons, placeholders and messages can be translated: 26 new `settings_ui.*` keys in `locales/en.json`, plus five `treasury.*` keys for the bank-history descriptions.
- A security warning in the log when someone who isn't an admin tries to open or change `/ems settings`.

### Documentation
- `docs/FEATURES.md`, `docs/INSTALLATION.md` and `docs/TROUBLESHOOTING.md`: where down and death alerts go with `oxide-dispatch`, with no dispatch resource, and with another one.
- `docs/ESX_INSTALL.md` and `docs/QBX_INSTALL.md` point to `itemimages/ox_inventory_items.lua` for the item definitions.
- `docs/ADMIN.md`: clock-in points, the boss rank rule for roster edits, and the fleet purchase limit.
- `docs/EXPORTS.md`: `SetSetting` returns `false` when a save fails.

## [1.1.0] - 2026-09-11

### Added
- The boss menu can open inside the optional `oxide-tablet` resource as an "EMS Command" app. When that resource is running, department bosses get a tile that opens the same management desktop from anywhere, and the boss point at the facility keeps working. Without it, nothing changes. Turn it off under **Boss menu → Host in oxide-tablet** in `/ems settings`; the first-start default lives in the new `shared/config/boss.lua`.
- New locale key `tablet.boss_app_label` in `locales/en.json`.

### Fixed
- Settings saved in `/ems settings` no longer revert to the shipped defaults after a restart when the startup read of `oxide_settings` comes back empty (a restored or blank database, or a renamed resource folder). Startup now only fills in missing values and never overwrites a saved one.
- Decimal settings no longer show up as `0.90000000000009` in `/ems settings` after a round trip through the database; values are snapped to ten significant digits when loaded and saved.

### Documentation
- The `hospital:server:emergencyAlert` / `ambulanceAlert` and `hospital:client:CheckStatus` / `TreatWounds` / `RevivePlayer` compatibility events are QBCore and Qbox only. `docs/EXPORTS.md` and `docs/FEATURES.md` now say so, and the ESX guide no longer lists them.
- `docs/CONFIGURATION.md` covers the new boss-menu setting.

## [1.0.2] - 2026-09-03

### Fixed
- `painkillers` no longer stops working as a cure when `oxide-medical` is installed. Both resources were claiming the item and whichever started last won, so on some servers taking painkillers did nothing at all, with no error anywhere. `oxide-medical` owns it now, because it is the cure for fever.
- The Refresh and Close buttons at the bottom of the `/ems admin` sidebar no longer sit flush against their own border.

### Added
- `ClaimPainkillers` in `shared/config/compat.lua`, set per framework and off by default. Turn it on to take `painkillers` back for oxide-ems — on Qbox that restores the stock damage-effect stack, and the fever cure stops working.

### Documentation
- New troubleshooting entry for "No Medical Records" and "The medical system is not available", covering both causes: `oxide-medical` not running, and an `o-link` older than 1.4.1, which is too old to connect the two resources. A working `/med record` rules out neither.
- Install guides now tell you to confirm `painkillers`, `bandage`, `ifaks` and your first-aid item really are in your inventory's item list. They ship with every supported framework, but trimmed item lists drop them and a missing entry fails silently.

## [1.0.1] - 2026-08-29

### Fixed
- Dying no longer teleports you away from where you died and replaces your character with a random pedestrian. FiveM's built-in auto-respawn is now switched off on every framework, the way the ambulance job oxide-ems replaces used to do it.
- Settings you switch **off** in `/ems settings` no longer revert to their default when the server restarts.

## [1.0.0] - 2026-08-22

First release.

### Added
- **EMS department builder.** Departments, facilities, grades, garages, uniforms, radio channels and the supply shop are all created in-game through `/ems admin` and stored in the database — there is no list of hospitals to edit in a config file. A department's internal name is the framework job name, so hiring a medic sets their job the way your framework expects.
- **Two facility types.** Hospitals handle emergencies: beds, hospital respawn, x-ray machines and operating tables. Doctor's offices handle walk-ins: a reception bell that pages on-duty staff, plus lockers, a supply shop and clock-in. Both types share clock-in, personal lockers, the management desk and the supply shop.
- **Interaction points are placed in-world.** Every clock-in spot, bed, locker, management desk, supply counter, reception desk, x-ray machine and operating table is placed by walking to it and picking the spot. Beds show a preview patient lying in place, and x-ray screens and surgery monitors show a live preview quad you can resize with the arrow keys before you save.
- **Field care pipeline.** Examine a patient to open a diagnosis overlay with a scripted camera, seven clickable body regions and the patient's vitals. Badly hurt patients cannot be revived on the street — they have to be stabilized, transported and revived at a hospital bed.
- **Stabilize, then transport.** Stabilizing pauses the patient's bleedout timer. Bleeding wounds must be treated before a stabilization takes. A stabilized patient can be loaded into an ambulance seat or wheeled on the stretcher, and can climb out of a stopped vehicle themselves if their medic disconnects.
- **Stretcher.** Take the stretcher out of the back of an ambulance, drag it to the patient, load them, wheel them back and load the stretcher with the patient still on it.
- **Treatment as a surgery minigame.** Every treatment — in the field, at the clinic and on the operating table — runs a timed keypress sequence. The result is graded: a clean run cures the condition, a middling run cures it but leaves a complication, and a bad run fails and can make things worse. The sequence and the score are both generated and re-checked on the server.
- **X-ray machines.** Place a machine, position a patient on it, run a scan and the result appears on an in-world screen for everyone standing nearby — a skeleton highlight plus a findings list of fractures and retained bullets.
- **Operating tables with a live monitor.** Place a patient on the table and the in-world monitor shows their name, blood type, condition list and an ECG driven by their real pulse — flatlining when it should.
- **Hospital beds.** Beds heal to full over a configurable time. A medic can check a downed patient in as the revive; with nobody on duty, patients (or a bystander) can check them in themselves, subject to a fee and a stabilization requirement.
- **Hospital respawn.** Place a respawn point at any hospital and players who give up and respawn wake up at the nearest one, behind a fade-out cutscene. Works on every framework.
- **Duty, payroll and treasury.** Clock in at a facility, draw a salary on an interval, and let a department boss run the roster, treasury, back pay and fleet from a desktop-style management panel. Salaries can come out of the department treasury or straight from the framework.
- **Department fleet.** Vehicles are individual, persistent rows. Checked-out vehicles survive a server restart, keep their fuel and damage, and re-link to the medic when they clock back in. Bosses buy new vehicles and recover wrecks from the treasury.
- **Uniforms.** Bosses and admins build uniforms in-game from what they are currently wearing, choosing exactly which clothing slots the uniform owns. Everything outside those slots stays the medic's own clothes, and uniforms revert automatically on clock-out.
- **Supply shop.** A department-scoped catalog. Rows with no rank requirement are public self-service; rows with one require an on-duty medic at that rank or above. Sales go to the department treasury.
- **Dispatch alerts.** Automatic alerts when a player goes down or dies, a distress keybind (`G`) for downed players, `/911e` for anyone, a clinic reception page and a bed-request alert when a patient tries to check in while EMS is staffed.
- **Radio channels.** Departments own numbered channels with a rank requirement and an optional duty requirement. Medics are tuned to their department's default channel automatically at clock-in.
- **Medical bag.** A usable kit item that unpacks into field supplies.
- **Station markers.** Hold `H` to see in-world cards pointing at the nearest interaction points that are useful to you — reception desks if you are a civilian, clock-in points if you are a medic.
- **In-game settings editor.** `/ems settings` opens a searchable editor for every setting in the resource. Changes are stored in the database and take effect without a restart.
- **Runs on QBCore, ESX and Qbox.** oxide-ems replaces `qb-ambulancejob`, `esx_ambulancejob` and `qbx_ambulancejob`, and keeps the surfaces third-party resources depend on working: the `hospital:*` events radial menus and police "officer down" relays use, the stock medical items, the stock admin commands, and the QBCore doctor-count callback. On Qbox, `qbx_medical` stays in charge of the death loop; on QBCore and ESX, oxide-ems runs it.
- **Multi-department support.** With `AllowMultiDepartment` on, one character can be enrolled in several EMS departments at once, each with its own rank and badge, following whichever job they currently have active. Mirrors into `g-multijob` when it is installed.

### Notes
- Run [`sql/install.sql`](sql/install.sql) before first start.
- Register the four EMS items (`medical_bag`, `medical_bandage`, `medical_splint`, `medical_surgical_kit`) in your inventory and copy the images from [`itemimages/`](itemimages/). See [Installation](docs/INSTALLATION.md).
- The stretcher prop is streamed from the resource, which means a **full server restart** after installing — `restart oxide-ems` is not enough for the prop to load.
- Disable the ambulance job that shipped with your framework before starting oxide-ems.
