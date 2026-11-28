# DR-0016: expo-sqlite for Offline Storage

**Status:** Proposed  
**Date:** 2026-11-27

## Context

Run My Day works fully offline on every platform.

Data kept on each device:

* every definition, such as tasks, routines, task views, and progressions
* the last good calendar read, covering 24 hours back to 14 days ahead
* 90 days of history
* a snapshot of each value read from history, such as a streak, a run count toward a level, a skip count, or a learned estimate
* changes made offline that the server has not yet confirmed

Data kept only on the server:

* history older than 90 days

Example: the "Run 5k" level moves up after 5 runs in a row that meet its metric conditions. The phone keeps the snapshot "4 of 5 runs in a row." On a hike with no signal, the 5th run is completed. The snapshot becomes 5 of 5, and the level moves up offline.

Challenges:

* A task view filter nests groups of all-or-any conditions, which needs relational queries.
* One codebase serves Android, iOS, and web.

## Decision

* Client storage is expo-sqlite on Android, iOS, and web.
* One SQLite schema and one set of queries serve every platform.
* The web build calls `navigator.storage.persist()`. When Chrome grants the request, Chrome deletes the stored data only when the user chooses to.
* Chrome grants the request without a prompt, and only to a site it considers important. Otherwise, Chrome denies the request silently.
* Chrome counts an installed site as one sign of importance.
* The web build asks the user to install the app.
* The web build calls `navigator.storage.persist()` again after installation.
* The web build checks the result with `navigator.storage.persisted()`.
* The status stays Proposed until the Expo web trial from DR-0004 passes two checks:
  * A task saved in expo-sqlite is still listed after a reload with the network off.
  * Drizzle queries on expo-sqlite run in the web build.

Example: in the web build with the network off, the task "Stretch" is created and the page is reloaded. "Stretch" is still listed, read by a Drizzle query from expo-sqlite.

## Alternatives Considered

Full history on every device, rejected:

* Storage grows by about 30 MB a year, without limit.
* Partial sync is the established pattern for keeping device storage small.

expo-sqlite on phones and IndexedDB on web, rejected:

* Every query and migration would be written twice.

A sync tool that includes its own storage, such as PowerSync, TinyBase, or LiveStore, rejected:

* The storage choice would be tied to a sync choice that has not been made.

## Rationale

* One schema serves every platform.
* SQL handles the nested filter groups of task views.
* Device storage stays at about 7 MB of history, from 90 days at about 30 MB a year.

## Consequences

* Expo marked web support in expo-sqlite as alpha up to SDK 57.
* The SDK 58 docs no longer mark web support in expo-sqlite as alpha.
* The web build needs Metro set up for WASM files and COOP/COEP HTTP headers.
* If the web trial fails, the fallback is IndexedDB on web.
* A condition is evaluated on the device and on the server, and both must give the same answer.

## Sources

### [Expo SQLite](https://docs.expo.dev/versions/latest/sdk/sqlite/)

Retrieved 2026-11-27.

- Fact: expo-sqlite runs on Android, iOS, and web.
  > "platforms: ['android', 'ios', 'macos', 'tvos', 'web', 'expo-go']"
- Fact: The web build needs Metro set up for WASM files and COOP/COEP HTTP headers.
  > "To use `expo-sqlite` on web, you need to configure Metro bundler to support **wasm** files and add HTTP headers to allow [`SharedArrayBuffer`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) usage."
  > "If you deploy your app to web hosting services, you will also need to add the `Cross-Origin-Embedder-Policy` and `Cross-Origin-Opener-Policy` headers to your web server."

### [Expo SQLite (SDK 57)](https://docs.expo.dev/versions/v57.0.0/sdk/sqlite/)

Retrieved 2026-11-27.

- Fact: Expo marked web support in expo-sqlite as alpha up to SDK 57.
  > "Web support is in [alpha](https://docs.expo.dev/more/release-statuses.md#alpha) and may be unstable."

### [[docs] Remove alpha status from expo-sqlite web, patch-project, and DOM components (expo/expo#48539)](https://github.com/expo/expo/pull/48539)

Retrieved 2026-11-27.

- Fact: The SDK 58 docs no longer mark web support in expo-sqlite as alpha.
  > "remove alpha or experiment tag for expo-sqlite web, patch-project and some features in dom components"

### [Persistent storage | web.dev](https://web.dev/articles/persistent-storage)

Retrieved 2026-11-27.

- Fact: When Chrome grants the request from `navigator.storage.persist()`, Chrome deletes the stored data only when the user chooses to.
  > "Persistent storage is kept by the browser, even if storage is running low. It's only deleted if the user chooses to remove it using their site settings."
- Fact: Chrome grants the request without a prompt, and only to a site it considers important. Otherwise, Chrome denies the request silently.
  > "Chrome, and most other Chromium-based browsers automatically handle the permission request, and do not show any prompts to the user. Instead, if a site is considered important, the persistent storage permission is automatically granted, otherwise it is silently denied."
- Fact: Chrome counts an installed site as one sign of importance.
  > "The heuristics to determine if a site is important include:"
  > "Has the site been installed or bookmarked?"
- Fact: A denied request can be made again later.
  > "If the request was denied, it can be requested again later and will be evaluated using the same heuristics."

### [Drizzle ORM - Expo SQLite](https://orm.drizzle.team/docs/connect-expo-sqlite)

Retrieved 2026-11-27.

- Fact: Drizzle runs queries on expo-sqlite.
  > "Drizzle ORM has the best in class toolkit for Expo SQLite:"

### [Sync Streams (PowerSync)](https://docs.powersync.com/sync/overview)

Retrieved 2026-11-27.

- Fact: Partial sync is the established pattern for keeping device storage small.
  > "This enables *partial sync*: each client syncs only the relevant subset of data, instead of the entire database."

### [Shapes - Guide (Electric)](https://electric-sql.com/docs/guides/shapes)

Retrieved 2026-11-27.

- Fact: Partial sync is the established pattern for keeping device storage small.
  > "Defining shapes allows you to sync just the data you want and just the data that's practical to sync onto the local device."

### [PowerSync Docs](https://docs.powersync.com/intro/powersync-overview)

Retrieved 2026-11-27.

- Fact: PowerSync is a sync tool that includes its own storage.
  > "PowerSync keeps in-app SQLite synced with your backend so apps update across users and devices in real time, respond instantly, and continue working offline."

### [TinyBase](https://tinybase.org/)

Retrieved 2026-11-27.

- Fact: TinyBase is a sync tool that includes its own storage.
  > "TinyBase is a reactive in-memory data store with persistence and synchronization for local-first JavaScript and TypeScript apps that need to feel instant, work offline, and stay consistent when they come back online."

### [LiveStore: Local-first data layer for high-performance apps](https://livestore.dev/)

Retrieved 2026-11-27.

- Fact: LiveStore is a sync tool that includes its own storage.
  > "LiveStore is a next-generation state management framework based on reactive SQLite and git-inspired syncing (via event-sourcing)."