# Changelog

All notable changes to oxide-carplayer will be documented in this file.

## [2.0.0] - 2026-09-29

### Added
- Per-server map mode: `oxide-carplayer:mapmode inherit|combined|separate|disabled`
- Paste-ready ox_inventory item entry in `itemimages/ox_inventory_items.lua`

### Changed
- Renamed to Car Player. All in-game text, keybind labels and the jailbreak USB icon use the new name
- The open command is now `/carplayer` (was `/carplay`)
- Mini-player keybind commands are now `carplayer_mp_pause`, `carplayer_mp_next` and `carplayer_mp_repeat`
- The map now uses the shared o-link map, with Cayo Perico support
- Requires o-link 1.8.0 or newer

### Migration Guide
- Players' custom Car Player keybinds reset to the defaults once
- Playlists and settings saved by the old `oxide-carplay` resource do not carry over, because FiveM stores them per resource name
- Remove the old `oxide-carplay` folder and its `ensure` line before starting `oxide-carplayer`

---

## [1.2.0] - 2026-07-06

### Changed
- Migrated from `community_bridge` to `o-link` for framework abstraction
- Updated dependency from `community_bridge` to `o-link` in fxmanifest
- Progress bar disable options now use `o-link` naming (`move`, `car`, `combat`)

### Improved
- Added early distance culling in the audio position thread — vehicles beyond hearing range are silenced immediately, skipping expensive per-frame audio calculations
- Hoisted player ped, coordinates, and vehicle lookups outside the per-vehicle loop to avoid redundant native calls each tick

---

## [1.1.0]

### Added
- New Settings tab in the Car Player UI with per-character persistence via KVP
- Theme color customization with 12 preset colors and a custom hue/saturation picker
- Mini player enable/disable toggle — players can hide the floating HUD entirely
- Mini player position selector (top-left, top-right, bottom-left, bottom-right, bottom-center)
- Default volume preference — applied when entering a vehicle with no active playback
- Album art vinyl spin toggle

### Changed
- Theme colors now use CSS custom properties — all UI elements update instantly when the accent color changes
- Server-side settings validation with hex color, position, volume, and boolean sanitization

---

## [1.0.0]

### Features
- In-vehicle YouTube music player with metadata fetching (noembed.com, Piped API, Invidious fallback)
- 3D positional audio via xsound with vehicle openness detection and closed-window muffling
- Queue system with up to 20 tracks, auto-advance on song end, add/remove/play controls
- Playlist system with save/load/delete, up to 10 playlists x 50 tracks, YouTube playlist import
- Per-character playlist persistence via KVP storage (no database required)
- Multi-passenger audio synchronization via vehicle statebags
- Proximity scanning for nearby vehicle music discovery and desync recovery
- Tiled navigation map with real-time player position, heading, street/zone labels, weather, and time
- Quick Nav waypoints to gas stations, stores, and garages
- Jailbreak system with USB item requirement, progress bar animation, and circuit-trace minigame
- Procedurally generated wire traces with configurable difficulty levels
- Hidden POI locations unlocked via successful jailbreak
- Configurable keybind (default M) with vehicle-only activation
- Full locale system via ox_lib (locales/en.json)
- Multi-framework support via Community Bridge (QBCore, ESX, QBox, etc.)
