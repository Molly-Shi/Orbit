# Orbit

**v1.1.0**

*Every memory finds its orbit.*

**Reduce communication overhead · Stay synchronized · Continuous awareness · Shared life journey**

- **Reduce communication overhead** — Tonight's Plan and the daily check-in replace a back-and-forth text exchange with a two-tap update.
- **Stay synchronized** — one live database, no manual refresh; a change on one device appears on the other within about a second.
- **Continuous awareness** — the Orbit view and Recent Shares turn scattered daily moments into something both of you keep seeing, not something either of you has to ask about.
- **Shared life journey** — Places, Memos, and Mood History accumulate over time into a record neither person is keeping alone.

A private, single-page web app for two. Track daily shares, plan tonight, check in on how the day went, log places you've been, and keep memories only the two of you know — all in one file, synced in real time through your own Firebase project.

---

## What's inside

| Module | What it does |
|---|---|
| **Sign-in** | Tap a star to sign in as Rey (deep blue) or Meng (sky blue) — no accounts, no passwords. Each identity gets its own cursor trail and accent color throughout the app. |
| **Orbit** (home) | Every share either of you posts appears as a point on one of two interleaved, slowly-rotating orbits. On desktop, hover a point for a quick preview; on mobile, tap once to preview and tap again for the full detail. A flat "Recent Shares" list underneath covers the same content for quick scanning. |
| **Tonight's Plan** | Each person picks how tonight looks: Call, Chat, Meet in person, or Solo time — laid out as a fixed 2×2 grid. When your choices match, the card shows a short message; when they don't, it gently flags the difference. |
| **Daily check-in** | Four quick sliders — Mood, Energy, Balance, Connection — each 0–5, defaulting to the middle. If you open the app after 6 PM and haven't checked in yet, the prompt appears automatically. A "View history" link opens the full record of every day either of you has rated, nothing is ever overwritten. |
| **Schedule** | Two modes: **Personal Schedule** (side-by-side columns — you can see each other's time but only edit your own) and **Shared Schedule** (a joint to-do list either of you can add to and check off). |
| **Places** | Log places you've been — name, a visit-type tag, an optional city note, notes, and an optional photo. Free-form, no map integration or API key required. |
| **Memos** | A private library of memories — title, date, story, and an optional photo. Only visible to the two of you. |
| **Delete** | Shares, memories, and places can each be deleted — but only by whoever created them, and only after tapping "Delete" twice (the second tap must land within a few seconds) so a stray tap can't destroy something by accident. |

Tags for shares (Reading, Food, Inspiration, Mood, Travel, Film, Music, Work, Leisure, Gaming) and for places (Date spot, Food, Café, Trip, Landmark, Stay, Everyday) are editable inline, and you can also type a custom tag on the fly. The interface is in English, but what you write in any text field isn't limited to it.

---

## Getting started

Once deployed (see below), using it day to day is simple:

1. **Both of you open the same deployed URL** (e.g. `https://yourname.github.io/orbit/`). That's what makes you share data — not the file itself, but the live database it's connected to.
2. **Tap your star to sign in.**
3. **Everything saves automatically and syncs in real time.** The moment either of you posts a share, adds a comment, or updates a schedule, it appears on the other person's screen within about a second. The small dot next to the date in the top bar shows **Live** when connected and **Reconnecting…** if the connection drops.

---

## Deploying (one-time setup)

The app is a static site backed by your own Firebase Realtime Database. Three files need to live together in the same GitHub repository: `index.html`, `firebase-app-compat.js`, and `firebase-database-compat.js`. The two `.js` files are the Firebase SDK, hosted locally instead of loaded from Google's CDN — this avoids failures on networks where `gstatic.com` is slow or blocked (you may have seen a `firebase is not defined` console error if that CDN failed to load).

### 1. Create a Firebase project

1. Go to **[console.firebase.google.com](https://console.firebase.google.com)** and create a free project (Spark/free plan is enough for two people). If project creation hangs on "Google Analytics," turn Analytics off — it isn't needed here.
2. **Build → Realtime Database → Create Database.** Pick any region; start in **test mode**.
3. **Project settings → General → Your apps**, click the Web icon (`</>`) to register a web app, and copy the `firebaseConfig` object it gives you. If you don't see a `databaseURL` field in that config, copy the real database URL shown on the Realtime Database page instead (it includes your region, e.g. `...-default-rtdb.asia-southeast1.firebasedatabase.app`) and add it in manually.

### 2. Connect the app to your project

Open `couple-space.html` (soon to be `index.html`) in a text editor, find `const firebaseConfig = {...}` near the top of the `<script>` block, and paste your real values in, replacing every placeholder. Save.

Until this is filled in, the app shows a **"Setup needed"** screen instead of sign-in — that's expected, not a bug.

### 3. Put it on GitHub Pages

1. Create a GitHub repository (public or private both work).
2. Rename `couple-space.html` to `index.html`. Upload it **together with** `firebase-app-compat.js` and `firebase-database-compat.js` — all three files in the same folder, no subfolders.
3. Repo **Settings → Pages** → **Source: Deploy from a branch** → branch `main`, folder `/ (root)` → **Save**.
4. Wait a minute or two, then refresh the Pages settings page — it'll show a live URL like `https://yourname.github.io/reponame/`. That's the link to share with your partner.

### 4. Lock down your database

Test mode leaves the database open to anyone who has the URL. Before relying on it long-term, go to **Realtime Database → Rules** and choose one:

**Simplest (fine for a private, unlisted link):**
```json
{ "rules": { ".read": true, ".write": true } }
```

**More secure (requires sign-in):** enable Anonymous auth under **Build → Authentication**, then set rules to `"auth != null"` for both `.read` and `.write`. This needs a small code addition to sign in anonymously before connecting — ask for this if you want the extra safety margin.

---

## How syncing works

- The app reads and writes a flat set of keys (`shares-index`, `share:<id>`, `schedule:Rey`, `daily-ratings`, etc.) in your Firebase Realtime Database.
- A single live listener on the database root pushes the full current state to both devices the instant anything changes — no polling, no fixed delay.
- Adding an item (a share, a memory, a place) writes its content and its index together in one atomic update, so the other device never sees a half-written entry.
- Sync pauses while a modal is open, so it never overwrites something you're mid-way through typing.
- If the connection drops, the **Live** indicator switches to **Reconnecting…** and catches up automatically once it returns.

---

## Data & privacy

- All content lives in **your own** Firebase Realtime Database — not on any third party's servers beyond Google/Firebase's infrastructure.
- Photos are compressed client-side (max ~900px wide, JPEG) before saving, to keep the database small.
- No third-party analytics, ads, or tracking. The only outbound network calls are loading fonts and syncing with your Firebase database.
- **Take the database rules seriously** — see step 4 above. With open rules, anyone who has your link (or finds your project ID some other way) can read or write your data.

---

## Customizing

Everything lives in one file, in three sections:

- `<style>` — design tokens at the top (`:root` and the `body[data-user="Rey"|"Meng"]` blocks) for colors, fonts, radius/shadow scale.
- `<body>` — all screens and modals, in plain HTML.
- `<script>` — app logic, organized under section-header comments (e.g. `/* ===================== PLACES ===================== */`).

Common tweaks:
- **Change the two names**: search-and-replace `Rey` and `Meng` throughout (HTML, JS, and the `body[data-user="…"]` CSS selectors).
- **Add/remove tags**: edit the `TAGS` or `PLACE_TAGS` arrays near the top of the script.
- **Change colors**: edit `--accent` / `--accent-2` / `--glow` in the two `body[data-user="…"]` CSS rules.

---

## Known limitations

- Needs the one-time Firebase + GitHub Pages setup above — until `firebaseConfig` is filled in, the app shows a setup screen instead of sign-in.
- No offline mode — an internet connection is needed to load fonts and sync data.
- Shares and memories can be deleted, but not edited after posting.
- The realtime listener re-fetches the whole database on every change — simple and reliable for two people, but not optimized for very heavy photo traffic. If storage ever gets tight, moving photos to Firebase Storage (instead of embedding them as base64) would be the next improvement.

---

Made for two people, one shared sky. ✦
