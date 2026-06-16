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
- `joestechsolutions/jts-pilot-intake-template` — JTS client intake template
- `joestechsolutions/jts-pilot-zw` — ZW Home Construction pilot
- `joestechsolutions/vans-archive-hair-salon-questionaire` — Van's app
- `joestechsolutions/whisper-walkie` — push-to-talk voice typing
- `joestechsolutions/semantic-galaxy` — 3D embedding visualizer
- `joestechsolutions/nick-cleaning-assistant`
- `joestechsolutions/sellerdoor` (private fork)
- `joestechsolutions/aistudio-skate-workshop-app`

## What's under joblas (personal)

- `joblas/career-ops` — Path C portfolio lead
- `joblas/renfaire-directory` — collaborator work with ryanadams20
- `joblas/skate-workshop-clean` — personal
- `joblas/jts-pilot-zw` — **DUPLICATE**, should be deleted (real one is under joestechsolutions)
- `joblas/jts-pilot-intake` — **DUPLICATE**, should be deleted
- `joblas/jts-pilot-intake-template` — **DUPLICATE**, should be deleted
- `joblas/vans-app` — **WRONG ORG**, should be migrated to joestechsolutions (client work)
- `joblas/joestechsolutions-nextjs` — **WRONG ORG**, should be migrated to joestechsolutions (company site code)

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
