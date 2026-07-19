# le régime — personal skincare PWA

An offline-capable phone app for running your daily routines, managing the A313 retinization plan, tracking product usage, and logging skin progress. Built as a static site for GitHub Pages.

## What it does

- **Today** — an *ordonnance* (prescription) card tells you tonight's plan: which phase week you're in, whether tonight is an A313 night, and the exact AM/PM checklist. Tap steps to complete them; the pharmacy cross fills when the day is done, and your streak builds.
- **Routines** — all four evening variants (A313 / off-night / barrier-rest) plus morning, and seasonal + travel + maintenance guidance.
- **Products** — your shelf with star ratings and **usage management**: mark a product "opened" and it estimates how many days of use remain and flags when to reorder.
- **Progress** — a quick daily log with skin-state tags, a "what to expect" timeline (week 2 → 1 year) that highlights where you are now, and your journal.
- **More** — troubleshooting flows (dry / peeling / burning / red / purging / sensitive), the science in plain English, lifestyle tips, and setup (A313 start date, tonight override, backup/reset).

All data lives in your browser via `localStorage` — private to your device, works offline. Use **More → Export** to back it up.

## Deploy to GitHub Pages

1. Create a repo (e.g. `le-regime`) and add every file in this folder to the root.
2. Push to GitHub.
3. Repo **Settings → Pages → Build and deployment**: Source = *Deploy from a branch*, Branch = `main` / `/root`.
4. Open the published URL on your phone → Share → **Add to Home Screen**. It installs as a standalone app with the pharmacy-cross icon and runs offline.

Because it's a service-worker PWA, it must be served over **https** (GitHub Pages is), not opened as a local `file://`.

## Files

| file | purpose |
|------|---------|
| `index.html` | the whole app (HTML + CSS + JS) |
| `manifest.webmanifest` | PWA install metadata |
| `sw.js` | offline service worker |
| `icon-192/512.png`, `icon-maskable-512.png`, `apple-touch-icon.png` | app icons |

## Editing your plan

- **Products** and **routines** are defined at the top of the `<script>` in `index.html` (`PRODUCTS`, `ROUTINES`). Edit text, amounts, areas, or `lifeDays` (usage estimate) there.
- **A313 phases** are in the `PHASES` array — change frequency, week ranges, or which weekdays are A313 nights.

## Note

Educational and personal — not a substitute for individualized care from a dermatologist.
