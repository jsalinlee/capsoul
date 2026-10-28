# Capsoul — MVP Spec

Draft for review · 2026-10-28

## Vision

A personal time capsule: a calendar-first scrapbook that is effortless to add to and a pleasure to look back on, like a family photo album. Closed by design: no strangers, no influencers, no ads.

## Principles

- **Ease over depth.** A new entry takes 3 taps or fewer from app open. No modal chains, no multi-page forms.
- **Looking back is the payoff.** Moving through time (months, days) is fast and visual.
- **The user owns their data.** On-device first; export everything at any time.

## MVP screens

### 1. Month view (home)
- Grid of days. A day with entries shows a thumbnail and its mood (see open questions 1–2).
- Swipe between months; "Today" button to jump back.
- Tap a day → Day view.

### 2. Day view
Entries for one day. Two alternative layout templates are built as prototypes for comparing UI; one will be chosen and the other removed:
- **Timeline:** vertical line, newest first. Each entry's mood emoji *is* the bullet point, so the day's mood arc is visible at a glance. Entries collapse to title and/or a small thumbnail; tap to expand.
- **Schedule:** hour grid like a calendar app, entries placed at their time (morning at top).
- During prototyping, a dev-only toggle switches between the two templates for the same day.

### 3. New / edit entry (single screen)
- Photos (0 or more), mood emoji (optional), title (optional), note (optional).
- Time defaults to now; editable, so past days can be backfilled.
- Save returns to where the user came from.

### 4. Export
- Export everything: entries as JSON plus original photo files, handed to the system share sheet (save to Files, Drive, etc.).

## Data model (on-device SQLite)

| Table | Fields |
| --- | --- |
| entries | id (UUID), occurred_at (UTC instant), tz_offset_min, title, body, mood (emoji, nullable), created_at, updated_at, owner_id, visibility (default `private`) |
| photos | id (UUID), entry_id, file_path, width, height, sort_order, taken_at |

Decisions:
- **Photos are copied into the app's own storage**, not referenced from the camera roll. Deleting a photo from the gallery must not break the capsule. Cost: extra storage (a resize option can come later).
- **UUIDs and owner_id/visibility from day one**, so future backup, sync and sharing don't need a migration of every row.
- **Store the UTC time plus the local offset**, so the "which day" of an entry doesn't shift when the user travels.

## Out of scope for MVP

Accounts, friends, tagging, feed · cloud backup · location, audio, rich text · reminders/notifications · importing from other apps.

## Roadmap after MVP

1. Backup to the user's own cloud (Google Drive, Dropbox, OneDrive).
2. Social: friends, tagging with approval, a personal feed of own and tagged posts. Needs accounts and a backend of our own; storage providers don't cover sharing between users.
3. Printable album export (PDF).

## Platform and devices

- Expo (React Native), TypeScript, Expo Router. Scaffolded on Expo SDK 58 (React Native 0.88): iOS 16.4+, Android 7+.
- Test devices: Galaxy Z Flip 6 (primary: tall ~22:9 screen, fold/unfold resizes the app, cover screen), iPhone 12, older iPad (iPadOS version to confirm).
- Layout rules: flexbox, no fixed pixel heights, safe-area aware, handles window size changes at runtime.

## Design constraint

Daily You is inspiration for *feel* only. Do not view, copy or adapt its source code, assets or UI layouts.

## Open questions

1. A day with several moods: what does the month cell show — the latest mood, the most frequent, or a "day mood" the user picks?
2. Day thumbnail: first photo, latest photo, or one the user pins?
3. Moods: any emoji, or a curated set of about 8? (A curated set keeps mood → color mapping possible.)
4. Keep mood-colored borders on calendar cells in addition to the emoji, or emoji only?
