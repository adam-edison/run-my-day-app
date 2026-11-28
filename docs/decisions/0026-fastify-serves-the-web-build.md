# DR-0026: Fastify Serves the Web Build

**Status:** Accepted  
**Date:** 2026-11-28

## Context

The Expo web export produces static files in the `dist` folder: the web app's HTML, JavaScript, and images. The files must be served from somewhere.

Sound and music files are never served by the server. Each device plays its own local files, as recorded in DR-0003.

Facts from earlier decisions:

* The web build is exported as static files. Expo API routes are not used, as recorded in DR-0004.
* Workbox generates a service worker after the export, for offline use.
* expo-sqlite on web needs the `Cross-Origin-Embedder-Policy` and `Cross-Origin-Opener-Policy` headers, as recorded in DR-0016.
* Only Chromium-based desktop browsers are supported, as recorded in DR-0008.
* The server is reached over Tailscale or a tunnel.

A service worker must be on the same origin as the page. A cross-origin API call in `cors` mode must be permitted by CORS.

Example: the laptop opens `https://home-mac.tailnet-name.ts.net/` and gets both the app and the API.

## Decision

* The Fastify server serves `dist` with `@fastify/static`, in local hosting, self-hosting, and the hosted version.
* Fastify sets `Cross-Origin-Embedder-Policy: credentialless` on the files.
* Fastify sets `Cross-Origin-Opener-Policy: same-origin` on the files.
* The app and the API share one origin.
* A CDN may be added to the hosted version later, if load times require it.
* When a new service worker is waiting, the app shows an update bar with an update button.
* The update button activates the waiting service worker, then reloads the page.
* A new service worker never takes over without the user's action, so a running routine is not interrupted.

Example: the laptop loads the "Morning" routine page from the Fastify server. The page calls the API on the same origin with no CORS setup.

## Alternatives Considered

A new service worker takes over as soon as it installs (`skipWaiting`), rejected:

* The app's code could change in the middle of a running routine.
* Chrome's Workbox documentation warns that `skipWaiting` "may be a bad idea."

A new service worker waits until every tab is closed, rejected:

* A tab left open all day keeps the old version.

Cloudflare Pages or Netlify, rejected:

* The app and the API are on different origins, so the server needs CORS.
* A public site cannot reach a server that is private inside a tailnet unless the device runs Tailscale.
* Each self-hoster needs an account with the host.

GitHub Pages, rejected:

* No source read describes custom HTTP headers on GitHub Pages. Without the COOP and COEP headers, expo-sqlite web cannot work.
* The app and the API are on different origins, as with Cloudflare Pages or Netlify.

## Rationale

* `@fastify/static` can set custom headers on each file, such as COOP and COEP.
* One origin needs no CORS setup for API calls.
* The app is reachable wherever the server is reachable, including inside a private tailnet.
* A self-hoster deploys one thing.

## Consequences

* The Fastify process also serves static files. For one user the load is small.
* No official source compares one origin with a static host for this case. The choice is not verified.
* Chrome ignores caching headers when it checks for a new service worker script, so a cached `sw.js` does not delay an update.
* A tab left open keeps the old version until the user presses the update button.

## Sources

### [The service worker lifecycle | web.dev](https://web.dev/articles/service-worker-lifecycle)

Retrieved 2026-11-28.

- Fact: Chrome ignores caching headers when it checks for a new service worker script.
  > "Most browsers, including Chrome 68 and later, default to ignoring caching headers when checking for updates of the registered service worker script."
- Fact: A new service worker waits until every tab is closed.
  > "Once successfully installed, the updated worker will `wait` until the existing worker is controlling zero clients."

### [ServiceWorkerRegistration: updateViaCache property - MDN](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerRegistration/updateViaCache)

Retrieved 2026-11-28.

- Fact: By default, the HTTP cache is not used for service worker script updates.
  > "the HTTP cache is not consulted for updates to the service worker script"

### [The service worker lifecycle | Workbox](https://developer.chrome.com/docs/workbox/service-worker-lifecycle)

Retrieved 2026-11-28.

- Fact: Chrome's Workbox documentation warns against `skipWaiting`.
  > "You can speed up activation of updated service workers by calling `self.skipWaiting`, but this may be a bad idea."

### [SQLite | Expo Documentation](https://docs.expo.dev/versions/latest/sdk/sqlite/)

Retrieved 2026-11-27.

- Fact: A deployed web build needs the COEP and COOP headers.
  > "If you deploy your app to web hosting services, you will also need to add the"
  > "`Cross-Origin-Embedder-Policy` and `Cross-Origin-Opener-Policy` headers to your web server."
- Fact: The documented values are `credentialless` and `same-origin`. (Values read from the page's configuration example.)

### [Publishing websites | Expo Documentation](https://docs.expo.dev/guides/publishing-websites/)

Retrieved 2026-11-27.

- Fact: The export writes files to `dist`.
  > "The resulting project files are located in the **dist** directory."
- Fact: Self-hosting is allowed.
  > "You can also self-host or use a third-party service."

### [@fastify/static](https://github.com/fastify/fastify-static)

Retrieved 2026-11-27.

- Fact: The plugin serves static files.
  > "Plugin for serving static files as fast as possible."
- Fact: The plugin can set custom headers.
  > "A function to set custom headers on the response."

### [Headers | Cloudflare Pages](https://developers.cloudflare.com/pages/configuration/headers/)

Retrieved 2026-11-27.

- Fact: Pages applies `_headers` rules to static files.
  > "and its rules will be applied to static asset responses."
- Fact: Pages allows 100 header rules.
  > "You may define up to 100 header rules."

### [Custom headers | Netlify](https://docs.netlify.com/manage/routing/headers/)

Retrieved 2026-11-27.

- Fact: Netlify supports custom headers.
  > "You can configure custom headers for your Netlify site in two ways:"

### [Cross-Origin-Embedder-Policy | MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Cross-Origin-Embedder-Policy)

Retrieved 2026-11-27.

- Fact: Cross-origin API calls still need CORS.
  > "a cross-origin resource requested in `cors` mode must support (and be permitted by) CORS."

### [ServiceWorkerContainer.register() | MDN](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerContainer/register)

Retrieved 2026-11-27.

- Fact: The service worker must be on the same origin as the page.
  > "The `scriptURL` and scope are not same-origin with the registering page."
