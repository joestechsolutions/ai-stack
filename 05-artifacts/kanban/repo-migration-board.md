# board: repo-migration-2026-06-16

Context: Build-once-use-many retrofit + GitHub org cleanup (joestechsolutions=work, joblas=personal). Locked 2026-06-16.

## done
- [x] Retrofit jts-pilot-intake-template: 3 placeholders + config.template.yaml + For Clients section (commit 3c19b6e on joestechsolutions)
- [x] Update ~/bin/jts-pilot-new: env/flag overrides, joestechsolutions org default, apostrophe bug fix
- [x] Push ai-stack to joestechsolutions/ai-stack (7 commits, daily 02:00 push cron)
- [x] Set up daily ai-stack-push systemd timer
- [x] Add config.yaml to jts-pilot-zw (live ZW form, commit 896f30c on joestechsolutions)
- [x] Open hermes-forge PR fix/remove-home-lurkr-hardcode (branch on origin, awaiting Joe review)
- [x] Update ~/projects/archive-salon-app remote to joestechsolutions/vans-app (push after transfer)
- [x] Delete stale local ~/jts-pilot-zw (joblas clone, no working tree, no unpushed commits)
- [x] Classify skateboard-workshop-app: PERSONAL (hobby, stays at joblas)
- [x] Update ai-stack BOUNDARIES.md, PRINCIPLES.md, REPO-MIGRATION-ACTION.md
- [x] Update memory entry: build-once-use-many + GitHub rule
- [x] Kill opencode pid 89127 zombie (SIGTERM→SIGKILL, freed 732MB RAM, gateway unaffected)
- [x] Re-clone JTS site safety-net to ~/projects/security-scans/jts-site/joestechsolutions-nextjs/ (955MB, master @ 27878564)
- [x] Discover vans-app has 3 repos: joblas/vans-app (Expo MVP, force-pushed 2026-06-16), joestechsolutions/vans-app (301 redirect), joestechsolutions/vans-archive-hair-salon-questionaire (intake form source). Intake form LIVE at vans-app.pages.dev via Cloudflare Pages. See VANS-APP-TRANSFER-PLAN.md
- [x] Fix local archive-salon-app remote: was pointing at joestechsolutions/vans-app (redirect), now points at joblas/vans-app. In sync, no work lost.
- [x] Snapshot full intake form to ~/projects/archive-salon-app/legacy/questionnaire/ (gitignored, local-only reference): form HTML + served bytes + Cloudflare Worker + logos + ARCHIVE-NOTES.md
- [x] Decide: intake form is ARCHIVE MATERIAL (Joe: "we don't need that questionnaire anymore, we already got that information from her"). Form submissions went to joe@joestechsolutions.com via Resend (in email archive, not in repo)
- [x] Add .gitignore (legacy/ is local-only) + AGENTS.md note in archive-salon-app. Committed locally as af46ba8 — NOT PUSHED (waits for org transfer)
- [x] Fix hermes-health.sh: backup dir now uses fallback (`~/.local/backups/hermes` when `/var/backups` not writable) matching hermes-backup.sh; check_http() now accepts space-separated code lists; SSH/RPC port 22/135 excluded from "exposed" check (WSL host ports); iptables warning downgraded to "iptables not readable (WSL)" instead of "ACCEPT (permissive)" — all checks now pass
- [x] Create systemd timer hermes-daily-backup.timer (04:00 daily, RandomDelaySec 10min) — 2 backups now exist, will run nightly going forward

## blocked_needs_jo_browser
- [ ] Archive joblas/vans-archive-hair-salon-questionaire — actually `joestechsolutions/vans-archive-hair-salon-questionaire` (the questionnaire source repo) — Settings → ⚠️ Archive
- [ ] Delete vans-app.pages.dev Cloudflare Pages project (Workers & Pages → vans-app → Settings → Delete) — **BLOCKED: CF OAuth token expired 2025-10-19, need Joe to re-auth wrangler or paste API token at desk**
- [ ] Delete joestechsolutions/vans-app redirect (Settings → ⚠️ Delete) — required before transfer can land at the canonical name
- [ ] Transfer joblas/vans-app → joestechsolutions/vans-app (Settings → Transfer) — NOW SAFE: form is archive material, no live deploys
- [ ] After transfer: push local commits af46ba8 (.gitignore + AGENTS.md update) to new joestechsolutions/vans-app
- [ ] After transfer: optionally delete joblas/vans-app source (code is at joestechsolutions now)
- [ ] Transfer joblas/joestechsolutions-nextjs → joestechsolutions/jts-site (SEE JTS-SITE-TRANSFER-PLAN.md for 4-stage zero-downtime sequence)
- [ ] Delete joblas/jts-pilot-zw (duplicate of joestechsolutions one)
- [ ] Delete joblas/jts-pilot-intake-template (marked REPO RENAMED)
- [ ] Delete joblas/jts-pilot-demo-client (test of spin-up, README still says "this is a template")
- [ ] Review + merge PR fix/remove-home-lurkr-hardcode on hermes-forge

## blocked_needs_jo_decision
- [ ] (RESOLVED) Was the force-push to joblas/vans-app intentional? YES — Joe confirmed Expo MVP is the new canonical home, questionnaire is archive material
