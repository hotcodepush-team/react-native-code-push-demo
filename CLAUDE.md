# CLAUDE.md

The HotCodePush React Native demo: a minimal React Native 0.82 app on `@hotcodepush/react-native-code-push`, the app that receives every update, the SDK's device test and the docs' screenshots.
Stack: TypeScript and React Native on the New Architecture with Hermes; the iOS project on CocoaPods, the Android project on Gradle.

The plan is the private `handbook` repo, checked out beside this one: `../handbook/docs/`.
`sdk-api.md` is the SDK's specification — the configuration, the resource file and the methods; `onboarding.md` describes the demo loop this app exists for.
When code and plan disagree, stop and surface it; never improvise.

## Layout

```
App.tsx, index.js            the one screen and the entry file
hotcodepush.json             the project's configuration, as `init` writes it; placeholder ids until the app is created
ios/, android/               the native projects, committed, wired as `init` wires them
maestro/                     the update lifecycle contract's flows
```

## Commands

| Command             | Does                     |
| ------------------- | ------------------------ |
| `npm run lint`      | Prettier                 |
| `npm run typecheck` | TypeScript               |
| `npm start`         | Metro, for a debug build |

Run `npm run fmt` before every commit.
The native builds: `pod install` in `ios/`, then `xcodebuild -workspace ios/HotCodePushDemo.xcworkspace -scheme HotCodePushDemo -configuration Release -destination 'generic/platform=iOS Simulator' build`, and `./gradlew assembleRelease` in `android/`.

## The wiring

Everything `npx hotcodepush init` adds to a React Native project is committed here as it adds it:

- `ios/HotCodePushDemo/AppDelegate.swift` returns `HotCodePush.bundleURL()` in release builds, and `android/.../MainApplication.kt` imports the SDK's `getDefaultReactHost`: React Native runs the bundle the SDK serves.
- The Xcode phase "Create HotCodePush binary", after "Bundle React Native code and images", and the `apply from` line at the end of `android/app/build.gradle` run the CLI's `binary create` on what the build bundled.
- `ios/Podfile` pins `HotCodePushCore` at the commit the SDK names.

`binary create` writes `hotcodepush.json` into the app — the project's file plus `builtAt`, `fingerprint`, `embeddedBundleManifest` and `embeddedBundleId` — and creates the store build's binary; it needs a login or `HOTCODEPUSH_TOKEN`, and without one, or with `HOTCODEPUSH_OFFLINE=1`, the build names no channel and takes no updates.
A debug build bundles nothing and asks Metro for its JavaScript; the build step still writes its `hotcodepush.json`, without an embedded bundle, so every sync answers `SKIPPED` with `DEBUG_BUILD`; the device test and a first try of an update use release builds.
`HOTCODEPUSH_FILES_BASE_URL` and `HOTCODEPUSH_UPDATES_BASE_URL` point the SDK at another host, the local stack or staging.

`ios/Podfile` takes React Native's prebuilt core and dependencies, so a clean iOS build is a minute instead of a quarter of an hour.
The Android manifest permits cleartext to `10.0.2.2` and `localhost` alone, and `Info.plist` local networking, for the device test's local stack.

## Dependencies during the build phase

The SDK is pinned to the pkg.pr.new build of one commit, `https://pkg.pr.new/hotcodepush-team/react-native-code-push/@hotcodepush/react-native-code-push@<sha>`, never `@main`; a bump is one edit of that sha, and of the pod's commit in `ios/Podfile` when the SDK's `package.json` names another.
The CLI is pinned the same way, `https://pkg.pr.new/hotcodepush-team/cli/hotcodepush@<sha>`.
Every other dependency is pinned to an exact version and bumped by Renovate.

## Agent workspace

- `.claude/skills/` holds the developer skills copied from `hotcodepush-team/.github`, pinned in `skills-lock.json`.
- Commits are conventional commits; `main` is trunk, CI is the gate, and a commit that lands an issue says `Closes #<n>`.
