# Changelog

## [1.2.0] - 2026-11-05

### Added
- A level-up banner that lists what the new level unlocked, and "+XP" pop-ups as XP is earned. Both are Oxide HUD panels that can be moved or switched off in `/hud`, and either can be turned off for everyone under **Players → Lab XP**.
- The lab journal: one-time XP for each stage's first run and each product line's first batch (the first full run now reaches level 2), and XP for personal bests and the first counterfeit that passes, read at the light box.
- Line mastery: 5, 15 and 40 batches of a product line reach Familiar, Skilled and Master. Each tier pays XP once and gives that line sharper readings, 10% tighter batches or a profile badge.
- Daily and weekly lab goals (3 daily and 2 weekly by default), drawn for each player's level, with one swap a day. Goals pay XP only and reset by the server's clock.
- Master ranks past level 10: every 4,000 XP is a rank, from Master Presser I to V, each with a perk pick: tighter batches, slower die wear, faster brand reputation, a shorter delivery cooldown or faster machines. **Reset every pick** in the Ranks tab clears the picks for $25,000 cash by default.
- `/pills stats` now opens the Lab Profile, with Overview, Lines, Goals and Ranks tabs, in place of the career card.
- The Lab app on oxide-phone and oxide-tablet, with level progress and today's goals widgets. Players install it from the device's app store; add the app ID `pills_lab` to the device's preinstalled apps to put it on every device.
- Optional gang influence from finished batches (needs `oxide-gangs`) and an optional skills mirror that copies pill XP to a skill system o-link supports. Both are off by default.
- Settings for all of the above in `/pills settings`: **Players → Lab XP, Line Mastery, Lab Goals, Master Ranks and Lab App** (`LabXp`, `LineMastery`, `LabGoals`, `MasterRanks`, `LabApp`) and **Advanced → Gang Activity** (`GangActivity`).
- Admin commands: `/pills setxp`, `/pills resetjournal`, `/pills setmastery`, `/pills resetgoals`, `/pills setrank` and `/pills respec`, also from the server console with a player ID.
- For developers: the `GetPlayerRank` export, the `oxide-pills:server:levelUp`, `oxide-pills:server:rankUp` and `oxide-pills:server:batchSeized` server events, and the lab journal in `GetPlayerProgression`.

### Changed
- XP from the `AddXP` export (oxide-drugselling sales) now shows the pop-up and the banner like pills' own XP.
- A drum tumbler, ball mill or pan coater run that finishes after its player left now raises their level at once.
- `/pills setlevel` shows the level-up banner when it raises a level, and says so when the change can't be saved.
- An edited level table in `/pills settings` must start at level 1 with 0 XP, and each level must need more XP than the one before.

### Fixed
- Only oxide-drugselling can report a street sale to pills. Before, any other server script could, and build a brand's reputation with it.
- A delivery drop can no longer be paid twice when a second handoff arrives while the first is still being processed.

### Documentation
- Updated the features, configuration, admin, exports and installation guides for progression, the Lab Profile and the Lab app.

### Migration Guide
- On start, oxide-pills creates `pills_journal`, `pills_line_mastery`, `pills_goals` and `pills_ranks`. The shared `drugs_progression` table is unchanged. If your database user can't create tables, run `sql/migrations/007_progression.sql` (safe to run more than once).
- The new settings are added on start with their defaults. Saved settings, including your XP rewards and level table, are kept.
- A level table saved before this version keeps working, and the rest of **Players → Progression** still saves while its levels are left as they are. The new rule applies once you edit the levels.
- Existing XP, levels and totals stay. The journal, mastery and goals start with this update: existing characters can earn each first-time bonus once, and earlier batches don't count toward mastery. Players already past 15,000 XP get their master ranks at once, with perk picks waiting.
- Merge the new keys in `locales/en.json` into custom translations.

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
