# Changelog

## [1.6.0] - 2026-10-26

### Added
- Works with Oxide HUD (free): players move the alert popups in `/hud`, which shows two sample alerts while none is up. The popups can't be switched off and don't hide with the HUD, so responders never miss a call. Moved to the lower half of the screen they stack upward; moved to the left half they slide in from the left. The popups and the Dispatch Hub take the server's accent colour on decorative parts only; priority, code and status colours never change (`Config.UseHudAccent` in `shared/config.lua`, default `true`). Needs `o-link` 1.12.1 or newer. Without Oxide HUD nothing changes.

### Fixed
- The resource no longer fails to start when a customized `shared/config/weather.lua` has a function inside the weather settings. The function is ignored and the server console names it.

### Documentation
- `docs/CONFIGURATION.md` and `docs/FEATURES.md`: Oxide HUD and `Config.UseHudAccent`.

## [1.5.0] - 2026-10-10

### Added
- AI dispatch is no longer tied to OpenAI. Each chained stage (speech-to-text, language model, text-to-speech) has its own `Provider` in `shared/config/ai_dispatch.lua`: `openai`, `groq`, `openrouter`, `deepinfra`, `lemonfox`, the self-hosted `ollama`, `lmstudio`, `kokoro` and `speaches`, or `custom` with your own `BaseUrl`. Keys go in `oxide:ai-dispatch:<provider>_key` convars (for example `oxide:ai-dispatch:groq_key`). Self-hosted providers need none. The language model must support tool calling.
- Realtime mode can run through another gateway: set `Realtime.Provider = 'custom'` and an `https://` `Realtime.BaseUrl`. Realtime always needs a key, a custom gateway included; without one the server console shows an error when the resource starts.
- New AI dispatch options: `LLM.Temperature`, `LLM.MaxTokens` (set either to `false` to leave it out of the request), `TTS.Format`, `Realtime.TranscriptionModel`, and per-channel `chainedVoice` and `realtimeVoice`.
- 911 and 311 calls from `oxide-phone` go through the same dispatch rules as `/911` and `/311`, including their rate limit. The departments the phone names for each line (`Config.Emergency` in `oxide-phone`) receive its calls too.
- The caller's phone number is saved on civilian alerts that are not anonymous and shown next to their name on the alert pop-up, the Dispatch Hub list and the alert's detail panel, when your server runs a phone resource.
- With a phone resource installed, responder replies arrive on the caller's phone from the department. Without one, they show on screen as before.
- With `oxide-weather`, alerts record the weather at the scene (conditions, temperature, wind) and show it on the alert. Turn it off with `Config.Weather.Context`.
- Optional storm alerts: severe weather from `oxide-weather` raises a `WEATHER` alert for police, EMS and fire, updates as the storm changes and closes when it ends. Off by default (`Config.Weather.Storms`).
- `oxide-dispatch:weather` shows and changes the weather settings from the server console, or in game for admins. Changes are saved and apply straight away; the file only sets the values for the first start.
- AI dispatch can answer "what's it about?": a new `get_alert_details` tool reads back an alert's message, street, caller and number, age, vehicle, weather at the scene and the units on it. It only sees alerts sent to that channel's department, and anonymous callers stay anonymous. `list_active_alerts` now gives each alert's message and age too.
- Optional spoken alert announcements: with `Config.AIDispatch.AnnounceAlerts = true`, the AI dispatcher reads every new alert out on the channels it is sent to. Off by default. In chained mode it costs one text-to-speech request per alert per channel with someone listening.
- With `oxide-weather`, the AI dispatcher knows the current weather where the officer is, so "what's the weather like?" gets a real answer. `Config.Weather.Context` turns it off.
- Two new 911 categories for fire: Brush / Wildfire (10-74) and Rescue / Extrication (10-76).
- The fire group recognises `lsfd` and `firefighter` as well as `fire`, and any department name you add to `Config.ResponderGroups.fire.jobs` is treated as fire everywhere, including the AI dispatcher's channel rules.
- 911 and 311 calls save the street and area the caller is in, from the phone, `/911`, `/311` and the radial menu alike. It shows on the alert pop-up, the Dispatch Hub and to the AI dispatcher. A call waits at most `Config.Civilian.locationTimeoutMs` (1 second) for it and goes through without a street otherwise.
- Map mode: `inherit`, `combined`, `separate` or `disabled`, set with `oxide-dispatch:mapmode` from the server console or by an admin in game. In `separate` mode the Hub has a Mainland / Cayo Perico picker.

