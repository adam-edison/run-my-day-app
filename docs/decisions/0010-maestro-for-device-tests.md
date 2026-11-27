# DR-0010: Maestro for Device Tests

**Status:** Accepted  
**Date:** 2026-11-27

## Context

The Android and iOS apps include native code that Jest and Vitest can't run, such as background audio and platform-specific files. Device tests run the built app on an Android emulator and an iOS simulator.

## Decision

Maestro runs device tests on an Android emulator and an iOS simulator in GitHub Actions. The tests run on a schedule and before a release.

Simulators and emulators don't show some behavior:

- background audio with the screen locked
- audio timing by ear
- on-device text-to-speech speed
- Android battery saving in the background
- touch and layout on a real screen

That behavior is checked by hand before a release, on an Android phone and an iPad.

## Alternatives Considered

- **Maestro on real phones:** Rejected because Maestro runs only on iOS simulators, not on physical iPhones or iPads.
- **Appium:** Rejected because its tests are written in code and run slower, and a real iPhone needs a signed WebDriverAgent.
- **Detox:** Rejected because its Expo support is community-driven and lags new Expo versions.

## Rationale

- **Simple flows:** Maestro flows are short YAML files.
- **Expo support:** Expo documents Maestro for device tests.
- **No cost:** macOS runners are free in public repositories.

## Consequences

- iPhone layout is checked only in the iOS simulator. The manual checks run on an iPad.
- iOS simulator tests need macOS runners. They would use 10 times the Linux rate of minutes if the repository became private.
