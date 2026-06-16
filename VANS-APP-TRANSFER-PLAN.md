# vans-app — current state & transfer plan

> Written 2026-06-16 by Hermes. Updated as facts change.

## TL;DR

- **`joblas/vans-app` (default branch: main)** now contains the **Expo React Native MVP** (the real Archive app), commit `a977298` "feat: Archive salon Visual Sliders MVP (v0.1)" by `joe@joestechsolutions.com`. Force-pushed 2026-06-16 01:35 UTC.
- **`joblas/vans-app` (HEAD before force-push)** was `1831689` — apparently never had the intake form code.
- **`joestechsolutions/vans-app`** is a **301 redirect** to `joestechsolutions/vans-archive-hair-salon-questionaire`.
- **`joestechsolutions/vans-archive-hair-salon-questionaire`** is the **actual source for the live intake form** — commit `dd2eadb` "Security hardening for live production site" (2026-05-21), the latest in a 21-commit history.
- **Live URL for the intake form:** `https://vans-app.pages.dev` (Cloudflare Pages, returns HTTP 200, "Hey Van! Let's Build Your App ✦" questionnaire)
- **No Vercel project** references either `vans-app` repo (searched all 7 projects, none match `vans*` or `archive*`).

## Vercel / Cloudflare impact assessment

| Repo | Vercel? | Cloudflare Pages? | Transfer risk |
|---|---|---|---|
| `joblas/vans-app` | No | Probably yes (`vans-app.pages.dev`) | **HIGH** — CF Pages will lose webhook, last deploy serves the form. Need to know if CF deploys the form from this repo or from `vans-archive-hair-salon-questionaire` |
| `joestechsolutions/vans-app` | No | Unknown | N/A — it's just a redirect |
| `joestechsolutions/vans-archive-hair-salon-questionaire` | No | Unknown | Unknown — if this is what `vans-app.pages.dev` deploys from, deleting/renaming breaks the live form |

## Known unknowns (need Joe to verify)

1. **Which GitHub repo does `vans-app.pages.dev` deploy from?** (The Cloudflare Pages → Git integration config. Not visible without Cloudflare admin access.)
2. **Was the force-push to `joblas/vans-app` intentional?** (Confirms the real app should live there.)
3. **Is the intake form code reachable from any other clone?** (reflog on `joblas/vans-app` shows only `a977298` + `1831689`. The intake form's 21 commits are in `vans-archive-hair-salon-questionaire`, so probably yes.)
4. **Is the live intake form still wanted?** (It collects planning info from Van; if the Expo MVP supersedes it, archive the form.)

## Recommended actions (pending answers)

| # | Action | Pre-condition |
|---|---|---|
| 1 | Confirm `joblas/vans-app` is the new canonical home for the real app | Joe confirmation |
| 2 | Determine which repo Cloudflare Pages is watching for `vans-app.pages.dev` | Cloudflare admin access |
| 3 | If form is deployed from `vans-archive-hair-salon-questionaire`: leave that repo alone | Cloudflare admin |
| 4 | If form is deployed from `joblas/vans-app`: **do not transfer** — the form code isn't in this repo's history (it was force-pushed away). Find the form's actual GitHub source. | Cloudflare admin |
| 5 | Once deploy source is known, transfer active repos using the 4-stage plan from `JTS-SITE-TRANSFER-PLAN.md` | Above |

## Safety net already in place

- `~/projects/archive-salon-app/` — full local clone of the Expo MVP, in sync with `joblas/vans-app` HEAD. Origin reset to `joblas/vans-app`.
- `~/projects/security-scans/jts-site/joestechsolutions-nextjs/` — full local clone of the JTS site, 955MB with `node_modules`, in sync with `joblas/joestechsolutions-nextjs` HEAD.
- `~/projects/security-scans/jts-site/vahsq/` — shallow clone of `joestechsolutions/vans-archive-hair-salon-questionaire` (the intake form source). Has the form HTML + `vans-chat-worker` Cloudflare Worker code.

## Related docs

- `JTS-SITE-TRANSFER-PLAN.md` — 4-stage zero-downtime transfer sequence for the live JTS website.
- `REPO-MIGRATION-ACTION.md` — overall migration action list.
- `BOUNDARIES.md` — org rule (joestechsolutions=work, joblas=personal).
