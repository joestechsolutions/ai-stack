# AI-Stack Consolidation Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

> ⚠️ **REVISION (2026-07-03, mid-execution):** Task 1's Step 1.1 originally said to move the
> real `~/.fcc/.env` into `ai-stack/02-routing/fcc.env` and symlink `~/.fcc/.env` back to it.
> **That was a security defect** — `~/.fcc/.env` holds live API keys and `joestechsolutions/ai-stack`
> is a **PUBLIC** repo with a daily auto-push, so the original step would have published the
> keys. The implemented fix inverts the design to match `02-routing/hermes-config.yaml`:
> the **real file stays at `~/.fcc/.env`** (gitignored, never committed) and
> `ai-stack/02-routing/fcc.env` is a **symlink → `~/.fcc/.env`** (tracked as mode `120000`,
> no secret bytes in git). Task 2's "commit" step was likewise a defect — `.env` is
> `.gitignore`'d; the strip is an uncommitted local edit and `.env` was untracked via
> commit `f4dd3d53`. Tasks 1 & 2 are complete in this corrected form; Task 3 proceeds as
> written (its symlinks point at non-secret dirs and are safe). See the design doc's
> Revision section for full rationale.

**Goal:** Make `~/ai-stack` the single source of truth for the live AI stack by relocating the fcc routing env into `02-routing/` and symlinking the missing AI infra into `04-reference/`.

**Architecture:** Approach A (symlink consolidation) from the approved design doc. No code changes to fcc or Hermes — python-dotenv follows the symlinked env transparently, and `04-reference/` gains read-only symlinks matching the existing pattern. The dual-`.env` footgun is removed by stripping already-dead tier-routing lines from `~/free-claude-code/.env`.

**Tech Stack:** bash, symlinks, python-dotenv (consumer, unchanged), systemd user unit `free-claude-code.service`, git (`~/ai-stack` → `joestechsolutions/ai-stack`, `~/free-claude-code` local).

## Global Constraints

- **No code changes to fcc (`~/free-claude-code/api/`, `config/`) or Hermes** — only config files, symlinks, and comments move. The approved spec rules out Approach C.
- **Leave `FCC_SMOKE_MODEL_*` lines in `~/free-claude-code/.env` untouched** — they are smoke-test config, not live tier routing, and not all are overridden by `~/.fcc/.env`. Stripping them is out of scope.
- **Only these `MODEL_*` tier-routing lines may be stripped from `~/free-claude-code/.env`:** `MODEL`, `MODEL_OPUS`, `MODEL_SONNET`, `MODEL_HAIKU`, `MODEL_VISION`, `MODEL_FALLBACKS` — each is also defined in `~/.fcc/.env` (verified 2026-07-03), so they are already dead via later-wins.
- **GitHub account consolidation and repo merging are out of scope** — not selected by the user.
- **Phase 2 (generate `fcc.env` from `ROUTING.yaml`) is deferred** — do not build `sync-fcc-env.sh` or add a `claude_tiers:` block in this plan.
- **No git worktree** — this work touches live config (`~/.fcc/.env` symlink, systemd restart) across multiple repos; a worktree cannot isolate live-config effects. Execute directly.
- **fcc service restart window:** `systemctl --user restart free-claude-code.service` takes ~5s; this Claude Code session routes through fcc and will briefly interrupt. Restart only in tasks that change live routing (Tasks 1 and 2).

---

## Task 1: Relocate the live fcc env into `02-routing/` + symlink + cross-ref header

**Files:**
- Move: `~/.fcc/.env` → `~/ai-stack/02-routing/fcc.env` (content preserved byte-for-byte)
- Create: `~/.fcc/.env` (symlink → `~/ai-stack/02-routing/fcc.env`)
- Modify: `~/ai-stack/02-routing/fcc.env` (prepend cross-reference header comment)
- Commit to: `~/ai-stack` (the new `02-routing/fcc.env`)

**Interfaces:**
- Consumes: the existing `~/.fcc/.env` (the live fcc routing override, currently the source of truth for `MODEL_OPUS/MODEL_VISION`/etc.).
- Produces: a symlinked `~/.fcc/.env` that fcc's `config.settings.managed_env_path()` resolves transparently; the canonical routing file now lives at `~/ai-stack/02-routing/fcc.env` (used by Task 2's verification and by the memory update in Task 4).

