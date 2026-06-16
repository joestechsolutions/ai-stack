# GitHub Migration — action needed from Joe

I cannot transfer repos across orgs from this terminal. Joe needs to do these in a browser (5 min total).

## Steps

### 1. Migrate `joblas/vans-app` → `joestechsolutions/vans-app`

- Go to https://github.com/joblas/vans-app/settings
- Scroll to bottom → **"Danger Zone"** → **"Transfer"**
- New owner: `joestechsolutions`
- New name: `vans-app`
- Confirm

After transfer, the local clone at `~/projects/archive-salon-app` is already
pointed at `https://github.com/joestechsolutions/vans-app.git` (I updated
it). You can `git push` to publish any unpushed commits.

### 2. Migrate `joblas/joestechsolutions-nextjs` → `joestechsolutions/jts-site`

- Go to https://github.com/joblas/joestechsolutions-nextjs/settings
- Transfer → owner `joestechsolutions`, name `joestechsolutions-nextjs`
- After transfer, rename to `jts-site` (Settings → Rename, or `gh repo rename jts-site --repo joestechsolutions/joestechsolutions-nextjs`)

### 3. Delete 3 `joblas/*` JTS pilot duplicates (now safe)

The canonical versions are at `joestechsolutions/*`. The `joblas/*` versions are duplicates:

- `joblas/jts-pilot-zw` — duplicate of `joestechsolutions/jts-pilot-zw`
- `joblas/jts-pilot-intake-template` — marked "REPO RENAMED"
- `joblas/jts-pilot-intake` — already gone (was transferred out earlier)

To delete (joestechsolutions account needs `delete_repo` scope on the joblas account — the easiest way is just Settings → Delete in the browser):

- https://github.com/joblas/jts-pilot-zw/settings → Delete
- https://github.com/joblas/jts-pilot-intake-template/settings → Delete

## Already done ✓

- ✅ `jts-pilot-intake` already gone (X Failed error in my log = "already changed name")
- ✅ Local `~/projects/archive-salon-app` remote updated to point at `joestechsolutions/vans-app`
- ✅ `jts-pilot-intake-template` repo retrofit (3 placeholders extracted, config.template.yaml added, "For clients" section in README) — pushed to `joestechsolutions/jts-pilot-intake-template`
- ✅ `~/bin/jts-pilot-new` updated to use placeholders, accept `--email` / `--company` / `--contact` flags, default to `joestechsolutions` org

## After the transfers, update remote

Once `joestechsolutions/vans-app` and `joestechsolutions/jts-site` exist, the local clones need a remote refresh:

```bash
cd ~/projects/archive-salon-app && git push origin main  # publish unpushed commits

# And if there's a local jts-site clone:
cd ~/path/to/jts-site
git remote set-url origin https://github.com/joestechsolutions/jts-site.git
git push origin master  # or main
```

## Why I couldn't do this myself

- `gh repo transfer` doesn't exist (it was a hallucination)
- The transfer API requires **admin rights on the source repo** — the `joestechsolutions` gh account is the destination, but only the owner of `joblas` (you, Joe) can initiate a transfer out
- Deletion of `joblas/*` repos requires the `delete_repo` scope on the `joblas` account, which the active `joestechsolutions` gh session doesn't have

---

## Additional findings from this session

### Already done in this session ✓

- ✅ **`joestechsolutions/jts-pilot-zw`** — added `config.yaml` documenting its current customizations. No hardcode change (live form, ZW-specific values are correct as-is).
- ✅ **`joestechsolutions/hermes-forge`** — opened PR `fix/remove-home-lurkr-hardcode` (branch on origin). Removes `/home/lurkr` hardcode from `dashboard/backend/main.py` (now uses `Path(__file__).parent.parent / "frontend" / "dist"`) and `scripts/hermes-sync.sh` (now derives `REPO_DIR` from `$HOME`). Closes one item in PRODUCT-KILL-ANALYSIS.md. Review + merge: https://github.com/joestechsolutions/hermes-forge/pull/new/fix/remove-home-lurkr-hardcode

### New "to delete" candidates (browser action)

- **`joblas/jts-pilot-demo-client`** — looks like a test of the spin-up script. README still says "This is a template repo, not a real client form" — it was never customized. Safe to delete.
- **`joblas/skateboard-workshop-app`** — single-file 103KB static HTML, 55-byte README. Classify as PERSONAL (hobby skate project), not work. Stays at joblas, no action needed.

### Other small cleanups needed

- **`~/jts-pilot-zw` (local clone)** — points to `joblas/jts-pilot-zw` (the redirect one). After you delete the joblas one, re-point or delete the local clone too. The canonical one is at `~/jts-pilot-zw-org` and is already pointed at joestechsolutions.
