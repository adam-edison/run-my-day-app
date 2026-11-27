# DR-0004: Expo for Android, iOS, and Web

**Status:** Proposed  
**Date:** 2026-11-27

## Context

Run My Day runs on Android, iOS, and web. The phone is used both to plan the day and to run it, so every platform needs the same features. The app works fully offline on every platform. Building a separate app for each platform would build every feature more than once.

## Decision

The client is one Expo codebase for Android, iOS, and web.

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

- Web support in Expo is less mature than native support, so some libraries work only on native.
- Service workers can serve stale files from their cache. Updates to the web app need a clear refresh path.
- Builds and app store releases go through Expo EAS.
