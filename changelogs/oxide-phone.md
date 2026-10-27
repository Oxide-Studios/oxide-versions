# Changelog

## [1.1.1] - 2026-10-26

### Fixed
- Players using a controller now hear the notification sound. The controller buzz just before it raised a script error that stopped the alert.
- A settings reset that fails in `/phone settings` now shows why instead of doing nothing, and the setting keeps its value.
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.

## [1.1.0] - 2026-10-20

### Added
- **Clear All** on the lock screen and in the Notifications list: click the **X** beside **Notifications**, then **Clear**.
- Lock screen stacks open into a list under the app's name, with **Show less** and an **X** that clears that app's notifications. A closed stack shows the newest notification, the edges of the ones behind it and how many more there are.
- The passcode keypad checks the passcode as soon as the last digit is in, with no arrow or Enter to press. A wrong passcode shakes the dots, says so and clears for another try.
- The Gigs app lists Pest Control (`oxide-pestcontroljob`) while it runs on your server, and its shift updates arrive as Gigs notifications.
- With Oxide HUD (and `o-link` 1.12.1 or newer), the card that rises while the phone is put away is a HUD panel: players move it or switch it off in `/hud`, and it hides with the HUD. While the camera is up, other resources' HUD panels hide too.

### Changed
- The lock screen, the Notifications list, banners and app badges all show the same notifications, so clearing one anywhere clears it everywhere.
- An app's badge is the number of its waiting notifications. Clearing them, or reading what they are about, removes it. Messages, Mail and Phone no longer count unread items. Apps from other resources can still show their own number.
- A locked phone lists every waiting notification instead of only the newest ten, and a notification that arrives while it is locked shows there straight away.
- Notifications from apps a player turned off no longer show on the lock screen.

### Fixed
- Notifications in an opened lock screen stack no longer overlap. The list scrolls in its own space between the widgets and the buttons at the bottom.
- App badges that stayed after their notifications were cleared.

### Documentation
- `docs/FEATURES.md` covers the new lock screen, Clear All, badges, the passcode keypad and a new "With Oxide HUD" section. `docs/CONFIGURATION.md` notes that the put-away card keeps the phone's own colours with Oxide HUD. `docs/FEATURES.md`, `docs/INSTALLATION.md` and `docs/INTEGRATIONS.md` list Pest Control among the gig jobs.

### Migration Guide
- The phone adds a new column to `oxide_phone_devices` itself when it starts. If your database user can't alter tables, run `sql/migrations/0016_pin_length.sql` once before starting this version.
- A passcode set before this update needs the arrow (or Enter) once. After it is entered correctly, the keypad checks it on its own.

## [1.0.2] - 2026-10-10

### Fixed
- Changing the phone camera's mode no longer sends the EMS distress signal. Camera mode was on **G**, the distress key in `oxide-ems`; it is now on **F7** for every player, including those who already joined your server. Players who had moved camera mode to another key need to set it again in FiveM's key bindings.

### Added
- `Config.Camera.modeKey` (default `'F7'`) sets the starting key for camera mode. Change it in `/phone settings` → Device → Media → Camera, then restart the phone. Players can still rebind it in FiveM's key bindings.

### Documentation
- `docs/FEATURES.md`, `docs/ADMIN.md` and `docs/CONFIGURATION.md` show F7 for camera mode and the new `Config.Camera.modeKey`.

## [1.0.1] - 2026-10-05

### Fixed
- The Phone Wiper icon (`items/phone_wiper.png`) was flat clip art that did not match the other item icons. It is now a photoreal icon in the same style.

### Added
- An icon for the `phone` item (`items/phone.png`), showing the bundled phone. Until now the phone used your inventory's stock phone picture.

### Documentation
- `INSTALLATION.md`, `ASSETS.md` and `items/ox_inventory_items.lua` list all five item icons.

To update: copy `phone.png` and `phone_wiper.png` from `items/` into your inventory's image folder (`ox_inventory/web/images/` or `qb-inventory/html/images/`), replacing the old files.

## [1.0.0] - 2026-10-03

