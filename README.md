# React Native code push demo by HotCodePush

The demo app for [React Native code push](https://hotcodepush.com/react-native-code-push) with `@hotcodepush/react-native-code-push`: one screen showing the bundle version, the current release, the device id, the last sync and the last rollback, a button that syncs now and one that opens the debug screen.

## Getting started

```sh
nvm use
npm ci
npx hotcodepush login
cd ios && pod install && cd ..
npx react-native run-ios --mode Release       # or: npx react-native run-android --mode release
```

OTA updates are off in a debug build, where Metro serves the JavaScript, so run a release build.
Every native build runs the CLI's build step, which writes `hotcodepush.json` into the app; an Xcode archive or an Android non-debug build also registers the store build in HotCodePush.
Without a login the file names no channel and the app takes no updates.
Set `HOTCODEPUSH_FILES_BASE_URL` and `HOTCODEPUSH_UPDATES_BASE_URL` for the build to point it at the local stack or staging.

## Usage

Run the app once: it shows `v1` and the current release `embedded`.
Change `VERSION` in `App.tsx`, release with `npx hotcodepush release create`, which bundles each platform itself, and reopen the app to see the new label.

## Development

```sh
npm start           # Metro, for a debug build
npm run lint && npm run typecheck
npm run fmt         # run before every commit
```

The flows in `maestro/` are the update lifecycle contract the monorepo's `e2e/` runner drives on the simulator and the emulator; each flow's header names what it expects.
By hand: install a release build, release `v2`, then `maestro test -e EXPECTED_VERSION=v2 -e EXPECTED_RELEASE_NUMBER=1 maestro/golden-path.yaml`.
`CLAUDE.md` holds the native build commands and the wiring `npx hotcodepush init` adds.

## Documentation

See [hotcodepush.com/docs/react-native](https://hotcodepush.com/docs/react-native).

## License

See [LICENSE](./LICENSE).
