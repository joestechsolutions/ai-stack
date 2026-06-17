# Skate Workshop v0.1 — "Ship This Weekend" Audit

**Repo:** `/home/lurkr/skate-workshop-clean` (absolute)
**Bundle ID:** `com.theskateworkshop.v2` (iOS) · `com.theskateworkshop.v2[.dev]` (Android dev)
**Apple:** `appleTeamId=U8U36PLUJ6` · `ascAppId=6755618537` · `joe@joestechsolutions.com` — already in `eas.json` submit block.
**Last commit:** `808af12 fix: UI polish + bug fixes for issues #149–#152` (2 commits total in this clean branch).

---

## Current State (1 paragraph)

This is iteration #7+. Two commits ("clean start" + polish) hide 10 months of rewrites. The repo is **104,913 LOC across 297 TS/TSX files**, a full Expo SDK 53 / RN 0.84 / TypeScript app with Expo Router, Supabase backend (81 migrations), Skia annotation engine, and ~50 services. **The locked workflow already exists and is the best-built thing in the repo**: `src/features/video-coach/` (~1,791 LOC) is a properly refactored `CoachReviewScreen` + `useCoachVideoPlayer` + `useTimedAnnotations` + `usePlaybackControls` + `CoachVideo` + `CoachCanvas` (Skia overlay) — entry point `app/coach-review.tsx`. Surrounding it is a *massive* surface area that must be cut: 14 tabs, role-select, email verification, waiver, tier management, invite codes, skatepark booking, gamification, messaging, social feed, billing, daily challenges. None of that is the locked workflow. `node_modules/` is missing — full `npm install --legacy-peer-deps` is step 0.

## Tech Stack Verdict

**KEEP. Do not rebuild.** This is the right base:
- Expo SDK 53 + RN 0.84 — EAS works, OTA updates configured (`u.expo.dev/e40c53ab-…`), Android+iOS from one codebase.
- `@shopify/react-native-skia` is already wired into `CoachCanvas` — exactly the right primitive for frame annotation.
- `expo-camera` (record) + `expo-video` (playback) + `expo-video-thumbnails` + `react-native-view-shot` — full video pipeline present.
- Supabase storage + `feedbackService` — upload/signed-URL flow is already implemented.
- EAS Build + Submit are pre-configured with real Apple creds. `eas build --profile preview` works today.

**Rebuild cost if we ditch:** 4–6 weeks to re-stand the Skia canvas, video pipeline, and Supabase schema. **Continue cost:** strip the surface, point one route at the existing `CoachReviewScreen`.

## v0.1 Scope (brutally small — record → annotate → send)

- **One screen, three steps, no tabs.** Single stack: `Record` → `Review` (the existing `CoachReviewScreen`) → `Send`.
- **Auth:** hardcoded coach (Willie) + athlete picker by email. No sign-up, no waiver, no email verification, no role-select, no invite codes. Drop `app/(auth)/*`, `app/role-select.tsx`, `app/(admin)/*`, `app/(shared)/*`.
- **Record:** `expo-camera` → save to local file. No filters, no trim UI, no trim controls. (Trim in v0.2 if needed.)
- **Annotate:** existing Skia canvas. Brush + color + text + undo. Frame-step is a stretch goal — keep current time-based annotations only.
- **Send:** upload to Supabase storage bucket `videos` (already exists), insert `feedback_videos` row with `coach_id` + `athlete_id` + `annotations` JSON, push notification via `expo-notifications` to athlete's stored token. Existing `feedbackService.createFeedback` does all of this.
- **Athlete side:** push notification deep-links to `app/coach-review.tsx` with `videoUri` — same screen, read-only annotations. No athlete-side recording, no library, no social.
- **Cut from v0.1 entirely:** booking, calendar, payments, messaging/chat, community feed, training plans, trick system, gamification, achievements, skatepark finder, daily challenges, billing, tier management, invite codes, admin dashboard, analytics, bug-report-to-GitHub.

## Build Steps for the Weekend (10 steps, ~2 days)

