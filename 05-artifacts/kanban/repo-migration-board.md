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

## blocked_needs_jo_browser
- [ ] Transfer joblas/vans-app → joestechsolutions/vans-app (Settings → Transfer) — **verify Cloudflare Pages deploy source FIRST**
- [ ] Transfer joblas/joestechsolutions-nextjs → joestechsolutions/jts-site (SEE JTS-SITE-TRANSFER-PLAN.md for 4-stage zero-downtime sequence)
- [ ] Delete joblas/jts-pilot-zw (duplicate of joestechsolutions one)
- [ ] Delete joblas/jts-pilot-intake-template (marked REPO RENAMED)
- [ ] Delete joblas/jts-pilot-demo-client (test of spin-up, README still says "this is a template")
- [ ] Review + merge PR fix/remove-home-lurkr-hardcode on hermes-forge

## blocked_needs_jo_decision
- [ ] vans-app.pages.dev: which GitHub repo is Cloudflare watching? (CF admin needed — check Pages project → Settings → Builds → Connected repository)
- [ ] Is the live intake form still wanted, or does the Expo MVP supersede it?
- [ ] Was the force-push to joblas/vans-app intentional? (Confirms it as the new canonical home)
