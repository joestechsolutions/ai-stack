# Vans App (Archive Salon) — MVP Audit & Path to TestFlight

**Date:** 2026-06-16 · **For:** Joe (blasj408@gmail.com) · **Client:** Van / Archive salon

---

## Current state

A functional, minimal Expo React Native MVP is committed and stable. The app is a **3-screen, local-first formula capture tool** (List → Capture → Detail) with custom visual sliders, segmented developer-volume picker, and camera/library photo capture. Persistence is a single JSON file in `expo-file-system`'s document directory. It runs in Expo Go today. **It has never been built for a device** — there is no `eas.json`, no bundle ID, no Apple Developer signing, no `app.json` `ios.bundleIdentifier` or `android.package`. The 2 unpushed commits (`af46ba8` AGENTS.md note, `0021139` .gitignore) need to land after the org transfer (`joblas/vans-app` → `joestechsolutions/vans-app`).

## Tech stack (LOCKED per AGENTS.md)

- **Expo SDK 46.0.21** · React Native 0.69.9 · React 18.0.0 · TypeScript strict
- UI: `@react-native-community/slider` 4.2.3, `expo-haptics` ~11.3.0, `expo-image-picker` ~13.3.1
- Storage: `expo-file-system` (transitive) → JSON file in document dir
- No react-navigation, no NativeWind, no reanimated (fence list holds)

## What's already working

| Area | File | LOC | Status |
|---|---|---|---|
| 3-screen router (`list` / `capture` / `detail`) | `App.tsx` | 63 | ✅ |
| `FormulaList` — pill cards + FAB | `src/screens/FormulaList.tsx` | 173 | ✅ |
| `FormulaCapture` — 8-field form, validation, haptics | `src/screens/FormulaCapture.tsx` | 221 | ✅ |
| `FormulaDetail` — hero photo + stat grid, long-press delete | `src/screens/FormulaDetail.tsx` | 244 | ✅ |
| `RatioSlider` (haptic on step) | `src/components/RatioSlider.tsx` | 51 | ✅ |
| `VolumePicker` (10/20/30/40 vol pill) | `src/components/VolumePicker.tsx` | 49 | ✅ |
| `PhotoCapture` (camera → library fallback) | `src/components/PhotoCapture.tsx` | 63 | ✅ |
| `formulaStore` (read/write/update/remove, write queue) | `src/services/formulaStore.ts` | 73 | ✅ |
| `Formula` + `ClientProfile` types | `src/types.ts` | 23 | ✅ |
| Editorial theme (colors + typography) | `src/theme/` | 15 | ✅ |
| Icons + splash (5 PNGs) | `assets/` | — | ✅ |
| **`ClientList.tsx`** | `src/_archive/` | — | 🅿️ parked, not imported |

**Totals:** 26 files (excl. legacy + .git), 1,189 LOC total, 912 LOC in `src/`.

## What's missing for a TestFlight build

1. **`eas.json`** — no EAS Build config exists at all
2. **`app.json` iOS bundle** — missing `ios.bundleIdentifier` (e.g. `com.joestechsolutions.archivesalon`)
3. **`app.json` Android package** — missing `android.package` (e.g. `com.joestechsolutions.archivesalon`)
4. **Apple Developer account** — Joe needs a paid account ($99/yr) tied to `blasj408@gmail.com` or a JTS team
5. **EAS Apple credentials** — `eas build --platform ios` will prompt; needs ASC API key OR Apple ID + app-specific password
6. **App Store Connect app record** — bundle ID must be registered on App Store Connect first
7. **Privacy manifest / usage descriptions** — `NSCameraUsageDescription` in `app.json` `ios.infoPlist` (required since May 2024 for `expo-image-picker`)
8. **`eas-cli` + login** — `npm i -g eas-cli && eas login`
9. **`expo-build-properties` (optional but recommended)** — to set iOS deployment target ≥ 13.4 (RN 0.69 requirement) and enable New Architecture off
10. **First-run smoke test on a real device** — Expo Go is not TestFlight; need a dev client build at minimum
11. **Splash screen plugin** — currently no `splash` block in `app.json`; will use the default blue Expo screen unless added
12. **EAS project** — `eas init` to create the project ID and link to `joestechsolutions/vans-app` after transfer

