# EarGuard — Developer Log (DEVLOG)

> Chronological record of the EarGuard project: decisions made, tools installed, errors hit, and milestones reached. Spans 27 Mar – 11 Apr 2026.

---

## Status

| Field | Value |
|-------|-------|
| Status | 🟡 In Progress |
| Current Phase | Tutorial (berealclone — learning React Native before building EarGuard) |
| Next Step | Complete berealclone tutorial, then begin EarGuard from scratch |
| Last Updated | 11 Apr 2026 |
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

## 7. Roadmap

### Current Phase — Learning (berealclone tutorial)
- [x] Dev environment set up
- [x] First native Android build
- [x] Tab navigation working
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

---

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

---

## 9. Resources

### Debugging Guide

All errors encountered during this phase — root cause analysis, exact fix steps, code changes, and prevention tips — are documented in the dedicated PDF:

📄 **[React Native Debugging Field Guide](./docs/RN-Debugging-Field-Guide.pdf)**

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
