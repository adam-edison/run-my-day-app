# DR-0024: Docker Compose for the Self-Hosted Server

**Status:** Accepted  
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
* Run My Day is offered as local hosting, self-hosting, and a paid hosted version, as recorded in DR-0049.

Example: a Mac mini at home runs the server. The server sends the phone's 7:00 "Morning" alert.

## Decision

* Local hosting and self-hosting both run with Docker Compose.
* One `compose.yaml` runs on the user's own computer, an always-on home machine, or a rented server (a VPS).
* `compose.yaml` starts two containers: the server and the official `postgres:18` image.
* The database files live in a named volume.
* Postgres has a health check that runs `pg_isready`.
* Compose starts the server only after the Postgres health check passes.
* Secrets come from a plain `.env` file by default. The user copies `.env.example` to `.env` and fills in the values.
* A user with their own Doppler project can start the server through `doppler run` in place of the `.env` file.
* Varlock checks the variables against `.env.schema` in both cases.
* Each release publishes a server image to GitHub Container Registry.
* `compose.yaml` uses the published image by default. A user can build the image from source in its place.

Example: the self-hoster runs `docker compose up` on the Mac mini. Postgres reports ready, then the server starts and sends the 7:00 "Morning" alert.

## Alternatives Considered

Doppler only, rejected:

* A user who clones the public repository has no access to the project's Doppler workspace. Each user would need a Doppler account.

Plain Node.js process with Postgres installed on the machine, rejected:

* Each self-hoster installs and upgrades Postgres by hand.
* It differs from the Docker Compose setup used in development, as recorded in DR-0011.

## Rationale

* The same Docker Compose setup already runs Postgres in development.
* The official image needs only one required variable, `POSTGRES_PASSWORD`.
* One `compose.yaml` serves a laptop, a home machine, and a VPS.
* A `.env` file needs no account.

## Consequences

* A home machine has no public address. A tunnel is needed for Google push.
* Alerts and calendar reads stop when the machine sleeps or loses power.
* Postgres 18 images store data in a versioned folder. The volume path must match the major version.
* Init scripts run only on an empty data folder.
* A `.env` file stores secret values on disk on the user's machine.
* GitHub Container Registry storage and bandwidth are currently free. GitHub promises at least one month's notice before any change.
* GitHub's Container registry page states that public images can be accessed anonymously. The same page also states that an access token is needed to install public packages.
## Sources

### [GitHub Packages billing](https://docs.github.com/en/billing/concepts/product-billing/github-packages)

Retrieved 2026-11-28.

- Fact: GitHub Container Registry storage and bandwidth are currently free.
  > "GitHub Packages usage is **free** for **public packages**."
  > "Container image storage and bandwidth for the Container registry is currently free."
- Fact: GitHub promises at least one month's notice before any change.
  > "If you use Container registry, you'll be informed at least one month in advance of any change to this policy."

### [Working with the Container registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)

Retrieved 2026-11-28.

- Fact: Public images can be accessed anonymously.
  > "You can also access public container images anonymously."
- Fact: The same page states that an access token is needed to install public packages.
  > "You need an access token to publish, install, and delete private, internal, and public packages."

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
