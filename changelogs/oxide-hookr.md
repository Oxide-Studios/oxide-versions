# Changelog

## [1.0.0] - 2026-10-03

### Added
- First release of `oxide-hookr`, the swipe-to-match dating app that comes with Oxide Phone. Runs inside `oxide-phone` on QBCore, QBX and ESX through `o-link` (1.11.0 or newer).
- Profiles with up to 6 photos from the phone's Photos or Camera, a bio, interests and prompts, who players are and who they want to see, an age range (18+) and a distance. Profiles can be paused.
- Discover: a deck of cards to drag or tap through, with likes, Super Likes and rewinds under daily limits.
- A match when the like is mutual, then a chat with text and photos, read receipts and live updates. Unmatch, block and report are one tap away.
- Notifications for new matches and messages that open the right chat, follow the phone's preview settings and reach only the phone logged in to the account. Profiles can be shared to Messages as `hookr://` links.
- Uses the phone's accounts: a SIM gets one the first time Hookr opens, with a password, log out, switching account and logging in on another phone.
- Distances by area only, never the exact spot, refreshed at most every five minutes.
- Evidence for police Forensics: profile changes, swipes, matches, messages (unsent ones too), blocks and reports. A phone police hold opens Hookr read-only.
- Moderation in the app and with `/hookr`: the report queue with the reported chat, removing photos or profiles, banning and unbanning. Every action is logged.
- Database tables are created automatically at first start; `sql/migrations/` ships for reference.
- English locale (`locales/en.json`).
