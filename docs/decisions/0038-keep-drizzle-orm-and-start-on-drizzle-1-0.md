# DR-0038: Keep Drizzle ORM and Start on Drizzle 1.0

**Status:** Draft  
**Date:** 2026-11-28

## Context

Each device stores data in expo-sqlite, with one SQLite schema and one set of queries on every platform, as recorded in DR-0016. The server stores data in PostgreSQL.

Drizzle facts:

* Drizzle has a native expo-sqlite driver.
* Expo apps must bundle SQL migrations into the app as strings.
* Drizzle runs migrations on the device.
* The `useLiveQuery` hook makes any Drizzle query reactive.
* The npm `latest` tag is 0.45.4.
* Version 1.0 is at release candidate `1.0.0-rc.6` on npm.
* Version 1.0 changes the migration folder format and removes the journal.
* Version 1.0 brings the Relational Queries V2 API.

expo-sqlite on the web:

* Drizzle's expo-sqlite page does not mention the web. Web use goes through expo-sqlite's WebAssembly build.
* expo-sqlite on the web needs Metro config for wasm files.
* The static host must send the Cross-Origin-Embedder-Policy and Cross-Origin-Opener-Policy headers.
* The web build of SQLite has no full-text search.

Example: the user ticks off a routine step on the phone. A `useLiveQuery` query on the routine screen updates the checklist without a manual refresh.

## Decision

* The server and the devices keep Drizzle ORM.
* The project starts on Drizzle 1.0.
* Migrations on devices use `babel-plugin-inline-import` and add `sql` to Metro's `sourceExts`.
* The web build configures Metro for wasm files.
* The static host sends the Cross-Origin-Embedder-Policy and Cross-Origin-Opener-Policy headers.

## Alternatives Considered

Prisma, rejected:

* Prisma was not checked for expo-sqlite support.
* The npm latest version is a release candidate, 8.0.0-rc.36.

Kysely, rejected:

* Kysely is a query builder.
* Kysely was not checked for expo-sqlite support.

Starting on Drizzle 0.45, rejected:

* Version 1.0 changes the migration folder format and relational queries. Starting on 0.45 would mean a migration soon after.

## Rationale

* Drizzle documents an expo-sqlite driver with bundled migrations and live queries.
* Starting on 1.0 avoids an early migration of the migration folder and the relational queries.

## Consequences

* Drizzle 1.0 is a release candidate now.
* No official source recommends starting on 1.0. This choice is own reasoning.
* Drizzle's expo-sqlite page does not mention the web.
* The web build of SQLite has no full-text search.
* Prisma and Kysely were not compared against sources.

## Open Questions

1. Does the project start on Drizzle 1.0, which is a release candidate now, or on 0.45, which is stable now, and upgrade later? Example: on 1.0, the first migration folder already uses the new format. On 0.45, the migration folder and relational queries change during the upgrade.

## Sources

### [Drizzle ORM - Expo SQLite](https://orm.drizzle.team/docs/connect-expo-sqlite)

Retrieved 2026-11-27.

- Fact: Drizzle has an expo-sqlite driver.
  > "Native ORM driver for Expo SQLite."
- Fact: Migrations must be bundled into the app.
  > "Expo / React Native requires you to have SQL migrations bundled into the app and we've got you covered."
- Fact: Migration files are bundled as strings.
  > "It's necessary to bundle SQL migration files as string directly to your bundle."
- Fact: Drizzle queries can update the screen when data changes.
  > "With `useLiveQuery` hook you can make any Drizzle query reactive:"

### [Roadmap | Drizzle ORM](https://orm.drizzle.team/roadmap)

Retrieved 2026-11-27.

- Fact: Version 1.0 changes the migration folder.
  > "Migrate to folder v3, remove journal"
- Fact: Version 1.0 brings a new relational query API.
  > "Relational Queries V2 API"

### [drizzle-orm on npm](https://registry.npmjs.org/drizzle-orm)

Retrieved 2026-11-27.

- Fact: The npm tags show 0.45.4 as latest and 1.0.0-rc.6 as the release candidate.
  > "\"latest\":\"0.45.4\"" and "\"rc\":\"1.0.0-rc.6\""

### [SQLite | Expo Documentation](https://docs.expo.dev/versions/latest/sdk/sqlite/)

Retrieved 2026-11-27.

- Fact: The web needs Metro config for wasm files and extra HTTP headers.
  > "To use expo-sqlite on web, you need to configure Metro bundler to support wasm files and add HTTP headers to allow"
- Fact: The static host must send two cross-origin headers.
  > "If you deploy your app to web hosting services, you will also need to add the Cross-Origin-Embedder-Policy and Cross-Origin-Opener-Policy headers"
- Fact: The web build of SQLite has no full-text search.
  > "On web, expo-sqlite uses a prebuilt WebAssembly build of SQLite without full-text search (FTS)."