- [ ] **Step 1: Verify the source env exists and capture its fingerprint**

Run:
```bash
test -f ~/.fcc/.env && echo "source exists" || echo "MISSING — abort"
sha256sum ~/.fcc/.env
wc -l ~/.fcc/.env
```
Expected: `source exists`, a sha256 line, and a line count (≈110+). Save the sha256 — the moved file must match it.

- [ ] **Step 2: Move the env into ai-stack and symlink back**

Run:
```bash
mv ~/.fcc/.env ~/ai-stack/02-routing/fcc.env
ln -s ~/ai-stack/02-routing/fcc.env ~/.fcc/.env
readlink -f ~/.fcc/.env
```
Expected: `/home/lurkr/ai-stack/02-routing/fcc.env`

- [ ] **Step 3: Verify the moved file is byte-identical to the original**

Run:
```bash
sha256sum ~/ai-stack/02-routing/fcc.env
```
Expected: the **exact same sha256** captured in Step 1. If it differs, abort and investigate (the move should be lossless).

- [ ] **Step 4: Prepend the cross-reference header to `fcc.env`**

The header documents where the companion Hermes routing entries live. Run:
```bash
python3 - <<'EOF'
from pathlib import Path
p = Path.home() / "ai-stack/02-routing/fcc.env"
header = (
    "# fcc.env — live Claude-tier → model routing for the fcc proxy.\n"
    "# Canonical home: ~/ai-stack/02-routing/fcc.env (symlinked from ~/.fcc/.env).\n"
    "# Companion Hermes task→model routing lives in ./ROUTING.yaml —\n"
    "#   when a model ref changes here, check ROUTING.yaml task_routing: / models: too.\n"
    "# Phase 2 (deferred): generate these MODEL_* lines from ROUTING.yaml.\n"
    "\n"
)
text = p.read_text()
if not text.startswith("# fcc.env —"):
    p.write_text(header + text)
    print("header added")
else:
    print("header already present")
EOF
```
Expected: `header added`

- [ ] **Step 5: Restart fcc and confirm health**

Run:
```bash
systemctl --user restart free-claude-code.service
sleep 4
curl -s -o /dev/null -w "health: HTTP %{http_code}\n" http://localhost:8082/health
```
Expected: `health: HTTP 200`

- [ ] **Step 6: Replay the routing check — prove the symlinked env loads**

Run:
```bash
python3 - <<'EOF'
import base64, json, urllib.request
png = base64.b64encode(b"\x89PNG\r\n\x1a\n\x00\x00\x00\rIHDR\x00\x00\x00\x01\x00\x00\x00\x01\x08\x06\x00\x00\x00\x1f\x15\xc4\x89\x00\x00\x00\nIDATx\x9cc\x00\x01\x00\x00\x05\x00\x01\r\n-\xb4\x00\x00\x00\x00IEND\xaeB`\x82").decode()
def go(content):
    body={"model":"claude-opus-4-20250514","max_tokens":16,"messages":[{"role":"user","content":content}]}
    req=urllib.request.Request("http://localhost:8082/v1/messages",data=json.dumps(body).encode(),headers={"Content-Type":"application/json","x-api-key":"freecc"})
    with urllib.request.urlopen(req,timeout=60) as r:
        raw=r.read(); i=raw.find(b'"model":"'); return r.status, raw[i:i+30].decode() if i>=0 else "?"
