# DR-0024: Docker Compose for the Self-Hosted Server

**Status:** Draft  
**Date:** 2026-11-28

## Context

One person can host Run My Day themselves. The Fastify server and the PostgreSQL database must run somewhere for that person.

Facts from earlier decisions:

* The server is Fastify with oRPC and Drizzle, and stores data in PostgreSQL.
* The repository is public under the MIT license, as recorded in DR-0001. Anyone can run their own copy.
* Each development worktree runs its own Postgres container through Docker Compose, as recorded in DR-0011.
* Secrets come from Doppler through `doppler run`, checked by Varlock against `.env.schema`, as recorded in DR-0011.
* Push to open clients uses server-sent events (SSE), as recorded in DR-0018.
* The server needs a public HTTPS address for Google's push notifications, as recorded in DR-0021.
* The server sends web push at each block start when the web tab is closed, as recorded in DR-0019.
* The server must run all day.
* A paid hosted multi-user version follows later.

Example: a Mac mini at home runs the server. The server sends the phone's 7:00 "Morning" alert.

## Decision

* The documented default runs the self-hosted version with Docker Compose on an always-on home machine, such as a Mac mini, a NAS, or a Raspberry Pi.
* The same `compose.yaml` also runs on a rented server (a VPS).
* `compose.yaml` starts two containers: the server and the official `postgres` image.
* The database files live in a named volume.
* Postgres has a health check that runs `pg_isready`.
* Compose starts the server only after the Postgres health check passes.

Example: the self-hoster runs `docker compose up` on the Mac mini. Postgres reports ready, then the server starts and sends the 7:00 "Morning" alert.

## Alternatives Considered

Docker Compose on a VPS as the default, rejected:

* It has a monthly bill.
* The server is open to the internet, so the self-hoster must keep it patched.

Plain Node.js process with Postgres installed on the machine, rejected:

* Each self-hoster installs and upgrades Postgres by hand.
* It differs from the Docker Compose setup used in development, as recorded in DR-0011.

## Rationale

* The same Docker Compose setup already runs Postgres in development.
* The official image needs only one required variable, `POSTGRES_PASSWORD`.
* A home machine has no monthly server bill.
* One `compose.yaml` serves a home machine and a VPS.

## Consequences

* A home machine has no public address. A tunnel is needed for Google push.
* Alerts and calendar reads stop when the machine sleeps or loses power.
* Postgres 18 images store data in a versioned folder. The volume path must match the major version.
* Init scripts run only on an empty data folder.
* No official source compares home machines with VPS hosting for this case.
* No official source states that a home machine avoids a monthly bill, or that a VPS needs patching. These points are not verified.

## Open Questions

1. Is the reference setup a home machine or a VPS? Example: a Mac mini at home runs the server, and the phone's 7:00 "Morning" alert is sent by it.
2. Does the self-hoster use Doppler, or a plain `.env` file? Example: a stranger who clones the public repository has no access to the project's Doppler workspace.
3. Is the server published as a ready-made Docker image, for example on GitHub Container Registry, or built from source by the self-hoster?
4. Which Postgres major version is pinned? Example: `postgres:18` stores data under `/var/lib/postgresql/18/docker`, and moving to 19 needs an upgrade step.

## Sources

### [Control startup order | Docker Docs](https://docs.docker.com/compose/how-tos/startup-order/)

Retrieved 2026-11-27.

- Fact: Compose waits for a health check to pass before it starts a service that depends on it.
  > "Compose waits for healthchecks to pass on dependencies marked with `service_healthy`."
- Fact: The Docker docs show a Postgres health check with `pg_isready`.
  > "uses the `pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}` command to check if the PostgreSQL database is ready"

### [postgres - Official Image | Docker Hub](https://hub.docker.com/_/postgres)

Retrieved 2026-11-27.

- Fact: The image requires a password variable.
  > "This environment variable is required for you to use the PostgreSQL image."
- Fact: Postgres 18 images use a versioned data folder.
  > "For 18 it is `/var/lib/postgresql/18/docker`."
- Fact: Older images keep data at a fixed path.
  > "Mount the data volume at `/var/lib/postgresql/data` and not at `/var/lib/postgresql`."
- Fact: Init scripts run only on an empty data folder.
  > "scripts in `/docker-entrypoint-initdb.d` are only run if you start the container with a data directory that is empty."
