# DR-0029: User ID and Row-Level Security from the First Migration

**Status:** Accepted  
**Date:** 2026-11-28

## Context

Run My Day is offered as local hosting, self-hosting, and a paid hosted version, as recorded in DR-0049. The hosted version serves many paying users on one server. Some choices must be made now so that the hosted version is not blocked.

Facts from earlier decisions:

* Each user connects Google Calendar once, and the server stores that user's refresh token, as recorded in DR-0021.
* The hosted version must serve thousands of users, as recorded in DR-0021.
* Each self-hoster creates their own Google Cloud project, as recorded in DR-0021.
* Every secret is an environment variable from Doppler or a local file, checked by Varlock against `.env.schema`, as recorded in DR-0011 and DR-0024.
* Better Auth handles sign-in and sessions, with Drizzle integration.
* The architecture principles require the database to enforce data integrity.
* The repository is public under MIT, as recorded in DR-0001.

Row-level security (RLS) is a PostgreSQL feature. A policy on a table limits which rows a query can see or change.

Example: the `task` table holds "Stretch hips" for one user. A query for another user's tasks must never return that row.

## Decision

Data separation:

* Every user-owned table gets a `user_id` column from the first migration.
* Push channels, outbox entries, and history rows also carry `user_id`.
* RLS is turned on from the first migration. RLS policies filter each table by the request's user ID.
* The server connects as a role that does not own the tables, or the tables use `FORCE ROW LEVEL SECURITY`.

Secrets and configuration:

* Google refresh tokens are encrypted at rest.
* The encryption key comes from the `TOKEN_ENCRYPTION_KEY` environment variable, from Doppler or a local `.env` file, as recorded in DR-0024.
* The key is stored apart from the database and is never in a database dump.
* The install step looks for `TOKEN_ENCRYPTION_KEY`. When it does not exist, the install step generates a random key and writes it to the local `.env` file.
* All configuration comes from environment variables, including the Google client ID, the Sentry DSN, and the backup check interval.
* The public code holds no credentials.

One code path:

* The self-hosted version runs the same multi-user code with one account.
* No code path checks for a "single-user mode".
* Local hosting and self-hosting require sign-in with Better Auth, as the hosted version does. Tailscale limits which devices reach the server, and sign-in limits who uses them.
* Rate limits are per user, not per server.

Google Cloud project:

* The hosted version uses one Google Cloud project for all its users.
* With push notifications and a 5-minute check, 1,000 users with 2 calendars each make about 400 requests per minute, against a limit of 10,000 per project.

Example: a request for user A sets A's user ID in its transaction. A query on `task` returns only A's "Stretch hips", even without a `WHERE user_id = ...` clause.

## Alternatives Considered

A user ID column on every table, filtered in application code only, rejected:

* One missing `WHERE user_id = ...` in one query exposes another user's rows.

Single-user shortcuts now, migration later, rejected:

* Every table, query, and test would change later.

## Rationale

* Putting the tenant ID in every table is the advised design for multi-tenancy.
* Policies restrict rows per user for normal queries and for changes.
* With the current user ID set for the request, every query in the request is filtered to that user.
* RLS matches the principle that the database enforces data integrity.
* OWASP advises storing encryption keys apart from the encrypted data.
* The Twelve-Factor App advises config in environment variables. Its test is whether the code could be open-sourced without leaking credentials.

## Consequences

* Table owners bypass RLS unless `FORCE ROW LEVEL SECURITY` is set.
* When the encryption key is lost, the stored Google tokens cannot be read. Each user must connect Google Calendar again.
* The user must back up the `.env` file apart from the database dumps.
* Each request must set the user ID in its transaction. This adds work to every Drizzle query.
* No official source states that one multi-user code path should serve the self-hosted version.
* No official source states that push channels, outbox entries, and history rows need `user_id` for this case.
* No official source states that rate limits should be per user for this case.
## Sources

### [PostgreSQL: Row Security Policies](https://www.postgresql.org/docs/current/ddl-rowsecurity.html)

Retrieved 2026-11-27.

- Fact: Row security policies restrict rows per user for queries and changes.
  > "tables can have _row security policies_ that restrict, on a per-user basis, which rows can be returned by normal queries or inserted, updated, or deleted by data modification commands."
- Fact: Table owners bypass RLS unless forced.
  > "Table owners normally bypass row security as well, though a table owner can choose to be subject to row security with ALTER TABLE ... FORCE ROW LEVEL SECURITY."

### [Row Level Security for Tenants in Postgres - Crunchy Data](https://www.crunchydata.com/blog/row-level-security-for-tenants-in-postgres)

Retrieved 2026-11-27.

- Fact: The tenant ID should be in every table.
  > "Keep in mind that when designing your app for multi-tenancy, ideally you have that ord_id in every table."
- Fact: Queries in a request are filtered by the current tenant ID.
  > "any queries executed within the request will automatically be filtered based on the current tenant ID"

### [OWASP Cryptographic Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)

Retrieved 2026-11-27.

- Fact: Keys should be stored apart from the encrypted data.
  > "Where possible, encryption keys should be stored in a separate location from encrypted data."
- Fact: Not storing sensitive data is the best protection.
  > "The best way to protect sensitive information is to not store it in the first place."

### [The Twelve-Factor App: Config](https://12factor.net/config)

Retrieved 2026-11-27.

- Fact: Config belongs in environment variables.
  > "The twelve-factor app stores config in environment variables (often shortened to env vars or env)."
- Fact: The test is whether the code could be open-sourced without leaking credentials.
  > "is whether the codebase could be made open source at any moment, without compromising any credentials."
