# DR-0010: Maestro for Device Tests

**Status:** Accepted  
**Date:** 2026-11-27

## Context

The Android and iOS apps include native code that Jest and Vitest can't run, such as background audio and platform-specific files. Device tests run the built app on an Android emulator and an iOS simulator.

Simulators and emulators differ from real devices in some hardware and API behavior. This behavior is checked on real devices:

- background audio with the screen locked
- audio timing by ear
- on-device text-to-speech speed
- Android battery saving in the background
- touch and layout on a real screen

## Decision

Maestro runs device tests on an Android emulator and an iOS simulator in GitHub Actions. The tests run on a schedule and before a release.

The behavior in that list is checked by hand before a release, on an Android phone and an iPad.

## Alternatives Considered

- **Maestro on real phones:** Rejected because Maestro runs only on iOS simulators, not on physical iPhones or iPads.
- **Appium:** Rejected because its tests are written in code, and a real iPhone needs a signed WebDriverAgent.
- **Detox:** Rejected because its Expo support comes from a community-maintained config plugin. The Expo team removed Detox support from its own config plugins.

## Rationale

- **Simple flows:** Maestro flows are short YAML files.
- **Expo support:** Expo documents Maestro for device tests.
- **No cost:** macOS runners are free in public repositories.

## Consequences

- iPhone layout is checked only in the iOS simulator. The manual checks run on an iPad.
- iOS simulator tests need macOS runners. If the repository became private, a macOS minute would cost about 10 times a Linux minute: $0.062 against $0.006.

## Assumptions

* Appium tests run slower than Maestro tests. Not found in official sources as of 2026-11-28.
* Simulators and emulators don't show Android battery saving in the background. A source contradicts this.

## Sources

### [iOS - Maestro documentation](https://docs.maestro.dev/get-started/supported-platform/ios)

Retrieved 2026-11-27.

- Fact: Maestro runs on iOS simulators.
  > "Run tests on any iOS Simulator managed by Xcode."

### [Maestro CHANGELOG.md](https://raw.githubusercontent.com/mobile-dev-inc/Maestro/main/CHANGELOG.md)

Retrieved 2026-11-27.

- Fact: Maestro does not run on physical iPhones or iPads.
  > "iOS: stop listing physical iPhones as devices, and fail attempted runs early with"
  > "Physical iOS devices are not yet supported"

### [Maestro documentation](https://docs.maestro.dev/)

Retrieved 2026-11-27.

- Fact: Maestro flows are YAML files.
  > "Maestro is the simplest and most effective framework for painless mobile and web UI automation using intuitive YAML flows."

### [Run E2E tests on Expo Workflows with Maestro - Expo Documentation](https://docs.expo.dev/eas/workflows/examples/e2e-tests/)

Retrieved 2026-11-27.

- Fact: Expo documents Maestro for device tests.
  > "In this guide, you'll learn how to run end-to-end (E2E) tests on Expo Workflows using Maestro."

### [Appium in a Nutshell - Appium Documentation](https://appium.io/docs/en/latest/intro/)

Retrieved 2026-11-27.

- Fact: Appium tests are written in code.
  > "Appium aims to support automation code written in different languages (JS, Java, Python, etc.)."

### [Provisioning Profile - Appium XCUITest Driver](https://appium.github.io/appium-xcuitest-driver/latest/getting-started/provisioning-profile/)

Retrieved 2026-11-27.

- Fact: A real iPhone needs a signed WebDriverAgent.
  > "For real devices, Apple requires all apps to have a valid provisioning profile before they can be installed"

### [GitHub - isilher/expo-detox-config-plugin: Expo config plugin for Detox E2E testing (SDK 54+, community maintained)](https://github.com/isilher/expo-detox-config-plugin)

Retrieved 2026-11-27.

- Fact: Detox's Expo support comes from a community-maintained config plugin.
  > "Expo config plugin for Detox E2E testing (SDK 54+, community maintained)"
- Fact: The Expo team removed Detox support from its own config plugins.
  > "The official Expo team removed Detox support from their config-plugins repository."

### [Testing and Debugging in Simulator](https://developer.apple.com/library/archive/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator/TestingontheiOSSimulator.html)

Retrieved 2026-11-27.

- Fact: Simulators don't show some behavior of a real device.
  > "Finally, there are some hardware and API differences in Simulator. These differences may affect your app when testing in Simulator."
  > "As a result, the simulator is not an accurate test of an app's performance, memory usage, and networking speed."
- Fact: The iOS Simulator does not offer the background modes key that background audio needs.
  > "Simulator APIs don't have all the features that are available on a device. For example, the APIs don't offer:"
  > "The `UIBackgroundModes` key"
- Fact: The iOS Simulator does not show on-device speed, such as text-to-speech speed.
  > "As a result, the simulator is not an accurate test of an app's performance, memory usage, and networking speed."
- Fact: Touch in the Simulator differs from touch on a real screen.
  > "Also keep in mind that some user interface elements can be easier to interact with in Simulator using a mouse than when trying to interact with the app through touch on a device."

### [Audio latency | Android NDK](https://developer.android.com/ndk/guides/audio/audio-latency)

Retrieved 2026-11-28.

- Fact: Audio timing depends on the device model.
  > "Round-trip audio latency varies greatly depending on device model and Android build."

### [Optimize for Doze and App Standby](https://developer.android.com/training/monitoring-device-state/doze-standby)

Retrieved 2026-11-28.

- Fact: Android's documentation tests Doze and App Standby on a virtual device as well as a hardware device.
  > "Configure a hardware device or virtual device with an Android 6.0 (API level 23) or higher system image."

### [GitHub Actions billing](https://docs.github.com/en/billing/concepts/product-billing/github-actions)

Retrieved 2026-11-27.

- Fact: macOS runners are free in public repositories.
  > "Standard GitHub-hosted runners in public repositories"
- Fact: In a private repository, macOS minutes cost more than Linux minutes.
  > "For example, jobs that run on Windows and macOS runners hosted by GitHub cost more to run than jobs on Linux runners."

### [Actions runner pricing](https://docs.github.com/en/billing/reference/actions-runner-pricing)

Retrieved 2026-11-27.

- Fact: A Linux 2-core minute costs $0.006.
  > "$0.006"
- Fact: A macOS minute costs $0.062.
  > "$0.062"
