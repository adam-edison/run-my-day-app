# DR-0026: Fastify Serves the Web Build

**Status:** Draft  
**Date:** 2026-11-28

## Context

The Expo web export produces static files in the `dist` folder. The files must be served from somewhere.

Facts from earlier decisions:

* The web build is exported as static files. Expo API routes are not used, as recorded in DR-0004.
* Workbox generates a service worker after the export, for offline use.
* expo-sqlite on web needs the `Cross-Origin-Embedder-Policy` and `Cross-Origin-Opener-Policy` headers, as recorded in DR-0016.
* Only Chromium-based desktop browsers are supported, as recorded in DR-0008.
* The server is reached over Tailscale or a tunnel.

A service worker must be on the same origin as the page. A cross-origin API call in `cors` mode must be permitted by CORS.

Example: the laptop opens `https://home-mac.tailnet-name.ts.net/` and gets both the app and the API.

## Decision

Self-hosted version:

* The Fastify server serves `dist` with `@fastify/static`.
* Fastify sets `Cross-Origin-Embedder-Policy: credentialless` on the files.
* Fastify sets `Cross-Origin-Opener-Policy: same-origin` on the files.
* The app and the API share one origin.

Hosted version:

* A static host or CDN may be chosen later.

Example: the laptop loads the "Morning" routine page from the Fastify server. The page calls the API on the same origin with no CORS setup.

## Alternatives Considered

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
* No official source states how the caching of `sw.js` affects updates for this case.

## Open Questions

1. Serve the app from the server, or from a static host? Example: the laptop opens `https://home-mac.tailnet-name.ts.net/` and gets both the app and the API.
2. Must the hosted version use a CDN from day one, or can it also start with Fastify serving the files?
3. How is the service worker file cached? Example: if `sw.js` is cached for a day, a fix to "Morning" reaches the laptop a day late.

## Sources

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
