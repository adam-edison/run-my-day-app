# DR-0027: Nightly pg_dump Backups

**Status:** Draft  
**Date:** 2026-11-28

## Context

The self-hosted Postgres database must be backed up, and a restore must be tested.

Postgres on the server holds all data, including history older than 90 days. Devices do not keep that history, as recorded in DR-0016.

Postgres runs in Docker Compose in development, as recorded in DR-0011.

The PostgreSQL docs state that databases should be backed up regularly. The docs describe three approaches: SQL dump, file-level backup, and continuous archiving.

Example: a nightly dump runs at 3:00. A disk fails at 18:00. The day's edits that no device still holds are lost.

## Decision

* A scheduled job runs `pg_dump -Fc` each night.
* Each dump is kept for a set number of days.
* A copy of each dump is kept off the machine, such as in cloud storage.
* Restores use `pg_restore`.
* A scheduled test restores the latest dump into an empty database and checks it.

Example: at 3:00 the job writes a custom-format dump and copies it to cloud storage. A later test restores it into an empty database and finds the "Morning" routine.

## Alternatives Considered

Continuous archiving (WAL archiving) with point-in-time recovery, rejected:

* It is more complex to run than a dump.
* It restores only the whole database cluster.

Copying the Docker volume's files, rejected:

* A file-level backup restores only the whole cluster.
* The PostgreSQL docs list it as a separate approach with its own conditions.

## Rationale

* A dump is a consistent snapshot and does not block normal use.
* Custom format is compressed and allows restoring single tables.
* A dump loads into newer Postgres versions, so it also serves major upgrades.

## Consequences

* Changes since the last dump are lost. Devices keep 90 days of history and the outbox, so some recent changes could sync again.
* `pg_dump` does not include roles. `pg_dumpall` covers roles.
* Roles must exist before a restore.
* No official source states the schedule, the retention, or the restore test for this case.
* The conditions of a file-level backup are not checked in detail.

## Open Questions

1. How much loss is acceptable? Example: with a nightly dump at 3:00, a disk failure at 18:00 loses the day's edits that no device still holds.
2. Where are copies kept? Example: a second disk at home, or cloud storage such as Backblaze B2 or S3.
3. How long are copies kept? Example: 7 nightly and 4 weekly copies.
4. Is the restore test automated, for example in a scheduled job, or done by hand each month?

## Sources

### [Chapter 25. Backup and Restore | PostgreSQL](https://www.postgresql.org/docs/current/backup.html)

Retrieved 2026-11-27.

- Fact: Databases should be backed up regularly.
  > "As with everything that contains valuable data, PostgreSQL databases should be backed up regularly."
- Fact: There are three approaches.
  > "There are three fundamentally different approaches to backing up PostgreSQL data:"

### [SQL Dump | PostgreSQL](https://www.postgresql.org/docs/current/backup-dump.html)

Retrieved 2026-11-27.

- Fact: A dump is a consistent snapshot.
  > "Dumps created by pg_dump are internally consistent, meaning, the dump represents a snapshot of the database at the time pg_dump began running."
- Fact: A dump does not block other work.
  > "pg_dump does not block other operations on the database while it is working."
- Fact: Custom format allows restoring selected tables.
  > "it has the added advantage that tables can be restored selectively."
- Fact: Custom format is restored with `pg_restore`.
  > "A custom-format dump is not a script for psql, but instead must be restored with pg_restore"
- Fact: Roles must exist before a restore.
  > "Before restoring an SQL dump, all the users who own objects or were granted permissions on objects in the dumped database must already exist."

### [pg_dump | PostgreSQL](https://www.postgresql.org/docs/current/app-pgdump.html)

Retrieved 2026-11-27.

- Fact: `pg_dump` dumps one database.
  > "pg_dump only dumps a single database."
- Fact: Roles need `pg_dumpall`.
  > "To export an entire cluster, or to export global objects that are common to all databases in a cluster (such as roles and tablespaces), use"
- Fact: A dump loads into newer versions.
  > "the output of pg_dump can be expected to load into PostgreSQL server versions newer than pg_dump's version."

### [Continuous Archiving and Point-in-Time Recovery | PostgreSQL](https://www.postgresql.org/docs/current/continuous-archiving.html)

Retrieved 2026-11-27.

- Fact: Point-in-time recovery is supported.
  > "it is possible to restore the database to its state at any time since your base backup was taken."
- Fact: It is more complex to run.
  > "This approach is more complex to administer than either of the previous approaches"
- Fact: It restores only a whole cluster.
  > "this method can only support restoration of an entire database cluster, not a subset."
