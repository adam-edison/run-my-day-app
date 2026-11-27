# DR-0016: expo-sqlite for Offline Storage

**Status:** Proposed  
**Date:** 2026-11-27

## Context

Run My Day works fully offline on every platform.

Data kept on each device:

* every definition, such as tasks, routines, task views, and progressions
* the last good calendar read, covering 24 hours back to 14 days ahead
* history for a recent window
* a snapshot of each value read from history, such as a streak, a run count toward a level, a skip count, or a learned estimate
* changes made offline that the server has not yet confirmed

Data kept only on the server:

* history older than the recent window

Example: the "Run 5k" level moves up after 5 runs in a row that meet its metric conditions. The phone keeps the snapshot "4 of 5 runs in a row." On a hike with no signal, the 5th run is completed. The snapshot becomes 5 of 5, and the level moves up offline.

Challenges:

* A task view filter nests groups of all-or-any conditions, which needs relational queries.
* One codebase serves Android, iOS, and web.

## Decision

* Client storage is expo-sqlite on Android, iOS, and web.
* One SQLite schema and one set of queries serve every platform.
* The web build calls `navigator.storage.persist()`, so Chrome deletes the stored data only when the user chooses to.
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
* Device storage stays at a few MB.

## Consequences

* Expo marks web support in expo-sqlite as alpha.
* The web build needs Metro set up for WASM files and COOP/COEP HTTP headers.
* If the web trial fails, the fallback is IndexedDB on web.
* A condition is evaluated on the device and on the server, and both must give the same answer.
* The length of the recent history window is decided with sync.