## v0.1 scope (no feature creep)

- ✅ In: 3 screens, 3 components, JSON persistence, local photo URIs
- 🅿️ Out (parked per AGENTS.md): Scan-to-Trash inventory, Real-Time Prosperity Engine, Supabase sync, auth, multi-user, analytics, editorial polish, `ClientList.tsx`

## Build steps (to TestFlight)

1. **Finish org transfer** (Joe browser): archive questionnaire repo, delete CF Pages project, delete the JTS redirect repo, transfer `joblas/vans-app` → `joestechsolutions/vans-app`. See `~/ai-stack/VANS-APP-TRANSFER-PLAN.md`.
2. **Push local commits:** `cd ~/projects/archive-salon-app && git push -u origin main` (after transfer).
3. **Add bundle IDs to `app.json`:** `ios.bundleIdentifier` + `android.package` + `ios.infoPlist.NSCameraUsageDescription` + `splash` block.
4. **Create `eas.json`** with `preview` and `production` profiles, submit config pointing to `production` for TestFlight.
5. **Install & login:** `npm i -g eas-cli && eas login` (Joe's Expo account tied to `blasj408@gmail.com`).
6. **Init EAS project:** `eas init` (creates project at `expo.dev/joestechsolutions/vans-app`).
7. **Configure credentials:** `eas credentials` → upload Apple ASC API key OR sign in with Apple ID.
8. **First build:** `eas build --platform ios --profile preview` → verify on TestFlight (internal testers).
9. **Android internal track:** `eas build --platform android --profile preview` → upload to Google Play internal testing.
10. **Iterate:** fix any install/runtime issues from device feedback, then `eas build --profile production` and `eas submit -p ios` for TestFlight beta review.

## Top 3 blockers

1. **Apple Developer account** — without it, no signing, no TestFlight. Joe needs to enroll/transfer before any iOS build is possible. (Cost + admin time.)
2. **Org transfer not complete** — `joblas/vans-app` is still the canonical remote, and `joestechsolutions/vans-app` is a redirect. EAS project should land on the final URL from the start to avoid renaming later. Also, local commits are **2 commits ahead of origin** and can't push until transfer is done.
3. **No `eas.json` + missing bundle IDs / `NSCameraUsageDescription`** — zero EAS plumbing exists. Must be added before the first `eas build`. The 2-minute fix, but it's the gate.

## Honest "TestFlight in N days" estimate

**3–5 business days**, assuming:

- Day 1: Joe finishes org transfer + enrolls Apple Developer (or it's already done under a JTS team)
- Day 1–2: write `eas.json`, add bundle IDs + privacy strings, `eas init`, configure ASC credentials
- Day 2–3: first `eas build --platform ios --profile preview`, fix any iOS-specific runtime issue (RN 0.69 + Expo 46 are 2022-era; expect 1–2 small native hiccups — likely `react-native-slider` autolinking or haptics on a newer iOS)
- Day 3–4: install on a real device, smoke-test all 3 screens + photo capture, push to TestFlight internal
- Day 4–5: hand to Van for sanity check; address any "this is ugly" feedback that fits in the no-polish scope

**Stretch to 7 days** if the Apple Developer enrollment is a fresh individual signup (Apple reviews ID). **Could be 1 day** if the account already exists and EAS credentials are configured from a prior JTS project. The code itself is shippable as-is — this is all DevOps + sign-in work, not engineering.

---

**Repo path:** `/home/lurkr/projects/archive-salon-app/`
**Remote (pre-transfer):** `https://github.com/joblas/vans-app.git`
**Target remote (post-transfer):** `https://github.com/joestechsolutions/vans-app.git`
**HEAD:** `af46ba8` · **MVP commit:** `a977298` · **Branch:** `main` (2 commits ahead of origin)
**Working tree:** clean · **`legacy/` is gitignored** · **`src/_archive/` parked**

**Org transfer context:** see `~/ai-stack/VANS-APP-TRANSFER-PLAN.md`. Do not push local commits until the transfer lands.
