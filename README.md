# ai-stack — Joe's context architecture

Single source of truth for the AI engineering stack. Built on the
"folders over agents" principle: context architecture survives model swaps,
agents and frameworks don't.

## Layout (5 layers of context)

| Layer | Path | What lives here | Source of truth |
|---|---|---|---|
| **01 — Identity** | `01-identity/` | SOUL.md, AGENTS.md, user profile | Symlinks to `~/.hermes/` |
| **02 — Routing** | `02-routing/` | ROUTING.yaml (the one true routing table) | This tree, plus symlinks to `~/.hermes/config.yaml` and `cron/jobs.json` |
| **03 — Contracts** | `03-contracts/` | Cron job index, skill index, stage contracts | This tree |
| **04 — Reference** | `04-reference/` | MemPalace, Obsidian, GitNexus-indexed repos | Symlinks (read-only) |
| **05 — Artifacts** | `05-artifacts/` | Cron output, kanban, working files | Live data + `work/` for ad-hoc |

## Why this exists

- **One tree to back up** instead of chasing files across `~/.hermes`, `~/.mempalace`, `~/Documents`, `/tmp`, `~/free-claude-code/projects/`
- **One routing table** (`02-routing/ROUTING.yaml`) that maps task kind → model → fallback. When a model gets replaced, you change one file.
- **Diffable context.** `git diff` the whole stack and see what changed.
- **Survives model swaps.** The 5-layer tree is a description of *what* you do with AI, not *how* (which is the agent's job).

## Daily usage

```bash
# Read the current routing
cat ~/ai-stack/02-routing/ROUTING.yaml

# Find a cron job's contract
ls ~/ai-stack/03-contracts/

# Check what skills exist
cat ~/ai-stack/03-contracts/skills/INDEX.txt

# Run a working artifact (a temporary file from a session)
ls ~/ai-stack/05-artifacts/work/

# Back up the whole context
git -C ~/ai-stack add -A && git -C ~/ai-stack commit -m "snapshot"
```

## When you change something

| You changed | Update |
|---|---|
| A cron model's `model:` or `provider:` | `02-routing/ROUTING.yaml` + `02-routing/cron-jobs-INDEX.yaml` (auto-derives) |
| The fallback chain | `~/.hermes/config.yaml` (still the runtime source) — note the change in ROUTING.yaml |
| A skill's purpose or scope | `03-contracts/skills/INDEX.txt` (one line per skill) |
| Your voice, your bio, your tier | `01-identity/` (the symlinked files in `~/.hermes/`) |

## Migration cheatsheet (when a model dies)

1. Add the new model to `providers:` in `~/.hermes/config.yaml`
2. Update `02-routing/ROUTING.yaml` — change the `model:` and `provider:` for the affected tier
3. Update cron jobs: `python3 -c "..."` against `02-routing/cron-jobs.json` (or use `cronjob update`)
4. Restart: `systemctl --user restart hermes-gateway`
5. Verify: `bash ~/hermes-health.sh`

## Verification

```bash
# Tree integrity
find ~/ai-stack -maxdepth 2 -type l -exec test -e {} \; -print | wc -l  # should be 0 broken
find ~/ai-stack -maxdepth 2 -type l -print | wc -l  # total symlinks

# Cron jobs still scheduled
crontab -l  # if using cron, OR
hermes cron list  # if using hermes cron

# Live services
bash ~/hermes-health.sh
```

## Relationship to other systems

- **`~/.hermes/`** — runtime config + service state. Symlinked INTO ai-stack so changes there are visible here.
- **`~/.mempalace/`** — semantic memory + knowledge graph. Symlinked as `04-reference/mempalace`.
- **`~/Documents/Obsidian Vault/`** — Obsidian notes. Symlinked as `04-reference/obsidian`.
- **`/tmp/`** — temporary working files. NOT symlinked (use `05-artifacts/work/` instead for anything that should survive the session).
