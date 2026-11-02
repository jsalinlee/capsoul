# Capsoul — MVP Spec

Draft for review · 2026-10-28

## Vision

A personal time capsule: a calendar-first scrapbook that is effortless to add to and a pleasure to look back on, like a family photo album. Closed by design: no strangers, no influencers, no ads.

## Principles

- **Ease over depth.** A new entry takes 3 taps or fewer from app open. No multi-page forms.
- **Modals: a guideline, not a ban.** Good use: short, glanceable sheets that keep secondary controls out of the main visual flow and close in one tap (e.g. the month/year picker). Avoid: modals that scroll, hold forms, or open other modals. Exceptions are fine when justified; note the reason in this spec.
- **Looking back is the payoff.** Moving through time (months, days) is fast and visual.
- **The user owns their data.** On-device first; export everything at any time.

## MVP screens

### 1. Month view (home)
- Grid of days. A day shows its thumbnail (see open question 1) and its day-mood emoji, if set. No mood-colored borders; cell colors are a future visual-customization feature, unrelated to mood.
- Swipe between months; "Today" button to jump back.
- Header shows month and year (e.g. "October 2026") and is tappable: it opens a compact month/year picker for jumping directly to any month. The header always reflects the month currently shown, including after swiping.
  - Affordance: a small down-chevron beside the title, so it reads as a control, not a label. Hit area covers the whole title, not only the chevron.
  - Picker: a bottom sheet with a year row (‹ 2026 ›) above a 3×4 grid of months; tapping a month jumps there and closes the sheet. Dismiss by tapping outside or swiping down. Current month highlighted; months with entries marked.
  - The model example of the modal guideline: navigation, not input, and it closes in one tap.
- Tap a day → Day view.

### 2. Day view
Entries for one day, with the day mood shown in the header (one tap to set or change; see Moods).

Two alternative layout templates are built as prototypes for comparing UI; one will be chosen and the other removed:
- **Timeline:** vertical line, newest first. Each entry's emoji *is* the bullet point, so the feel of the day is visible at a glance. Entries collapse to title and/or a small thumbnail; tap to expand.
- **Schedule:** hour grid like a calendar app, entries placed at their time (morning at top).
- During prototyping, a dev-only toggle switches between the two templates for the same day.

### 3. New / edit entry (single screen)
- Photos (0 or more), emoji (optional, any system emoji), title (optional), note (optional).
- Time defaults to now; editable, so past days can be backfilled.
- Save returns to where the user came from.

### 4. Export
- Export everything: entries as JSON plus original photo files, handed to the system share sheet (save to Files, Drive, etc.).

## Moods

Two separate concepts; only the day mood is ever analyzed.

| | Entry emoji | Day mood |
| --- | --- | --- |
| What | Free expression per entry; the timeline bullet | One overall feeling per day |
| Values | Any system emoji | Fixed preset list (below) |
| Interpreted by the app | Never | Yes: frequencies, and later the circumplex axes |
| Required | No | No |

Preset day moods (stable text keys, each shown with a default emoji): `happy`, `excited`, `grateful`, `calm`, `tired`, `bored`, `anxious`, `sad`, `angry`, `neutral`.

Picker: a radial layout that mirrors the circumplex. Right = pleasant, top = more energy; `neutral` in the center. Going around: excited → happy → grateful → calm → tired → bored → sad → anxious → angry. Neighbors are similar, so the user can refine ("more grateful than calm") instead of scanning a list. Shown in a bottom sheet (fits the modal guideline). Each mood shows its emoji and label; targets at least 48dp. Ring placements are approximate design choices, not research-exact coordinates.

Rules:
- **Moods are nominal (categories), not a scale.** Store text keys, never numbers. Never average them. Analysis uses counts and proportions.
- **Keys are permanent.** A key is never renamed, reused or deleted, so years of past data keep their meaning. Display names and emoji may change.
- **Circumplex coordinates (later).** Each key can carry derived coordinates on Russell's two axes, pleasantness and energy, for trend views. These are approximate placements, used for broad patterns (e.g. "mostly calm-pleasant this month"), not precise scores.
- **Not a clinical tool.** Insights are descriptive ("you logged calm 9 times in October"). Never diagnose, label the user's mental health, or imply a clinical meaning.
- **One mood per day in the UI**, stored in a `day_moods` table so allowing several later needs no migration.

## Data model (on-device SQLite)

| Table | Fields |
| --- | --- |
| entries | id (UUID), occurred_at (UTC instant), tz_offset_min, title, body, emoji (text, nullable), created_at, updated_at, owner_id, visibility (default `private`) |
| photos | id (UUID), entry_id, file_path, width, height, sort_order, taken_at |
| day_moods | id (UUID), local_date (YYYY-MM-DD), mood_key, created_at, owner_id |

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
4. Mood insights: frequency views, then circumplex-based trends.
5. Visual customization (day/month border colors, themes), independent of mood.
6. Possibly: several moods per day; user-defined day moods (the user places each one on the pleasantness/energy grid, which gives it coordinates for analysis); custom uploaded images for entry emoji.

## Platform and devices

- Expo (React Native), TypeScript, Expo Router. Scaffolded on Expo SDK 58 (React Native 0.88): iOS 16.4+, Android 7+.
- Test devices: Galaxy Z Flip 6 (primary: tall ~22:9 screen, fold/unfold resizes the app, cover screen), iPhone 12, older iPad (iPadOS version to confirm).
- Layout rules: flexbox, no fixed pixel heights, safe-area aware, handles window size changes at runtime.

## Design constraint

Daily You is inspiration for *feel* only. Do not view, copy or adapt its source code, assets or UI layouts.

## Open questions

1. Day thumbnail: first photo, latest photo, or one the user pins?
