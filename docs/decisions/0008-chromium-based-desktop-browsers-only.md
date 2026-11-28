# DR-0008: Chromium-Based Desktop Browsers Only

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Run My Day runs as an app on Android and iOS, and as a web app on desktop. The web app depends on a service worker for offline use. Playwright, the tool for web boundary tests, supports service workers only in Chromium-based browsers.

## Decision

The web app supports Chromium-based desktop browsers only. Phone browsers, Safari, and Firefox are not supported. On a phone, Run My Day is used through the Android or iOS app.

The supported browsers are the Chromium-based browsers with the largest desktop market share:

- Google Chrome, 66.98%
- Microsoft Edge, 13.24%
- Opera, 2.65%
- Brave, 1.01%

The shares are worldwide desktop shares for October 2026. Other Chromium-based browsers are not supported.

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

## Sources

### [Service Workers | Playwright](https://playwright.dev/docs/service-workers)

Retrieved 2026-11-27.

- Fact: Playwright supports service workers only in Chromium-based browsers.
  > "Service workers are only supported on Chromium-based browsers."

### [Chromium Projects](https://www.chromium.org/chromium-projects/)

Retrieved 2026-11-27.

- Fact: Google Chrome is a Chromium-based browser.
  > "The Chromium projects include Chromium and ChromiumOS, the open-source projects behind the Google Chrome browser and Google ChromeOS, respectively."

### [Microsoft Edge Developer](https://developer.microsoft.com/en-us/microsoft-edge/)

Retrieved 2026-11-27.

- Fact: Microsoft Edge is a Chromium-based browser.
  > "Microsoft Edge is built on Chromium and provides the best-in-class extension and web compatibility."

### [About Brave | Brave](https://brave.com/about/)

Retrieved 2026-11-27.

- Fact: Brave is a Chromium-based desktop browser.
  > "The Brave browser for Android and desktop is a Chromium fork that removes privacy-harmful features and disables or proxies connections to Google when possible; for iOS it’s built on Apple’s WebKit."

### [Opera joins Supporters of Chromium-Based Browsers to help boost the open-source ecosystem - Blog | Opera News](https://blogs.opera.com/news/2025/01/opera-joins-supporters-of-chromium-based-browsers-open-source-ecosystem/)

Retrieved 2026-11-27.

- Fact: Opera is a Chromium-based browser.
  > "Opera has been based on Chromium since 2013 and we have benefited from the ecosystem as well as actively contributed to it throughout that time."

### [Desktop Browser Market Share Worldwide | Statcounter Global Stats](https://gs.statcounter.com/browser-market-share/desktop/worldwide)

Retrieved 2026-11-27.

- Fact: Worldwide desktop browser shares for October 2026 are Chrome 66.98%, Edge 13.24%, Opera 2.65%, and Brave 1.01%.
  > "Desktop Browser Market Share Worldwide - October 2026"
  > "Chrome: 66.98%"
  > "Edge: 13.24%"
  > "Safari: 7.83%"
  > "Firefox: 5.72%"
  > "Opera: 2.65%"
  > "Brave: 1.01%"
