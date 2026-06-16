# Build-Once-Use-Many — the product principle

**Rule (LOCKED 2026-06-16):** *What I build for myself must be composable for clients.* Default should work out-of-the-box; client-specific config overrides defaults, never replaces them. Simple for solo use, complex for client workspaces.

## What this means in practice

Every tool / app / system Joe ships for himself must answer **YES** to these questions before merge:

1. **Standalone-installable** — can a fresh user clone + run + get the same working result Joe gets?
2. **Config-overridden, not hardcoded** — no `/home/lurkr/...` paths, no Joe-specific names baked in. All paths via `$HOME` or config file. All names (branding, company, contact) via config.
3. **Composability tiers** — works at three levels:
   - **Simple:** zero config beyond the minimum (e.g. `npm install && npm run dev`)
   - **Default:** sensible out-of-box behavior, no Joe-specific assumptions
   - **Client-grade:** config file + env vars let a client override branding, paths, providers, endpoints
4. **No "Joe's Tech Solutions" in code or copy** — branding lives in `config/brand.yaml` or equivalent. Default values point to neutral strings.
5. **README has a "For clients" section** — short paragraph explaining how to install on a client workspace, what the override points are.

## What this is NOT

- Not "build everything for clients" — solo use is fine, but the **shape** of the build needs to be composable.
- Not "over-engineer for hypothetical clients" — solo-first, client-ready means *defaults work for me, escape hatches exist for clients*. Don't add config knobs nobody will touch.
- Not "license it / productize it" — that's a separate decision. This is about *technical shape*, not commercial.

## Retroactive audits (priority order)

When time permits, audit existing repos against this checklist. Priority order (most-reused / highest-leverage first):

| Repo | Status | Action needed |
|---|---|---|
| `joestechsolutions/hermes-forge` | 🟡 partial | Bootloader had hardcoded paths (fixed 2026-05-25). Verify no remaining `/home/lurkr` references. |
| `joestechsolutions/ai-platform-bootloader` | 🟡 partial | Same as above. |
| `joestechsolutions/mempalace` | ✅ strong | "Local-first, entity-first, pluggable backends" baked into AGENTS.md. No retrofit needed. |
| `joestechsolutions/whisper-walkie` | 🟡 unknown | Check for branding hardcoded; add config layer. |
| `joestechsolutions/jts-pilot-intake-template` | 🟡 partial | Template (good) but has `joestechsolutions` references that should be `{{company_name}}`. |
| `joestechsolutions/jts-pilot-zw` | 🟢 client-grade | Per-client fork; has client-specific config (good). |
| `joestechsolutions/vans-archive-hair-salon-questionaire` | 🟢 client-grade | Per-client (good). |
| `joestechsolutions/semantic-galaxy` | ❓ unknown | TBD |

## For new repos

Before creating a new repo, add a `CONFIG.md` to the empty repo with:

```markdown
# Configuration

| What | Default (Joe's) | Override env | Override file |
|---|---|---|---|
| Company name | "Joe's Tech Solutions" | `COMPANY_NAME` | `config/brand.yaml` |
| Repo paths | `~/ai-stack/...` | — | `config/paths.yaml` |
| Brand colors | hex codes | — | `config/brand.yaml` |
| Provider keys | Joe's keys | `<PROVIDER>_API_KEY` | `.env` |
```

## When reviewing a PR

If a PR hardcodes a Joe-specific value, flag it. Suggested response:

> "Per build-once-use-many: this should be config-overridable, not hardcoded. Default can stay as Joe's, but add a `<VAR>` env / `<KEY>` config file entry so clients can override."
