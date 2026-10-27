# Changelog

## [1.0.0] - 2026-10-16

### Added
- First release of `oxide-hud`, a free player HUD that every player builds for themselves in game.
- Runs on QBCore, Qbox and ESX through `o-link` 1.12.0 or newer. No database and no items to set up.
- In-game editor (`/hud`): add, drag, resize, snap to a grid and to other blocks, align and space, group, lock and hide, with undo and redo and keyboard shortcuts.
- 66 pieces of data: health, armour, hunger, thirst, stress, oxygen and stamina; speed, RPM, gear, fuel, engine and body health, throttle, odometer and 14 vehicle lamps; aircraft and boat instruments; cash, bank, server ID and character name; weapon, ammo and clip; street, area and heading; voice, radio and the clock; and the player's own text.
- 13 styles with their own options: ring, bar, shape, dial, value, indicator, compass, tape, artificial horizon, scale, voice, image and decor.
- Groups that lay blocks out in rows, columns, grids, honeycombs, rings or stacks, with frames behind them, closing the gap when a block hides.
- Rules on every block: hide above or below a value, warning and critical levels, and a pulse, starting from `Config.AutoHide`.
- 42 templates: status sets, player and finance cards, location boxes, ID cards, weapon panels, and speedometers for every vehicle: Sweep, Ghost, Instrument and Rail for cars and bikes, Helm for boats and submarines, Glass for helicopters and planes, plus a Classic set of eleven more.
- New players get the speedometer that fits each vehicle: Sweep in cars and on bikes, Helm on water, Glass in the air.
- A layout on foot, one in a vehicle, and one per vehicle type (car, bike, boat, submarine, helicopter, plane), each following the one above it until changed.
- Sample data in the editor (driving, flying, boating, armed, underwater, low needs, on the radio and more) to place blocks without leaving the editor.
- Server accent colour with an optional lock (`Config.Theme`), and each player's own accent and data colours.
- Saved layouts and share codes for the whole HUD or one group.
- A server default HUD from a share code (`Config.DefaultLayout`) that every player who never edited their HUD follows, and an optional editing lock (`Config.LockEditing`).
- Layouts pinned to the screen's edges and sized to its height, so a HUD looks the same at every resolution.
- Hides by itself at character select, in the pause menu, while dead or downed, and while another script's menu has the mouse; GTA's own street and area names return while it is hidden.
- Other resources' notifications, progress bars, job timers and cards are placed clear of the HUD; players can move them, hide them while editing or switch them off in game.
- Speed units (`Config.SpeedUnit`), boat and aircraft units (`Config.Units`), stall and pull up warnings (`Config.Flight`), an optional server logo (`Config.ServerLogo`) and reserved screen areas (`Config.ReservedAreas`).
- Reads voice from `pma-voice` or SaltyChat, fuel from most fuel resources and the seatbelt from `qbx_seatbelt` or `qb-smallresources`, all optional.
- Developer API: `olink.hud.AddPanel` places any resource's panels around the HUD, with a complete example in `docs/API.md`.
- English locale (`locales/en.json`) covering every HUD and editor text.
