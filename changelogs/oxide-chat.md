# Changelog

## [2.1.0] - 2026-10-26

### Added
- Works with Oxide HUD (free): players move the chat box in `/hud` instead of `/chat customize`, starting from the spot they already had, and the chat box takes the server's accent colour (`Config.UseHudAccent` in `config/main.lua`, default `true`); message colours keep their own. Needs `o-link` 1.12.1 or newer. Without Oxide HUD nothing changes

### Changed
- Attempts to open or change `/chat settings` without admin rights are now written to the log

### Fixed
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it
- `/chat settings` now says why a change was not saved, for example "settings are still loading, try again shortly" or "could not be saved, see the server console", instead of "value rejected". The old value stays, also when the database fails to save the change, and a failed Reset to default now shows an error
- The `SetSetting` export now returns `false` when a change is refused or the database fails to save it, instead of always `true`. It refuses keys `/chat settings` doesn't list, and changes made before the settings finish loading at startup, which used to be lost
- Saved settings can no longer be replaced by the config file's defaults when the database returns no rows at startup
- Decimal settings in `/chat settings` no longer show rounding noise such as 0.90000000000009

### Documentation
- `docs/EXPORTS.md`: when `SetSetting` returns `false`, and that the server console names the reason
- `docs/ADMIN.md`: the EMS job radio is `/medic`
- `docs/CONFIGURATION.md` and `docs/FEATURES.md`: chat position with and without Oxide HUD, and `Config.UseHudAccent`

## [2.0.0] - 2026-08-27

### Added
- Custom chat channels: build your own slash-command channels (job radios, staff chat, gang chat) with per-channel styling, aliases, scope (members / global / broadcast / proximity), job or admin restriction, minimum grade, on-duty requirement, and cooldown — all in `Config.ChatChannels` or live in-game
- In-game server settings editor (`/chat settings`, admin only): configuration is now edited live and saved to the database, no file edits or restarts needed after setup
- Friends & block list: `/friend add [id]` / `/friend remove [id]` plus a friends panel in the chat UI with requests, online status, and blocking; blocking stops private messages in both directions
- Inline images & GIFs: pasted image links embed in any message type (allowed hosts set via `Config.ImageHosts`), plus a Discord-style GIF picker powered by Giphy (add `set oxide:chat:giphy_key your-key` to server.cfg); images have their own cooldown and optional job/admin restriction
- @mentions: type `@` to autocomplete online player names; mentions are highlighted, including your own name when someone mentions you
- Chat category tabs above the message list (All / Local / Global / Job / PMs by default), fully configurable via `Config.Categories`; players can hide the tab bar
- Automatic announcements: a rotating list of server-wide broadcasts on a timer (`Config.AutoBroadcasts`), in order or random, skipped while the server is empty, toggled live with `/chat autobroadcast on|off`
- Announcements support an optional heading with `Title | Body` syntax
- 10 chat style presets (Bubbles, Discord, Timeline, Masonry, Compact, Cards, Float, Panel, Retro, Glass) plus new appearance options: chat height, 12/24-hour timestamps, message density, and a private-message color
- Integration API for other resources: read or change any chat setting at runtime (`GetSetting` / `SetSetting`) and query or manage the friends & block list from your own scripts — see `docs/EXPORTS.md`

### Changed
- **BREAKING**: new required dependencies `o-link` and `oxmysql` — update your server.cfg start order (see docs/INSTALLATION.md)
- **BREAKING**: `Config.ChatTypes` was replaced by `Config.ChatChannels`, one list that styles the built-in message types and defines custom channels; the default job radios (`/lspd`, `/medic`, `/mechanic`) are now ordinary channel rows you can edit, remove, or copy
- Admin commands consolidated under `/chat`: `/announce` → `/chat announce`, `/clearchat` → `/chat clear`, `/testchat` → `/chat test`; player customization opens with `/chat customize`
- Admin commands now use the standard framework admin check, so stock QBCore/QBX/ESX admin setups work without extra ACE configuration
- Chat position and size are now per-player settings (the fixed `Config.UI` position/width/height options were removed)

### Fixed
- Sending while on cooldown now tells you how long to wait instead of silently dropping the message
- Command suggestions correctly mark optional vs required arguments and refresh reliably when other resources start

### Migration Guide
1. In server.cfg, make sure `oxmysql` and `o-link` start before `oxide-chat`.
2. Database tables are created automatically on first start. To import them manually instead, run `sql/install.sql` (fresh install) or `sql/migrations/0001_friends_and_blocks.sql` + `sql/migrations/0002_settings.sql` (existing install).
3. If you had customized `Config.ChatTypes`, re-apply those styles as rows in `Config.ChatChannels` — or just edit them in-game via `/chat settings`.
4. After the first start, settings live in the database; change them with `/chat settings` in-game. `config/main.lua` only supplies defaults for keys the database doesn't have yet.
5. Players who used a removed style preset are automatically switched to the new Bubbles preset.

## [1.0.1] - 2026-01-21

### Fixed
- Chat no longer disappears when idle in settings modal (removed 5-second preview timeout)
- Keybinds now work when chat input loses focus:
  - ESC closes chat regardless of input focus state
  - T refocuses the input when clicked away

### Changed
- Emoji picker now follows the "Command Suggestions" position setting (above/below input)
- Split "Background Opacity" into two separate settings:
  - "Messages Background" - controls chat container/messages area opacity
  - "Input Background" - controls input box, suggestions, and emoji picker opacity
- Removed "Blur Intensity" slider (blur now fixed at 12px)

## [1.0.0] - 2026-03-23

- Modern glassmorphism chat UI
- Proximity-based roleplay messaging (/me, /do, /whisper, /shout)
- Job-specific radio channels
- Private messaging system
- Customizable settings (position, opacity, colors, density)
- Command autocomplete with suggestions
- Emoji picker
- FiveM chat API compatibility layer