### Changed
- The alert pop-up map, Hub map and heatmap now use `o-link`'s shared map, which includes Cayo Perico. This resource no longer ships its own map images.
- AI dispatch errors now name the stage that is missing a key (`missing_key_stt`, `missing_key_llm`, `missing_key_tts`) instead of `missing_openai_key`, and the startup log lists each stage's provider.
- The startup key check only looks at the modes your channels use, so a chained-only server is no longer warned about a missing realtime key.
- In chained mode, a request to a provider gives up after 20 seconds and is retried once, so a rate limit or an unreachable provider ends in an error instead of a long silence on the radio. Set them with `Chained.RequestTimeoutSec` and `Chained.MaxRetries`.
- OpenAI receives the reply length cap as `max_completion_tokens`, so OpenAI's newer models can run the dispatcher.
- The note an AI dispatcher gets for a new alert includes the street, and for a 911 or 311 call the caller, their number and what they said.
- The dispatcher keeps at most 30 background notes (the officer's location, other officers' radio traffic, alerts filed or cancelled), so a long shift on the channel no longer makes every request larger. Set it with `Chained.LLM.MaxSystemNotes`.

### Fixed
- Alerts on Cayo Perico now show on the dispatch maps.
- In chained mode, another officer talking while the dispatcher was running a lookup could make the dispatcher's next reply fail.
- An officer on an AI dispatch channel who placed a 911 call themselves never got that call into their dispatcher's awareness. Only the officer whose own AI dispatcher filed an alert is skipped now.
- Fire alerts reached firefighters who were off duty. With `Config.Broadcast.onDutyOnly`, the fire group now follows your framework's duty toggle (`frameworkDuty = true` in `Config.ResponderGroups`), and a firefighter who clocks in gets the open alerts straight away. If your fire job has no way to go on duty, set `frameworkDuty = false` on the fire group.
- `/911` and `/311` with a written description, and phone calls, always went to police. They now go to the departments the words point to: a fire to fire, a medical call to EMS, both when both are mentioned. Calls with no recognised words still go to police. The words are each category's new `keywords` in `Config.Civilian`.
- Alerts for police, EMS and fire together never reached fire, because the job cap cut the list at 12. `Config.Broadcast.maxJobsPerAlert` is now 32, and a trim is logged.

### Documentation
- `docs/AI_DISPATCH.md`: new "Choosing a provider", "Spoken alert announcements" and "Weather" sections, the realtime key rule, the `get_alert_details` tool, and what a rate limit looks like.
- `docs/CONFIGURATION.md`: the new AI settings, per-provider keys, `Config.Civilian.locationTimeoutMs`, `shared/config/weather.lua` and "Shared map mode".
- `docs/ADMIN.md`: the `oxide-dispatch:weather` and `oxide-dispatch:mapmode` commands.
- `docs/EXPORTS.md`: the `CivilianCall` export, `GetSetting` / `SetSetting` for the weather settings, and the caller number and weather on alerts.
- `docs/FEATURES.md`: phone calls, caller numbers, street names, replies on the phone, weather on alerts, alert details and announcements for the AI dispatcher.
- `docs/INSTALLATION.md`: the `o-link` versions this release needs, and updating an existing database.
- `docs/TROUBLESHOOTING.md`: the new AI error codes.

### Migration Guide
1. Back up your database.
2. Update `o-link` first. The maps need 1.8.0 or newer. Phone calls, caller numbers and replies on the phone need 1.11.0 or newer.
3. On first start, two columns are added to `dispatch_alerts` automatically (`caller_number` and `weather_context`). Your database user needs ALTER permission. To add them by hand instead, run `sql/migrate_caller_number.sql` and `sql/migrate_weather_context.sql` once. Both are safe to run more than once.
4. If you keep your own config files, also copy the new `shared/config/weather.lua`. Your current `shared/config/ai_dispatch.lua` keeps working as before. To get the fire-routing fixes, also bring over `maxJobsPerAlert = 32` in `shared/config.lua` (the old 12 still drops fire from alerts for police, EMS and fire), the `keywords` lists and the two new 911 categories in `shared/config/panic.lua`, and the fire group's new job names and `frameworkDuty = true` in `shared/config/jobs.lua`.
5. Restart the whole server, not only the resource. This version adds new files.

## [1.4.0] - 2026-07-15

