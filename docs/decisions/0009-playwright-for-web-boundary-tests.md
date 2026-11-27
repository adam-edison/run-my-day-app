# DR-0009: Playwright for Web Boundary Tests

**Status:** Accepted  
**Date:** 2026-11-27

## Context

The web build runs in a browser, with a service worker for offline use. Code written only for the web, such as `Player.web.tsx`, runs only in a browser. Jest and Vitest run in Node, so they can't test the service worker, browser storage, or browser audio.

## Decision

Playwright tests the exported Expo web build in a real browser, against the real server. It covers:

- the service worker, and offline use with the network turned off
- sync of changes made offline after the connection returns
- code written only for the web

Playwright tests run in Chromium, the only browser engine Run My Day supports on desktop, as recorded in DR-0008.

- **Offline tests** turn off the network with `setOffline` and keep the service worker on, so cached files and queued changes are tested as users meet them.
- **Tests that intercept requests** block the service worker. Playwright's request routing does not see requests that the service worker handles.
- **Service worker update tests** run against the real server, because requests for an updated service worker script can't be routed.

Playwright tests run on a schedule in GitHub Actions and before a release.

## Alternatives Considered

- **Cypress:** Rejected because Cypress has no built-in offline switch. Its documented method sends Chrome DevTools Protocol commands, which work only in Chromium-based browsers and have reported hangs.
- **Jest with jsdom:** Rejected because jsdom has no service worker and no real audio.

## Rationale

- **Real browser:** The service worker and browser APIs run as they do for users.
- **Offline control:** Playwright turns the network off and on for a whole browser context.

## Consequences

- Playwright tests are slow, so they don't run on every commit.
- Service worker tests run in Chromium only.