is_,im = go([{"type":"text","text":"hi"},{"type":"image","source":{"type":"base64","media_type":"image/png","data":png}}])
ts,tm = go("say hi")
print(f"image: HTTP {is_} | {im}")
print(f"text : HTTP {ts} | {tm}")
print("PASS" if ("kimi" in im and "glm-5.2" in tm) else "FAIL")
EOF
```
Expected:
```
image: HTTP 200 | "model":"kimi-k2.7-code","cont
text : HTTP 200 | "model":"glm-5.2","content":[]
PASS
```
If `FAIL` or any non-200: the symlink didn't resolve. Run `readlink -f ~/.fcc/.env` (must show `~/ai-stack/02-routing/fcc.env`) and `cat ~/ai-stack/02-routing/fcc.env | grep MODEL_OPUS` (must show `ollama/glm-5.2:cloud`). Fix before continuing.

- [ ] **Step 7: Commit to ai-stack**

Run:
```bash
git -C ~/ai-stack add 02-routing/fcc.env
git -C ~/ai-stack commit -m "consolidation: relocate live fcc routing env into 02-routing/ + symlink

~/.fcc/.env now symlinks to ~/ai-stack/02-routing/fcc.env (dotenv follows the
symlink transparently). Cross-ref header added pointing to ROUTING.yaml.
Verified: fcc /health 200; image→kimi-k2.7-code; text→glm-5.2.

Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>"
```
Expected: a single commit created on `~/ai-stack` main.

---

## Task 2: Strip dead tier-routing `MODEL_*` from `~/free-claude-code/.env` + pointer comment

**Files:**
- Modify: `~/free-claude-code/.env` (remove 6 `MODEL_*` lines, add a pointer comment; leave all else including `FCC_SMOKE_MODEL_*` untouched)
- Commit to: `~/free-claude-code` (currently on `main`)

**Interfaces:**
- Consumes: the symlinked `~/.fcc/.env` from Task 1 (it now provides the live `MODEL_*` routing, so stripping the dead copies here does not change live behavior).
- Produces: a `~/free-claude-code/.env` with no tier-routing lines — the single source of truth for live routing is now unambiguously `~/ai-stack/02-routing/fcc.env`.

**Depends on:** Task 1 (the symlink must be providing routing before these lines are removed, or fcc loses its model mapping on the next restart).

- [ ] **Step 1: Snapshot the current `MODEL_*` tier lines (for reversal if needed)**

Run:
```bash
grep -nE '^(MODEL|MODEL_OPUS|MODEL_SONNET|MODEL_HAIKU|MODEL_VISION|MODEL_FALLBACKS)=' ~/free-claude-code/.env | tee /tmp/fcc-model-lines-backup.txt
```
Expected: 6 lines (MODEL, MODEL_OPUS, MODEL_SONNET, MODEL_HAIKU, MODEL_VISION, MODEL_FALLBACKS) with line numbers. Save this for reversal. If any line is missing, it was already absent — proceed (the strip is idempotent).

- [ ] **Step 2: Confirm `FCC_SMOKE_MODEL_*` lines are present and will be left alone**

Run:
```bash
grep -cE '^FCC_SMOKE_MODEL_' ~/free-claude-code/.env
```
Expected: a count ≥ 1 (the smoke-test config). This count must be **unchanged** after Step 4.

- [ ] **Step 3: Remove exactly the 6 tier-routing lines and insert the pointer comment**

Run:
```bash
python3 - <<'EOF'
from pathlib import Path
p = Path.home() / "free-claude-code/.env"
text = p.read_text()
strip_keys = ("MODEL=", "MODEL_OPUS=", "MODEL_SONNET=", "MODEL_HAIKU=",
              "MODEL_VISION=", "MODEL_FALLBACKS=")
lines = text.splitlines(keepends=True)
kept = [ln for ln in lines if not any(ln.startswith(k) for k in strip_keys)]
pointer = (
    "# Live model routing lives in ~/ai-stack/02-routing/fcc.env\n"
    "# (symlinked from ~/.fcc/.env, which overrides this file). Edit there.\n"
)
if "# Live model routing lives in ~/ai-stack/02-routing/fcc.env" not in "".join(kept):
    # place the pointer where the MODEL_OPUS block used to be — heuristic: first MODEL_ occurrence line index
    kept_with_pointer = []
    inserted = False
    for ln in kept:
        if not inserted and ln.startswith("#") is False and "=" in ln and not ln.startswith("FCC_SMOKE") and not ln.startswith("ANTHROPIC") and not ln.startswith("OLLAMA") and not ln.startswith("OPENROUTER") and not ln.startswith("DEEPSEEK") and not ln.startswith("NVIDIA"):
            # insert pointer before the first remaining non-comment, non-known-key line
            kept_with_pointer.append(pointer)
            inserted = True
        kept_with_pointer.append(ln)
    kept = kept_with_pointer if inserted else [pointer] + kept
