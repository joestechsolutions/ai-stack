# vans-app — current state & transfer plan

> Written 2026-06-16 by Hermes. Updated 2026-06-16 with intake form decision.

## TL;DR

- **`joblas/vans-app` (default branch: main)** = the **Expo React Native MVP** (the real Archive app), commit `a977298`. Force-pushed 2026-06-16 01:35 UTC. **← this is what we want at `joestechsolutions/vans-app`**
- **`joestechsolutions/vans-app`** is a **301 redirect** to `joestechsolutions/vans-archive-hair-salon-questionaire`. We need to break this so the new transferred repo can land at the right URL.
- **`joestechsolutions/vans-archive-hair-salon-questionaire`** = the **intake questionnaire form source**, commit `dd2eadb` "Security hardening for live production site".
- **`vans-app.pages.dev` (Cloudflare Pages, served by CF)** was the live form. **OFFLINE 2026-06-16** (Joe: "We don't need that intake questionnaire anymore for the pan to app").
- **Form submissions** went to `joe@joestechsolutions.com` via Resend (in your email archive, not in the repo).

## Joe's decision (2026-06-16)

"We don't need that intake questionnaire anymore for the pan to app. We already got that information from her and now she should just be building an app. So maybe put that in archive all that and some old data."

Translation:
1. The intake form is **archive material**, not a live intake.
2. The Expo MVP (real app) supersedes it.
3. Tear down the form's hosting + repos, keep a local archive for design reference.

## What was done in this session

| Action | Status | Where |
|---|---|---|
| Snapshot intake form source code + served HTML + CF Worker to `~/projects/archive-salon-app/legacy/questionnaire/` | ✅ done | local only, gitignored |
| Snapshot `archived-2026-06-16-pages-dev-render.html` (last live bytes) | ✅ done | local only |
| Add `.gitignore` rule for `legacy/` | ✅ done | `~/projects/archive-salon-app/.gitignore` |
| Update `AGENTS.md` with legacy/ note + planned transfer URL | ✅ done | `~/projects/archive-salon-app/AGENTS.md` (commit af46ba8, **not pushed**) |
| Re-clone JTS site safety net | ✅ done | `~/projects/security-scans/jts-site/joestechsolutions-nextjs/` (955MB) |

## What still needs browser action (Joe)

| # | Action | Risk | Sequence |
|---|---|---|---|
| 1 | **Archive** `joestechsolutions/vans-archive-hair-salon-questionaire` | none — form is archive material | Settings → ⚠️ Archive this repository |
| 2 | **Delete** `vans-app.pages.dev` Cloudflare Pages project | none — last build remains cached 30d | Cloudflare dashboard → Workers & Pages → vans-app → Settings → Delete |
| 3 | **Delete** the `joestechsolutions/vans-app` redirect | none | GitHub: Settings → ⚠️ Delete this repository (URL `/joestechsolutions/vans-app`) |
| 4 | **Transfer** `joblas/vans-app` → `joestechsolutions/vans-app` (as `joestechsolutions`) | low — no live deploys reference this name | GitHub: Settings → ⚠️ Transfer → new owner `joestechsolutions` |
| 5 | **Push** local commits (af46ba8, .gitignore) to the new `joestechsolutions/vans-app` | none | `cd ~/projects/archive-salon-app && git push -u origin main` |
| 6 | (Optional) **Delete** `joblas/vans-app` after transfer completes | none — code is at joestechsolutions now | GitHub: joblas/vans-app → Settings → ⚠️ Delete |

## Why the order matters

- **Step 1 first** (archive questionnaire): the redirect at `joestechsolutions/vans-app` points to this. If we delete the redirect first, the redirect is just a name collision; if we delete the questionnaire repo first, the redirect 404s. Either is fine, but archiving first is the cleanest.
- **Step 2 second** (CF Pages delete): this stops new deploys of the form. The current build keeps serving until CF garbage-collects (typically 30 days).
- **Step 3 third** (delete redirect): the redirect was a confusing alias. Now it's gone, the URL `joestechsolutions/vans-app` is free.
- **Step 4 fourth** (transfer): with the redirect gone, GitHub can place the transferred repo at the canonical name.
- **Step 5 fifth** (push local commits): the .gitignore and AGENTS.md update need to land at the new home.
- **Step 6 last** (delete source): only after the transfer fully completes, to avoid leaving a gap.

## Why this is safe

- The intake form is being **archived, not lost**. The full source code + the served HTML + the Cloudflare Worker are all in `~/projects/archive-salon-app/legacy/questionnaire/`. Form submissions are in Joe's email archive (via Resend).
- The Expo MVP (real app) is unchanged. It's already in `~/projects/archive-salon-app/` and on `joblas/vans-app` at the same commit. Transfer is just a metadata change on the GitHub side; the code is identical.
- Cloudflare Pages is being deleted explicitly, not via webhook surprise. We control the order.

## Safety nets in place

- `~/projects/archive-salon-app/legacy/questionnaire/` — full form source (HTML + logos + Cloudflare Worker + the served bytes)
- `~/projects/archive-salon-app/` — the Expo MVP, in sync with `joblas/vans-app` main (2 ahead locally, .gitignore + AGENTS.md, not yet pushed)
- `~/projects/security-scans/jts-site/joestechsolutions-nextjs/` — full JTS site clone, 955MB, master @ 27878564
- `~/projects/security-scans/jts-site/vahsq/` — shallow clone of the questionnaire source repo (still on disk as a backup)

## Related docs

- `JTS-SITE-TRANSFER-PLAN.md` — 4-stage zero-downtime transfer sequence for the live JTS website.
- `REPO-MIGRATION-ACTION.md` — overall migration action list.
- `BOUNDARIES.md` — org rule (joestechsolutions=work, joblas=personal).
- `~/projects/archive-salon-app/legacy/README.md` — local archive manifest.
- `~/projects/archive-salon-app/legacy/questionnaire/ARCHIVE-NOTES.md` — questionnaire-specific notes.