1. **Fri evening (1h):** `npm install --legacy-peer-deps` in `skate-workshop-clean/`. Confirm `npx expo-doctor` is clean. (EAS will rebuild `node_modules` anyway — this is just to validate `tsc` locally.)
2. **Fri evening (1h):** Add `EXPO_PUBLIC_SUPABASE_URL` + `EXPO_PUBLIC_SUPABASE_ANON_KEY` to EAS secrets (`eas env:create`). Confirm `feedback_videos` and `videos` bucket exist on the `opdylccotyxejwikbqyb` project (migrations 20251117* are already there).
3. **Sat morning (3h):** Carve a minimal `app/_layout.tsx` — root stack with just `/(record)` → `/review` → `/send`. Delete route imports for everything else. Keep the splash, ditch the auth gate.
4. **Sat morning (2h):** New `app/(record)/index.tsx` — `expo-camera` viewfinder, big record button, save URI to a Zustand store. ~80 LOC.
5. **Sat afternoon (3h):** New `app/review.tsx` — wraps existing `<CoachReviewScreen videoUri={...} />` from `src/features/video-coach`. Strip the athlete/portal logic from `coach-review.tsx` route; keep just videoUri + initial annotations params. Verify Skia draws.
6. **Sat afternoon (2h):** New `app/send.tsx` — athlete email picker (type-ahead against `profiles` table, role='athlete'), title field, single "Send" button that calls `feedbackService.createFeedback` + `expo-notifications` schedule. ~120 LOC.
7. **Sat evening (1h):** Athlete read view: when app opens via push deep link (`theskateworkshop://review?videoId=…`), route straight to `app/review.tsx` in read-only mode (gate the `addAnnotation` toolbar). Reuse the same screen.
8. **Sun morning (2h):** Polish: loading states, error toasts, Willie's avatar + name in the corner, app icon + splash (already in `app.json`). No new design work — reuse existing theme tokens.
9. **Sun afternoon (2h):** `eas build --platform ios --profile preview` (real device, no simulator). Test on Willie's iPhone via TestFlight internal group. Fix the inevitable two native build issues.
10. **Sun evening (1h):** `eas build --platform android --profile preview` (APK). Sideload to one Android device. If green: `eas submit --platform ios --profile preview --latest` to push to TestFlight. v0.1 is shipped.

## Blockers (ranked)

1. **Apple Developer Program membership** — must be active for `joe@joestechsolutions.com`. The `ascAppId` is already registered, so this is likely fine, but verify the membership hasn't lapsed ($99/yr). TestFlight internal testers can be added in minutes once the build is up.
2. **No working build in this iteration yet.** Only 2 commits and `node_modules/` is missing. There is a non-trivial chance `npx expo prebuild` + EAS will surface config drift (Skia version, Reanimated 3.17, RN 0.84 new-arch off). Budget 2–4h of fixup Sunday morning.
3. **Push notifications on iOS require a real APNs key** in EAS (`eas credentials` → iOS → Push Notifications). Free, but it's a setup step. If skipped, v0.1 still works — the link can be opened manually from the athlete's email.
4. **(Not a blocker, but risk)** `EXPO_PUBLIC_GOOGLE_MAPS_API_KEY` is hardcoded as a fallback in `app.config.js`. Leave it alone for v0.1 — skatepark finder is cut. If EAS env var isn't set, the key compiles as a string literal but is never used. Safe to ignore.

## Honest Verdict

**MAYBE — shippable in a weekend, but only with discipline.**

The repo is huge but the *one thing that matters* (`CoachReviewScreen` + Skia canvas + video pipeline) is already built and refactored cleanly. That is genuinely fortunate — most of the work that would eat a weekend is already done.

What will eat the weekend is **resisting scope creep**: the 14 tabs and 81 migrations will seduce anyone reviewing the codebase. The v0.1 commit should be *deletions* of `app/(auth)/*`, `app/(admin)/*`, `app/(shared)/*`, `app/(tabs)/*`, `app/(coach)/*`, `app/messages/*`, `app/feedback/*`, `app/onboarding/*`, `app/booking/*`, plus half of `src/services/` and `src/screens/`. The single biggest risk is Joe (or the next reviewer) opening `app/_layout.tsx` and trying to keep one of those.

**Recommendation: take the weekend. The 80/20 is already in the box. Spend Saturday cutting, Sunday building and submitting. Realistic ship: Sunday 9pm with one iOS TestFlight build and one Android APK. If Sunday's EAS build hits a native config wall that needs >4h, push to a 2-week sprint — but plan for the weekend.**

**If you want a 95% weekend guarantee instead of 80%:** budget the 2-week sprint, do the same cut, ship a known-good v0.1 by Day 10. The cut-list above is identical either way.
