# DR-0039: Keep PostgreSQL on the Server

**Status:** Draft  
**Date:** 2026-11-28

## Context

The server keeps history older than 90 days, and devices keep data in SQLite, as recorded in DR-0016. The server receives every outbox entry and resolves conflicts per field, as recorded in DR-0017.

PostgreSQL facts:

* Each major version is supported for 5 years after its first release.
* Version 19 is current and is supported until November 19, 2031.
* Version 18 is supported until November 14, 2030.

Example: the phone and the laptop both edit the Workout block's end time while offline. The server stores both outbox entries in PostgreSQL and resolves the conflict per field.

## Decision

* The server keeps PostgreSQL.
* The server uses the newest major version that the chosen host offers, 18 or 19.

## Alternatives Considered

SQLite on the server, rejected:

* SQLite does not fit more than one server instance. The shared store of @fastify/rate-limit plans for more than one instance.

## Rationale

* Each PostgreSQL major version gets 5 years of support.
* PostgreSQL serves more than one server instance.

## Consequences

* PostgreSQL is a separate service to run.
* No official source states that SQLite on the server does not fit more than one instance. This point is own reasoning.
* No official source recommends the newest major version that the host offers. This choice is own reasoning.
* SQLite on the server would have used the same SQL dialect as the devices. The server and the devices use different SQL dialects.

## Open Questions

1. Which PostgreSQL major version and which host are used? The stack states neither. Example: a host that offers version 18 gets support until November 14, 2030. A host that offers version 19 gets support until November 19, 2031.

## Sources

### [Versioning Policy | PostgreSQL](https://www.postgresql.org/support/versioning/)

Retrieved 2026-11-27.

- Fact: Each major version is supported for 5 years.
  > "The PostgreSQL Global Development Group supports a major version for 5 years after its initial release."
- Fact: Version 19.0 is current, and version 18 is supported until November 14, 2030.
  > "19 | 19.0 | November 19, 2031" and "18 | 18.7 | November 14, 2030"
