# Changelog

## [1.1.0] - 2026-10-26

### Added
- Oxide Tablet support: with `oxide-tablet` installed, gang members get a **Gang** app that opens the gang dashboard, and `/gangs dashboard` opens straight into it. The app appears and disappears as players join and leave gangs, with no relog. Without the tablet nothing changes.
- A **Gang Status** home-screen widget for the tablet: territories held, gang and rank, a warning when turf is contested, the current war's score and time left, and a row per territory. Players add it themselves.
- War and territory events in the tablet's notification tray, with an unread count on the app. Territory events (contested, claimed, taken, lost) show a banner and play a chime; tapping a notification opens the matching dashboard screen.
- **Tablet** settings under System in `/gangs settings`: `Config.Tablet.useTablet`, `notifications` and `widget`.
- With Oxide HUD, players can move the war capture bar or switch it off in the `/hud` editor, and it hides with the HUD (needs o-link 1.12.1 or newer). Without Oxide HUD it stays where it was.
- The admin territory map uses o-link's shared map and can show Cayo Perico. Set `Config.MapMode` (`inherit`, `combined`, `separate`, `disabled`) with `oxide-gangs:mapmode <value>` in the server console, or `/oxide-gangs:mapmode <value>` in game as an admin.
- New server event `oxide:gangs:server:territoryClaimed`, fired when a gang claims unowned turf.

### Changed
- The settings editor's remaining English text (buttons, placeholders, tooltips, empty lists, the Admin badge) comes from `locales/en.json` and can be translated.
- The server log records refused `/gangs settings` attempts by non-admins, and whether each settings change came from the menu or another resource.
- Decimal settings no longer show rounding noise such as 0.90000000000009 in the editor.
- The map tiles now come from o-link, so the resource download is smaller.

### Fixed
- The gang turf colours on the minimap and pause map could stay blank after a player joined, until the resource restarted.
- If a setting can't be saved, the editor now says why (still loading, not an editable setting, an invalid number, or a database error) and the old value stays. Before, it always said settings were still loading, or kept a value that was never saved.
- A setting reset that fails now shows a message instead of doing nothing.
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.
- Saved settings can no longer be replaced by the file defaults when the database returns no rows at startup.
- Pressing ESC to cancel an in-world placement no longer closes the whole settings menu.
- The settings editor closes when the character logs out.
- The `SetSetting` export now returns whether the change was saved.

### Documentation
- `docs/FEATURES.md`, `docs/CONFIGURATION.md`: the Tablet app, widget and notifications; the war capture bar with Oxide HUD; police alerts without a dispatch resource arrive as a notification and map blip for the alert jobs (o-link 1.9.1 or newer); `Config.MapMode`.
- `docs/EXPORTS.md`: `oxide:gangs:server:territoryClaimed`.
- `docs/INSTALLATION.md`: o-link 1.8.0 or newer.

### Migration Guide
- No SQL to run. Update o-link to 1.8.0 or newer before installing this version (1.9.1 or newer for police alerts without a dispatch resource, 1.12.1 or newer for the war capture bar in the HUD editor), then restart the server fully once.
- If you edited or translated `locales/en.json`, copy the new lines for the tablet app, widget, notifications, HUD bar, map and settings editor from this version's file. Until you do, those show as their key names.

## [1.0.0] - 2026-08-31

### Added
- First release of `oxide-gangs`.
- Gang registry stored in your database, mirrored into QBCore and Qbox so existing scripts keep reading gangs normally. On ESX this becomes the gang system.
- Gang creation requests: players fill in a form, staff approve or deny from the admin panel or from chat.
- Owner-built request form — add, remove and reorder the questions players answer in `/gangs settings`.
- Per-gang rank ladder with twelve permissions, editable by the gang's own bosses.
- Gang dashboard (`/gangs dashboard`) with overview, roster, ranks, applications, treasury, garage, territory, wars, leaderboard and a boss-only activity log.
- Staff admin panel (`/gangs admin`) to create, edit, fund and disband gangs, review requests, place bases, manage fleets, draw territories and force-end wars.
- Recruiting by nearby in-person invite (`/gangs accept`) or by written application (`/gangs apply`).
- Shared gang treasury with a full transaction history, rank-gated deposits and withdrawals, and optional delegation to a banking account.
- Gang bases with a member-only map blip, an inventory stash and a garage point with multiple vehicle spawn bays.
- Gang vehicle fleet that keeps mods, fuel and damage between sessions and can be returned by any member with garage rights.
- Territory system: 34 pre-drawn Los Santos districts, influence earned from drug sales, base use and presence, hourly anti-farm caps, and decay that scales with how much turf a gang holds.
- Contest windows measured in real hours, so turf never flips from one gunfight.
- Member-only turf map painted onto the minimap and the pause map in each owning gang's color.
- Gang wars with matched treasury stakes, validated kill scoring, king-of-the-hill capture points, ceasefires and a clock settle. Winner takes the pot.
- Server-wide gang leaderboard ranking wins, turf held and total influence.
- Optional police alerts for contested turf, takeovers, war starts and capture points.
- Optional private radio channel per gang when `oxide-radio` is installed.
- Optional Gang Intel tab in the `oxide-police` MDT showing gangs, turf and wars.
- Optional gang standing in `oxide-blackmarket` — selling on your own turf pays extra reputation.
- Territory economy modifiers that tune yield, growth, sale price and buyer density for the Oxide drug resources.
- Live settings editor (`/gangs settings`) — change nearly every setting in game, no restart and no file editing.
- Database tables are created automatically at boot; `sql/install.sql` ships for manual installs.
- English locale (`locales/en.json`) covering every notification, command, panel and menu.
