# le régime — personal skincare PWA

## Two people, one app

The app holds separate routines for **Meghan** and **Mom**. Each has her own products, A313 schedule, troubleshooting advice and science notes, and — importantly — completely separate logs, journals, custom products and GitHub backup settings.

Each person opens her own link **once**:

- Meghan → `https://<you>.github.io/<repo>/?who=meghan`
- Mom → `https://<you>.github.io/<repo>/?who=mom`

The app saves that choice to the device, so from then on it opens as the right person even without the `?who=` (this matters because an installed Android PWA launches from the manifest and drops the query string). Then just **Add to Home Screen** as normal.

To change on a device: **More → Whose routine**. Nothing is overwritten when switching — each person's data lives under its own storage key.


An offline-capable phone app for running your daily routines, managing the A313 retinization plan, tracking product usage, and logging skin progress. Built as a static site for GitHub Pages.

## What it does

- **Today** — a **Tonight** card shows your plan for the day: which phase week you're in, whether tonight is an A313 night, and the exact AM/PM checklist. Tap steps to complete them; the pharmacy cross fills when the day is done, and your streak builds.
- **Routines** — all four evening variants (A313 / off-night / barrier-rest) plus morning, and seasonal + travel + maintenance guidance.
- **Products** — your shelf with star ratings and **usage management**: mark a product "opened" and it estimates how many days of use remain and flags when to reorder. The estimate is a model — bottle life × how often the plan uses the product — so **tap the opened-date chip to correct it**: set the real number of days one bottle lasts *you* (the defaults assume a textbook dose, which nobody applies), or hit **Start a new bottle today** when you replace one. Your figure is remembered per product and is shown on the card as *N-day bottle (yours)*. **Add your own products** with the *Add a product* button (name, brand, life estimate, and optionally which routines it joins so it shows up in your checklist), and **remove** ones you don't use — built-ins go to a *Removed* list you can restore from; custom ones delete outright.
- **More** — troubleshooting flows, the science in plain English, lifestyle tips, setup (A313 start date, **A313 frequency hold**, tonight override), and a **Backup & updates** section: **Export** a `.json` backup, **Import** one back, or **Copy my data for Claude** to paste into chat. This is the update loop — when Claude sends a new `index.html`, replace the file, open the app, and *Import* your backup to carry every log and custom product across.
- **Progress** — a quick daily log with skin-state tags, a "what to expect" timeline (week 2 → 1 year) that highlights where you are now, and your journal. **Logging a symptom tag can reshape your plan automatically:** log `red`/`stinging` and it offers a *barrier rest* (cleanse + moisturize only) for the next few nights; log `peeling`/`dry` and it offers to *ease A313* down one frequency step for the rest of the week or 7 days; log `breakout`/`purging` and it reassures you it's likely normal purging and to hold steady (no cut). Accepting rewrites your upcoming evenings, shows a banner on Today, and gives you an **End early** button. All of this runs in the app — no need to regenerate anything.
- **More** — troubleshooting flows (dry / peeling / burning / red / purging / sensitive), the science in plain English, lifestyle tips, and setup (A313 start date, tonight override, backup/reset).

All data lives in your browser via `localStorage` — private to your device, works offline. Use **More → Export** to back it up.

## Skips

Every step has a small ⊘ button. Tapping it records an **intentional skip** with a reason (Traveling, Too tired, Event, Out of product, Skin felt sensitive, Deliberate rest, or your own text).

A skip is deliberately *not* a symptom: it never triggers an A313 schedule change, and it counts toward the day being complete so your streak survives. Tap ⊘ again to undo.

**Seeing your history:** the **Progress** tab opens with a **month calendar**. Each day is colour-coded — solid green for a fully completed day, amber for partial, blank for untracked — with a small amber dot marking A313 nights and a ⊘ on days with an intentional skip. Arrows move between months. **Tap any day** to see that date's full breakdown: every morning and evening step marked done ✓, skipped ⊘ with its reason, or not logged. Below the calendar, four stats summarise the month: percent of days complete, A313 nights done, current streak and best-ever streak.

## A313 nights and the start date

A313 nights fall on **fixed weekdays** defined per phase (Mom starts Mon & Thu; Meghan starts Tue & Sat). The **start date** sets which *week* of the plan you're in — it does not move which weekdays are retinoid nights. Before the start date, no A313 night is ever scheduled: the app shows "not started yet" and gives the plain evening routine.

## Holding the A313 frequency

The phase engine escalates on a fixed calendar. Meghan's plan is **2 nights a week for weeks 1–4, then 3 nights a week from week 5 onward** (it no longer builds to nightly). Mom's is 2 → 3 → 4. **More → Setup → A313 frequency** overrides that with a standing instruction: *stay at N nights until I say otherwise*. Pick anything from **pause A313** through **nightly**, or *follow the plan* to hand control back.

Unlike barrier-rest and ease-off, a hold has **no end date**. The plan calendar keeps advancing underneath it — you still see your week and phase — but the frequency that phase asks for is ignored until you lift the hold. A banner on Today shows what is held and what the plan wanted instead (*"plan would move to every other night in wk 3"*), with a **Resume** button.

Which weekdays a held frequency uses come from the profile's own phase table wherever it defines that frequency, so a held 2 is Tue & Sat for Meghan and Mon & Thu for Mom — exactly how each plan starts. Frequencies the table doesn't define fall back to an evenly spread default.

