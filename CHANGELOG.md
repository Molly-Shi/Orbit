# Changelog

All notable changes to Orbit.

## v1.1.0 (2026-07-23)

Major change: the app is now gated by a single 4-digit passcode instead of a two-identity login.

- Removed the Rey/Meng identity picker. The login screen now shows one star and a 4-digit passcode entry; a correct code signs straight into Meng's session.
- Renamed the sidebar "Switch" control to "Lock" — it returns to the passcode screen.
- Simplified Tonight's Plan, Personal Schedule, and Today's Mood (+ Mood History) to a single-person view, since only one identity can sign in now.
- Tonight's Plan options changed from relationship-oriented choices (Call, Chat, Meet in person, Solo time) to general personal-evening categories (Work, Exercise, Rest, Socialize) — a starting set meant to be adjusted further.
- Rey's historical shares, comments, memories, and places are preserved and still visible (in the orbit view, Recent Shares, and Memos) — only future sign-in and new entries are Meng-only.
- Fixed two crash bugs found while making this change: `renderSchedule()` and `renderMood()` were still looping over both identities and would have thrown errors after the Rey-specific elements were removed from the page.

## v1.0.2 (2026-07-23)

- Fixed the login-screen logo/version label sitting behind the iPhone status bar (time/battery icons) — a mobile-specific layout rule was overriding the safe-area spacing with a fixed value that didn't account for the notch/status bar.
- Fixed a cream-colored gap sometimes appearing at the bottom of the screen on mobile (a viewport-height edge case was letting the page's default background show through). The app background now stays dark before login and switches cleanly to its normal light background after.

## v1.0.1 (2026-07-23)

- Added a small version label next to the "Orbit" wordmark on the login screen, so the running version is visible from inside the app itself (previously only in the code/docs).

## v1.0.0 — Stable Release (2026-07-23)

First version considered stable for regular, everyday use. Core sync, all seven modules, and the mobile/PWA issues found during testing are resolved as of this point.

---

## Fixes — mobile & PWA

- Fixed home-screen "+" button becoming unresponsive after using the mobile orbit preview (a leftover CSS class was leaving an invisible layer over it).
- Fixed "Add to Home Screen" on iOS showing a cut-off layout — added standalone-app meta tags, dynamic viewport height, and safe-area padding for the notch/status bar.
- Fixed orbit taps and Recent Shares taps becoming unresponsive on mobile — orbit nodes were being fully rebuilt 60 times a second, which could drop a tap mid-gesture and load down the main thread. Nodes now persist; only their position updates each frame.
- Fixed daily-rating sliders rendering off-center on some browsers (a step/value mismatch).
- Fixed "View history" and all modal close (✕) buttons not responding reliably on iOS — converted to real `<button>` elements with a larger tap target.
- Fixed photo upload not opening the file picker on some mobile browsers — the tap target is now the file input itself instead of a JS-proxied click.
- Removed the redundant second close control on the daily-rating modal.
- Disabled accidental double-tap/pinch zoom.
- Added a white edge to the cursor dot so it stays visible against similarly colored UI.

## Features

- **Delete**: shares, memories, and places can be deleted by their author only, with a tap-twice-to-confirm guard.
- **Mood history**: a "View history" panel showing every day either person has checked in, with a per-dimension mini bar chart.
- **Daily check-in** redesigned to four dimensions — Mood, Energy, Balance, Connection — each 0–5.
- **Tonight's Plan**: four options (Call, Chat, Meet in person, Solo time) in a fixed 2×2 grid.
- **Orbit hover/tap preview**: desktop hover and mobile first-tap now show a quick preview card before opening the full detail.
- Added Work, Leisure, and Gaming tags.
- Removed the Amap map integration from Places (was a paid service) — replaced with free-form manual entry: name, tag, city note, notes, photo.
- Removed the redundant Orbit/List view toggle.

## Sync & infrastructure

- Migrated storage from Claude-artifact-only storage to a self-hosted **Firebase Realtime Database**, enabling real deployment on GitHub Pages.
- Real-time sync via a single root listener instead of polling.
- Share/memory/place writes made atomic (content + index written together) to fix new entries occasionally vanishing on save.
- Firebase SDK is now hosted locally (`firebase-app-compat.js`, `firebase-database-compat.js`) instead of loaded from Google's CDN, to avoid failures on restricted networks.

## Design

- Renamed to **Orbit**, tagline *"Every memory finds its orbit."*
- Login screen redesigned: five-point glowing stars with an orbiting dust field, tap-to-enter (no "Enter" button), lightweight pulse + crossfade transition.
- Logo redesigned as two crossing orbit rings; moved to the top-left corner.
- Color palette softened — Meng's sky blue lightened and desaturated, Rey's blue softened to match.
- Login title given a fixed two-line break so it doesn't wrap oddly on narrow screens.
- Home view's three cards (Recent Shares / Tonight's Plan / Today's Mood) made equal width; fixed a mobile margin bug that made them narrower than the orbit box above.
- All navigation and Tonight's Plan icons unified into a single simple line-icon style (replacing mixed emoji/glyphs).
