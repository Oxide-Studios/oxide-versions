# Changelog

## [1.0.2] - 2026-10-26

### Fixed
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.

### Documentation
- `docs/FEATURES.md` and `docs/TROUBLESHOOTING.md` list every wreck the server writes off: blown up, burnt out or crashed beyond repair, or engine and body both gone. Troubleshooting also notes the restart write-off fixed in 1.0.1.

### When you update
- There's no database migration in this version.

## [1.0.1] - 2026-10-24

### Fixed
- Units left out in the field are no longer deleted as wrecked about 30 seconds after a server or resource restart when no player is near them.
- Blown-up units, and helicopters that crash, are now written off within seconds. Before, a blown-up engine or chief car could stay listed as out.

### When you update
- There's no database migration in this version. A new server file is added, so restart the whole server after updating, or run `refresh` and then `restart oxide-fire`.

## [1.0.0] - 2026-10-18

First release.

### Added
- **A fire department built in game.** Grades, salaries, uniforms, gear-room loadouts and a setup checklist per department in `/fire admin`. Badge numbers are given automatically; bosses edit badges and callsigns from the management desktop. Hire and dismiss with `/fire hire` and `/fire dismiss`, or from the boss desktop, where a hire is a job offer the player accepts.
- **Stations** with clock-in points, a call board, personal lockers, a gear room and a management desk. Clock in at a clock-in point. `/fire duty` clocks out anywhere, and clocks in anywhere when **Require station clock-in** is turned off.
- **Radio channels** with `oxide-radio`. Departments own numbered channels with a rank requirement and an optional duty requirement, set up in `/fire admin` → Radio. Firefighters are tuned to their department's default channel when they clock in.
- **Persistent apparatus.** Engines, ladders, brush units, rescues and chief cars, bought from a catalog, taken out of station bays and kept in the world across restarts. Engines, ladders and brush units carry water, foam and hose outlets, and every unit has an equipment compartment. Each catalog entry has a vehicle type: Car, Bike, Quad, Boat, Helicopter or Plane. The catalog ships an engine (`firetruk`) and a chief car (`lguard`). Add your own fire vehicles in `/fire settings`, and set hose outlet positions for add-on trucks in `/fire admin` → Models.
- **Synced fires** that every player sees the same way, with Casual, Standard and Realistic difficulty. On Standard and Realistic, smouldering hot spots come back unless they are soaked or pulled apart with the axe.
- **Hot spot markers.** Each smouldering spot shows burnt ash, embers and a faint glow, steams when it is soaked, and breaks up when it is pulled apart with the axe. Change it or turn it off in `Config.Fire.hotspot`.
- **Vehicle, brush and structure fire calls** at incident sites and wildfire zones, from an automatic call generator, with dispatch alerts. Build sites with the **incident editor** in `/fire admin` → Incident Sites: fly around the site and place its spot, fire points, victim spots and a vehicle fire's wreck. Problems are shown before you save.
- **Hoses** with jet, fog and foam patterns that cool every burning spot the water reaches. Also hydrants from the map (plus any you list in `Config.Hydrants.extra`), supply lines, fire extinguishers, and the water cannon on the stock `firetruk`. Water pushes props, vehicles and people.
- **Breathing air (SCBA).** A backpack and mask every player sees, an air timer, a head-out warning and a low-air alarm others can hear, refilled at the gear room or an apparatus. Smoke hurts anyone breathing it without a mask, or gives them smoke inhalation with `oxide-medical`.
- **Rescue carries and drags** for downed players, and **victim search** in structure fires, with an alert to medics and a handoff to them.
- **Station life.** A station bell for new calls, phone page-outs, paid rig checks, and lightning that starts brush fires with `oxide-weather`.
- **Firefighting feedback.** Points for knocking spots down, deductions for mistakes, and a fire card showing how far the fire is knocked down. Feedback only, never paid, and it can be switched off.
- **Treasury, payroll and back pay.** The treasury is a bank account (starting at 30,000) or a department balance, and it never goes below zero. A payout the bank refuses goes back into the treasury. Unpaid back pay stays owed for 30 days.
- **Admin tools.** Live incidents (start, stop and spread fires), the call generator, and `/fire writeoff <plate>` for a unit lost where the server can't see it.
- **`/fire settings`** edits department, duty, payroll, station, apparatus, call, safety and station-life settings in game, saved in the database. Fire, hose and tool tuning stays in the config files.
- **Works with Oxide HUD (free).** Players move or hide the firefighting panels in `/hud`, and the panels take the server's accent colour (`Config.UseHudAccent`). Without Oxide HUD they sit where `Config.HudLayout` puts them.
- **Optional integrations:**
  - `oxide-dispatch` or another dispatch resource. Without one, firefighters get a notification and a map blip.
  - `oxide-weather`, for wind, rain and lightning.
  - Phone page-outs.
  - `oxide-tablet`, for the Fire Command app.
  - Banking resources, for department treasuries.
  - EMS jobs, for rescue handoffs.
  - `oxide-medical`, for smoke inhalation.
  - `g-multijob`, with multi-department enrollment on.
- **Exports** to start, read and clear fires, start calls, report lightning, and read stats and settings. See `docs/EXPORTS.md`.
- **Runs on QBCore, Qbox and ESX.** The database tables are created on first start.

### Before you start
- Add a fire job to your framework with the department's name. Register the `scba` item and copy its image; `itemimages/ox_inventory_items.lua` has the `ox_inventory` entry. See `docs/INSTALLATION.md`.
- On ESX, use an inventory `o-link` supports, not ESX's built-in one.
- **Use banking accounts** is on by default. Without a supported banking resource, turn it off in `/fire settings`, or the treasury stays at 0 and no salaries are paid.
- Restart the whole server after installing, so the nozzle, couplings and SCBA backpack load.
- Needs `o-link` 1.12.1 or newer.
