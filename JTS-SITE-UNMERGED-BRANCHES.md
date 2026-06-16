# JTS site (joblas/joestechsolutions-nextjs) — unmerged branches audit

> Audit run 2026-06-16 by Hermes. Before org transfer, list all unmerged work so nothing is lost in the move.

## Current state

- **Live production:** `master` @ `27878564` (2026-04-19) "Update button text to match 30min Calendly link" — Vercel `prj_YTsKmpaSbxWC4RPq9azDV18Blkpy`, serving `www.joestechsolutions.com`
- **Last 5 Vercel deploys:** all 2026-04-19, all `READY` — site is stable
- **Health check:** `curl -I https://www.joestechsolutions.com` returns `HTTP/2 200`
- **Total open branches (non-master):** 22

## Unmerged feature branches

| Branch | Ahead | Last commit | Notes |
|---|---|---|---|
| `blog/22-agent-architecture` | 19 | 2026-02-18 | Blog post draft, 4mo old |
| `blog/add-video` | 21 | 2026-02-18 | Blog post draft, 4mo old |
| `blog/video-autoplay` | 23 | 2026-02-18 | Blog post draft, 4mo old |
| `deps-update-2026-02-24` | 28 | 2026-02-24 | Dep updates, 4mo old |
| `deps/weekly-update-2026-03-09` | 1 | 2026-03-09 | Dep update, 3mo old |
| `deps/weekly-update-2026-03-16` | 1 | 2026-03-16 | Dep update, 3mo old |
| `feat/blog-launch-post` | 16 | 2026-02-17 | Blog work, 4mo old |
| `feat/joe-images-on-site` | 16 | 2026-02-17 | Site images, 4mo old |
| `feat/lurkr-architecture-diagram` | 1 | 2026-03-30 | Lurkr diagram, MERGED via PR #38 (probably already on master) |
| `feature/3-tier-checkout` | 4 (with 5.7M LOC deletion) | 2026-02-17 | **STALE/TOO BIG — DO NOT MERGE** |
| `feature/ai-server-landing-redesign` | 4 | 2026-02-17 | Landing redesign, 4mo old |
| `feature/ai-server-landing-v2` | 2 | 2026-02-17 | Landing v2, 4mo old |
| `fix/intakes-api-types` | 4 | 2026-02-17 | API types fix, 4mo old |
| `fix/lurkr-diagram-collapsible` | 1 | 2026-03-30 | Lurkr diagram, MERGED via PR #39 |
| `fix/npm-audit-minimize` | 28 | 2026-03-01 | Audit fix, 3mo old |
| `fix/payment-before-booking` | 5 | 2026-02-17 | Payment flow, 4mo old |
| `fix/seo-improvements` | (n/a) | 2026-03-09 | MERGED via PR #37 |

## Recommendation

- **Before org transfer:** Review each branch. The Feb 2026 cluster is mostly work that was abandoned when focus shifted. Either:
  - **Discard** (delete remote branch) if work is no longer wanted
  - **Fast-forward to current master** (rebase + merge) if work is still relevant
  - **Park** (rename to `archive/...` for future reference) if valuable but not active
- **Especially review `feature/3-tier-checkout`** — diff is 22,167 files, 5.7M LOC deleted. Almost certainly a botched force-push that nuked node_modules. Safe to **delete**, not to merge.
- **Do NOT auto-merge any of these** without Joe's review of the actual diffs.

## What this means for the transfer

The transfer itself is **safe** — GitHub's transfer is a metadata change, all branches and tags come along. The unmerged branches will be at `joestechsolutions/joestechsolutions-nextjs` (or whatever it's renamed to) after the transfer. The decision about which branches to keep can happen *after* the transfer.

## Open question for Joe

Joe, do you want me to:
1. **Document the branches in this file and forget about them** (current state — transfer is independent of branch cleanup)
2. **Prune the obviously-stale ones** (Feb 2026 cluster, except `3-tier-checkout` which is special)
3. **Investigate each one** and report what's actually in them (1-2 hours of work)

The safest answer is (1) — transfer first, clean up after. The risk of (2) is accidentally deleting work. The cost of (3) is time.
