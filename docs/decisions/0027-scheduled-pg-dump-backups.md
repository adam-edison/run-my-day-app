# DR-0027: Scheduled pg_dump Backups

**Status:** Accepted  
**Date:** 2026-11-28

## Context

The self-hosted Postgres database must be backed up, and a restore must be tested.

Postgres on the server holds all data, including history older than 90 days. Devices do not keep that history, as recorded in DR-0016.

Postgres runs in Docker Compose in development, as recorded in DR-0011.

The PostgreSQL docs state that databases should be backed up regularly. The docs describe three approaches: SQL dump, file-level backup, and continuous archiving.

Example: on a self-hosted Mac mini, a dump runs at 17:00. The disk fails at 17:40. The edits from those 40 minutes that no device still holds are lost.

## Decision

* A scheduled job runs `pg_dump -Fc` every hour in local hosting and self-hosting.
* A scheduled job runs `pg_dump -Fc` every day in the hosted version.
* Local hosting and self-hosting keep the last 24 hourly, 7 daily, and 4 weekly dumps. Older dumps are deleted.
* In local hosting and self-hosting, each dump is written to a folder set by an environment variable.
* The user chooses the folder, such as a second disk or a folder that a sync app copies to cloud storage.
* In the hosted version, each dump is kept in the hosting provider's storage, off the server machine.
* Restores use `pg_restore`.
* After each dump, the job checks that the file exists, is not empty, and that `pg_restore --list` can read its table of contents.
* The server reports an error when a dump check fails, or when no new dump exists for 2 hours in local hosting and self-hosting.
* The restore steps, including creating the database roles first, are documented.

Example: at 14:00 the job on a Mac mini writes a custom-format dump to a Dropbox folder. `pg_restore --list` reads it, and Dropbox copies it to the cloud. At 16:00 the disk is full and no dump has been written since 14:00. The server reports an error.

## Alternatives Considered

Continuous archiving (WAL archiving) with point-in-time recovery, rejected:

* It is more complex to run than a dump.
* It restores only the whole database cluster.

Built-in upload to S3-compatible storage in local hosting and self-hosting, rejected:

* Each user would need a storage account and keys.

Copying the Docker volume's files, rejected:

* A file-level backup restores only the whole cluster.
* The PostgreSQL docs list it as a separate approach with its own conditions.

## Rationale

* A dump is a consistent snapshot and does not block normal use.
* Custom format is compressed and allows restoring single tables.
* A dump loads into newer Postgres versions. The same dump also serves major upgrades.

## Consequences

* Changes since the last dump are lost. Devices keep 90 days of history and the outbox. Some recent changes could sync again from the devices.
* `pg_dump` does not include roles. `pg_dumpall` covers roles.
* Roles must exist before a restore.
* No official source states the schedule, the retention, or the restore test for this case.
* The conditions of a file-level backup are not checked in detail.
* A dump folder on the same disk as the database is lost with that disk. The user must choose a folder elsewhere.
* The dump check does not restore the data. A full restore is not tested automatically.

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
