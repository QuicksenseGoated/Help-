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