p.write_text("".join(kept))
print("done")
EOF
```
Expected: `done`

If the heuristic pointer placement is awkward, that's fine — the comment just needs to exist near where the MODEL_* lines were. Content correctness matters, not exact line number.

- [ ] **Step 4: Verify the strip + the smoke models are intact**

Run:
```bash
echo "remaining MODEL_* tier lines (must be 0):"
grep -cE '^(MODEL|MODEL_OPUS|MODEL_SONNET|MODEL_HAIKU|MODEL_VISION|MODEL_FALLBACKS)=' ~/free-claude-code/.env
echo "remaining FCC_SMOKE_MODEL_* lines (must equal Step 2 count):"
grep -cE '^FCC_SMOKE_MODEL_' ~/free-claude-code/.env
echo "pointer present:"
grep -c "# Live model routing lives in ~/ai-stack/02-routing/fcc.env" ~/free-claude-code/.env
```
Expected:
```
remaining MODEL_* tier lines (must be 0):
0
remaining FCC_SMOKE_MODEL_* lines (must equal Step 2 count):
<same count as Step 2>
pointer present:
1
```
If tier lines count ≠ 0, the strip missed something — re-run Step 3. If smoke count changed, **stop** — that's a bug; restore from `/tmp/fcc-model-lines-backup.txt` is not enough (that only has MODEL lines), so restore `~/free-claude-code/.env` from git: `git -C ~/free-claude-code checkout -- .env` and re-attempt.

- [ ] **Step 5: Restart fcc and replay the routing check — proves the symlink provides routing**

Run:
```bash
systemctl --user restart free-claude-code.service
sleep 4
curl -s -o /dev/null -w "health: HTTP %{http_code}\n" http://localhost:8082/health
python3 - <<'EOF'
import base64, json, urllib.request
png = base64.b64encode(b"\x89PNG\r\n\x1a\n\x00\x00\x00\rIHDR\x00\x00\x00\x01\x00\x00\x00\x01\x08\x06\x00\x00\x00\x1f\x15\xc4\x89\x00\x00\x00\nIDATx\x9cc\x00\x01\x00\x00\x05\x00\x01\r\n-\xb4\x00\x00\x00\x00IEND\xaeB`\x82").decode()
def go(content):
    body={"model":"claude-opus-4-20250514","max_tokens":16,"messages":[{"role":"user","content":content}]}
    req=urllib.request.Request("http://localhost:8082/v1/messages",data=json.dumps(body).encode(),headers={"Content-Type":"application/json","x-api-key":"freecc"})
    with urllib.request.urlopen(req,timeout=60) as r:
        raw=r.read(); i=raw.find(b'"model":"'); return r.status, raw[i:i+30].decode() if i>=0 else "?"
is_,im = go([{"type":"text","text":"hi"},{"type":"image","source":{"type":"base64","media_type":"image/png","data":png}}])
ts,tm = go("say hi")
print(f"image: HTTP {is_} | {im}")
print(f"text : HTTP {ts} | {tm}")
print("PASS" if ("kimi" in im and "glm-5.2" in tm) else "FAIL")
EOF
```
Expected:
```
health: HTTP 200
image: HTTP 200 | "model":"kimi-k2.7-code","cont
text : HTTP 200 | "model":"glm-5.2","content":[]
PASS
```
`PASS` here confirms the dead `MODEL_*` copies were safe to remove — the symlinked `~/.fcc/.env` is providing the routing.

- [ ] **Step 6: Commit to free-claude-code**

Run:
```bash
git -C ~/free-claude-code add .env
git -C ~/free-claude-code commit -m "config: strip dead MODEL_* tier lines from .env (now in ai-stack/02-routing/fcc.env)

