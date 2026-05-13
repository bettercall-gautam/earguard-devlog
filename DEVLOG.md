# EarGuard — Developer Log (DEVLOG)

> *This devlog documents my learning process while building EarGuard. It's a personal knowledge base, not a runnable project (yet).*

> Chronological record of the EarGuard project: decisions made, tools installed, errors hit, and milestones reached. Spans 27 Mar – 4 May 2026.

---

## Status

| Field | Value |
|-------|-------|
| Status | 🟡 In Progress |
| Current Phase | Tutorial (berealclone — learning React Native before building EarGuard) |
| Next Step | Complete berealclone tutorial, then begin EarGuard from scratch |
| Last Updated | 4 May 2026 |
| Platform | Android (Windows dev environment) |
| SDK | Expo SDK 54 |

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Tech Stack](#2-tech-stack)
3. [Known Limitations](#3-known-limitations)
4. [Environment Setup](#4-environment-setup)
5. [Decisions Log](#5-decisions-log)
6. [Daily Entries](#6-daily-entries)
7. [Roadmap](#7-roadmap)
8. [Milestones](#8-milestones)
9. [Resources](#9-resources)

---

## 1. Project Overview

**EarGuard** is a React Native + Expo Android app for hearing protection.

**Core features (planned):**
- Detects headphone plug-in and Bluetooth connection
- Distinguishes physically connected vs. actively playing audio using `AudioManager.isStreamActive()`
- Tracks cumulative listening duration per session and per day
- Sends alerts when safe listening thresholds are crossed
- Whitelist feature for car Bluetooth to avoid false alerts
- Local database for listening history

**Planned architecture:**
- Foreground Service — keeps the timer alive when the screen is off
- BroadcastReceiver — detects headphone plug/unplug events
- Room Database — local persistence (SQL-compatible)
- Notification system — threshold alerts

**Project folder:**
```
C:\Users\Win\Desktop\Coding_Stuff\React_Native\berealclone
```

> The project is currently named `berealclone` because it started as a tutorial practice app (BeReal clone) to learn React Native before building EarGuard proper.

---

## 2. Tech Stack

| Layer | Choice |
|-------|--------|
| Language | JavaScript (React Native) |
| Framework | React Native + Expo SDK 54 |
| Build system | expo-dev-client (custom native builds) |
| IDE | VS Code + Android Studio Panda 2 |
| Emulator | Pixel 8 AVD, API 35 |
| Package manager | npm + Chocolatey (Windows) |
| Database (planned) | Room Database |
| Background processing (planned) | Android Foreground Service |

---

## 3. Known Limitations

**React Native vs Kotlin for system-level features:**
EarGuard needs deep Android system access — Bluetooth detection, audio stream monitoring, and a persistent background timer. In Kotlin these are all built-in, rock solid, and officially documented. In React Native they require third-party native modules which are sometimes less maintained and less reliable for this level of system integration.

This tradeoff was made consciously. React Native was chosen because of existing JavaScript familiarity and cross-platform potential, with the understanding that native module quality will need to be evaluated carefully for each EarGuard feature before committing to it.

**Expo Go not usable for this project:**
EarGuard uses custom native modules. Expo Go cannot run these. All development and testing must go through `npx expo run:android` (custom dev build). This means every native change requires a full rebuild rather than a quick QR scan.

**Emulator limitations for EarGuard testing:**
The Android emulator has partial support for Bluetooth and audio hardware features. Full EarGuard testing (headphone detection, audio stream monitoring) will require a real physical Android device connected via USB.

---

## 4. Environment Setup

### Tools Installed (in order)

| Tool | Version | How Installed | Purpose |
|------|---------|--------------|---------|
| Chocolatey | 2.7.0 | PowerShell (Admin) install script | Windows package manager |
| Microsoft OpenJDK 17 | 17.0.18 | `choco install -y microsoft-openjdk17` | Java for Android build tools |
| Node.js LTS | LTS | `choco install -y nodejs-lts` | Runtime for Expo/React Native CLI |
| Android Studio | Panda 2 (2025.3.2) | Manual from developer.android.com/studio | IDE + Emulator + SDK |
| Android SDK | API Level 35/36 | Android Studio Setup Wizard | Android build tools |
| expo-dev-client | 6.0.20 | `npx expo install expo-dev-client` | Custom native dev build |

### JAVA_HOME Configuration

JDK 17 installed via Chocolatey landed at an unresolvable path. Used Android Studio's bundled JDK instead.

Set permanently via Admin PowerShell:
```powershell
[System.Environment]::SetEnvironmentVariable("JAVA_HOME", "C:\Program Files\Android\Android Studio\jbr", "Machine")
[System.Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\Program Files\Android\Android Studio\jbr\bin", "Machine")
```

Verified: `java -version` returned `openjdk version "21.0.9"` ✅

> On Windows, always point JAVA_HOME to Android Studio's bundled JDK. Chocolatey JDK installs can land at unpredictable paths.

### AVD Setup

- Emulator: Pixel 8 (API 35 system image)
- Created via Android Studio → Device Manager
- Hardware acceleration enabled

---

## 5. Decisions Log

| Date | Decision | Reason | Alternatives Rejected |
|------|----------|--------|-----------------------|
| 27 Mar | React Native over Kotlin | Existing JS familiarity, cross-platform future, friend's recommendation | Kotlin (Android only, new syntax from scratch) |
| 27 Mar | expo-dev-client over Expo Go | EarGuard needs custom native modules which Expo Go cannot run | Expo Go (incompatible with native packages) |
| 27 Mar | Learn as you build, not vibe-code | App has complex system-level features — blind vibe coding would break things with no understanding of why | Pure vibe coding (too risky for native features) |
| 29 Mar | Chocolatey for package management | Standard Windows dev tool, easy CLI installs | Manual installers (harder to reproduce setup) |
| 5 Apr | Keep project at SDK 54, downgrade emulator Expo Go | Project had previously been broken at SDK 55 — safer to hold at 54 and match the emulator | Upgrading project to SDK 55 (risk of reintroducing old issues) |
| 5 Apr | Use Android Studio's bundled JDK for JAVA_HOME | Chocolatey JDK installed to an unresolvable path | Chocolatey JDK (couldn't locate it on the system) |
| 10 Apr | Remove @expo/ui entirely | Canary version was broken on Android; tutorial author tested on iOS only | Keeping canary and debugging it (unstable, not worth it) |
| 10 Apr | Replace Host + Button with View + TouchableOpacity | Standard React Native components, stable, no native dependency | @expo/ui equivalents (broken on Android canary) |
| 4 May | Use `.env` with `EXPO_PUBLIC_` prefix for Supabase keys | Expo SDK 54 requires the `EXPO_PUBLIC_` prefix for environment variables to be accessible in the bundle | Hardcoding keys (unsafe, can't commit to git) |

---

## 6. Daily Entries

---

### 27 Mar 2026

- Shared the EarGuard project documentation with Claude for review
- Evaluated AI builders (Bolt, Lovable, Emergent, Cursor) — all ruled out for system-level Android
- Finalized tech stack and approach — see [Decisions Log](#5-decisions-log)

---

### 29 Mar 2026

- Installed full dev environment: Chocolatey → JDK 17 → Node.js LTS → Android Studio Panda 2
- All tools installed via PowerShell (Admin) — required for system-level installs
- Android Studio Setup Wizard handled SDK downloads automatically
- System check passed: 51.4 GB free disk space, 64-bit Windows, sufficient RAM

---

### 4 Apr 2026

- Deleted old broken berealclone project (earlier SDK version conflicts)
- Created fresh project:
  ```powershell
  npx create-expo-app@3 berealclone
  ```
- SDK 54 confirmed in `package.json`: `"expo": "~54.0.33"` ✅

---

### 5 Apr 2026

- Emulator's Expo Go had auto-updated to SDK 55 — incompatible with project SDK 54
- Downloaded Expo Go SDK 54 APK manually, drag-dropped onto emulator, disabled auto-update
- Installed `expo-dev-client` successfully
- Installed `@expo/ui` canary at tutorial's instruction (caused crash later — see debug guide)
- Fixed JAVA_HOME — see [Environment Setup](#4-environment-setup) and [Decisions Log](#5-decisions-log)
- Created missing `app/(tabs)/_layout.tsx` with Tabs navigator — tab bar appeared ✅

---

### 6 Apr 2026

- First successful native Android build:
  ```
  BUILD SUCCESSFUL in 1h 12m 56s
  422 actionable tasks: 349 executed, 73 from cache
  ```
- App installed on Pixel 8 emulator via `npx expo run:android` ✅
- Data used: ~500 MB to 1 GB (first build, one-time cost — Gradle caches everything after)

---

### 7 Apr 2026

- Emulator froze on boot — fixed with Cold Boot from Device Manager
- Confirmed rule: use `npx expo run:android` (not `npx expo start`) whenever native packages are involved
- Fixed `@expo/ui` import for Android: `@expo/ui/jetpack-compose` not `@expo/ui/swift-ui` (iOS only)
- PC lagging from Gradle Daemon — killed with `.\gradlew --stop`

---

### 10 Apr 2026

- `@expo/ui` canary (`v0.2.0-canary`) confirmed broken on Android — removed entirely
- Clean rebuild completed — app running with no crashes ✅
- Replaced `<Host>` and `<Button>` from `@expo/ui` with `<View>` and `<TouchableOpacity>` from React Native
- Metro hot reload working correctly
- Generated React Native Debugging Field Guide PDF from all chat sessions

---

### 12 Apr 2026

#### Issue 1: Emulator Black Screen on Startup

- Emulator launched but showed pure black screen with no boot animation
- ADB reported `device offline` when running `npx expo run:android`
- Root cause: corrupted AVD snapshot from previous session
- Fix:
```powershell
  adb kill-server
  adb start-server
```
  Then Cold Boot the AVD from Android Studio → Device Manager → three dots → Cold Boot Now
- Emulator booted successfully after cold boot ✅

#### Issue 2: App Icon Not Updating (The Big One)

Spent the majority of the session on this. Full breakdown:

**What was attempted (did NOT work):**
- Editing `app.json` foregroundImage path and rebuilding — Gradle skipped icon processing due to incremental build cache (392 of 422 tasks marked `up-to-date`)
- Clearing emulator launcher cache
- Uninstalling and reinstalling the APK
- Running `gradlew clean` and rebuilding

**Root causes discovered:**
1. `npx expo run:android` does NOT reprocess icon assets from `app.json`. Icons are processed during `npx expo prebuild` which only runs once when the android folder is first generated. Every subsequent build reuses the old compiled icon files.
2. Running `npx expo prebuild --platform android` wiped `android/local.properties` which caused every subsequent `npx expo run:android` to silently crash with zero terminal output.
3. The original `patrick_bateman.png` had a solid background and non-square dimensions (704x489) — Android adaptive icons require a transparent background and square dimensions.

**Image requirements for Android adaptive icons:**
- Square dimensions (1024x1024 recommended, 416x416 worked fine)
- Transparent background (used remove.bg to strip background)
- Foreground image sits on top of `backgroundColor` in `app.json`

**Fix sequence that finally worked:**
```powershell
# Step 1: Regenerate Android icon files from app.json
npx expo prebuild --platform android

# Step 2: Recreate local.properties (wiped by prebuild)
echo "sdk.dir=C\:\\Users\\Win\\AppData\\Local\\Android\\Sdk" > android/local.properties

# Step 3: Full PC restart (ADB and terminals were in broken state)

# Step 4: Build and install targeting phone directly
npx expo run:android --device
```
<!-- To recorrect the commit message April 12 not April 13-->


**Key learnings:**
- Always run `npx expo prebuild` before expecting icon/asset config changes to take effect
- `local.properties` must exist in the `android/` folder — prebuild can wipe it
- Android adaptive icons need transparent background PNGs — iOS does not
- When both emulator and phone are connected, use `--device` flag to get the picker, or pass the ADB device ID directly
- `adb reverse tcp:8081 tcp:8081` is needed to tunnel Metro from PC to physical device over USB

#### Physical Device Setup

- Switched from charging cable to data cable
- Enabled USB Debugging on Samsung phone (SM_A042F)
- Confirmed ADB detection: `R9ZWC0LSNMX device` in `adb devices`
- Used `adb reverse tcp:8081 tcp:8081` to tunnel Metro bundler to phone
- App now running on physical device ✅


#### Result

Patrick Bateman icon successfully showing on phone home screen ✅

---

### 4 May 2026

#### Supabase Auth Setup (BeReal Clone Tutorial)

- Installed `@react-native-async-storage/async-storage` and `@supabase/supabase-js` via `npx expo install`
- Created `lib/supabase/client.ts` with Supabase client using AsyncStorage for session persistence
- Created `context/AuthContext.tsx` with React Context exporting `signUp` and `signIn` functions
- Created `.env` with `EXPO_PUBLIC_SUPABASE_URL` and `EXPO_PUBLIC_SUPABASE_ANON_KEY`

#### Root Layout Fixes

Three errors fixed in `app/_layout.tsx`:

**"Attempted to navigate before mounting Root Layout":** React was trying to call `router.replace()` before the root component finished mounting. Fixed by wrapping navigation inside `setTimeout(..., 0)` within `useEffect` to defer it until after React commits the mount.

**"NativeViewGestureHandler must be used as descendant of GestureHandlerRootView":** Expo Router needs `GestureHandlerRootView` wrapping the entire app for gesture-based navigation to work. Added `<GestureHandlerRootView style={{ flex: 1 }}>` around the Stack navigator.

**`SafeAreaView` edges prop TypeScript error:** The `edges` prop exists in iOS type definitions but not Android, causing a type mismatch. Removed the prop entirely — SafeAreaView works correctly on both platforms by default.

**Cache clearing:** Used `npx expo start -c` multiple times to clear Metro cache when environment variables or file moves caused stale state. Saved as a go-to command.

#### VS Code Root Folder Issue (Major Debugging Session)

VS Code showed persistent "Cannot find module" errors for both supabase and async-storage imports. `npx tsc --noEmit` returned clean but VS Code flagged errors anyway.

**Root cause:** VS Code was opened in the parent folder `React_Native` instead of `React_Native\berealclone`. node_modules only exists inside berealclone, so VS Code couldn't resolve any imports. Additionally, `client.ts`, `AuthContext.tsx`, and `.env` had all been created in the parent folder during earlier work.

**Fix steps:**
- Closed all VS Code windows
- Opened fresh PowerShell, navigated to `C:\Users\Win\Desktop\Coding_Stuff\React_Native\berealclone`
- Ran `code .` to open VS Code with correct root
- Moved files from parent into berealclone: `Move-Item ../lib/supabase/client.ts lib/supabase/client.ts`, `Move-Item ../context/AuthContext.tsx context/AuthContext.tsx`
- Recreated `.env` inside berealclone with actual Supabase credentials
- Cleaned up empty folders in parent directory

**Key learning:** Always verify VS Code's root folder with `pwd` in the terminal. If the path doesn't end with the project name, all imports will fail because VS Code looks for node_modules in the wrong place.

#### SignUp Screen Debugging

Built `signup.tsx` with email/password form, validation alerts, and Supabase signup call wired through `useAuth()`.

**Bugs encountered and fixed:**
- Missing `return` statements after `Alert.alert()` calls caused multiple alerts to fire simultaneously
- Empty `try` block with no `await signUp()` call — button had no effect
- Missing closing brace `}` on `handleSignUp` function caused entire screen to render blank
- Extra `};` at end of file caused Metro TransformSyntaxError
- `SafeAreaView` `edges` prop removed (causes TypeScript error on Android due to iOS/Android typing mismatch)
- `.env` file missing Supabase credentials caused "Network request failed" on signup attempt

#### Login Screen

Built `login.tsx` with the same pattern as signup: email/password form, `useAuth()` for `signIn` function, and navigation link to signup page. Removed unused `useRoute` import that was causing an ESLint warning.

#### Onboarding Screen (Started)

Built `onboarding.tsx` with profile completion UI: header section, profile image placeholder, and edit badge button.

**Bug:** The `form` view (containing the image placeholder) was placed OUTSIDE the `content` view in the JSX structure, causing layout issues. Fixed by moving the closing `</View>` tag of `content` to wrap both the `header` and `form` views together. They need to be siblings inside `content`, not separate children of `SafeAreaView`.

**Status:** Image placeholder section working correctly. Full onboarding form (name, username inputs) not yet completed.

**Result:** Signup screen renders correctly, form validation alerts work, Supabase signup call connected. Login screen UI complete. Onboarding screen layout fixed, image placeholder working. Tested on both Pixel 8 emulator and physical Samsung device (SM-A042F). Next step: test full signup + signin flow with real credentials, then complete onboarding form inputs.

---
### 5 May 2026

#### Bug Fixes Carried Over From May 4 Session

**Metro cache glitch (false syntax error):** App showed `Non-js exception: Compiling JS failed: 248105:3 '}' expected at end of object literal` on startup. Spent time investigating code before realising the fix was simply `npx expo start --clear`. No code was broken. Key learning: always clear Metro cache before assuming a code bug exists.

**`isAuth` not wired to AuthContext:** `_layout.tsx` had `const isAuth = false` hardcoded as a local variable, never reading from `useAuth()`. Fixed by splitting `_layout.tsx` into two components: `RootLayout` (renders `AuthProvider` and `GestureHandlerRootView`) and `RootLayoutNav` (calls `useAuth()` and handles navigation logic). This split is required because `useAuth()` can only be called inside a component already wrapped by `AuthProvider` — calling it in the same component that renders the provider causes a context error.

**Rogue `useEffect` in signup.tsx:** A `useEffect(() => { router.push("/(auth)/onboarding") }, [])` was left in from testing — it fired immediately on mount, sending users to onboarding before they could type anything. Removed it. Navigation to onboarding now happens only after a successful Supabase signup call.

**Login button had no `onPress` handler:** The Sign In button in `login.tsx` was decorative. Wired it up to a proper `handleSignIn` async function with email/password validation, Supabase `signIn` call, loading state, and error alert.

**Unused import in login.tsx:** `useRoute` from `@react-navigation/native` was imported but never used. Removed.

#### expo-image-picker Setup

- Installed `expo-image-picker` via `npx expo install expo-image-picker` (not `npm i` — Expo install auto-picks the SDK 54 compatible version)
- Added `expo-image-picker` plugin to `app.json` plugins array with `photosPermission` string
- Ran `npx expo prebuild --platform android` to apply plugin changes to native Android manifest
- Recreated `android/local.properties` immediately after prebuild (prebuild wipes it silently every time)
- Ran `npx expo run:android` to rebuild with native module compiled in
- **Key learning:** `expo-image-picker` is a native module — it requires a full rebuild after install. Hot reload is not enough.
- **Permission behaviour:** On the Android emulator, media library permissions are auto-granted silently. The permission dialog WILL appear on real physical devices. This is expected behaviour, not a bug.

#### Emulator Gallery Setup

- Used `adb push` to push test images into the emulator's `/sdcard/Pictures/` folder
- Used `adb shell am broadcast` to force Android's media scanner to register new files so the photo picker can find them
- **Key learning:** Files dropped into Downloads folder are NOT visible in the photo picker. Must be pushed to `/sdcard/Pictures/` or equivalent media folder.

#### Onboarding Screen Completed

- Completed `onboarding.tsx` with full name and username `TextInput` fields, profile image picker using `expo-image-picker`, circular image preview with edit badge, and Complete Setup button with loading state
- Image picker flow: request permission → launch library → crop to 1:1 aspect ratio → display selected image in circular preview

#### Supabase RLS Policies (Profiles Table)

Set up Row Level Security on the `profiles` table in Supabase SQL Editor. RLS is a database-level security layer that restricts what each authenticated user can read or write, even if someone obtains the API key.

Four policies created:

| Policy | Operation | Rule |
|--------|-----------|------|
| Users can insert their own profile | INSERT | `auth.uid() = id` |
| Users can update their own profile | UPDATE | `auth.uid() = id` |
| Users can view their own profile | SELECT | `auth.uid() = id` |
| Users can view other profiles | SELECT | `auth.uid() != id` |

`auth.uid()` is a built-in Supabase function that returns the ID of the currently authenticated user.

#### Supabase Database Function + Trigger

Created a PostgreSQL function `handle_new_user` and a trigger `on_auth_user_created` that automatically inserts a blank row into `public.profiles` whenever a new user signs up via Supabase Auth.

**Why this matters:** Supabase Auth and your `profiles` table are separate systems. When a user signs up, Supabase creates an entry in `auth.users` automatically, but your `profiles` table stays empty unless you manually insert a row. The trigger bridges this gap automatically.

#### Supabase Storage Policies (Profiles Bucket)

Created a public `profiles` bucket in Supabase Storage with four policies:

| Policy | Operation | Target | Rule |
|--------|-----------|--------|------|
| Public can read profile images | SELECT | public | `bucket_id = 'profiles'` |
| Users can upload their own profile images | INSERT | authenticated | `bucket_id = 'profiles' AND foldername[1] = auth.uid()` |
| Users can update their own profile images | UPDATE | authenticated | `bucket_id = 'profiles' AND foldername[1] = auth.uid()` |
| Users can delete their own profile images | DELETE | authenticated | `bucket_id = 'profiles' AND foldername[1] = auth.uid()` |

The `storage.foldername(name)[1]` check ensures each user can only access files inside their own folder (named after their user ID), not anyone else's.

**Result:** Full Supabase backend configured. Auth, RLS, database trigger, and storage policies all in place. Onboarding screen UI complete. Ready to wire up the complete signup → onboarding → profile save flow.

---

## 7. Roadmap

### Current Phase — Learning (berealclone tutorial)
- [x] Dev environment set up
- [x] First native Android build
- [x] Tab navigation working
- [x] Supabase auth setup (client, context, .env)
- [x] Root layout errors fixed (navigation, gesture handler, SafeAreaView)
- [x] Signup screen built with form validation
- [x] Login screen built
- [x] Complete onboarding screen (name, username inputs)
- [ ] Wire up onboarding form to Supabase (save name, username, profile image)
- [ ] Test full signup + signin flow with real credentials
- [ ] Complete remaining tutorial screens
- [ ] Complete the full berealclone tutorial

### Next Phase — EarGuard Development
- [ ] Create fresh EarGuard project
- [ ] Implement headphone detection (wired + Bluetooth)
- [ ] Implement audio stream monitoring (`AudioManager.isStreamActive()`)
- [ ] Build Foreground Service for background timer
- [ ] Build Room Database schema for listening history
- [ ] Implement threshold alerts and notification system
- [ ] Add Bluetooth whitelist feature (car audio)
- [ ] Test on physical Android device via USB
- [ ] Polish UI
- [ ] Play Store release

## 8. Milestones

| Date | Milestone |
|------|-----------|
| 27 Mar 2026 | Project concept finalized, React Native chosen |
| 29 Mar 2026 | Full dev environment set up on Windows |
| 4 Apr 2026 | Fresh berealclone project created (SDK 54) |
| 5 Apr 2026 | Emulator running, Expo Go SDK mismatch resolved |
| 5 Apr 2026 | expo-dev-client installed, JAVA_HOME fixed, tab navigation working |
| 6 Apr 2026 | **First successful native Android build** (1h 12m, 422 tasks) |
| 6 Apr 2026 | App installed on Pixel 8 emulator for the first time |
| 10 Apr 2026 | `@expo/ui` canary crash resolved, clean rebuild done |
| 10 Apr 2026 | React Native Debugging Field Guide PDF generated |
| 11 Apr 2026 | This DEVLOG created |
| 12 Apr 2026 | Emulator black screen fixed via ADB restart + Cold Boot |
| 12 Apr 2026 | Physical device (Samsung SM_A042F) connected via USB for development |
| 12 Apr 2026 | App icon successfully changed to custom image on physical device |
| 4 May 2026 | Supabase auth setup complete, signup/login screens built |
| 5 May 2026 | expo-image-picker integrated, Supabase RLS + storage policies configured, onboarding screen UI complete |
---

## 9. Resources

### Debugging Guide

All errors encountered during this phase — root cause analysis, exact fix steps, code changes, and prevention tips — are documented in the dedicated PDF:

📄 **[React Native Debugging Field Guide](./RN_Debugging_Guide_v2.pdf)**

### Key Commands

```powershell
# Create a new Expo project
npx create-expo-app@3 <project-name>

# Start Metro (JS only, Expo Go compatible projects)
npx expo start

# Full native build + install on emulator (use when native packages are involved)
npx expo run:android

# Install an Expo-compatible package (auto-picks correct version for your SDK)
npx expo install <package-name>

# Remove a package
npm uninstall <package-name>

# Clean Gradle build cache
cd android
.\gradlew clean
cd ..

# Kill Gradle Daemon (frees RAM when PC is lagging)
.\gradlew --stop

# Verify Java
java -version

# Delete a folder (PowerShell)
Remove-Item -Recurse -Force <folder-name>

# Set JAVA_HOME permanently (Admin PowerShell only)
[System.Environment]::SetEnvironmentVariable("JAVA_HOME", "C:\Program Files\Android\Android Studio\jbr", "Machine")
```

### Official Docs

- [Expo Docs](https://docs.expo.dev)
- [Expo Android Studio Emulator Setup](https://docs.expo.dev/workflow/android-studio-emulator/)
- [Android Studio Download](https://developer.android.com/studio)
- [Expo Go APK Downloads (specific SDK versions)](https://expo.dev/go)

---

*EarGuard DEVLOG | Gautam | Started 27 Mar 2026*
