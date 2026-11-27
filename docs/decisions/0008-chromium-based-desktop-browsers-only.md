# DR-0008: Chromium-Based Desktop Browsers Only

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Run My Day runs as an app on Android and iOS, and as a web app on desktop. The web app depends on a service worker for offline use. Playwright, the tool for web boundary tests, supports service workers only in Chromium-based browsers.

## Decision

The web app supports Chromium-based desktop browsers only. Phone browsers, Safari, and Firefox are not supported. On a phone, Run My Day is used through the Android or iOS app.

The major Chromium-based desktop browsers are:

- Google Chrome
- Chromium
- Microsoft Edge
- Brave
- Opera and Opera GX
- Vivaldi
- Arc
- Dia
- Perplexity Comet
- ChatGPT Atlas

Example: on a Mac, you open Run My Day in Chrome, not Safari.

## Alternatives Considered

- **Every major desktop browser:** Rejected because service worker and offline behavior could be tested automatically only in Chromium.
- **Phone browsers as well:** Rejected because the Android and iOS apps cover phones.

## Rationale

- **Tested offline use:** Every supported browser runs the same automated offline tests.
- **Smaller test matrix:** One browser engine is tested on desktop.

## Consequences

- Users of Safari and Firefox on desktop must use a Chromium-based browser.
- Run My Day may still open in an unsupported browser, but offline use there is not tested.
