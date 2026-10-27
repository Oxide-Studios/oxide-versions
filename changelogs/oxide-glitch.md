# Changelog

## [1.0.1] - 2026-10-18

### Fixed
- Glitch no longer fails to start ("migrations failed", duplicate column) when `sql/migrations/0002_characters.sql` was imported by hand or an earlier start stopped part-way. The file can run any number of times, and Glitch completes a partial one on its next start.

### Changed
- Installation guide: both migration files are listed, importing them by hand is safe more than once, and the database user also needs permission to change tables.

## [1.0.0] - 2026-10-03

### Added
- First release of `oxide-glitch`, the live streaming app that comes with Oxide Phone. Runs inside `oxide-phone` on QBCore, QBX and ESX through `o-link` (1.11.0 or newer).
- Browse live channels, most watched first, in seven categories, with a Following tab and search.
- Channels with an avatar, banner, bio, followers, tier and Partner check mark, live or offline with the last stream's figures.
- Watching: the live picture from the streamer's phone with the chat below, real and simulated messages in one, emotes, badges, slow mode, followers-only mode and alerts for follows, subs and cheers.
- Support a streamer with follows, monthly subscriptions paid from the bank (`Config.Money.subPrice`) and cheers. The streamer gets the money less a platform cut that falls as their tier rises.
- Going live with a title and a category, the front or back camera and the microphone, and a dashboard with real and simulated viewers, earnings, follows, subs, cheers, a hype meter, alerts and chat moderation tools.
- Streaming as a job: a simulated audience reacts to what the streamer does in the city and pays ads, subs and cheers into a pending balance cashed out to the bank, with hourly and daily caps, a minimum stream length and no pay while AFK.
- Three tiers (Streamer, Affiliate and Partner) earned with followers, average viewers and streams, each paying more. Admins can grant or remove Partner.
- A follow counts once per character, and a ban covers the character on every account.
- Notifications for lives, subs and cheers that reach only the phone logged in to the account.
- Moderation: reports on channels and chat messages, an admin queue in the app, bans that keep accounts out of chat and off streams, and `/glitch`. Every admin action is logged.
- Evidence for police Forensics: real chat, follows, subs, cheers, deleted messages and cash-outs. A phone police hold opens Glitch read-only.
- Database tables are created automatically at first start; `sql/migrations/` ships for reference.
- English locale (`locales/en.json`).
