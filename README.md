Orbit

v1.2.0

Every memory finds its orbit.

A private, single-page journal for one. Track daily moments, plan tonight, check in on how the day went, log places you've been, and keep memories only you know — all in one file, synced in real time through your own Firebase project (useful if you use it across your phone and computer).

What's inside
Module	What it does
Sign-in	A single glowing star and a 4-digit passcode gate — no accounts, no identity picker.
Orbit (home)	Every share you post appears as a point on one of two rings — recent shares on the inner ring, older ones on the outer ring — slowly rotating, with a faint decorative third ring and background stars for atmosphere. On desktop, hover a point for a quick preview; on mobile, tap once to preview and tap again for the full detail. A flat "Recent Shares" list underneath covers the same content for quick scanning.
Tonight's Plan	Pick how tonight looks from a small set of options (Work, Exercise, Rest, Socialize by default — easy to edit to whatever fits your life).
Daily check-in	Four quick sliders — Mood, Energy, Balance, Connection — each 0–5, defaulting to the middle. If you open the app after 6 PM and haven't checked in yet, the prompt appears automatically. A "View history" link opens the full record of every day you've rated, nothing is ever overwritten.
Schedule	A simple personal schedule — add a plan with a date, time, and note; delete anything you no longer need.
Places	Log places you've been — name, a visit-type tag, an optional city note, notes, and an optional photo. Free-form, no map integration or API key required.
Memos	A private library of memories — title, date, story, and an optional photo.
Delete	Shares, memories, and places can each be deleted, but only after tapping "Delete" twice (the second tap must land within a few seconds) so a stray tap can't destroy something by accident.

Tags for shares (Reading, Food, Inspiration, Mood, Travel, Film, Music, Work, Leisure, Gaming) and for places (Date spot, Food, Café, Trip, Landmark, Stay, Everyday) are editable inline, and you can also type a custom tag on the fly. The interface is in English, but what you write in any text field isn't limited to it.

Getting started

Once deployed (see below), using it day to day is simple:

Open the deployed URL (e.g. https://yourname.github.io/orbit/) on any device you want to use it from.
Enter your passcode.
Everything saves automatically and syncs in real time across every device you sign in from. The small dot next to the date in the top bar shows Live when connected and Reconnecting… if the connection drops.
Deploying (one-time setup)

The app is a static site backed by your own Firebase Realtime Database. Three files need to live together in the same GitHub repository: index.html, firebase-app-compat.js, and firebase-database-compat.js. The two .js files are the Firebase SDK, hosted locally instead of loaded from Google's CDN — this avoids failures on networks where gstatic.com is slow or blocked (you may have seen a firebase is not defined console error if that CDN failed to load).

1. Create a Firebase project
Go to console.firebase.google.com and create a free project (Spark/free plan is enough). If project creation hangs on "Google Analytics," turn Analytics off — it isn't needed here.
Build → Realtime Database → Create Database. Pick any region; start in test mode.
Project settings → General → Your apps, click the Web icon (</>) to register a web app, and copy the firebaseConfig object it gives you. If you don't see a databaseURL field in that config, copy the real database URL shown on the Realtime Database page instead (it includes your region, e.g. ...-default-rtdb.asia-southeast1.firebasedatabase.app) and add it in manually.
2. Connect the app to your project

Open couple-space.html (soon to be index.html) in a text editor, find const firebaseConfig = {...} near the top of the <script> block, and paste your real values in, replacing every placeholder. Save.

Until this is filled in, the app shows a "Setup needed" screen instead of sign-in — that's expected, not a bug.

3. Put it on GitHub Pages
Create a GitHub repository (public or private both work).
Rename couple-space.html to index.html. Upload it together with firebase-app-compat.js and firebase-database-compat.js — all three files in the same folder, no subfolders.
Repo Settings → Pages → Source: Deploy from a branch → branch main, folder / (root) → Save.
Wait a minute or two, then refresh the Pages settings page — it'll show a live URL like https://yourname.github.io/reponame/.
4. Lock down your database

Test mode leaves the database open to anyone who has the URL. Before relying on it long-term, go to Realtime Database → Rules and choose one:

Simplest (fine for a private, unlisted link):

json
{ "rules": { ".read": true, ".write": true } }

More secure (requires sign-in): enable Anonymous auth under Build → Authentication, then set rules to "auth != null" for both .read and .write. This needs a small code addition to sign in anonymously before connecting — ask for this if you want the extra safety margin.

5. Set your passcode

Find const APP_PASSCODE = '...' near the top of the <script> block and change it to whatever 4-digit code you want. This is the only thing standing between the login screen and your data, so treat the deployed link + this code together as your account credentials.

How syncing works
The app reads and writes a flat set of keys (shares-index, share:<id>, daily-ratings, etc.) in your Firebase Realtime Database.
A single live listener on the database root pushes the full current state to every connected device the instant anything changes — no polling, no fixed delay. This is what lets you use the app from your phone and your computer and see the same data on both.
Adding an item (a share, a memory, a place) writes its content and its index together in one atomic update, so a device never sees a half-written entry.
Sync pauses while a modal is open, so it never overwrites something you're mid-way through typing.
If the connection drops, the Live indicator switches to Reconnecting… and catches up automatically once it returns.
Data & privacy
All content lives in your own Firebase Realtime Database — not on any third party's servers beyond Google/Firebase's infrastructure.
Photos are compressed client-side (max ~900px wide, JPEG) before saving, to keep the database small.
No third-party analytics, ads, or tracking. The only outbound network calls are loading fonts and syncing with your Firebase database.
Take the database rules seriously — see step 4 above. With open rules, anyone who has your link (or finds your project ID some other way) can read or write your data. Combined with the passcode gate in step 5, this gives you two layers: the link+rules control who can reach the database at all, and the passcode controls who can get past the app's own front door.
Customizing

Everything lives in one file, in three sections:

<style> — design tokens at the top (:root block) for colors, fonts, radius/shadow scale.
<body> — all screens and modals, in plain HTML.
<script> — app logic, organized under section-header comments (e.g. /* ===================== PLACES ===================== */).

Common tweaks:

Change the passcode: edit const APP_PASSCODE near the top of the script.
Add/remove tags: edit the TAGS or PLACE_TAGS arrays near the top of the script.
Change colors: edit --accent / --accent-2 / --glow in the body[data-user="Meng"] CSS rule.
Edit Tonight's Plan options: edit the MEET_OPTIONS array — each entry is an icon, a key, and a label.
Known limitations
Needs the one-time Firebase + GitHub Pages setup above — until firebaseConfig is filled in, the app shows a setup screen instead of sign-in.
No offline mode — an internet connection is needed to load fonts and sync data.
Shares and memories can be deleted, but not edited after posting.
The realtime listener re-fetches the whole database on every change — simple and reliable at this scale, but not optimized for very heavy photo traffic. If storage ever gets tight, moving photos to Firebase Storage (instead of embedding them as base64) would be the next improvement.
The passcode gate is a simple front door, not strong security — the code lives in plain text in the file. It's meant to keep out casual visitors to your link, not a determined attacker. Use the stricter database rules (step 4) if you need real protection for the data itself.

Made for one person, keeping their own record. ✦
