# DR-0020: No TanStack Query

**Status:** Accepted  
**Date:** 2026-11-27

## Context

TanStack Query fetches server data for screens and keeps a cached copy of it.

Each device keeps its own copy of the data in expo-sqlite, as recorded in DR-0016. The Sync actor sends changes to the server through the outbox and fetches changes from the server, as recorded in DR-0017 and DR-0018. The server stays the source of truth.

Example: the task "Stretch" is created on the phone. The phone saves "Stretch" and an outbox entry, and the task list shows "Stretch" at once. The Sync actor sends the outbox entry to the server later.

## Decision

* TanStack Query is not used.
* Screens read data only from the device's own database.
* Only the Sync actor calls the server.
* History older than 90 days is fetched from the server by the Sync actor.

## Alternatives Considered

TanStack Query for every screen, rejected:

* TanStack Query would keep a second copy on top of the device's database, and the two copies could disagree.
* Reads from the device's database are local. Loading states, refetches, and retries for reads are not needed.

TanStack Query for history older than 90 days only, rejected:

* A second way to call the server would be added beside the Sync actor.

## Rationale

* Each device has one copy of the data.
* Every call to the server goes through the Sync actor.
* One library fewer is maintained.

## Consequences

* The Sync actor retries failed calls, through the outbox.
* History older than 90 days can be viewed only online.

## Sources

### [Overview | TanStack Query React Docs](https://tanstack.com/query/latest/docs/framework/react/overview)

Retrieved 2026-11-27.

- Fact: TanStack Query fetches server data for screens and keeps a cached copy of it.
  > "makes fetching, caching, synchronizing and updating server state in your web applications a breeze"
- Fact: TanStack Query is built for data held on a server.
  > "Is persisted remotely in a location you may not control or own"