### Added
- The AI radio dispatcher can now run in languages other than English. A new `Config.AIDispatch.Chained.STT.Language` setting in `shared/config/ai_dispatch.lua` tells the speech-to-text stage which language your officers speak over the radio — set it to a two-letter code like `'es'` (Spanish), `'fr'` (French), `'de'` (German), `'pt'` (Portuguese), or `'ja'` (Japanese); roughly 57 languages are supported. To make the dispatcher *reply* in your language, also translate the system prompt in `shared/config/ai_dispatch_prompt.lua` (or add an instruction like "Always respond in Spanish"). The reply voice needs no configuration — it speaks whatever language the AI's reply is written in. A full walkthrough lives in the new "Language support" section of `docs/AI_DISPATCH.md`.

### Notes
- Existing servers can update in place — no SQL changes required for the 1.4.0 release.
- `Config.AIDispatch.Chained.STT.Language` only applies to **chained** mode — Realtime mode detects the spoken language automatically and needs no setting.
- The new setting won't exist in your current `shared/config/ai_dispatch.lua`. Leaving it out keeps current behavior — the dispatcher listens for English exactly as before.

## [1.3.5] - 2026-07-07

### Changed
- The radial menu was consolidated into a single **Dispatch** wedge for everyone. Civilians see Call 911 / Call 311; responders additionally get a Set Status submenu, Set Callsign, Panic / Backup, and — when `Config.DispatchHub.RadialEnabled = true` — the Dispatch Hub, all in one place instead of three separate top-level menus. The Call 911 / Call 311 labels are now translatable in `locales/en.json`.
- The `radialId` field in `Config.ResponderGroups` (`shared/config/jobs.lua`) is no longer used — all groups share the single Dispatch menu. Configs that still contain it keep working; the field is simply ignored and can be deleted.

### Fixed
- Vehicle plates spoken to AI Dispatch are now cleaned up before use. Speech-to-text spells plates out with spaces ("6 2 j y n 8 0 5"), which could make plate lookups fail and voice-created vehicle BOLOs store unusable plates — plates now have spaces and dashes stripped and are uppercased, and blank plates are rejected.

### Notes
- Existing servers can update in place — no SQL changes required for the 1.3.5 release.

## [1.3.4] - 2026-07-03

### Fixed
- Dispatch map blips and panic alert cards now stay correct when a responder goes on duty, goes off duty, or switches department. Before this fix a responder's on-screen alerts could drift out of sync with what they were supposed to see — clocking in after an alert had already gone out wouldn't show that alert's blip, and going off duty or changing department could leave old blips and panic cards stuck on screen. Now, every time a responder's job or duty status changes, their alert display is rebuilt from scratch to match exactly the alerts they currently qualify for.

### Notes
- Existing servers can update in place — no SQL changes required for the 1.3.4 release.
- No configuration changes are required. This fix applies automatically to every responder group (police, EMS, fire, and any custom groups).

## [1.3.3] - 2026-06-29

### Fixed
- Panic alert cards could get stuck on a responder's screen and never go away. They now clear in three cases that previously left them pinned: when the responder logs out or switches characters, when the panic is closed but the responder had changed job/department or gone off duty since it fired, and — as a final safety net — automatically after a short grace period if the close signal is ever missed. The safety timer follows your `Config.AutoExpire` settings, and is turned off (cards stay until manually closed) when `Config.AutoExpire.enabled = false`.
- The responder callsign popup now uses the standard input dialog so it looks and behaves consistently across all supported frameworks.

### Changed
- The default AI dispatch police channel (`911`) now points at the `police` job out of the box (it previously used a placeholder job name), so the example config works on a fresh install without editing. This only affects new installs — your existing `shared/config/ai_dispatch.lua` is untouched.

### Notes
- Existing servers can update in place — no SQL changes required for the 1.3.3 release.
- Security reminder: the OpenAI key convar must use `set oxide:ai-dispatch:openai_key sk-...`, never `setr`. `setr` copies the value to every connecting player and exposes your key. If your `server.cfg` currently uses `setr` for this convar, change it to `set`.
- The unused `Config.Debug` line was removed from `shared/config.lua`. It did nothing, so no action is needed.

## [1.3.2] - 2026-05-28

### Fixed
- The auto-expire sweeper now actually runs again — a background-task issue was silently preventing it from executing, so resolved or timed-out alerts could pile up until the resource was restarted. Servers using `Config.AutoExpire.enabled = true` will once again see old alerts close on their own.

### Notes
- Existing servers can update in place — no SQL changes required for the 1.3.2 release.
- You'll now see a `sweeper started` info line in the server console on resource start (with the sweep interval and default TTL) confirming the sweeper is live. If you have `Config.AutoExpire.enabled = false`, a warning prints instead so it's obvious the sweeper was intentionally turned off.

## [1.3.1] - 2026-05-28

