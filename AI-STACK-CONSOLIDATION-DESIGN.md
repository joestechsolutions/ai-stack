# AI-Stack Consolidation — Design

**Date:** 2026-07-03
**Status:** Approved (Approach A) — implemented, with one security-driven correction (see Revision below)
**Scope:** `~/ai-stack` (this repo) + `~/.fcc/`, `~/free-claude-code/.env`, `~/.claude/`

## ⚠️ Revision (2026-07-03, during implementation) — fcc.env must be a symlink, not a committed file

The original Step 1.1 said to `mv ~/.fcc/.env ~/ai-stack/02-routing/fcc.env` and symlink
`~/.fcc/.env` back to it — i.e. the **real file** lives in ai-stack (committed) and
`~/.fcc/.env` is the symlink. **This was wrong.** `~/.fcc/.env` contains live API keys
(NVIDIA NIM, OpenRouter, Ollama Cloud, Vercel), and `joestechsolutions/ai-stack` is a
**PUBLIC** repo with a daily auto-push. The original approach would have pushed the keys
to public GitHub.

**Corrected design (as implemented):** match the existing `02-routing/hermes-config.yaml`
pattern — the **real file stays at `~/.fcc/.env`** (gitignored, never committed), and
`~/ai-stack/02-routing/fcc.env` is a **symlink pointing *to* `~/.fcc/.env`**. ai-stack tracks
the symlink (mode `120000`), so the live routing config is reachable/diffable from ai-stack
for backup WITHOUT any secret bytes entering git. Verified: 0 bytes of real secret values
in any reachable ai-stack commit; `git show HEAD:02-routing/fcc.env` returns the 21-byte
symlink target path, not env contents.

Consequence for Step 1.2: stripping `MODEL_*` from `~/free-claude-code/.env` is a **local,
gitignored, uncommitted edit** (`.env` is `.gitignore` line 9 — it must never be committed).
Task 2's "commit" step was a plan defect; the strip is applied to the on-disk gitignored
file and left uncommitted, which is the correct state for a secrets file. A follow-up
commit `f4dd3d53` untracked `.env` in `~/free-claude-code` (it had been force-added by
mistake) so the gitignored file stays out of git going forward.

## Goal

Make `~/ai-stack` the true single source of truth for the live AI stack — one directory
you back up, one place you edit live model routing, one tree you `git diff` to see what
changed. Two concrete gaps to close:

1. **Routing gap.** `02-routing/ROUTING.yaml` (Hermes task→model) and `~/.fcc/.env` (fcc
   Claude-tier→model) are parallel routing surfaces that don't reference each other, and
   `~/.fcc/.env` is not linked into ai-stack — so it is invisible to the daily snapshot
   and not diffable alongside `ROUTING.yaml`. Compounding this, `~/.fcc/.env` silently
   overrides `~/free-claude-code/.env` (python-dotenv, later-wins), a footgun documented in
   the `fcc-managed-env-override` memory.
2. **Repo gap.** `04-reference/` symlinks `free-claude-code`, `mempalace`, `obsidian`,
   `open-design` — but `reel-mcp`, `claude-video`, `~/.claude/skills`, `~/.hermes/skills`,
   and `~/.claude/settings.json` (harness/MCP config) are not linked. `reel-mcp` and
   `claude-video` are not mentioned anywhere in ai-stack.

## Approach (chosen: A — Symlink consolidation)

`ROUTING.yaml` and `~/.fcc/.env` are different schemas consumed by different runtimes
(Hermes vs fcc). Forcing them into one file would require code changes in both consumers
for no real gain (Approach C, rejected). Generating `fcc.env` from `ROUTING.yaml`
(Approach B) is a real script + a new YAML section that partly duplicates existing blocks;
deferred to Phase 2 unless the two files demonstrably drift.

Approach A does 90% of the benefit with zero code change: relocate the live fcc env into
`02-routing/` and symlink back, so the whole live routing surface lives in one directory
that the daily snapshot already backs up.

## Part 1 — Routing consolidation

### Step 1.1 — Relocate the live fcc env into ai-stack

```
mv ~/.fcc/.env ~/ai-stack/02-routing/fcc.env
ln -s ~/ai-stack/02-routing/fcc.env ~/.fcc/.env
```

fcc's `config/settings.py` `managed_env_path()` returns `~/.fcc/.env`; python-dotenv opens
the path and transparently follows the symlink. No service code change. The systemd unit
`free-claude-code.service` keeps reading from `~/.fcc/.env` as before.

### Step 1.2 — Kill the dual-`.env` confusion

