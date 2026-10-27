# Changelog

## [1.2.1] - 2026-10-26

### Fixed
- The resource no longer fails to start when a customized `shared/config/weather.lua` has a function inside the hypothermia settings. The function is ignored and the server console names it.

### Documentation
- `docs/TROUBLESHOOTING.md` counts smoke inhalation among the medic-only conditions (twelve of twenty-eight).
- `docs/EXPORTS.md` lists the `GetSetting` and `SetSetting` exports for the hypothermia settings, and `docs/FEATURES.md` counts eighteen server exports.

### When you update
- There's no database migration in this version.

## [1.2.0] - 2026-10-18

### Added
- **Smoke inhalation.** A resource that reports players breathing smoke, such as Oxide Fire, gives them a `smoke_inhalation` sickness that follows how much they breathed. It starts as a cough, then shortness of breath, then dizziness and weakness, then failing to breathe. From moderate up it costs health, and blood oxygen drops at each stage. It clears in clean air, or at once when a medic treats it. Tune it in the new `shared/config/exposure.lua`. Without a resource that reports smoke, nothing changes.
- **`ReportExposure` export** for developers whose resources create smoke, also available as `olink.medical.ReportExposure` with `o-link` 1.12.1 or newer. See `docs/EXPORTS.md`.
- `itemimages/ox_inventory_items.lua`: paste-ready `ox_inventory` definitions for the medical items, next to their images.
- The database tables are created automatically on first start.

### Fixed
- Hypothermia's shivering symptom shows its name instead of a raw text key.
- The saved hypothermia settings are no longer rewritten in the database on every server start.
- `/med test` no longer deletes a real fracture the tested player already had; it removes only the conditions it added.

### When you update
- If you keep your own edited copies of `shared/config/sickness.lua` and `shared/config/vitals.lua`, copy the new `smoke_inhalation` entries into them.
- There's no database migration in this version.

## [1.1.0] - 2026-09-11

### Added
- Hypothermia. With a weather resource that reports temperature and cover through `o-link`, players left out in the cold long enough start shivering and pick up a `hypothermia` sickness, which clears on its own once they have been warm for a while. Off by default: turn on `Enabled` in `shared/config/weather.lua`, where `ColdTemperature`, `HypothermiaSeconds` and `RecoverySeconds` set the threshold, how long it takes to set in and how long recovery takes. Without a weather resource that reports through the bridge, turning it on does nothing.
- That `WeatherIntegration` tuning is this resource's first database-backed setting. It is seeded into `oxide_settings` from `shared/config/weather.lua` on first start (the table is created automatically) and read from the database from then on; change it with the `SetSetting` export rather than by editing the file.

### Changed
- Rain exposure now asks your weather resource through `o-link` whether it is raining and whether the player is under cover, and falls back to the game's own weather and interior check when nothing answers. No weather resource is required and the fallback behaves as before; `rainWeather` in `shared/config.lua` applies to the fallback only.

### Fixed
- A fracture's limp no longer vanishes after a jump, a fall, a ragdoll, climbing, vaulting or getting into a vehicle, and it returns within a second when another script resets the walk style. The limp is what stops an injured player sprinting, so one jump used to remove the penalty for the rest of the injury.

### Documentation
- The rain section in `docs/FEATURES.md` and the `rainWeather` row in `docs/CONFIGURATION.md` describe the bridge-first behaviour, and both files now cover hypothermia and the `WeatherIntegration` setting.

## [1.0.1] - 2026-09-03

### Added
- Item images now ship with this resource, in `itemimages/`: `antacid`, `antibiotics`, `antiviral`, `medical_bandage` and `medical_splint`. They previously lived only inside `oxide-ems`, so installing `oxide-medical` on its own left you with no images to copy.

### Documentation
- `painkillers` is now listed alongside the other five cure items in every install guide, with a copy-paste entry for each inventory. It is stock on QBCore, ESX and Qbox and easy to assume is present, but trimmed item lists drop it — and an unregistered cure item fails silently: using it does nothing and nothing appears in F8 or the server console.
- Troubleshooting covers that silent failure directly, and adds a stale `o-link` as a cause of cure items never registering.
- Documented that `oxide-ems` deliberately leaves `painkillers` alone, so the fever cure keeps working when both resources are installed.

