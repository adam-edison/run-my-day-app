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

- **Cypress:** Rejected because Cypress has no built-in offline switch. Its documented offline recipe overrides `navigator.onLine` and fails requests with `cy.intercept`. That recipe does not turn off the browser's real network.
- **Jest with jsdom:** Rejected because jsdom has no service worker and no real audio.

## Rationale

- **Real browser:** The service worker and browser APIs run as they do for users.
- **Offline control:** Playwright turns the network off and on for a whole browser context.

## Consequences

- Playwright tests are slow, so they don't run on every commit.
- Service worker tests run in Chromium only.

## Sources

### [BrowserContext | Playwright](https://playwright.dev/docs/api/class-browsercontext)

Retrieved 2026-11-27.

- Fact: Playwright turns the network off and on for a whole browser context with `setOffline`.
  > "Whether to emulate network being offline for the browser context."
- Fact: Playwright's request routing does not see requests that the service worker handles. Tests that intercept requests block the service worker.
  > "browserContext.route() will not intercept requests intercepted by Service Worker."
  > "We recommend disabling Service Workers when using request interception by setting serviceWorkers to 'block'."

### [Service Workers | Playwright](https://playwright.dev/docs/service-workers)

Retrieved 2026-11-27.

- Fact: Requests for an updated service worker script can't be routed.
  > "Requests for updated Service Worker main script code currently cannot be routed."
- Fact: Service worker tests run in Chromium only.
  > "Service workers are only supported on Chromium-based browsers."

### [Network | Playwright](https://playwright.dev/docs/network)

Retrieved 2026-11-27.

- Fact: Tests that intercept requests block the service worker.
  > "and it appears network events are missing, disable Service Workers by setting serviceWorkers to 'block'."

### [cypress-example-recipes/examples/server-communication__offline](https://github.com/cypress-io/cypress-example-recipes/tree/master/examples/server-communication__offline)

Retrieved 2026-11-27.

- Fact: Cypress's documented offline recipe overrides `navigator.onLine` and fails requests with `cy.intercept`.
  > "Defining `onLine` as an own property on `window.navigator` shadows the getter inherited from `Navigator.prototype`,"
  > "Where an entire test is offline, the static `{ forceNetworkError: true }` response is enough."
- Fact: That recipe does not turn off the browser's real network.
  > "Neither touches the browser's real network stack, so nothing here is specific to one browser engine."

### [server-communication__offline recipe does not work with Chromium browsers v97+ · Issue #772](https://github.com/cypress-io/cypress-example-recipes/issues/772)

Retrieved 2026-11-27.

- Fact: Cypress's documented offline recipe no longer uses Chrome DevTools Protocol commands.
  > "the recipe now simulates the two things an application actually observes when the network drops"

### [Testing an Application in Offline Network Mode](https://www.cypress.io/blog/testing-application-in-offline-network-mode)

Retrieved 2026-11-27.

- Fact: Cypress has no built-in offline switch. Its older method sent Chrome DevTools Protocol commands.
  > "we need to actually go offline using the Network.emulateNetworkConditions command."
  > "The only browser that cannot run these tests is Firefox."

### [Does jsdom support video and audio elements? · Issue #2155 · jsdom/jsdom](https://github.com/jsdom/jsdom/issues/2155)

Retrieved 2026-11-27.

- Fact: jsdom has no real audio.
  > "Nobody has implemented a video or audio player in jsdom yet."
