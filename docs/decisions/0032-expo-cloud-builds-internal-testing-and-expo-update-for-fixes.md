# DR-0032: Expo Cloud Builds, Internal Testing, and Expo Update for Fixes

**Status:** Draft  
**Date:** 2026-11-28

## Context

One Expo codebase serves Android, iOS, and web, as recorded in DR-0004. The app needs native code for audio, on-device text-to-speech, AlarmKit, and exact alarms, as recorded in DR-0004 and DR-0019. Hand checks on an Android phone and an iPad happen before a release, as recorded in DR-0010. The repository is public, as recorded in DR-0001. Secrets must stay out of it.

Expo renamed its services. EAS Build is now Expo Build, and EAS Update is now Expo Update. The CLI command is still `eas`.

Facts about Expo distribution:

* Expo Submit works on macOS, Linux, and Windows. No Mac is needed to submit iOS builds.
* The first `eas submit` for Android creates a release on the Google Play internal testing track.
* A Google Play internal test allows up to 100 testers per app.
* TestFlight processing usually takes 10 to 15 minutes.
* The Expo free plan allows up to 15 Android and 15 iOS builds. The pricing page does not state the period.
* Free cloud builds run in a low-priority queue and stop after 45 minutes.
* Expo Update is free up to 1,000 monthly active users.

Facts about over-the-air updates:

* An update can change JavaScript, styling, and images.
* An update cannot change native code or the Expo SDK version.
* The runtime version keeps an update matched to a build's native code.
* The `fingerprint` policy changes the runtime version whenever native code changes.
* A channel is fixed in the build at build time. Any channel can point to any branch of updates.
* Apple guideline 2.5.2 forbids downloaded code that introduces or changes features.
* Google Play allows code that runs in an interpreter.

Costs:

* The Apple Developer Program costs $99 a year.
* Google Play charges a one-time $25 registration fee.
* Expo paid plans start at $19 a month.

Example: an announcement says "Workuot" in place of "Workout". The fix must reach the Android phone and the iPad.

## Decision

Builds:

* Expo Build makes the Android and iOS binaries in Expo's cloud on the free plan.
* When the free cloud quota runs out, `eas build --local` builds on the developer's Mac.

Distribution:

* Expo Submit sends iOS builds to TestFlight.
* Expo Submit sends Android builds to the Google Play internal testing track.

Over-the-air updates:

* Expo Update ships changes that touch no native code.
* The runtime version uses the `fingerprint` policy.
* The `preview` channel serves TestFlight and Play internal testing builds.
* The `production` channel serves the store builds.
* Updates carry bug fixes only, never new features.

Example: a native audio library is added. The fingerprint changes. A new build goes to TestFlight and Play internal testing. A later typo fix in an announcement ships through Expo Update on the `preview` channel.

## Alternatives Considered

Local builds only, with `eas build --local`, rejected:

* Each run builds one platform. The `all` option is disabled.
* Local builds have no caching.

No over-the-air updates, rejected:

* Every fix waits for a build and store processing.

## Rationale

* Cloud builds and Expo Submit need no Mac for submission.
* Internal testing on both stores covers one tester without public store listings.
* Expo Update ships a JavaScript fix in minutes at no cost below 1,000 monthly active users.
* The `fingerprint` policy prevents an update from reaching a build with different native code.
* Local builds keep releases possible after the free cloud quota runs out.

## Consequences

* The free plan limits cloud builds to 15 Android and 15 iOS builds. The pricing page does not state the period.
* Free cloud builds wait in a low-priority queue and stop after 45 minutes.
* No official source states whether local builds avoid the cloud build quota.
* The rule that updates carry fixes only is own reasoning from Apple guideline 2.5.2. No official source states this rule.
* Apple guideline 3.3.1(b), which Expo often cites for over-the-air updates, was not found on the page read.
* Submitting to the App Store needs a paid Apple Developer account.

## Open Questions

1. Does the app go to public store listings, or stay in TestFlight and Play internal testing for now? Example: only the owner uses the MVP. Internal testing would be enough, and no store review would be needed for listings.
2. Are the $99 Apple fee each year and the $25 Google fee accepted now, or after the MVP?

## Sources

### [Expo Build](https://docs.expo.dev/build/introduction/)

Retrieved 2026-11-27.

- Fact: Expo Build is a hosted build service.
  > "Expo Build is a hosted service that builds app binaries"
- Fact: local builds are possible.
  > "Use local builds with `eas build --local` to run builds on your machine."

### [Submit to app stores](https://docs.expo.dev/submit/introduction/)

