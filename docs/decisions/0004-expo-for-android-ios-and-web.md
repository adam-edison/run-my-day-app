# DR-0004: Expo for Android, iOS, and Web

**Status:** Proposed  
**Date:** 2026-11-27

## Context

Run My Day runs on Android, iOS, and web. The phone is used both to plan the day and to run it, so every platform needs the same features. The app works fully offline on every platform. Building a separate app for each platform would build every feature more than once.

## Decision

The client is one Expo codebase for Android, iOS, and web.

Code is shared by default. Code for one platform is added only where that platform needs it, such as background audio, which iOS and Android each configure differently. Platform code is kept in platform-specific files, such as `Player.ios.tsx`, and never in the client state actors.

Expo's web build is exported as static files. Offline use on the web is provided by a service worker, generated with Workbox after the export. Expo API routes are not used. The server is a separate Fastify server.

The status stays Proposed until a trial passes: one screen plus a service worker in Expo web, working offline.

## Alternatives Considered

- **A separate web app and a separate React Native app:** Rejected because each screen would be built twice.
- **A web app wrapped for phones (Capacitor):** Rejected because on-device text-to-speech and precise audio timing need native code.
- **Flutter:** Rejected because the rest of the stack is TypeScript.

## Rationale

- **One codebase:** Each feature is built once for all three platforms.
- **Native access:** Android and iOS get native audio and on-device model inference.
- **One language:** The client and server share TypeScript types and schemas.

## Consequences

- Some libraries work only on native, not on the web.
- Service workers can serve stale files from their cache. Updates to the web app need a clear refresh path.
- Builds go through Expo Build.
- App store releases go through Expo Submit.

## Sources

### [Develop websites with Expo](https://docs.expo.dev/workflow/web/)

Retrieved 2026-11-27.

- Fact: One Expo codebase renders React Native components on the web through React Native for web.
  > "React Native for web (RNW) is a set of component libraries such as `<View>`, and `<Text>`, that wrap `react-dom` primitives such as `<div>`, `<p>`, and `<img>`. RNW is optional when developing for web since you can use React DOM directly, but we often recommended it when building across platforms as it maximizes code reuse."

### [Platform-specific extensions and module](https://docs.expo.dev/router/advanced/platform-specific-modules/)

Retrieved 2026-11-27.

- Fact: Code for one platform is kept in a platform-specific file, such as `Player.ios.tsx`.
  > "You can create platform-specific files with extensions (for example, **.android.tsx**, **.ios.tsx**, **.native.tsx**, or **.web.tsx**) outside the **src/app** directory and use them from within the **src/app** directory."

### [Audio (expo-audio)](https://docs.expo.dev/versions/latest/sdk/audio/)

Retrieved 2026-11-27.

- Fact: iOS and Android each configure background audio differently.
  > "Android Adds `FOREGROUND_SERVICE` and `FOREGROUND_SERVICE_MEDIA_PLAYBACK` permissions. Also declares a media playback foreground service (`AudioControlsService`) in app's **AndroidManifest.xml**."
  > "iOS Adds the `audio` `UIBackgroundMode` capability"

### [Publish websites](https://docs.expo.dev/guides/publishing-websites/)

Retrieved 2026-11-27.

- Fact: Expo's web build is exported as static files.
  > "Whether you want to serve it locally or deploy to a hosting service, you'll need to export all JavaScript and assets of a project. This is known as a static bundle."
- Fact: Expo offers API routes.
  > "API routes are enabled by default with `server` output. Set `apiRoutes: false` to disable them in either mode."

### [Progressive web apps](https://docs.expo.dev/guides/progressive-web-apps/)

Retrieved 2026-11-27.

- Fact: A service worker for offline use is generated with Workbox after the export.
  > "Google's Workbox is the best way to add service workers to a website. Follow the guide for [using Workbox CLI](https://developer.chrome.com/docs/workbox/modules/workbox-cli/), and wherever it refers to a "build script" use `npx expo export -p web` instead."
- Fact: Service workers can serve stale files from their cache.
  > "If you accidentally ship a service worker that aggressively caches your website, users cannot request updates easily."

### [Contacts](https://docs.expo.dev/versions/latest/sdk/contacts/)

Retrieved 2026-11-27.

- Fact: Some libraries work only on native.
  > "platforms: ['android', 'ios', 'expo-go']"

### [Capacitor - Cross-platform Native Runtime for Web Apps](https://capacitorjs.com/docs)

Retrieved 2026-11-27.

- Fact: Capacitor wraps a web app for phones.
  > "If it works in the browser, it probably works in a mobile app when using Capacitor. Adding native functionality is straightforward with a Plugin API for Swift on iOS, Java on Android, and JavaScript for the web."

### [FAQ (Flutter)](https://docs.flutter.dev/resources/faq)

Retrieved 2026-11-27.

- Fact: Flutter does not use TypeScript.
  > "The Flutter framework is written in Dart. The underlying engine and graphics pipeline are implemented in C and C++."

### [Deploy on mobile (ONNX Runtime)](https://onnxruntime.ai/docs/tutorials/mobile/)

Retrieved 2026-11-27.

- Fact: Android and iOS get on-device model inference.
  > "Because the model is loaded and run on device, the model must fit on the device disk and be able to be loaded into the device’s memory."

### [Expo Build](https://docs.expo.dev/build/introduction/)

Retrieved 2026-11-27.

- Fact: Builds go through Expo Build.
  > "Expo Build is a hosted service for building app binaries for your Expo and React Native projects."

### [Submit to app stores](https://docs.expo.dev/submit/introduction/)

Retrieved 2026-11-27.

- Fact: App store releases go through Expo Submit.
  > "You have two options for getting a binary into each store: submit with **Expo Submit**, or upload manually through the store's own tools."

### [[docs] Rename EAS to Expo Services in the Reference section (expo/expo#52459)](https://github.com/expo/expo/pull/52459)

Retrieved 2026-11-27.

- Fact: Expo no longer uses the name EAS for its services.
  > "Phase 1 of the EAS name deprecation replaces them with "Expo Services" and "Expo [Service]" in customer-facing text."
