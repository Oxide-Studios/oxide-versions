# Changelog

## [1.1.1] - 2026-10-26

### Changed
- The `SetSetting` export refuses a change while settings are still loading at startup and returns `false`. Before, it accepted the change and then lost it.
- The reagent test result card and the engraver's brand name field no longer have a coloured bar down their left edge.

### Fixed
- oxide-pills no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.
- `/pills settings` now says why a change wasn't saved (settings still loading, a number that isn't valid, or a database error) instead of "value rejected", and keeps the old value. A failed reset now shows an error too.

## [1.1.0] - 2026-10-23

### Added
- Works with Oxide HUD (free): the stage header, the tallies and career card, the dust meter and the controls (key prompts with the press and rotary consoles) can be moved or switched off in `/hud`, with a preview in the HUD editor. They share spots with other jobs' cards and hide with the HUD (pause, cinematics, death, character select). Needs o-link 1.12.1 or newer; without Oxide HUD nothing changes.
- `Config.UseHudAccent` (`shared/config.lua`, default on): the stage HUD uses the Oxide HUD accent instead of red. Config file only.

### Fixed
- When a pharma delivery can't give back the dirty money or blister cards it took, every admin online is alerted with the player and amount, so it can be returned by hand.

## [1.0.0] - 2026-09-28

### Added
- First release of `oxide-pills`.
- Runs on QBCore, QBX and ESX through `o-link`. ESX needs `ox_inventory`.
- A full pill lab: mill, blend, press, sort and pack, each worked by hand at its own station.
- 17 placeable stations: a lab workbench with 2 machine slots, 11 benchtop machines and 5 floor machines, all kept through restarts.
- A hidden quality profile for every batch (potency, integrity, imprint, finish and consistency) that only a light box reading reveals.
- A mortar and pestle ground by circling the mouse, and a blade grinder you pulse and knock clean.
- A stand mixer loaded by hand: pour each ingredient for as long as you want it in the bowl.
- A hand-cranked press where your pace is the skill, and an electric press with four speeds.
- Die residue that builds as you press and is scrubbed off with a brush, and die wear that follows the die set.
- Tray sorting: throw out crumbled, capped, chipped, picked, blurred and patchy tablets before the batch is counted.
- Five product lines: Party Pills, NOVA Bars, VELA Blues, AXIA Percs and AURA Capsules.
- Counterfeit VELA Blues and AXIA Percs, judged on how well they pass for the real thing.
- A die engraver for cutting tooling to a product line and stamping its face.
- Player brands: a unique name and one of 8 symbols pressed onto party pills, with a reputation that sales build and overdoses and seizures damage.
- Hand-filled baggies, labelled pharmacy bottles and sealed blister cards, each protecting pills differently.
- Granulating sieve, drum tumbler, ball mill, pan coater and hardness tester for better batches.
- A capsule filling board for AURA Capsules: spread, tamp and cap 30 shells at a time.
- A rotary press worked by a crew of up to three (feeder, sorter and packer), or by one player alone.
- Dust from milling and pressing, with a wearable dust mask and a placeable dust collector.
- Mystery powder that only a reagent test kit can identify, and that can turn out to be fentanyl.
- Usable pills with effects per product line, and overdoses from batches that are too strong or from taking too many.
- Pills that break in crashes, falls and over time; broken tablets can be ground back down.
- Street sales through `oxide-drugselling` 2.0.0 or newer, priced by four buyer types who each judge a batch differently.
- A pharma delivery run that pays per blister card and launders dirty money at each drop.
- Pill ID lookup for brands and counterfeit imprints, with police evidence logging and an optional press serial trail.
- 10-level progression that unlocks stations and product lines, shows more detailed readings as players climb, and adds two level-10 perks.
- An in-character Pressing Manual item (`pill_manual`) covering the whole trade.
- Single `/pills` command: `/pills stats` and `/pills pillid` for players, and `/pills settings`, `/pills kit`, `/pills setlevel` and `/pills traytest` for admins.
- Live settings editor (`/pills settings`): change settings in game with no restart, and draw placement zones in the world.
- Blocked or allowed placement zones, and a per-item job and gang usage whitelist.
- Optional police alerts for each production stage and delivery handoff, off by default.
- Optional Pill ID tablet app with `oxide-tablet`, overdose conditions with `oxide-medical`, and territory bonuses with `oxide-gangs`.
- Model replacements for older game builds (`Config.ModelSwaps`).
- 59 inventory items with icons, and a ready-to-paste `items/ox_inventory_items.lua`.
- Database tables are created automatically at boot; `sql/install.sql` ships for manual installs.
- English locale (`locales/en.json`) covering every notification, prompt and menu.
