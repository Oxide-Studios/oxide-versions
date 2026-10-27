# Changelog

## [2.0.4] - 2026-10-02

### Fixed
- Pressing Play could leave the player on the aerial spawn camera with the character card still on screen and no way forward, when the spawn flow was started a second time before the location picker appeared. A second Play press is now ignored, the button shows "Loading..." until the picker opens, and a flow that fails part-way returns the player to character selection instead of leaving a dead camera.
- ESX: the card above each character showed $0 cash, $0 bank and "Unemployed" for everyone. Balances and jobs are now looked up by the character's identifier when the State ID lookup finds nothing.

### Requires
- ESX servers: o-link 1.10.1 or newer. It fixes "Failed to load character" when pressing Play on a character that is already loaded.

## [2.0.3] - 2026-08-01

### Fixed
- Some players landed on a character selection screen with no characters on it, or with characters missing. A slow-loading model or animation would stop the screen from finishing building. Characters now load one at a time, so a single slow model no longer takes the whole screen down with it.
- Raised how long the resource waits for a character model to load from 5 seconds to 30. Heavily modded servers were running out of time on the shorter limit.
- The mouse cursor did not appear on the character selection screen, leaving players unable to click anything. The loading screen was still holding on to the mouse; it is now dismissed first.
- Characters whose saved position was recorded incorrectly spawned in the open ocean. Those positions are now ignored — the character spawns at `Config.Spawn.new` instead, and "Last location" is no longer offered in the spawn picker for them.
- If anything went wrong while placing a character in the world, players were left on a permanent black screen with a frozen character and had to reconnect. The screen now always fades in and releases the character.
- Reopening character selection while the map spawn picker was on screen broke the camera. It is now ignored until the spawn picker is finished.

### Documentation
- `docs/CONFIGURATION.md`: corrected several settings that were documented as working but are not currently applied by the resource — `Config.Spawn.fallback`, the three `Config.Camera.selection` settings, and `Config.Logout.holdDuration` / `Config.Logout.fadeInDuration`. Each is now marked as such, with a note on what actually happens instead.

## [2.0.2] - 2026-06-15

### Fixed
- When two or more players were at the character selection or creation screen at the same time, they could see each other's characters standing in the scene. Each player is now kept in their own private copy of the scene, so they only ever see their own characters.

## [2.0.1] - 2026-06-11

### Fixed
- `Config.AllowDelete = false` now hides the delete button and blocks deletion server-side.
- `Config.RequireDeleteConfirmation = false` now skips the delete confirmation prompt.
- ESX: the `esx_multicharacter` folder can now be deleted entirely — `oxide-multichar` announces itself under that name so ESX stays in multi-character mode.

## [2.0.0] - 2026-06-01

### Added
- 3D scene-based character selection: players hover and click their characters in a rendered location instead of picking from a list.
- Floating info card above each character showing their name, cash, bank, and job.
- Multiple selection scenes (Apartment, Garage, Pier, Sinner Street), selectable in-screen and remembered between sessions.
- Built-in appearance editor for new characters (face, hair, makeup, eyes, clothing, accessories, and tattoos) on supported clothing resources. Configurable with `Config.CustomCreator`.
- Sky-view spawn picker: after choosing a character, the camera flies over the map and players click a marker to choose where to spawn. Configurable with `Config.SpawnSelector`.
- Spawn at your last logged-off location, or at any property your character owns.
- Free starter apartment for new characters on supported housing systems. Configurable with `Config.StarterApartment`.
- Extra character slots for VIPs, donators, or staff by permission or player ID, on top of the base slot count. Configurable with `Config.SlotGrants`.
- Option to pause or whitelist character creation. Configurable with `Config.AllowCreation` and `Config.CreationAce`.
- Name rules: block duplicate names on the same account and reserve specific words. Configurable with `Config.NameRules`.
- Scene display modes: keep each player's last scene, pick a random one, or always use the default. Configurable with `Config.Scenes.mode`.
- Server announcements shown on the selection screen. Configurable with `Config.Announcements`.
- Player-selectable accent color for the menu, saved per machine.
- `/logout` command to return to character selection, configurable with `Config.Logout`.

### Changed
- The resource is now fully framework-agnostic through `o-link`, so the same build works on QBCore, ESX, and QBX without code changes.
