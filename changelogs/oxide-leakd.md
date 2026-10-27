# Changelog

## [1.0.1] - 2026-10-10

### Fixed
- Photos added to a new post or story show in the composer straight away. They could take several seconds to appear.

## [1.0.0] - 2026-10-03

### Added
- First release of `oxide-leakd`, the photo and video sharing app that comes with Oxide Phone. Runs inside `oxide-phone` on QBCore, QBX and ESX through `o-link` (1.11.0 or newer).
- Home: a row of stories, then posts from people you follow. A post is up to 10 photos or one video, with likes, comments, saves and sharing to a chat or the phone's share sheet. Captions have tappable @mentions and #hashtags.
- Stories for 24 hours, watched full screen, with who watched them and replies that go to Direct.
- Reels: video posts one screen at a time.
- Search: popular posts, accounts and hashtag pages.
- Profiles with posts, reels, tagged posts and saved posts, followers and following, and private accounts that approve new followers.
- Direct: one-to-one chats with text, photos, shared posts and story replies, read receipts and live updates.
- Live: go live with the phone's camera. Followers are told, viewers anywhere on the map watch, comment and send hearts, and a private account's lives are for its approved followers.
- Blocking hides profiles, posts, stories, lives, comments, likes and tags both ways.
- A verified badge, given by admins.
- Notifications for likes, comments, follows, mentions, messages and lives that open the right screen and reach only the phone logged in to the account. A like, follow or tag taken back and given again notifies at most once every two days.
- Evidence for police Forensics: posts, edits, deletes, comments, likes, follows, messages, stories and live comments. A phone police hold opens Leakd read only.
- Moderation in the app and with `/leakd`: reports on posts, comments, stories and accounts, removing content, bans, ending lives and the verified badge. Every action is logged.
- Database tables are created automatically at first start; `sql/migrations/` ships for reference.
- English locale (`locales/en.json`).
