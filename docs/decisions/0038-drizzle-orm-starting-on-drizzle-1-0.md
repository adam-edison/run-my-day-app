# DR-0038: Drizzle ORM, Starting on Drizzle 1.0

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

Example: the user ticks off a routine step on the phone. The checklist on the routine screen must update without a manual refresh.

## Decision

* The server and the devices use Drizzle ORM.
* The project starts on Drizzle 1.0.
* Migrations on devices use `babel-plugin-inline-import` and add `sql` to Metro's `sourceExts`.
* The web build configures Metro for wasm files.
* The static host sends the Cross-Origin-Embedder-Policy and Cross-Origin-Opener-Policy headers.

Example: the user ticks off a routine step on the phone. A `useLiveQuery` query on the routine screen updates the checklist without a manual refresh.

## Alternatives Considered

Starting on Drizzle 0.45, rejected:

* Version 1.0 changes the migration folder format and relational queries. Starting on 0.45 would mean a migration soon after.

Prisma, not chosen:

* Prisma 7 uses a client without Rust. It needs a driver adapter for every database.
* Prisma's React Native and Expo support is a preview feature in Early Access.
* The npm `latest` tag of `prisma` is the release candidate 8.0.0-rc.36.

Kysely, not chosen:

* Kysely is a typed SQL query builder, not an ORM.
* Kysely's list of official dialects has no expo-sqlite dialect.
* Kysely's migration CLI is not part of the core.

## Rationale

* Drizzle documents an expo-sqlite driver with bundled migrations and live queries.
* Starting on 1.0 avoids an early migration of the migration folder and the relational queries.

## Consequences

* Drizzle 1.0 is a release candidate now.
* Drizzle's expo-sqlite page does not mention the web.
* The web build of SQLite has no full-text search.

## Assumptions

* Starting on the Drizzle 1.0 release candidate costs less than a migration from Drizzle 0.45 to 1.0 later. Not found in official sources as of 2026-11-28.

## Open Questions

1. Do you want to consider Prisma in place of Drizzle? Example: the server uses Prisma with PostgreSQL, and the phones use Prisma's React Native preview.
2. Do you want to consider Kysely in place of Drizzle? Example: the server uses Kysely's PostgreSQL dialect, and the phones use a community expo-sqlite dialect.

## Sources

### [Upgrade to Prisma ORM 7](https://www.prisma.io/docs/guides/upgrade-prisma-orm/v7)

Retrieved 2026-11-28.

- Fact: Prisma 7 uses a client without Rust.
  > "Upgrade to the new `prisma-client` provider which uses the new Rust-free client."
- Fact: Prisma 7 needs a driver adapter for every database.
  > "The way to create a new Prisma Client has changed to require a driver adapter for all databases."

### [prisma/react-native-prisma on GitHub](https://github.com/prisma/react-native-prisma)

Retrieved 2026-11-28.

- Fact: Prisma's React Native and Expo support is in Early Access.
  > "Early Access: Prisma ORM for React Native and Expo"
- Fact: Prisma's React Native support is a preview feature.
  > "React Native support is currently a preview feature"

### [Introduction | Kysely](https://kysely.dev/docs/intro)

Retrieved 2026-11-28.

- Fact: Kysely is a typed SQL query builder.
  > "a type-safe and autocompletion-friendly TypeScript SQL query builder"

### [Migrations | Kysely](https://kysely.dev/docs/migrations)

Retrieved 2026-11-28.

- Fact: Kysely's migration CLI is not part of the core.
  > "It is not part of the core, and your mileage may vary."

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