Strip the **tier-routing** `MODEL_*` lines from `~/free-claude-code/.env` — specifically
`MODEL`, `MODEL_OPUS`, `MODEL_SONNET`, `MODEL_HAIKU`, `MODEL_VISION`, `MODEL_FALLBACKS`.
Each of these is also defined in `~/.fcc/.env` (verified 2026-07-03), so they are already
dead via later-wins override — stripping them just removes the footgun.

Replace the stripped block with a comment pointing to the canonical home:

```
# Live model routing lives in ~/ai-stack/02-routing/fcc.env
# (symlinked from ~/.fcc/.env, which overrides this file). Edit there.
```

**Leave `FCC_SMOKE_MODEL_*` lines untouched.** Those are smoke-test config, not live
tier routing, and not all of them are overridden by `~/.fcc/.env` — stripping would risk
changing smoke-test behavior, which is out of scope. All other non-`MODEL_*` content
(API keys, timeouts, etc.) stays.

After this, there is exactly one file that carries live tier routing —
`~/ai-stack/02-routing/fcc.env` — and no silent-override surprise.

### Step 1.3 — Cross-reference header in `fcc.env`

Add a header comment at the top of `fcc.env` pointing to the related blocks in
`ROUTING.yaml` (`task_routing:`, `models:`) so an editor of one knows where the companion
entries live. Keeps the two files semantically aligned by documentation, not by machinery
(that's Phase 2's job if ever needed).

### Verification (Part 1)

1. `systemctl --user restart free-claude-code.service`
2. `curl -s -o /dev/null -w "%{http_code}" http://localhost:8082/health` → `200`
3. Replay the vision-routing check from the 2026-07-03 vision-routing work:
   - image-bearing `/v1/messages` request → `model: kimi-k2.7-code` (proves
     `MODEL_VISION` loaded from the symlinked env)
   - text-only request → `model: glm-5.2` (proves `MODEL_OPUS` loaded)
4. If either breaks, the symlink didn't resolve — `readlink -f ~/.fcc/.env` must show
   `~/ai-stack/02-routing/fcc.env`.

## Part 2 — Repo consolidation into `04-reference/`

Add symlinks matching the existing read-only pattern (siblings of `free-claude-code`,
`mempalace`, `obsidian`, `open-design`):

| New symlink | Target |
|---|---|
| `04-reference/reel-mcp` | `~/reel-mcp` |
| `04-reference/claude-video` | `~/claude-video` |
| `04-reference/claude-skills` | `~/.claude/skills` |
| `04-reference/hermes-skills` | `~/.hermes/skills` |
| `04-reference/claude-settings.json` | `~/.claude/settings.json` |

`04-reference` is read-only by convention (ai-stack references these, it does not own
them). The symlinks make the whole AI stack reachable from one tree and included in the
daily `git -C ~/ai-stack` snapshot as pointer entries.

### Optional follow-up (not required for this change)

`03-contracts/skills/INDEX.txt` is a curated 35-line list of *Hermes* skill categories,
separate from the symlink question. Confirm whether it should also enumerate
`~/.claude/skills` (Claude Code skills), or whether it is intentionally Hermes-only. If
the latter, no change. Do not block the consolidation on this.

## Out of scope

- **GitHub account consolidation** (`joblas` + `joestechsolutions`) — explicitly not
  selected. Untouched.
- **Merging the git repos** into one repository — explicitly not selected. Untouched.
- **Approach B / Phase 2** (`claude_tiers:` block in `ROUTING.yaml` + `sync-fcc-env.sh`
  with sign-off gate, mirroring `fleet-apply.sh`). Documented as a future upgrade; do not
  build now. Build it only if `ROUTING.yaml` and `fcc.env` demonstrably drift out of sync
  after this consolidation.

## Post-change housekeeping

- Update the `fcc-managed-env-override` memory: canonical home is now
  `~/ai-stack/02-routing/fcc.env` (symlinked from `~/.fcc/.env`). The "edit the managed file"
  instruction now points at the ai-stack path.
- Commit the new symlinks + relocated env via the established ai-stack snapshot pattern:
  `git -C ~/ai-stack add -A && git -C ~/ai-stack commit -m "consolidation: routing env + 04-reference symlinks"`.

## Risk

- **Low.** No code changes to fcc or Hermes. The only live-behavior change is the
  symlinked env path, which dotenv resolves transparently. Verified by the routing
  replay in Part 1. The `04-reference/` symlinks are additive and read-only.
- **Reversibility.** Step 1.1 is reversed by `mv ~/ai-stack/02-routing/fcc.env ~/.fcc/.env`
  (after `rm`-ing the symlink). Step 1.2 is reversed by restoring the stripped `MODEL_*`
  lines from git. Part 2 symlinks are removed with `rm`.