### Added
- First release of `oxide-phone`. Three apps come with it, each its own resource with its own changelog: Hookr (`oxide-hookr`), Leakd (`oxide-leakd`) and Glitch (`oxide-glitch`). The apps are optional.
- Runs on QBCore, QBX and ESX through `o-link` (1.11.0 or newer). Needs `ox_lib`, `oxmysql` and OneSync; `pma-voice` for calls.
- Phones are inventory items, each its own device: contacts, texts, mail, photos and the home screen stay with the phone when it changes hands.
- Players can carry several phones: a call rings only the phone that holds the number, and texts, mail and app notifications reach only the phone they are for.
- SIM cards carry the number: registered SIMs, free with a new phone by default, and burner SIMs that belong to nobody.
- Lost and stolen SIMs: the registered owner reports a number from any phone and gets a replacement with the same number, free by default (`Config.Sim.replacementCost`).
- Find Phone in Settings: locate, lock with a message on the lock screen (Lost Lock) or erase your phones from any phone you use.
- Passcode lock with a lockout after wrong guesses, and an optional battery with Low Power that charges in a vehicle, with a `powerbank` item or at charging stations.
- No-signal areas you draw, optional no signal indoors, and Airplane Mode (emergency calls still go through).
- The phone's own model in the hand in four colours.
- Charging stations you place in game from `/phone settings`: phones charge while players stand nearby, or on one of three pads where the phone lies in its colour until anyone (or only its owner) takes it. Stations built with your own map tools can be added too.
- Calls through `pma-voice`: ringing heard nearby, recents, favourites, keypad, hold, call waiting, conference calls of up to five, voicemail with greetings, blocked numbers and hidden caller ID.
- Messages: direct and group texts with photos, videos, voice messages, places and live locations, polls, contacts and drawings, plus reactions, replies, edits, unsend, read receipts and typing.
- Texts and mail to a phone that is off or out of signal wait and arrive later.
- Mail: an inbox per phone with addresses and aliases, attachments, drafts, scheduled sending and undo. Other resources can mail players, even offline ones.
- Camera with photo, video, time-lapse and portrait modes, filters, zoom, flash, timer and the selfie camera; players can walk with the camera up.
- Photos: library, albums, shared albums, a map of places, a Hidden album behind the passcode, Recently Deleted, an editor and video trimming.
- Photo and video storage through Fivemanage, S3-compatible storage (such as Cloudflare R2) or your own service, picked in `/phone settings` with its key in `server.cfg`.
- Notes, Calculator (with a scientific mode), Clock (alarms on the game clock, stopwatch and timer) and Maps (search, saved places, parked car and turn-by-turn directions).
- Wallet: send and request money between phone numbers through your framework's bank, with payment cards in Messages, per-transfer and daily limits, and refunds when a transfer can't arrive.
- Store: players get apps with a short simulated download (`Config.Store.simulateInstalls`), and you can preinstall any app for everyone (`Config.Store.preinstalled`).
- Dark Chat: anonymous chat rooms by handle, with photos.
- Feed: public posts with photos or a video, hashtags, mentions, replies, quotes, reposts, likes, bookmarks, follows, trending hashtags, a verified badge (`/phone feedverify`), player reports and admin moderation.
- Gigs app for the Oxide gig jobs: what to do now, pay so far, and start locations on a map with Set GPS and Show on Maps; shift updates arrive as notifications.
- Home screen with pages, a dock, folders, widgets and widget stacks, All Apps, search, five wallpapers plus your own images, Quick Controls, an app switcher and modes (Do Not Disturb, Sleep, Driving, Work).
- Wallpapers from image links: players paste an https image link for the home screen, the lock screen or both, from sites you approve (`Config.Home.wallpaperLinks`).
- Clock widget in six styles (City, Analog, Digital, Float, Solar and World Clock), small or medium, picked per widget in edit mode.
- Notifications with banners, a notification list, badges, eight ringtones and six text tones, and the phone rising from the pocket while put away.
- Sharing Nearby: photos, contacts, notes, places and Feed posts to a player standing next to you.
- 911 and 311 from the Phone app or the lock screen, to your dispatch resource or to the jobs you choose (`Config.Emergency`).
- Police and evidence: seize, lodge, return or destroy phones with a custody log, search authorizations, extraction reports that include recently deleted content, carrier records and traces, all in the Forensics app.
- Extraction reports attach to an `oxide-police` case when it runs, or go to your own MDT through an export.
- Counterplay: Erase All Content, and a `phone_wiper` item that removes content for good.
- Other resources can send mail, texts and notifications, and put their own apps and widgets on the phone with its accounts, photos, camera, location, links, live streams and evidence (`docs/BUILDING-APPS.md`).
- Stock QBCore scripts that send qb-phone mail keep working, and an optional layer answers common LB-Phone exports.
- Admin commands under `/phone`: look up a phone, remove a passcode or an app password, set a battery level, report a SIM lost or give a replacement, and list or end live streams.
- Live settings editor (`/phone settings`) for nearly every option, saved in the database.
- Database tables are created automatically at first start; `sql/install.sql` and `sql/migrations/` ship for manual installs.
- Optional integrations with `oxide-police`, `oxide-dispatch`, `oxide-drugselling`, `oxide-banking`, `oxide-tablet` and the Oxide gig jobs.
- English locale (`locales/en.json`).
