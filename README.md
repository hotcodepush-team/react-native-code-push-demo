# React Native code push demo by HotCodePush

The demo app for [React Native code push](https://hotcodepush.com/react-native-code-push) with `@hotcodepush/react-native-code-push`: one screen showing the bundle version, the current release, the device id, the last sync and the last rollback, a button that syncs now and one that opens the debug screen.

## Installation

```sh
nvm use
npm ci
cd ios && pod install
```

Open `ios/HotCodePushDemo.xcworkspace` in Xcode or `android/` in Android Studio and run a release build: `npx react-native run-ios --mode Release` or `npx react-native run-android --mode release`.
A release build bundles the JavaScript and runs the CLI's `binary create`, the build step, which writes the resource file `hotcodepush.json` into the app and creates the store build's binary with its embedded bundle, so log in first with `npx hotcodepush login` or set `HOTCODEPUSH_TOKEN`.
Without a token, or with `HOTCODEPUSH_OFFLINE=1`, the build goes on without a channel and takes no updates.
Point it at another host, the local stack or staging, by setting `HOTCODEPUSH_FILES_BASE_URL` and `HOTCODEPUSH_UPDATES_BASE_URL` for the build.
A debug build asks Metro for its JavaScript, `npm start`, and live updates are off in it.

## Usage

Run the app once: it shows `v1` and the current release `embedded`.
Change `VERSION` in `App.tsx`, release with `npx hotcodepush release create`, which bundles each platform itself, and reopen the app to see the new label.

## Documentation

The SDK reference is at [hotcodepush.com/docs/react-native](https://hotcodepush.com/docs/react-native).

## Development

```sh
npm run lint        # Prettier
npm run typecheck   # TypeScript
npm start           # Metro, for a debug build
```

The flows in `maestro/` are the update lifecycle contract the monorepo's `e2e/` runner drives on the simulator and the emulator — the golden path, the broken release that rolls back, the revoke, the incompatible release, the debug screen whose shared report names that skip's code, the release a build that carries a public key refuses, unsigned or signed with a key it does not trust, and the signed release on a build that carries the app's public key; by hand, install a release build, release `v2` with the CLI, then `maestro test -e EXPECTED_VERSION=v2 -e EXPECTED_RELEASE_NUMBER=1 maestro/golden-path.yaml`.

## License

See [LICENSE](./LICENSE).