Precedence: **barrier rest** (every night is a rest night) beats everything; an **ease-off** drops one step *below the held frequency* rather than below the phase's; a **ramp** supersedes all three; and a per-night override in Setup still wins for tonight only. The product usage gauge follows the held frequency too, so a hold at 2 nights doesn't keep estimating A313 at the phase's rate.

## History never moves

Past evenings aren't stored. The app works them out from the plan, so changing the plan used to re-label nights you had already ticked. Starting a ramp, for example, moved the A313 start date forward and wiped every past A313 night off the calendar.

Now every change to the plan freezes the past first:

- **Start date and frequency hold:** before either changes, the plan as it stood is saved for every day up to yesterday (`planHist`). Past days keep the week, phase and hold they actually had.
- **Phase table:** each table is versioned (`phasesVer`). Days logged under the old 2 → 3 → 5 → 7 → 4 table keep using it.
- **Ramps:** ending a ramp archives the days it actually ran (`rampPast`), so you can restart it without re-labeling the first run.
- **Barrier rest / ease-off:** ending one early cuts it off at yesterday instead of deleting it. Expired ones are kept.

Today is the one live day. When the plan changes mid-day, ticks already logged today are carried over by product.

## Reintroduction ramp

When skin is raw, stinging or reactive, **More → Setup → Reintroduction ramp** starts a dated 28-day rebuild that temporarily takes over the routines:

| Stage | Days | In the routine | Held back |
|---|---|---|---|
| 1. Barrier reset | 1–5 | cleanser, moisturizer, SPF | every active |
| 2. Vitamin C back | 6–12 | + Vinoperfect, alternate mornings | Cleanance, Sébium, A313 |
| 3. Cleanance back | 13–19 | + Cleanance, spots only | Sébium, A313 |
| 4. A313 restart | 20–28 | + two buffered short-contact A313 nights (days 21 and 26) | Sébium |

While it runs, the Today tab shows only the steps that belong to that stage, so there is nothing to remember. Skips, streak, journal and backups all keep working, and the **Routines** tab still shows the full routines for reference.

### After the ramp

Sébium does not simply reappear on every off night when the ramp ends — it is capped at **two nights a week, never two nights running**, T-zone only. The cap is a property of the routine step (`cap:{max:2,gap:2}`), not a fixed pair of weekdays: which nights are free moves with the A313 phase, so a hard-coded Mon/Thu would quietly fall to zero once both became retinoid nights. The app walks each week from Sunday and takes the first two nights that actually run that routine. In the Consolidate phase (A313 nightly) there are no free nights, so it correctly pauses until Maintenance.

The cap only applies from the day it arrived (`cadenceFrom`, pinned at first load). Logged ticks are stored as positions in an evening's step list, so a rule that changed the shape of past evenings would silently re-label nights you had already ticked — the pin makes that impossible.

Starting the ramp also moves the **A313 start date** to the day after it ends, so the 12-week plan restarts cleanly at two nights a week instead of resuming mid-phase. Ticks already logged today are carried over by product, not by position, so nothing is lost when the step list shrinks. Barrier-rest and ease-off adjustments are ignored while a ramp is active — the ramp supersedes them.

To stop early: **End** from the banner or **View plan → End the ramp**. Routines go back to normal immediately; A313 stays paused until its start date, which you can bring forward in Setup.

Mom's profile has its own version of the ramp (barrier → Rosaliac → Vinoperfect → A313).

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

- **Add or remove products from inside the app** (Products tab) — no code needed. Custom products can be attached to any routine and are included in exports.
- The built-in **products** and **routines** are defined at the top of the `<script>` in `index.html` (`PRODUCTS`, `ROUTINES`) if you want to change the defaults themselves. Edit text, amounts, areas, or `lifeDays` (the default usage estimate — per-product overrides set in the app live in `S.life` and win) there. A step can also carry `cap:{max,gap}` to hold a product to at most `max` nights a week with at least `gap` days between them.
- **A313 phases** are in the `PHASES` array — change frequency, week ranges, or which weekdays are A313 nights. A standing override set in the app lives in `S.hold` (`{nights, from}`) and takes precedence over the phase's frequency.

## Moving to a new version of the app

When Claude gives you an updated `index.html`: (1) in the current app go to **More → Export backup**, (2) replace `index.html` in your repo with the new one, (3) open the app and go to **More → Import backup** and pick the file. All your history, custom products, and settings carry over.

## Auto-backup to GitHub (optional)

The app can commit a dated JSON backup to a **private** repo once a day, automatically, whenever you open it.

Setup:
1. Create a **separate private repo** for backups (e.g. `skincare-backups`). Don't reuse the public Pages repo — backups contain your personal logs.
2. Create a **fine-grained personal access token** (GitHub → Settings → Developer settings → Fine-grained tokens): give it access to *only* that one repo, permission **Contents: Read and write**, and an expiry you're comfortable with.
3. In the app: **More → Auto-backup to GitHub**. Enter `owner/repo`, leave branch `main` and folder `backups` (or change), paste the token, tick **Back up automatically once a day**, and Save. Hit **Back up now** to test.

It writes `backups/le-regime-YYYY-MM-DD.json` plus a rolling `backups/latest.json`.

Notes and limits:
- The trigger is *app open* — it fires at most once per calendar day, and only on days you open the app. Phone browsers (especially iOS) can't run reliable scheduled background tasks, so there's no true unattended cron from the app itself.
- The token is stored in a **separate local key on your device only**. It is never included in Export, "Copy for Claude", or the committed backup. Treat it like a password; use a scoped, expiring token.
- To restore, download any backup JSON from the repo and use **More → Import backup**.

## Note

Educational and personal — not a substitute for individualized care from a dermatologist.