### Added
- Auto-detection can now be silenced for specific jobs — list any job name under `Config.Detection.JobBlacklist` and on-duty members of that job will no longer trip auto-alerts (service-weapon fire, melee, vehicle theft, etc). 911, panic, and manual MDT alerts are unaffected.

### Fixed
- The MDT Dispatch tab now keeps itself in sync with the live calls list — alerts that close or expire while you have the panel open will clear on their own within a few seconds, instead of sitting there until you reopen the tab.
- The police, EMS, and fire radial menus (and the civilian Player Interactions menu) now wait for the player to fully load before they appear. Fixes a startup error on QBX servers when joining during character selection.

### Notes
- Existing servers can update in place — no SQL changes required for the 1.3.1 release.
- `Config.Detection.JobBlacklist` is a new option and won't exist in your current `shared/config/detection.lua` — copy the block from the updated file (sits next to `WeaponBlacklist` / `ZoneBlacklist`) into your existing config, then uncomment whichever job names match your server's setup. Leaving it out (or empty) keeps current behavior — no jobs are filtered.

## [1.3.0] - 2026-05-27

### Added
- AI dispatch MDT tools now also work with **wasabi_mdt** and **lb-tablet** — auto-detected when installed (joining the existing `oxide-police` and `ps-mdt` support).
- The AI dispatcher now speaks a quick acknowledgement ("Running that plate", "Pulling those records") the instant it starts a lookup, so there's no dead air on the radio while it works. Phrases are fully customizable in `shared/config/ai_dispatch_acks.lua`.
- AI dispatcher audio now sounds like it's coming over a real comms radio — radio filter, light static, and mic-open / squelch effects around each transmission. Tune it (or turn it off) under `Config.AIDispatch.RadioFX`.
- Per-detector cooldowns — give each auto-detection (shooting, drive-by, melee, explosion, vehicle theft) its own cooldown via `Config.Detection.cooldownsMs`, instead of one shared timer for all of them.
- Detection chance — every detector now accepts a `chance` value (`0.0`–`1.0`) controlling how often a qualifying event actually creates an alert, so auto-detection can feel less constant.
- Server-side duplicate suppression — repeated identical auto-detect alerts from the same person within a short window are now dropped automatically, even if a cheating client bypasses the normal cooldown. Configurable via `Config.Detection.ServerDedup`.

### Changed
- The AI dispatcher now sounds much more like a real human dispatcher and less like a chatbot — assistant-style phrasing ("I'll go ahead and…", "Is there anything else?", etc.) has been removed.
- Melee auto-detection is more accurate — it no longer fires on air punches or missed swings; an actual hit on a target is now required.

### Notes
- Existing servers can update in place — no SQL changes required for the 1.3.0 release.
- All new settings ship enabled with sensible defaults — no action is needed unless you want to customize them.

## [1.2.0] - 2026-05-20

