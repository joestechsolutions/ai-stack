# jts-site transfer — zero-downtime plan

**Repo:** `joblas/joestechsolutions-nextjs` → `joestechsolutions/jts-site`
**Vercel project:** `joestechsolutions-nextjs` (id `prj_YTsKmpaSbxWC4RPq9azDV18Blkpy`)
**Production URL:** `https://www.joestechsolutions.com`
**Current production deploy:** stable since 30d ago. No code changes in flight.

**Current Vercel binding:**
- `link.org: joblas`
- `link.repo: joestechsolutions-nextjs`
- `link.productionBranch: master`
- `link.repoId: 1094490642`

**Pre-flight (do these BEFORE the transfer):**

1. **Open the live site in 2 browser tabs.** Tab A: `https://www.joestechsolutions.com`. Tab B: `https://joestechsolutions-nextjs.vercel.app` (the underlying Vercel domain). You'll watch these for any change.
2. **Open Vercel dashboard** → joestechsolutions-nextjs project → Settings → Git. Note the current "Connected Git Repository" path (`joblas/joestechsolutions-nextjs`).
3. **Open GitHub repo** in a new tab: `https://github.com/joblas/joestechsolutions-nextjs/settings`. You'll do the transfer from this page.

**The transfer (3 stages, ~10 min total, ZERO expected downtime):**

### Stage 1 — Transfer the repo (5 min)

1. In the GitHub Settings tab → scroll to **Danger Zone** → **Transfer**
2. New owner: `joestechsolutions`
3. New name: keep `joestechsolutions-nextjs` for now (we'll rename in stage 2 to keep the URL stable)
4. Confirm

**What happens:**
- GitHub moves the repo to the org. The URL changes from `github.com/joblas/joestechsolutions-nextjs` to `github.com/joestechsolutions/joestechsolutions-nextjs`. Old URL auto-redirects.
- Vercel's webhook fires: it tries to fetch from the new location. Depending on how the GitHub App is set up, this MAY or MAY NOT auto-update. Either way, the **live site keeps serving the last successful build** — there is no automatic redeploy.
- ⚠️ **WATCH tabs A and B.** If they go down or error, that's the only way to know. (They shouldn't, but you're checking.)

**If something breaks at this stage:**
- The site will still be on the old build, but Vercel will show errors in the dashboard
- Mitigation: Vercel has a "Redeploy" button on the Deployments tab that uses the last successful build. You can re-trigger that.

### Stage 2 — Rename to `jts-site` (1 min)

1. In the new GitHub repo (`joestechsolutions/joestechsolutions-nextjs`) → Settings → Rename → `jts-site`
2. Old URL (`.../joestechsolutions-nextjs`) auto-redirects to `.../jts-site`. Any external links keep working.

**What happens:**
- GitHub updates the canonical URL. Old URL 301s.
- Vercel: same as before. Live site unaffected. Vercel may or may not pick up the rename automatically.

### Stage 3 — Re-link Vercel (3 min, only if needed)

1. Vercel dashboard → joestechsolutions-nextjs project → Settings → Git
2. If the "Connected Git Repository" still shows `joblas/joestechsolutions-nextjs`:
   - Click **Disconnect**
   - Click **Connect Git Repository**
   - Select `joestechsolutions/jts-site`
   - Set Production Branch: `master`
3. **Do NOT trigger a redeploy.** The current production build stays live. We just want Vercel to know where the repo lives for future pushes.

**What happens:**
- Vercel's webhook is re-established to the new repo URL
- Future pushes to `master` will trigger builds
- The currently live production deploy is untouched

### Stage 4 — Test (1 min)

1. `cd ~/security-scans/jts-site/joestechsolutions-nextjs`
2. `git remote set-url origin https://github.com/joestechsolutions/jts-site.git`
3. `git fetch origin` (this should work after Stage 3)
4. `git push origin master --dry-run` (verify Vercel would accept the push)

If that works, you're done. The site never went down. Vercel is now pointed at the right place.

## What if it DOES go down?

The blast radius is small:
- **Cached pages:** Cloudflare or DNS-level caches may keep serving stale content for minutes/hours even if Vercel is down. Visitors won't notice a 5-minute blip.
- **Rollback option 1:** Vercel → Deployments → click "Promote to Production" on the last successful deploy. (2 clicks, 30 sec.)
- **Rollback option 2:** Vercel → Deployments → "Redeploy" the last good one. (Same effect.)
- **Rollback option 3:** Revert the transfer (GitHub lets you). Re-link Vercel to the old path. (5 min, awkward.)

The last successful production deploy is **30 days old and known-good**. If the worst happens, we have a known-good build to roll back to.

## Things that will NOT break

- ✅ DNS for `joestechsolutions.com` — points to Vercel, Vercel keeps serving
- ✅ SSL cert — Vercel-managed, will auto-renew
- ✅ Custom domain config — stored in Vercel, not in the repo
- ✅ Build artifacts — stored in Vercel CDN, not in the repo
- ✅ Visitors' browser tabs — they're hitting Vercel's CDN, not GitHub

## Things that COULD break (and how to detect)

- ❌ Vercel auto-rebuilds on the transfer event, fails because of URL change
  - Detect: Vercel dashboard shows a red build on the Deployments tab
  - Fix: Click "Redeploy" on the last good build (30d ago, commit `9ee06455`)
- ❌ Webhook gets stuck and you can't push
  - Detect: `git push` succeeds on GitHub, Vercel shows no build
  - Fix: Stage 3 above
- ❌ The 30d-old build has a runtime issue that the previous 21d-old `app` deploy didn't
  - Unlikely (the prod URL has been serving it for 30 days without issue)
  - Detect: Visitor reports an error
  - Fix: Promote a previous deploy or trigger a fresh build

## Why I'm not doing this myself

The transfer is a **one-way irreversible action** (or at least, 5+ minutes of awkward work to revert). The first attempt would also need the `joestechsolutions` GitHub org owner (you) to approve the transfer acceptance. I'd rather you do it in the browser where you can see exactly what's happening, and so you can hit the rollback if needed.
