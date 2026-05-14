# TimBot — Functional PWA Prototype

A self-contained PWA prototype of TimBot, a commuter auto-order feature. Frontend-only, no backend, no build step. Designed for stakeholder demos, usability testing, and quick deployment to Netlify (or any static host).

**What this is:** a fully interactive walk-through. Users can sign up, build their usual, pick a schedule and plan, see the day-of dashboard, swap their order, watch state transitions, rate the morning, and view history. Multiple "user profiles" can be created in one browser for back-to-back demos.

**What this is _not_:** production-grade. State is in `localStorage`. No payment, no real store integration, no auth, no backend.

---

## What's in this bundle

```
/
├── index.html            # The functional PWA (single-file SPA)
├── wireframe.html        # Original low-fi wireframe (archived, browse at /wireframe)
├── manifest.json         # PWA manifest
├── sw.js                 # Service worker
├── netlify.toml          # Netlify deploy config (headers + redirects)
├── _redirects            # Netlify fallback redirects
├── README.md             # this file
└── icons/                # PWA icons (192, 512, maskable, apple, favicon)
```

No build step, no npm install, no framework.

---

## Deploy to Netlify

**Drag & drop (fastest):**
1. Zip the contents of this folder (don't zip the folder itself — zip its contents).
2. Drag onto https://app.netlify.com/drop.
3. Done. Get a `*.netlify.app` URL.

**CLI:**
```bash
npx netlify deploy --prod --dir .
```

**Git-based:** point a new Netlify site at the repo path containing this folder. No build command. Publish directory = `.` (or the path to this folder).

The `netlify.toml` sets correct MIME types for the manifest + service worker, long-caches the icons, keeps HTML always-fresh, and adds basic security headers.

---

## Run locally

PWAs need a real HTTP server (the service worker won't register on `file://`).

```bash
# any of these work
python3 -m http.server 8080
npx serve .
npx http-server -p 8080
```

Then open http://localhost:8080.

---

## The flow

1. **Welcome** → name input → onboarding
2. **Onboarding 1** → pick drink, size, cream/sugar, food
3. **Onboarding 2** → pick days, default time, store
4. **Onboarding 3** → pick plan (3/4/5 mornings)
5. **Done** → confetti + summary, then dashboard
6. **Home dashboard** → today's status card, weekly view, shortcuts
7. **Swap modal** → 15s countdown, quick swaps, keep or skip
8. **Lock screen** → simulated geofence push at trigger time
9. **History** → past orders + lifetime stats
10. **Settings** → edit any part of the routine, switch/delete profile

State machine for the day-of order: `scheduled → preparing → ready → picked` (or `skipped`).

---

## Multi-user demo

Tap the **✦** floating button (top-right) to open the demo control panel. From there:

- **Switch between user profiles** created on this browser
- **Add a new user** without losing the current one
- **Jump directly to any screen** (home, swap, lock, history, settings, welcome)
- **Simulate order state transitions** (reset to scheduled, trigger geofence push, mark ready, mark picked up)
- **Seed 7 days of history** with one click for a richer demo
- **Reset everything** (wipes all profiles on this browser)

Multi-tab: if you open two tabs of the same deploy on the same device, both tabs sync via `BroadcastChannel` so you can show how state propagates.

Multi-user across devices: since this is frontend-only, each device gets its own clean session. Anyone visiting the deploy URL on a new browser will see the welcome screen and create their own profile — exactly what you want for a demo people can try independently.

---

## Browser support

- iOS Safari 14+ — install via Share → Add to Home Screen
- Android Chrome 90+ — install prompt fires automatically; or menu → Install app
- Desktop Chrome / Edge / Brave — install button in URL bar
- Firefox — works as a website; install is desktop-only and behind a flag

On desktop, the app renders inside a phone frame at 390×844 for a faithful mobile preview. On mobile, it fills the screen.

---

## Updating after deploy

The service worker is **network-first for HTML**, **cache-first for assets**. To force-refresh assets after a deploy, bump `CACHE_VERSION` at the top of `sw.js`. Visitors get the new version on their next visit.

For "clean install" testing during dev: DevTools → Application → Service Workers → Unregister, then hard reload. Or use the demo panel's "Reset everything" to also wipe state.

---

## Wireframe archive

The original low-fi wireframe (50K of annotated screens for design review) is preserved at `/wireframe.html` — accessible after deploy at `<your-url>/wireframe` thanks to the redirect in `netlify.toml`.

---

## Known limits (intentional, for prototype)

- No backend → no real payment, no real store, no real auth. Profile data lives in `localStorage` only.
- The QR on the ready screen is decorative — visually consistent per order but not scannable.
- Geofence trigger is manual (via the demo panel). Production would use the Geolocation API or a server-side trigger.
- Times shown are local browser time. No timezone handling.
- No analytics, no telemetry.
