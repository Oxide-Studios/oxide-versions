# Changelog

## [1.2.0] - 2026-10-26

### Added
- Works with Oxide HUD (free): players move the notification bubbles in `/hud` or switch them off there, and the tablet's own controls (the status bar's bell and count, the home screen's edit buttons and panels) take the server's accent colour (`Config.UseHudAccent` in `shared/config.lua`, default `true`). Apps keep their own colours. Needs `o-link` 1.12.1 or newer. Without Oxide HUD nothing changes.

### Fixed
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.

### Documentation
- `docs/CONFIGURATION.md` and `docs/FEATURES.md`: Oxide HUD and `Config.UseHudAccent`.

## [1.1.0] - 2026-10-04

### Added
- Apps can open other apps on the tablet. The player still needs the other app installed and allowed for them.
- The tablet also accepts the newer app message format used by `oxide-phone`, so an app built for both runs on either. Existing apps work as before.
- `itemimages/ox_inventory_items.lua` holds a paste-ready `tablet` entry for ox_inventory.

### Changed
- Every button and tooltip in `/tablet settings` can now be translated in `locales/en.json`.
- Attempts to open or change `/tablet settings` without admin rights are now written to the log.

### Fixed
- A `/tablet settings` change the database fails to save is now reported as failed and keeps its old value, instead of applying until the next restart.
- When a `/tablet settings` change or reset is refused, the menu now says why instead of always saying settings are still loading, and a failed reset shows a message.
- Pressing ESC to cancel an in-world placement no longer closes `/tablet settings` as well.
- `/tablet settings` now closes when your character logs out.
- Reports of game models that fail to load are now capped per player and in total, so one player cannot flood admins with warnings.

### Documentation
- `docs/INSTALLATION.md`: the ox_inventory steps now use `itemimages/ox_inventory_items.lua`.

## [1.0.0] - 2026-09-20

- Initial public release of `oxide-tablet`.
- A tablet players carry in their inventory, opened with the item, a keybind or `/tablet open`; the item requirement can be turned off.
- Other resources put their screens on the tablet as apps, and those apps appear on their own once both resources are running.
- Ships with its own App Store, Settings and Maps apps.
- Every tablet is its own device: a 15-digit IMEI saved on the item, keeping its name, home screen, apps and settings when it changes hands.
- Battery that drains while the tablet is out and recharges while it is put away, at rates you set; a flat tablet will not open until it has charged.
- Held tablet prop and animation other players can see, and the tablet puts itself away on death, on going down, or when the item leaves the player's inventory.
- More than one item can open the tablet, and an item can be locked to a fixed list of apps for a work-issued device.
- Home screen with up to 5 pages and a dock of up to 6 apps, drag-and-drop editing, an App Library for hidden apps, and per-tablet renaming.
- Calendar and ID widgets in small and medium sizes, plus widgets other resources add in small, medium and large.
- Eight built-in wallpapers, plus your own images placed in the `wallpapers/` folder.
- App Store where players install the apps they qualify for, with a preinstall list, or turned off so every available app installs itself.
- Apps can be limited to certain jobs, gangs or on-duty players by the resource that adds them.
- Maps app with places you add, the blips players already have on their map, name and icon overrides, search, GPS waypoints and a drawn route.
- Settings app for players: Do Not Disturb, banners, sound, volume, per-app mutes, larger text, bold text, reduced motion, renaming and resets.
- Notifications with banners, a tray under the status bar, app badge counts and a notification sound you can replace.
- Notification bubbles at the corner of the screen while the tablet is put away.
- Live settings editor (`/tablet settings`) that changes settings in game and saves them to the database, most applying with no restart.
- Admin commands to factory-reset a tablet by IMEI (`/tablet wipe`) and send test notifications (`/tablet notify`), plus `/tablet device` and `/tablet resethome` for players.
- Layouts, installed apps and player settings are saved with the tablet, or with the character when no tablet item is involved, and survive a restart.
- Database tables are created automatically at first start; `sql/install.sql` ships for manual installs.
- Runs on QBCore, QBX and ESX through `o-link` 1.8.0 or newer, with the `tablet` bridge namespace plus 16 client and 10 server exports for other scripts.
- Developer guide for building apps and widgets, including a host shim that puts an existing NUI page on the tablet.
- English locale (`locales/en.json`) covering every notification, command and screen.