### Added
- AI dispatch MDT tools now also work with [ps-mdt](https://github.com/Project-Sloth/ps-mdt) — auto-detected when installed.
- Bring-your-own MDT support: drop a small bridge file in `server/modules/ai_dispatch/mdt/` to wire any other MDT into the AI dispatch tools. See [`docs/MDT_BRIDGE_DEV.md`](docs/MDT_BRIDGE_DEV.md).
- New config block `Config.AIDispatch.Integrations.Mdt` — list of MDT backends in priority order.

### Changed
- `Config.AIDispatch.Integrations.OxidePolice` replaced by `Integrations.Mdt`. `oxide-police` is enabled by default in the new list, so no action is needed unless you customized the old flag.

## [1.1.0] - 2026-05-18

### Added
- Optional AI Radio Dispatcher — assign one OpenAI-backed dispatcher per department radio channel; officers press PTT and the AI listens, replies verbally, and files dispatch alerts on their behalf. Off by default; switches on once you set `set oxide:ai-dispatch:openai_key sk-...` in `server.cfg`.
- Two AI provider modes selectable per channel: Realtime (sub-second voice, premium pricing) and Chained (~2s voice, much lower per-transmission cost).
- AI MDT toolset on police channels when `oxide-police` is installed — plate lookup, person lookup, dossier, BOLO create/list/clear, custody check, on-duty officer list.
- Per-channel and global identifier whitelist for restricting AI dispatch access on top of normal radio access.
- New EMS dispatch codes: `10-53` Person Down, `10-54` Cardiac Arrest, `10-56` Trauma / Injury, `10-58` Overdose.
- New fire / hazmat dispatch codes: `10-73` Vehicle Fire, `10-74` Brush / Wildfire, `10-75` Hazmat Incident, `10-76` Rescue / Extrication.
- Compatibility shims for legacy EMS alert flows on QBCore, QBX, and ESX so existing ambulance scripts now reach Oxide Dispatch automatically.
- New documentation file [`docs/AI_DISPATCH.md`](docs/AI_DISPATCH.md) covering AI setup, cost estimates, voice options, and the privacy notice you must show to players.

### Changed
- Renamed `10-70` from "Fire" to "Structure Fire" to match the new fire code family.
- AI dispatch channel defaults: ships with `911 = LSPD`, `921 = EMS`, `931 = Fire` enabled. BCSO / SASP entries shipped commented out.

### Notes
- Existing servers can update in place — no SQL changes required for the 1.1.0 release.
- If you used the old `10-70` "Fire" code in custom scripts, the code itself is unchanged; only the catalog title is now "Structure Fire".

## [1.0.0] - 2026-05-08

### Added
- Initial release of `oxide-dispatch`.
- Real-time job-targeted alert broadcast with optional on-duty filter (defers to `oxide-police` for LEO duty when present).
- MySQL persistence (`dispatch_alerts`, `dispatch_responders`) and rehydration of active alerts on resource start.
- Auto-expiry sweeper (`Config.AutoExpire`) that marks unresolved alerts as `expired` after a configurable TTL.
- 22-code dispatch catalog in `shared/config/alerts.lua` (10-11 Traffic Stop through 10-99 Narcotics, plus `panic`, `911`, `311`).
- Civilian 911/311 system with anonymous-by-default broadcasting, configurable category lists, radial menu, and `/911` / `/311` chat commands.
- Two-way civilian replies via `/replycaller <id> <message>` and `Civilian.SendReply` with caller-side toast.
- Panic / responder-needs-backup subsystem (`Config.Panic`) with `F4` keybind, `/panic` command, off-duty broadcast bypass, auto-waypoint on receive, and per-group title/message strings.
- Automatic crime detection (shooting, drive-by, melee, explosion, death, optional vehicle theft) with weapon hash blacklist and geographic zone blacklist (`Config.Detection`).
- Per-group responder statuses (`Config.ResponderStatuses`) — distinct status lists for police (Code-4, 10-7, etc.), EMS (En Route, On Scene, Transporting), fire (Returning), and a `_default` list for custom groups.
- Callsign management — radial Set Callsign action, `/callsign <value>` command, persisted to character metadata, surfaced on alerts and replies.
- Dispatch Hub UI — full-screen overview with active alerts list, map view, and optional 30-day heatmap. Master-switched via `Config.DispatchHub.RadialEnabled`.
- Heatmap subsystem (`Config.Heatmap`) — cached point density across 24h / 7d / 30d / all-time windows, capped at `maxPoints`.
- Per-responder-group radial submenu (Officer / Paramedic / Firefighter) with status toggles, Set Callsign, and Panic items.
- Accept-latest-alert keybind (`G`) — drops a GPS waypoint on the most recent alert and attaches the player as `responding`.
- Server exports: `CreateAlert`, `GetActiveAlerts`, `GetAlert`, `RespondToAlert`, `StopResponding`, `UpdateResponderStatus`, `CloseAlert`, `SetCallsign`, `SetOfficerStatus`, `GetOfficerStatus`, `TriggerPanic`.
- `o-link` `dispatch` namespace adapter (server + client) registered in `o-link/modules/dispatch/oxide-dispatch/`.
- Legacy compatibility shims:
  - `qb-core` — listens for `police:server:policeAlert` (server) and optionally `police:client:policeAlert` (client) with keyword-to-code resolution and configurable rate limit.
  - `es_extended` — listens for `esx_policejob:alert`, `esx_policejob:policeAlert`, `esx_outlawalert:policeNotify`, `esx_outlawalert:outlawNotify`, and `esx_phone:send` (police number).
- Admin commands: `/testdispatch [code] [jobGroup]` and `/testdispatchmany [count] [jobGroup] [durationSec]` (both ACE-gated as `command.testdispatch*`).
- Optional `oxide-police` integration adapter (`PoliceLookup`) — publishes per-player `oxide-dispatch:isLEO` (boolean) and `oxide-dispatch:responderGroup` (string|nil) statebags, reads `oxide-police`'s `police_departments` table for dynamic LEO job discovery, periodic refresh + per-player resync on `playerReady` / `jobChanged`. Falls back cleanly to `Config.ResponderGroups.police.jobs` when `oxide-police` is missing.
- English locale (`locales/en.json`) covering popup, hub, civilian, responder, and panic strings.