~/.fcc/.env overrides this file (later-wins via python-dotenv), so these
MODEL_OPUS/SONNET/HAIKU/VISION/FALLBACKS lines were already dead. Removing
them kills the dual-.env footgun. FCC_SMOKE_MODEL_* left untouched. Live
routing verified unchanged (image→kimi, text→glm-5.2) via the symlinked env.

Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>"
```
Expected: a single commit on `~/free-claude-code` main.

---

## Task 3: Add `04-reference/` symlinks for the missing AI infra

**Files:**
- Create: `~/ai-stack/04-reference/reel-mcp` → `~/reel-mcp`
- Create: `~/ai-stack/04-reference/claude-video` → `~/claude-video`
- Create: `~/ai-stack/04-reference/claude-skills` → `~/.claude/skills`
- Create: `~/ai-stack/04-reference/hermes-skills` → `~/.hermes/skills`
- Create: `~/ai-stack/04-reference/claude-settings.json` → `~/.claude/settings.json`
- Commit to: `~/ai-stack`

**Interfaces:**
- Consumes: the existing `04-reference/` symlink pattern (siblings `free-claude-code`, `mempalace`, `obsidian`, `open-design`).
- Produces: a `04-reference/` directory from which the entire AI stack is reachable; the daily `git -C ~/ai-stack` snapshot now includes these as pointer entries.

**Depends on:** nothing (independent of Tasks 1–2; structural, not routing).

- [ ] **Step 1: Verify all 5 targets exist**

Run:
```bash
for t in ~/reel-mcp ~/claude-video ~/.claude/skills ~/.hermes/skills ~/.claude/settings.json; do
  test -e "$t" && echo "OK  $t" || echo "MISSING $t"
done
```
Expected: 5 `OK` lines. If any `MISSING`, abort — do not create a symlink to a nonexistent target (it would be a dangling link).

- [ ] **Step 2: Create the 5 symlinks**

Run:
```bash
ln -s ~/reel-mcp              ~/ai-stack/04-reference/reel-mcp
ln -s ~/claude-video          ~/ai-stack/04-reference/claude-video
ln -s ~/.claude/skills        ~/ai-stack/04-reference/claude-skills
ln -s ~/.hermes/skills        ~/ai-stack/04-reference/hermes-skills
ln -s ~/.claude/settings.json ~/ai-stack/04-reference/claude-settings.json
```
Expected: no output (all five `ln -s` succeed). If any fails with "File exists", that symlink already exists — run `readlink -f ~/ai-stack/04-reference/<name>` to confirm it points to the right target; if it does, skip; if not, `rm` the bad link and re-run.

- [ ] **Step 3: Verify each symlink resolves to the correct target**

Run:
```bash
for l in reel-mcp claude-video claude-skills hermes-skills claude-settings.json; do
  echo "$l -> $(readlink -f ~/ai-stack/04-reference/$l)"
done
```
Expected:
```
reel-mcp -> /home/lurkr/reel-mcp
claude-video -> /home/lurkr/claude-video
claude-skills -> /home/lurkr/.claude/skills
hermes-skills -> /home/lurkr/.hermes/skills
claude-settings.json -> /home/lurkr/.claude/settings.json
```

- [ ] **Step 4: Confirm ai-stack git sees them as symlinks (pointer entries, not copied trees)**

Run:
```bash
git -C ~/ai-stack add 04-reference/
git -C ~/ai-stack diff --cached --stat
```
Expected: 5 new entries under `04-reference/`, each shown as a new file (mode `120000` = symlink). No large blob additions (if git shows thousands of insertions, the symlinks were created as regular files/copies — delete and re-create with `ln -s`).

- [ ] **Step 5: Commit to ai-stack**

Run:
```bash
git -C ~/ai-stack commit -m "consolidation: symlink reel-mcp, claude-video, skills dirs, settings into 04-reference

Whole AI stack now reachable from ~/ai-stack. Matches the existing
04-reference/ read-only-symlink pattern (free-claude-code, mempalace,
obsidian, open-design). Daily snapshot will back these pointers up.

