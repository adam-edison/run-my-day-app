# DR-0012: Web First

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Run My Day runs on Android, iOS, and web from one Expo codebase. Development and testing on the web need no native builds, no emulators, and no app store releases. Some phone capabilities that the design depends on are not yet proven.

## Decision

Every feature is built and tested first in the Expo web build, in Chromium. Mobile work waits until the features are built, except for trials of phone capabilities.

The code stays ready for phones:

- Every component is a React Native component, and all logic is in the shared core package. No web-only shortcuts are taken.
- Only the web platform shell is built. The Android and iOS parts of the platform shell, such as background audio, are built later in platform-specific files.
- Layouts are responsive from the start.

The phone capability trials run early, before features depend on their results:

- background audio with the screen locked, on Android and iOS
- on-device text-to-speech speed with ONNX Runtime
- audio starting within 100 milliseconds of its start mark on a phone
- local storage for offline use

The Expo web trial from DR-0004 is the first task.

## Alternatives Considered

- **Every feature on all platforms at once:** Rejected because every feature would need native builds, emulators, and device tests.
- **Mobile first:** Rejected because the phone platforms would set the pace of all development.

## Rationale

- **No native builds:** Features are built and tested in a browser, with no native builds.
- **Faster iteration:** Expo presents the iteration speed of web development as a goal for React Native development.
- **Early proof:** The trials show whether the phones can do what the design assumes before features depend on it.
- **One codebase:** Shared components and core code carry over to the phones unchanged.
- **Phone-ready layouts:** Responsive layouts fit phone screens.
- **Web trial first:** All early development runs in the web build.

## Consequences

- The phone apps are not usable until mobile work starts.
- A failed trial can change the design.
- Layouts made for a desktop screen may still need changes on phones.

## Sources

### [Develop websites with Expo](https://docs.expo.dev/workflow/web/)

Retrieved 2026-11-27.

- Fact: React Native components run in the Expo web build.
  > "React Native for web (RNW) is a set of component libraries such as `<View>`, and `<Text>`, that wrap `react-dom` primitives such as `<div>`, `<p>`, and `<img>`."
- Fact: Development on the web runs in a browser, with no native builds.
  > "You can now start the dev server and develop in the browser with:"

### [Increase iteration speed with PR previews | Expo](https://expo.dev/blog/increase-iteration-speed-with-pr-previews)

Retrieved 2026-11-28.

- Fact: Expo presents the iteration speed of web development as a goal for React Native development.
  > "bringing the iteration speed of web development to React Native"

### [Platform-specific extensions and module](https://docs.expo.dev/router/advanced/platform-specific-modules/)

Retrieved 2026-11-27.

- Fact: The Android and iOS parts of the platform shell are built in platform-specific files.
  > "You can create platform-specific files with extensions (for example, **.android.tsx**, **.ios.tsx**, **.native.tsx**, or **.web.tsx**) outside the **src/app** directory and use them from within the **src/app** directory."

### [Deploy on mobile (ONNX Runtime)](https://onnxruntime.ai/docs/tutorials/mobile/)

Retrieved 2026-11-27.

- Fact: ONNX Runtime runs models on the phone.
  > "Because the model is loaded and run on device, the model must fit on the device disk and be able to be loaded into the device’s memory."