Retrieved 2026-11-27.

- Fact: the first Android release goes to internal testing.
  > "the default `eas submit` command creates the first release on the internal testing track"
- Fact: TestFlight builds are not released to the App Store automatically.
  > "A TestFlight build is not automatically released to the App Store."

### [Submit to the Apple App Store with Expo Submit](https://docs.expo.dev/submit/ios/)

Retrieved 2026-11-27.

- Fact: no Mac is needed to submit.
  > "Expo Submit works on macOS, Linux, and Windows, so you don't need a Mac to ship iOS builds."
- Fact: TestFlight processing is short.
  > "becomes available in TestFlight after processing (usually 10-15 minutes)"
- Fact: a paid Apple account is needed.
  > "A paid Apple Developer account is required to submit apps to the Apple App Store."

### [Submit to the Google Play Store with Expo Submit](https://docs.expo.dev/submit/android/)

Retrieved 2026-11-27.

- Fact: the first submission works through Expo Submit.
  > "If this is your app's first submission, the default `eas submit` command works out of the box"

### [Expo Update](https://docs.expo.dev/eas-update/introduction/)

Retrieved 2026-11-27.

- Fact: updates change non-native pieces only.
  > "enabling an app to update its own non-native pieces (such as JS, styling, and images) over-the-air"
- Fact: native changes need a new binary.
  > "Anything that requires a new app binary version"

### [Runtime versions and updates](https://docs.expo.dev/eas-update/runtime-versions/)

Retrieved 2026-11-27.

- Fact: runtime versions match updates to native code.
  > "Runtime versions are a property that guarantees compatibility between a build's native code and an update."
- Fact: the `fingerprint` policy follows native changes.
  > "This will increment the runtime version whenever anything that may impact the native runtime changes"
- Fact: the `appVersion` policy follows the app version.
  > "The \"appVersion\" policy will increment the runtime version whenever the app version is incremented"

### [Manage branches and channels with EAS CLI](https://docs.expo.dev/eas-update/eas-cli/)

Retrieved 2026-11-27.

- Fact: channels are set at build time.
  > "Channels are specified at build time and exist inside a build's native code."
- Fact: any channel can link to any branch.
  > "With Expo Update, we can link any channel to any branch"

### [Expo Services pricing](https://expo.dev/pricing)

Retrieved 2026-11-27.

- Fact: the services were renamed.
  > "EAS Build is now Expo Build, EAS Update is now Expo Update"
- Fact: the free plan has a build cap.
  > "Up to 15 Android and 15 iOS builds"
- Fact: free builds time out at 45 minutes.
  > "45-minute build timeout"
- Fact: the free plan has no update overage.
  > "The Free plan has no update overage pricing; it is limited to its included monthly active users."

### [Run Expo Build locally with local flag](https://docs.expo.dev/build-reference/local-builds/)

Retrieved 2026-11-27.

- Fact: local builds run one platform at a time.
  > "You can only build for a specific platform (option `all` is disabled)."

### [App Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)

Retrieved 2026-11-27.

- Fact: guideline 2.5.2 forbids downloaded code that changes features.
  > "may not download, install, or execute code which introduces or changes features or functionality of the app"

### [TestFlight](https://developer.apple.com/testflight/)

Retrieved 2026-11-27.

- Fact: TestFlight allows many external testers.
  > "You can also invite up to 10,000 external testers to join your beta program."

### [Set up an open, closed, or internal test](https://support.google.com/googleplay/android-developer/answer/9845334)

Retrieved 2026-11-27.

- Fact: internal tests allow 100 testers.
  > "An internal test can have up to 100 testers per app."
- Fact: internal tests can start before app setup is complete.
  > "You can start an internal test before completing app setup."

### [Device and Network Abuse](https://support.google.com/googleplay/android-developer/answer/9888379)

Retrieved 2026-11-27.

- Fact: apps may not update themselves outside Google Play.
  > "may not modify, replace, or update itself using any method other than Google Play's update mechanism"
- Fact: interpreted code is an exception.
  > "code that runs in a virtual machine or an interpreter where either provides indirect access to Android APIs"

### [Apple Developer Program](https://developer.apple.com/programs/)

Retrieved 2026-11-27.

- Fact: membership costs $99 a year.
  > "$99 annual membership"

### [Get started with Play Console](https://support.google.com/googleplay/android-developer/answer/6112435)

Retrieved 2026-11-27.

- Fact: Google Play charges a one-time fee.
  > "There is a US$25 one-time registration fee."