Co-Authored-By: Claude Opus 4.8 (1M context) <noreply@anthropic.com>"
```
Expected: a single commit on `~/ai-stack` main.

---

## Task 4: Update the `fcc-managed-env-override` memory to the new canonical path

**Files:**
- Modify: `~/.claude/projects/-home-lurkr-free-claude-code/memory/fcc-managed-env-override.md`

**Interfaces:**
- Consumes: Task 1's new canonical path (`~/ai-stack/02-routing/fcc.env`).
- Produces: an accurate memory record so future sessions know where the live fcc routing lives (the old memory said "edit `~/.fcc/.env`", which is now a symlink — the canonical edit target is the ai-stack path).

**Depends on:** Task 1 (the relocation must be done and verified before the memory is updated to describe it).

- [ ] **Step 1: Read the current memory file**

Run:
```bash
cat ~/.claude/projects/-home-lurkr-free-claude-code/memory/fcc-managed-env-override.md
```
Read the output. Note the line that says editing `~/.fcc/.env` is the way to change live model routing — that is what needs updating.

- [ ] **Step 2: Update the memory body to point at the canonical ai-stack path**

Using the Edit tool, change the instruction from editing `~/.fcc/.env` to editing `~/ai-stack/02-routing/fcc.env` (symlinked from `~/.fcc/.env`). Concretely, replace the passage instructing edits to `~/.fcc/.env` with:

```
**Canonical home (since 2026-07-03):** the live fcc routing env is
`~/ai-stack/02-routing/fcc.env`, symlinked from `~/.fcc/.env`. Edit the
ai-stack path (it's backed up by the daily snapshot); `~/.fcc/.env` is just
the symlink fcc reads through. Restart to apply:
`systemctl --user restart free-claude-code.service`. The dual-.env footgun
is gone — `~/free-claude-code/.env` no longer carries MODEL_* tier lines.
```

- [ ] **Step 3: Verify the memory reflects the new path**

Run:
```bash
grep -c "ai-stack/02-routing/fcc.env" ~/.claude/projects/-home-lurkr-free-claude-code/memory/fcc-managed-env-override.md
```
Expected: ≥ 1 (the new canonical path appears). Also confirm no instruction to "edit `~/.fcc/.env`" remains as the primary edit target (the symlink path may still appear as a mention, but the *canonical* edit instruction must point at ai-stack).

- [ ] **Step 4: No commit (memory files are not in a git repo)**

Memory files live under `~/.claude/projects/.../memory/` and are not tracked by git. No commit step. The `MEMORY.md` index line for this memory already exists and its one-line hook still applies ("edit the managed file + restart to change live model routing"); optionally refine it to "edit `~/ai-stack/02-routing/fcc.env` + restart."

---

## Self-Review (run after writing the plan — recorded here per skill)

**1. Spec coverage:**
- Spec Part 1 Step 1.1 (relocate + symlink) → Task 1 Steps 2–3 ✓
- Spec Part 1 Step 1.2 (strip dead MODEL_*, leave smoke models, pointer comment) → Task 2 ✓
- Spec Part 1 Step 1.3 (cross-ref header in fcc.env) → Task 1 Step 4 ✓
- Spec Part 1 Verification (restart, health, image/text routing replay) → Task 1 Step 5–6 and Task 2 Step 5 ✓
- Spec Part 2 (5 symlinks) → Task 3 ✓
- Spec Part 2 Optional follow-up (skills INDEX.txt) → intentionally not a task (spec marked it non-blocking) ✓
- Spec Out of scope (accounts, repo merge, Phase 2) → Global Constraints ✓
- Spec Post-change housekeeping (memory update + ai-stack commit) → Task 4 + per-task commits ✓

**2. Placeholder scan:** No TBD/TODO. Every step has exact commands and expected output. The one heuristic (pointer-comment placement in Task 2 Step 3) is explicitly called out as a heuristic where content matters more than line number — not a placeholder, a documented tolerance.

**3. Type/name consistency:** `~/ai-stack/02-routing/fcc.env` is used identically across Tasks 1, 2, 4. The 6 `MODEL_*` keys to strip are listed identically in Global Constraints and Task 2. The 5 symlink names in Task 3 match the spec's table exactly. Routing-check script content is identical between Task 1 Step 6 and Task 2 Step 5 (intentional repeat per skill's "engineer may read tasks out of order" rule).

No issues found; no fixes needed inline.