## [1.0.0] - 2026-08-22

First release.

### Added
- **A persistent medical record per character.** Blood type, a DNA hash, an immune stat and the full list of active injuries and illnesses, stored in the database and reloaded on login. Untreated conditions survive a disconnect and a server restart.
- **Four kinds of condition.** Fractures (13 types across the body), wounds (gunshot, laceration, retained bullet), diseases (influenza, wound infection, pneumonia, hepatitis) and sicknesses (nausea, fever, dizziness, dehydration, food poisoning, cardiac arrest) — each with its own severity, effects, cure and catalog entry you can edit.
- **Injuries come from what actually hit you.** The game's own damage events are classified by weapon and by the bone that was struck: a shotgun to the leg is a different injury from a knife to the arm. Repeated hits on the same spot make the existing injury worse rather than stacking up duplicates, and bullet wounds roll for a retained bullet that only a surgeon can take out.
- **Injuries you can feel.** Broken legs make you limp and slow you down, broken arms make your aim drift, bleeding wounds leave blood on the ground behind you, and illnesses make you cough, retch and sway. Everything is driven off the condition list, so it clears the moment you are treated.
- **A vitals simulation.** Pulse, blood pressure and blood oxygen are derived every couple of seconds from the injuries a player is carrying and a blood-volume model that drains while they bleed and recovers while they are on their feet. Severe blood loss collapses the pulse; a collapsed pulse becomes cardiac arrest, which drops the player and can only be brought back by a medic.
- **Diseases that get worse over time.** Each disease moves through timed stages, growing in severity and picking up new symptoms as it goes. Curing one grants a temporary immunity window so a player isn't reinfected the moment they recover.
- **Diseases that spread.** Contagious diseases pass to players standing nearby, with masks on either side cutting the chance and a healthy immune system resisting it.
- **Ways to get ill without being shot.** Standing out in the rain can give you a cold; a dirty wound can turn septic; going hungry or staying stressed erodes your immunity until something takes hold; low thirst brings on dehydration that clears when you drink.
- **Vague symptom notifications.** Players are told something is wrong, never what — "your chest aches with every breath", not "you have fractured ribs". Working out the diagnosis is the medic's job. Every line is in a config file you can rewrite.
- **Self-treatment for the simple things.** Bandages stop bleeding, splints set an arm, antibiotics and antivirals cure illnesses. The serious injuries — a broken spine, a cracked skull, a retained bullet, pneumonia — are marked medic-only and refuse to be self-treated, which is what gives a hospital something to do.
- **Damage over time from untreated injuries.** Severe conditions drain health on a tick until they are treated, applied so that reaching zero routes through your server's normal death handling.
- **Godmode-aware.** The whole system suspends for a player in godmode automatically, with no dependency on the admin resource that sets it, and resumes the moment it is turned off. Any resource can suspend it deliberately too.
- **A full admin toolkit** under `/med`: inspect a record, list conditions, add, treat, remove and clear them, read and set the immune stat, inspect and manipulate vitals, list catalog types, fire a notification, hand out a cure, and run eleven end-to-end scenario simulations.
- **A built-in self-test.** `/med test` exercises the whole export surface against a live player — records, conditions, expiry, treatment, offline reads, vitals and cardiac arrest — and reports pass/fail per check.
- **A complete integration surface** for other resources: fifteen server exports, two client exports, an `o-link` `medical` namespace, a replicated statebag view of every player's condition, a separate vitals statebag, and events for every condition change.
- **Blood evidence handoff.** Bleeding from falls, fire, blades and animal attacks emits an event carrying the character's DNA, which a police resource can turn into forensic evidence. Firearm blood is deliberately left alone so it isn't dropped twice.
- **Runs on QBCore, ESX and Qbox** through `o-link`, with no direct calls to any framework.

### Notes
- Run [`sql/install.sql`](sql/install.sql) before first start.
- Register the three cure items (`antacid`, `antibiotics`, `antiviral`) in your inventory, plus `medical_bandage` and `medical_splint` if you are not already installing `oxide-ems`. See [Installation](docs/INSTALLATION.md).
- `oxide-medical` has no configuration UI and no database-backed settings — everything is tuned in `shared/config.lua` and `shared/config/`, and changes need a resource restart.
