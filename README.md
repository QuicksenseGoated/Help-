# Next Move — PWA

This is the installable version of Next Move. Same app, same single-file core — now wrapped with a manifest, service worker, and icons so Android Chrome will offer "Add to Home screen" / "Install app", and it'll keep working offline after first load.

## Files

```
index.html                  the app (unchanged logic, just a few new <head> tags + SW registration)
manifest.webmanifest         tells Android this is installable, sets icon/name/colors
service-worker.js            caches the app shell so it works offline
icon-192.png                 home screen icon (standard)
icon-512.png                 splash/store-size icon (standard)
icon-192-maskable.png         same icon, safe-zoned for circular/squircle launcher masks
icon-512-maskable.png
```

## Deploy to GitHub Pages

1. **Create a new repo** on GitHub (public works fine, doesn't need to be anything fancy — e.g. `next-move-app`).

2. **Upload all 7 files** straight into the root of that repo. On mobile: GitHub's web UI lets you do this via "Add file → Upload files" — drag in all 7 from this folder. On desktop with git:
   ```bash
   git init
   git add .
   git commit -m "Next Move PWA"
   git remote add origin https://github.com/YOUR_USERNAME/next-move-app.git
   git push -u origin main
   ```

3. **Turn on Pages**: in the repo, go to **Settings → Pages**. Under "Build and deployment", set Source to **Deploy from a branch**, branch **main**, folder **/ (root)**. Save.

4. **Wait ~1 minute**, then your app is live at:
   ```
   https://YOUR_USERNAME.github.io/next-move-app/
   ```

## Install it on your Android phone

1. Open that URL in **Chrome** on your phone (must be Chrome, not a Chrome-based browser like Brave for the install prompt to trigger reliably).
2. Chrome will either show an **"Install app"** banner automatically, or tap the **⋮ menu → Add to Home screen / Install app**.
3. Confirm — it installs with the custom icon and opens in its own window (no browser address bar), exactly like a normal app.

## Updating it later

If you come back and want changes to the app itself, edit `index.html` and re-upload it to the same repo path. Two things to know:

- **Bump the cache version** in `service-worker.js` — change `const CACHE_VERSION = 'nm-v1';` to `'nm-v2'` (etc.) whenever you update `index.html`. Without this, phones that already installed the app may keep serving the old cached version for a while.
- Changes show up the next time the installed app is opened with a network connection — the service worker fetches fresh and re-caches in the background.

## Notes

- All your data (missions, streak, brain dump, projects, night check-ins) lives in the browser's local storage **on that specific phone**. It doesn't sync anywhere — if you reinstall or clear browsing data, it resets to zero. There's no backend in this version, by design (matches the original spec).
- If GitHub Pages ever feels like overkill, any static host works identically — Netlify, Vercel, Cloudflare Pages — just drop the same 7 files in. The app doesn't care who's serving it, as long as it's HTTPS.

## v1.1 — Creator Roadmap & Smart Notifications

Added a Roadmap tab: a monthly calendar with color-coded day status (🟢 completed, 🟡 planned, 🔴 overdue, 🔵 published, ⚪ empty), a Daily Dashboard for planning content (Stream/Shorts/Long-form) and tracking goals (with priority + deadline + progress slider) and reminders (with repeat schedules) per day, Weekly and Monthly goal progress views, and an auto-generated Weekly Creator Report every Sunday (Creator Days, Streams, Shorts, Long-form hours, Ideas saved, Most Productive Day — never follower counts or views). The Coach now auto-detects two consecutive "overdue" days and proactively scales down that day's missions with a "let's simplify tomorrow" message, never framing it as failure.

## v1.2 — Voice Brain Dump & Daily Reflection

**Voice Brain Dump**: tap the mic button on the Brain Dump tab to record an idea out loud. It uses the browser's built-in Web Speech API (`webkitSpeechRecognition`) for transcription — this sends audio to the browser vendor's speech servers for processing (not fully offline), but requires no API key and works natively in Chrome. After recording, the raw transcript runs through a local, fully-offline heuristic "idea organizer" (`organizeIdeaText()` in the code) that extracts a Title, guesses a Category, drafts a Possible Hook, and generates a Checklist via pattern-matching — not a real AI call. Every field is editable before saving. Ideas can be favorited, deleted, and searched alongside regular typed Brain Dump entries.

**Honesty note on "AI Organized"**: this is a rules-based text processor, not a language model. It's instant, free, and offline, but won't be as flexible as a true AI rewrite — long or unusually-phrased transcripts may produce an imperfect title or miscategorize. Since every field is editable, that's a reasonable trade-off for a zero-backend app, but worth knowing.

**Daily Reflection**: replaces the old Night Check-in with a richer flow — a mood check-in (5 options: Amazing/Good/Okay/Tough/Really Hard) followed by three questions (proud of today, what got in the way, tomorrow's next move) shown one at a time with progress dots, ending in a success animation and "Every great creator wins one day at a time." Existing night check-in data remains valid; mood/obstacle fields are simply added on top of the same storage key.

**Journey tab**: a new 6th tab showing a 7-day mood trend chart and a searchable, newest-first timeline of every past reflection.

**Notifications**: morning/afternoon/evening local notifications use the browser Notifications API, requested once on first load and never re-prompted if denied. Because there's no push server, delivery only works reliably while the app/tab is open or recently backgrounded — there's no true OS-level background scheduling in this version.

**Service worker cache version is now `nm-v3`** — already bumped for this release.
