# DR-0039: Keep PostgreSQL on the Server

**Status:** Accepted  
**Date:** 2026-11-28

## Context

The server keeps history older than 90 days, and devices keep data in SQLite, as recorded in DR-0016. The server receives every outbox entry and resolves conflicts per field, as recorded in DR-0017.

PostgreSQL facts:

* Each major version is supported for 5 years after its first release.
* Version 19 is current and is supported until November 19, 2031.
* Version 18 is supported until November 14, 2030.
* Version 19 was released on November 19, 2026. The official Docker image has a `19` tag.
* Of the managed hosts checked, only Neon lists version 19. AWS RDS and Azure offer it as a preview. Google Cloud SQL, Crunchy Bridge, and DigitalOcean list versions up to 18.
* A `pg_dump` backup loads into newer PostgreSQL versions.

Example: the phone and the laptop both edit the Workout block's end time while offline. The server stores both outbox entries in PostgreSQL and resolves the conflict per field.

## Decision

* The server keeps PostgreSQL.
* The server uses PostgreSQL 18, in local hosting, self-hosting, and the hosted version.
* The move to version 19 waits until the hosts offer it. The move uses `pg_dump` and `pg_restore`.

## Alternatives Considered

SQLite on the server, rejected:

* SQLite does not fit more than one server instance. The shared store of @fastify/rate-limit plans for more than one instance.

## Rationale

* Each PostgreSQL major version gets 5 years of support.
* PostgreSQL serves more than one server instance.

## Consequences

* PostgreSQL is a separate service to run.
* No official source states that SQLite on the server does not fit more than one instance. This point is own reasoning.
* Drizzle and Better Auth state no PostgreSQL version limits.
* SQLite on the server would have used the same SQL dialect as the devices. The server and the devices use different SQL dialects.
## Sources

### [PostgreSQL Versioning Policy](https://www.postgresql.org/support/versioning/)

Retrieved 2026-11-28.

- Fact: Version 19 was released on November 19, 2026.
  > "| 19 | 19.0 | Yes | November 19, 2026 | November 19, 2031 |"

### [Docker Hub postgres tag 19](https://hub.docker.com/v2/repositories/library/postgres/tags/19)

Retrieved 2026-11-28.

- Fact: The official Docker image has a `19` tag.
  > "\"name\":\"19\""

### [pg_dump (PostgreSQL documentation)](https://www.postgresql.org/docs/current/app-pgdump.html)

Retrieved 2026-11-28.

- Fact: A `pg_dump` backup loads into newer PostgreSQL versions.
  > "the output of pg_dump can be expected to load into PostgreSQL server versions newer than pg_dump's version."

### [Neon Postgres compatibility](https://neon.com/docs/reference/compatibility)

Retrieved 2026-11-28.

- Fact: Neon lists version 19.
  > "Neon supports Postgres 14, 15, 16, 17, 18, and 19."

### [Amazon RDS for PostgreSQL versions](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html)

Retrieved 2026-11-28.

- Fact: AWS RDS offers version 19 as a preview.
  > "PostgreSQL version 19.0 in the Amazon RDS Preview environment"

### [Azure Database for PostgreSQL supported versions](https://learn.microsoft.com/en-us/azure/postgresql/flexible-server/concepts-supported-versions)

Retrieved 2026-11-28.

- Fact: Azure offers version 19 as a preview.
  > "PostgreSQL 19 is available in preview in Azure Database for PostgreSQL."

### [Cloud SQL database versions](https://docs.cloud.google.com/sql/docs/db-versions)

Retrieved 2026-11-28.

- Fact: Google Cloud SQL lists versions up to 18.
  > "PostgreSQL 18 (default) | 18.6 | September 25, 2025"

### [Crunchy Bridge plans and pricing](https://docs.crunchybridge.com/concepts/plans-pricing)

Retrieved 2026-11-28.

- Fact: Crunchy Bridge lists versions up to 18.
  > "PG15-18 only"

### [DigitalOcean database version support policy](https://docs.digitalocean.com/products/databases/version-support-policy/)

Retrieved 2026-11-28.

- Fact: DigitalOcean lists versions up to 18. This fact comes from the page's version table. No exact sentence was captured.

### [Versioning Policy | PostgreSQL](https://www.postgresql.org/support/versioning/)

Retrieved 2026-11-27.

- Fact: Each major version is supported for 5 years.
  > "The PostgreSQL Global Development Group supports a major version for 5 years after its initial release."
- Fact: Version 19.0 is current, and version 18 is supported until November 14, 2030.
  > "19 | 19.0 | November 19, 2031" and "18 | 18.7 | November 14, 2030"
