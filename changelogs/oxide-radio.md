# Changelog

## [1.2.0] - 2026-10-26

### Added
- Works with Oxide HUD (free): players move the channel roster in `/hud`, where it replaces the Reposition button, or switch it off there. The roster's channel light and talking highlight take the server's accent colour (`Config.UseHudAccent` in `shared/config.lua`, default `true`), and the roster stays up while the player is downed, since they keep hearing their channel. Needs `o-link` 1.12.1 or newer. Without Oxide HUD nothing changes.
- `itemimages/ox_inventory_items.lua` holds a paste-ready `radio` entry for ox_inventory.

### Changed
- The Admin badge in `/radio settings` can now be translated in `locales/en.json`.
- Attempts to open or change `/radio settings` without admin rights are now written to the log.

### Fixed
- The resource no longer fails to start when a customized config has a function inside a saved setting. The function is ignored and the server console names it.
- `/radio settings` now says why a change was not saved, for example "settings are still loading, try again shortly" or "could not be saved, see the server console", instead of "value rejected". The old value stays, also when the database fails to save the change, and a failed Reset to default now shows an error.
- The `SetSetting` export now returns `false` when a change is refused or the database fails to save it. It refuses a key `/radio settings` doesn't list, and any change made before the settings finish loading at startup. Before, those changes looked saved but were lost.
- Saved settings can no longer be replaced by the config file's defaults when the database returns no rows at startup.
- Decimal settings in `/radio settings` no longer show rounding noise such as 0.90000000000009.

### Documentation
- `docs/INSTALLATION.md`: the ox_inventory step uses `itemimages/ox_inventory_items.lua`.
- `README.md`, `docs/CONFIGURATION.md` and `docs/FEATURES.md`: EMS and Fire register their own channels; Police channels are set up in `/radio settings` → Channels.
- `docs/ADMIN.md`, `docs/CONFIGURATION.md`, `docs/EXPORTS.md` and `docs/FEATURES.md`: gang access to restricted channels.

## [1.1.0] - 2026-09-02

### Added
- **Gang radio channels** — restricted frequencies can now be reserved for gangs as well as jobs. Every entry in the Restricted channels list gains a **Gangs** field alongside **Jobs**; a player gets in through a listed job **or** a listed gang. Gang names are matched without case sensitivity, unlike job names.
- `Min rank` gates by gang rank when access came through a gang, and `Duty only` never applies to gang access (gangs have no duty concept).
- Players are auto-tuned to a gang default channel when they join a gang, the same way jobs auto-tune at clock-on.
- The channel browser and one-tap channel list show gang channels to gang members only, so nobody sees a rival's traffic.

### Fixed
- Auto-tune at clock-on gave up entirely if the lowest default channel refused the join (for example a duty-only channel while off duty). It now tries each default channel you qualify for, lowest first.
- Being blocked from a restricted channel reported "restricted" even when the real reason was your rank or being off duty. The message now names the actual reason.

## [1.0.1] - 2026-08-24

### Added
- The `radio` item now ships with a ready-made inventory icon (`itemimages/radio.png`) — copy it into your inventory's image folder if your `radio` item doesn't already have one

## [1.0.0] - 2026-08-20

### Added
- One handheld radio for the whole server, built on top of `pma-voice`: a single `radio` item (`Config.Item`, `Config.RequireItem`) opens the radio for every player, with access to restricted channels decided by job rather than by a special item
- Open (civilian) frequencies: any whole number in the `Config.MinFrequency`–`Config.MaxFrequency` range (1–9999 by default) is tunable by anyone holding a radio
- Restricted job channels (`Config.JobChannels`): frequencies reserved for specific jobs, gated by case-sensitive job name, minimum grade (`minRank`), and an optional on-duty requirement (`dutyOnly`); restricted channels may sit on any frequency, not just the open dial range
- Runtime channel registration for other resources through the `RegisterChannel` / `UnregisterChannel` exports (used by `oxide-police` and `oxide-ems`): owner-scoped, shadow any config channel on the same frequency, and released automatically when the owning resource stops
- Server-authoritative access control, enforced at the `pma-voice` layer so a modded client cannot force itself onto a restricted or encrypted channel; access is re-checked on job change, duty change, and on a periodic sweep (`Config.RevalidateInterval`), removing players who lose access
- Channel encryption: any open frequency can be password-locked by a player tuned to it; passwords are 6–32 characters, stored salted and hashed server-side, and never sent to other clients. Five wrong attempts triggers a five-minute lockout tracked per character that survives reconnect. The lock creator or a server admin can change or remove the lock
- TeamSpeak-style channel browser inside the panel showing every active channel and its members, scoped to the viewer — restricted channels reveal members only to qualifying jobs, encrypted channels hide members until unlocked
- On-screen roster overlay of who is on your current channel, with Discord-style live speaking indicators, badge tags, and a downed indicator; toggled with a rebindable keybind (`Config.OverlayKey`, default `U`), shown only when `Config.ShowRoster` is on, and repositionable from the panel
- Per-character radio display names (stored in `radio_prefs`), shown on rosters in place of the character name
- Per-client radio preferences: mic-click sound (Default / On / Off, driving `pma-voice`'s own setting), starred favorite frequencies, and remembered encryption passwords
- Roster tags for integrators via the `SetMemberTag` export (`oxide-police` feeds officer badge numbers) and a downed flag via the `SetRadioDead` export or automatically through `o-link` death events — no hard dependency on any death or police resource
- Automatic integration with `oxide-police` / `oxide-ems`: when installed, their department channels register themselves and officers auto-join their default channel at clock-on with their badge on the roster
- Admin commands under `/radio`: `/radio settings` opens the live settings menu, `/radio unlock <frequency>` force-removes encryption from a frequency
- Live database-backed configuration through `/radio settings` — after the first start, settings are edited in-game with no file edits or restarts; the transmit and hold animations remain config-only in `shared/config.lua`
- Server exports for integrators: `RegisterChannel`, `UnregisterChannel`, `GetChannels`, `GetPlayerChannel`, `JoinChannel`, `LeaveChannel`, `SetRadioDead`, `SetMemberTag`, `GetSetting`, `SetSetting`
- Framework-agnostic through `o-link` (QBCore / QBX / ESX / Oxide Core) and localizable via `locales/*.json`
- Graceful behavior without `pma-voice`: the radio UI still works, but no voice is transmitted and players see a "voice unavailable" banner; voice availability also respects the `voice_enableRadios` convar
