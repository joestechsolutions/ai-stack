# Boundaries — where code lives

**Rule (LOCKED 2026-06-16):** *anything related to work, AI building, and app building* lives under the **`joestechsolutions`** GitHub org. Personal stuff lives under `joblas`.

## The split

| Org | Account | Use |
|---|---|---|
| `joestechsolutions` | work / business | AI building, app building, JTS client work, hermes stack, anything that ships or builds the stack. Tied to joestechsolutions.com (the company). |
| `joblas` | personal (blasj408@gmail.com) | Personal notes, career-ops (Path C job search), personal experiments. Tied to cloudyjoe.com (the personal site). |

## What's already under joestechsolutions ✓

- `joestechsolutions/hermes-forge` — Hermes stack forge
- `joestechsolutions/ai-platform-bootloader` — one-command deploy
- `joestechsolutions/jts-pilot-intake-template` — JTS client intake template (RETROFITED 2026-06-16: 3 placeholders + config.template.yaml + For-clients section, per build-once-use-many)
- `joestechsolutions/jts-pilot-zw` — ZW Home Construction pilot
- `joestechsolutions/vans-archive-hair-salon-questionaire` — Van's app intake
- `joestechsolutions/whisper-walkie` — push-to-talk voice typing
- `joestechsolutions/semantic-galaxy` — 3D embedding visualizer
- `joestechsolutions/nick-cleaning-assistant`
- `joestechsolutions/sellerdoor` (private fork)
- `joestechsolutions/aistudio-skate-workshop-app`
- `joestechsolutions/ai-stack` — context architecture (created 2026-06-16, daily 02:00 push cron, 5 commits)

## What's under joblas (personal)

- `joblas/career-ops` — Path C portfolio lead
- `joblas/renfaire-directory` — collaborator work with ryanadams20
- `joblas/skate-workshop-clean` — personal
- `joblas/skateboard-workshop-app` — hobby skate project (1-file static, 55B README). CLASSIFY: personal. Stays at joblas, no action.
- `joblas/jts-pilot-zw` — **DUPLICATE**, should be deleted (real one is under joestechsolutions)
- `joblas/jts-pilot-intake` — **DUPLICATE**, should be deleted (was a stale rename, now removed)
- `joblas/jts-pilot-intake-template` — **DUPLICATE**, should be deleted
- `joblas/jts-pilot-demo-client` — **STALE TEST** of spin-up script, README still says "This is a template repo". Should be deleted.
- `joblas/vans-app` — **WRONG ORG**, should be migrated to joestechsolutions (client work — local remote already updated)
- `joblas/joestechsolutions-nextjs` — **WRONG ORG**, should be migrated to joestechsolutions as `jts-site` (company site code)
- `joblas/cv-joseph` — personal portfolio site (cloudyjoe.com)
- `joblas/cbarrgs-vibe-haven`, `joblas/cbarrgs-marketing-agent` — personal creative project

## When creating a new repo

```bash
# Default: joestechsolutions
gh repo create joestechsolutions/<name> --public --source=<dir> --push

# Personal only: joblas
gh repo create joblas/<name> --public --source=<dir> --push
```

If unclear, ask Joe: "this is work/AI/app-building → joestechsolutions, or personal → joblas?"

## Old rules that this supersedes

- Earlier mempalace note (2026-06-08) said "JTS work vs career-ops separation." That rule still holds, but it's narrower than this. The new rule is **broader**: anything work/AI/app goes joestechsolutions; only Path C / personal goes joblas.
