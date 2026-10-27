# Changelog

## [1.1.2] - 2026-10-26

### Fixed
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.

## [1.1.1] - 2026-10-23

### Fixed
- Daily streaks survive reconnects and restarts. A player who had already worked that day or the day before went back to a 1-day streak.
- A player whose saved job timeout is `0000-00-00 00:00:00` (left by earlier versions on some databases) can load their progression again.

## [1.1.0] - 2026-10-20

### Added
- The shift summary (pop-up and phone text) lists the calls' own pay, then each bonus that paid on its own line: speed bonus, every-sign bonus, glue trap bonus, tips and daily streak bonus, before the co-op bonus and gross pay.
- The stage card shows the bonuses still open on a call as optional lines with their counts: "Bonus: find all 8 signs" from level 5, and "Bonus: 3 glue traps by roach signs" on cockroach calls. Setting `Config.Payment.fullFindBonus` or `Config.Payment.glueTrapBonus` to 0 hides its line.
- From level 5, taking the torch back out after the findings guides the search for the signs that are left.
- Carrying glue traps on a cockroach call shows how to lay them for the bonus, with the count.

### Changed
- Backspace holsters the sprayer wand: the hose and wand stow and the tank stays on the back. Press G anywhere to take the wand back out (a FiveM key binding players can change: "Pest Control: take out the sprayer wand"); the stage card shows the key while the wand is holstered. Only Return Sprayer at the van takes the tank off.
- The sprayer's stage card counts the foundation covered against the 85% the report needs (for example 62% / 85%), instead of runs filled.
- Property editor keys: C steps through the modes (was 1 to 5 and Tab) and U undoes (was Z), as in the Oxide Fire incident editor, so they no longer clash with inventories and radial menus.

### Fixed
- The every-sign bonus is paid on each technician's own level. Before, it followed the crew leader's level, so a level 5 partner of a lower-level leader never got it.
- The findings card counts the signs the inspection needs (for example 4 / 4), not every sign on site.
- The first-time devices tutorial now covers glue traps as well as bait stations, and the property editor's "no runs" hint names the right key.

## [1.0.0] - 2026-10-19

### Added
- A pest control gig job for QBCore, Qbox and ESX (needs o-link 1.11.0 or newer, ox_lib and oxmysql). Clock in at the Bugstars depot, take the company van and answer calls about cockroaches, rats and wasps at houses, restaurants and warehouses. No framework job or items to set up.
- Dispatch with call cards (E accept, Backspace reject), a call timer, a weighted pest mix and Cancel Current Task. Three missed or rejected calls end the shift.
- 12 properties (4 houses, 4 restaurants, 4 warehouses), 6 with wasp nest spots.
- UV inspection: cockroach and rat calls start as "Pest complaint". Find droppings, gnaw holes, grease marks, the burrow and the harbourage with the UV torch. A findings card then names the pest, the nest and the treatment. Hints shrink as the technician levels up.
- Treatments: a backpack sprayer for cockroaches (wide fan or long stream, tank refills at the van), bait stations for rats, glue traps on cockroach trails, and wasp nests foamed and bagged from a stepladder (level 3+). Stings cost health and money.
- Pet bowls and vegetable beds from level 2: spraying them brings a complaint and a charge.
- Gear comes from the van's rear doors, and the van only offers what the next step needs. Everything goes back in the van when a call ends.
- Filing a report at the van ends each call. Unfinished work pays less, adds a complaint and brings a customer callback.
- On-screen stage and findings cards, distance markers and key prompts. Hold H for the job waypoint. Other players see the gear and the spraying.
- 19 first-time tutorials (`/pestcontrol tutorials`, `/pestcontrol tutorialreset`).
- Two-player crews: a shared van and calls, each member paid on their own level and streak plus a 15% co-op bonus. The partner keeps the van if the leader leaves.
- Five levels (Trainee Technician to Master Exterminator) with higher pay and tips. Restaurants unlock at level 2; warehouses and wasp calls at level 3. Daily streak bonus.
- Pay: base pay plus distance, speed and find bonuses, level, streak and tips. Van damage, treatment charges, a gas fee and tax come off at clock-out. Summary as a pop-up or a phone message (`Config.shiftSummary`).
- No lost pay: shifts ended by going down, missed calls, the complaint limit or a restart are paid. A player who disconnects or logs out is paid at their next login.
- Complaints and a 30-minute suspension at three. A call reached faster than a vehicle could drive there (`Config.Calls.maxTravelSpeed`) pays no distance or speed bonus.
- In-game settings editor, `/pestcontrol settings` (admins): every setting with Reset to default, applied live, with Place in world buttons. Depots can be edited during shifts.
- In-world property editor (area, spray runs, sign spots, wasp nests, no-spray zones) that blocks saving a layout a call can't use.
- Shift menu with a camera view you frame in world, or a simple context menu (`Config.menuStyle`).
- Admin tools: `/pestcontrol level <level>`, Force pest (`Config.DebugForceSpecies`) and debug logging.
- Phone messages and an incoming-call ring on supported phones (`Config.usePhone`).
- Works with Oxide HUD (free): players move or hide the stage and findings cards in the `/hud` editor, and the cards follow the HUD accent (`Config.UseHudAccent`). Without Oxide HUD, or while it is stopped, the cards use HUD → Card positions (`Config.HudLayout`).
- Optional uniforms (off by default) and model replacements (`Config.ModelSwaps`).
- Custom props included (tank, wand, bait station, glue trap, wasp nest, sign models).
- Server exports: `IsOnShift`, `GetSetting`, `SetSetting`, `GetInfo`, `GetShift`, `GetProgression`, `SetUpdateHandler`.
- Database tables are created automatically; nothing to import. Restart the whole server after installing so the props load.
