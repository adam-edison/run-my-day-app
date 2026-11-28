# Kivaro Server Architecture

This document summarizes the server architecture of kivaro, the earlier project of the same author.
It serves as input for the architecture decisions of Run My Day.
All paths are relative to the kivaro repository root.

## Overview

The server is an Express 5 app in TypeScript that runs on Node 24 through `tsx`.
The repository is an npm workspaces monorepo with `apps/server`, `apps/web`, `apps/mobile`, and shared packages in `packages/`.

| Concern | Library and version (from `package.json`) |
| --- | --- |
| HTTP framework | `express` ^5.3.0 |
| Dependency injection | `awilix` ^13.0.5 |
| Validation and API contract | `zod` ^4.6.7, in `packages/api-contract` (version 2.2.0) |
| Database | PostgreSQL 18 (`postgres:18-alpine`), `pg` 8.23.1, `drizzle-orm` 1.0.0-rc.5, `drizzle-kit` 1.0.0-rc.5 |
| Authentication | `better-auth` ^1.7.21, `@better-auth/drizzle-adapter`, `@better-auth/expo` |
| Logging | `pino` ^10.4.0, `pino-http` ^11.0.0, `pino-pretty` ^13.2.0 |
| Monitoring | `@sentry/node` ^11.14.0 |
| Real-time push | `ws` ^8.23.0 |
| Settings | `varlock` ^1.25.0 with Doppler CLI |
| Music metadata | `music-metadata` ^12.3.5 |
| Tests | `vitest` ^5.0.5, `supertest` ^7.3.1, `@testcontainers/postgresql` 12.2.1, `@playwright/test` ^1.64.1 |
| Language and runner | `typescript` ~6.0.3, `tsx` ^4.23.15 |

```
HTTP request                         WebSocket upgrade
     |                                      |
     v                                      v
 Express app (src/app.ts)            server.on('upgrade')
  requestContext -> requestId ->     ChangeEventSocketController
  contractVersion -> pino-http              |
     |                                      v
     +--> /api/auth/*  -> BetterAuthClient  ChangeEventService (in-memory connections)
     +--> /api/*       -> ApiRouter
     |                     |
     |                Controller  (*.controller.ts, *.request.ts: zod parse)
     |                     |
     |                Service     (*.service.ts: rules, reads signed-in user)
     |                     |
     |                Domain      (*.domain.ts: types, repository interfaces, errors)
     |                     ^ implements
     |                Repository  (*.repository.ts: Drizzle queries)
     |                     |
     |                DatabaseClient -> PostgreSQL
     +--> /app/*       -> WebHostingMiddleware (static web build)

 Error middlewares in ApiRouter map domain errors to 400 / 401 / 404.
 All objects are singletons wired in the awilix container (src/container.ts).
```

## Structure

```
apps/server/
  bin/                  CLI scripts: migrate, delete test users, track-count benchmark, Postgres image update
  drizzle/              Generated SQL migrations, one timestamped folder each with migration.sql and snapshot.json
  src/
    main.ts             Entry point: parses config, builds container, runs migrations, starts server
    instrument.ts       Sentry init, loaded with tsx --import before main.ts
    app.ts              Registers middlewares and routers on Express in order
    server.ts           Starts listening and routes WebSocket upgrades
    container.ts        Awilix container with every service
    controller-list.ts  Chooses controllers per deployment
    config.ts           Zod parsing of environment variables into ServerConfig
    api/                ApiRouter, Controller interface, error middlewares
    authentication/     Better Auth client, signed-in-user middleware, auth routes
    database/           Drizzle schema files, standard columns, DatabaseClient
    request-context/    AsyncLocalStorage request context and request id
    request-logging/    pino-http middleware
    logging/            Pino logger factory and LoggerClient
    monitoring/         Sentry client and options
    change-event/       WebSocket push of change events
    sync/               Daily cleanup of soft-deleted rows
    validation/         ValidationError base class
    not-found/          NotFoundError base class
    contract-version/   Header with the lowest supported contract version
    web-hosting/        Static hosting of the web app build under /app
    <feature>/          One folder per feature: task, tag, metric, setting, user, health, greeting,
                        bug-report, client-log, music-location, library-scan, music-track,
                        playlist, play-queue, unreadable-file
  tests/
    _fakes/<feature>/   In-memory fakes of repository interfaces
    _fixtures/          Test container, test database, API helpers, factories
    <feature>/          Tests per feature, by test type suffix
packages/
  api-contract/         Zod schemas and types shared by server, web, and mobile
  client/               Shared client code (API client, queries, outbox)
```

## Feature modules and layers

Each feature folder holds the same file kinds, separated by file suffix.
For example, `apps/server/src/task/` holds:

- `task-services.ts` registers the feature's classes in the container.
- `task.controller.ts` registers routes and turns requests into service calls.
- `task.request.ts` parses the body, query, and params with schemas from `@kivaro/api-contract`.
- `task.service.ts` holds the use cases.
- `task.domain.ts` holds types, the repository interface, domain errors, and pure rule functions.
- `task.repository.ts` implements the repository interface with Drizzle.

The dependency direction is controller to service to domain interface.
The repository imports the domain module and implements its interface.
Services depend on `TaskRepositoryInterface`, not on the Drizzle class.
External systems use a `*.client.ts` file, such as `better-auth.client.ts`, `sentry.client.ts`, `logger.client.ts`, and `database.client.ts`.

```ts
// apps/server/src/task/task-services.ts
export function addTaskServices(container: AwilixContainer<TaskServices>): void {
  container.register({
    taskRepository: asClass(TaskRepository).singleton(),
    taskService: asClass(TaskService).singleton(),
    taskController: asClass(TaskController).singleton(),
  });
}
```

## Dependency injection

`apps/server/src/container.ts` builds an awilix container with `InjectionMode.PROXY` and `strict: true`.
Each class receives one object of its dependencies in the constructor.
`AppServices` extends one `*Services` interface per feature, so the container is fully typed.
`DatabaseClient` has a disposer that closes the pool.
Tests replace repositories with fakes by registering new values on the same container.

## Request flow

`apps/server/src/app.ts` registers these steps in order:

1. `RequestContextMiddleware` runs each request inside an `AsyncLocalStorage` store with a new UUID.
2. `RequestIdMiddleware` sets the `X-Request-Id` response header.
3. `ContractVersionMiddleware` sets the `X-Lowest-Supported-Contract-Version` header.
4. `RequestLoggingMiddleware` logs each request with pino-http.
5. `AuthenticationController` forwards `/api/auth/*` to Better Auth.
6. `ApiRouter` mounts each controller under `/api`, then the error middlewares.
7. `WebHostingMiddleware` serves the web build under `/app` when `WEB_DIRECTORY` is set.

Services read the signed-in user from `RequestContextService`, not from the request object.

```ts
// apps/server/src/task/task.service.ts
async create(newTask: NewTask): Promise<Task> {
  const { id: userId } = this.requestContextService.signedInUser;
  const newBacklogTask = toNewBacklogTask(newTask);
  return this.taskRepository.create(userId, newBacklogTask);
}
```

## API style

The API is REST over JSON with Express routes, such as `GET /api/tasks`, `POST /api/tasks`, and `PATCH /api/tasks/:taskId`.
The server does not use oRPC, tRPC, or OpenAPI generation.
Type safety across server and clients comes from the shared package `packages/api-contract`.
That package exports Zod schemas and inferred types, such as `taskSchema`, `newTaskSchema`, and `Task`.
Each response uses a typed Express `Response<Task>`.
Errors use the shared `ApiError` shape `{ error: string }`.
The contract package version drives client compatibility.
The server sends the lowest supported major version in a header, and clients compare it with their build's contract version (`packages/client/src/contract-version/`).
`npm run contract:snapshot` writes a schema snapshot of the contract.

## Validation

Each `*.request.ts` file calls `safeParse` on a contract schema and throws a feature `ValidationError` on failure.
Business rules live in `*.domain.ts`, such as `checkTaskEdit`, which rejects a Ready task without an estimate.

```ts
// apps/server/src/task/task.request.ts
export function toNewTask(body: unknown): NewTask {
  const result = newTaskSchema.safeParse(body);
  if (!result.success) {
    throw new TaskValidationError('Invalid request body');
  }
  return result.data;
}
```

## Error handling

Domain errors extend three base classes: `ValidationError` (`src/validation/validation.domain.ts`), `NotFoundError` (`src/not-found/not-found.domain.ts`), and `UnauthorizedError` (`src/authentication/authentication.domain.ts`).
`ApiRouter` registers one error middleware per base class in `src/api/`.
Each middleware checks `instanceof`, responds with 400, 404, or 401, or calls `next(error)`.
An unknown `/api` path returns 404 with `{ error: 'Not found' }`.
Controllers are async and throw without try/catch, and Express 5 forwards rejected promises to the error middlewares.
The `src/api/` folder has no custom handler for other errors, so those fall through to the Express default handler.

## Database access, schema, and migrations

- The database is PostgreSQL 18, started by `compose.yaml` in Docker for dev and local production.
- `src/database/database.client.ts` creates the Drizzle client from `config.database.url`, logs idle pool errors, and runs migrations.
- Schema files live in `src/database/`: `schema.ts`, `tag-schema.ts`, `metric-schema.ts`, `authentication-schema.ts`, `app-schema.ts`, `enum-schema.ts`, and `change-number-schema.ts`.
- `src/database/standard-columns.ts` defines shared `id` (UUID), `created_at`, and `updated_at` columns and a `user_id` foreign key with cascade delete.
- `src/database/sync-columns.ts` defines `change_number` from the Postgres sequence `change_numbers`, `deleted`, and `deleted_at`, with a check constraint that keeps the two delete columns consistent.
- `drizzle-kit generate` (`npm run db:generate`) writes SQL migrations to `apps/server/drizzle/`.
- `main.ts` runs pending migrations on each server start.
- `bin/migrate.ts` runs migrations by hand.
- Repositories build queries with Drizzle's query builder and use `database.transaction` for multi-table writes.
- Every repository method takes a `userId` and filters by it.
- `compose.backup.yaml` and `docker/database-backup/` run hourly backups and a restore script for local production.

```ts
// apps/server/src/database/sync-columns.ts
export const syncColumns = {
  changeNumber: bigint('change_number', { mode: 'number' })
    .notNull()
    .default(sql`nextval('change_numbers')`),
  deleted: boolean('deleted').notNull().default(false),
  deletedAt: timestamp('deleted_at', { withTimezone: true }),
};
```

## Authentication

- Better Auth handles email-and-password sign-in with cookie sessions stored in Postgres through the Drizzle adapter (`src/authentication/better-auth.client.ts`).
- The `expo()` plugin and the trusted origin `kivaro://` support the Android app.
- Sign-ups have a rate limit per hour from `AUTHENTICATION_SIGN_UPS_PER_HOUR`, stored in memory.
- `AUTHENTICATION_ALLOWED_HOSTS` sets the allowed base URL hosts.
- The server sets an `x-kivaro-client-ip` header from `src/authentication/client-ip.ts` for Better Auth's IP checks.
- `SignedInUserMiddleware` reads the session, throws `UnauthorizedError` without one, and stores the user in the request context.
- A Better Auth database hook on session delete notifies listeners, and the change-event service then closes that session's WebSockets.
- Controllers add the middleware per route: `router.get('/tasks', requireSignedInUser, getTasks)`.

## Logging and monitoring

- `src/logging/logger.ts` creates a pino logger with JSON or pretty output, and an optional log file through `pino.multistream`.
- `LoggerClient` wraps the logger for services.
- `src/logging/error-summary.ts` serializes errors.
- pino-http uses the request id from the request context and strips query strings from logged URLs.
- `src/instrument.ts` starts Sentry before the app when `SENTRY_DSN` is set.
- The Sentry environment comes from the Doppler config: `dev`, `loc`, `stg`, or `prd`.
- The `client-log` and `bug-report` features accept logs and bug reports from the web and mobile clients.

## Configuration and environment variables

- `src/config.ts` parses `process.env` with a Zod schema and throws one error that lists every invalid setting.
- The result is a nested `ServerConfig` object, registered in the container as `config`.
- Keys: `PORT`, `WEB_DIRECTORY`, `LOG_FILE`, `LOG_LEVEL`, `LOG_FORMAT`, `DATABASE_HOST`, `DATABASE_PORT`, `DATABASE_USER`, `DATABASE_PASSWORD`, `DATABASE_NAME`, `BETTER_AUTH_SECRET`, `AUTHENTICATION_ALLOWED_HOSTS`, `AUTHENTICATION_SIGN_UPS_PER_HOUR`, `DEPLOYMENT`, `DOPPLER_ENVIRONMENT`, `SENTRY_DSN`, `CLIENT_LOG_LEVEL`.
- The root `.env.schema` declares each key with a type and an app tag, such as `@tag(server)`.
- `apps/server/.env.schema` imports only the server keys and generates `apps/server/env.d.ts`.
- Each npm script runs `doppler run` to fetch values, then `varlock run` to check them and start the command.
- `.env.local`, `.env.dev.local`, and `.env.loc.local` override Doppler values per repo copy.
- `DEPLOYMENT=local-mac` enables the music controllers in `src/controller-list.ts`.

## Background jobs

The server has no job queue.
`SyncCleanupService.startDailyCleanup()` runs once at start and then every 24 hours with `setInterval`.
It deletes soft-deleted rows older than a cutoff in each sync table.
Library scans run in the server process, and `LibraryScanProgressService` tracks whether a scan runs for a user.

```ts
// apps/server/src/sync/sync-cleanup.service.ts
startDailyCleanup(): () => void {
  const dayMilliseconds = 86_400_000;
  const runCleanup = () => this.runCleanup();
  runCleanup();
  const cleanupTimer = setInterval(runCleanup, dayMilliseconds);
  return () => clearInterval(cleanupTimer);
}
```

## Real-time push

`server.ts` sends each HTTP upgrade to `ChangeEventSocketController`.
The controller checks the session cookie before it accepts the WebSocket.
`ChangeEventService` keeps open connections in in-memory maps by user id and by session id.
`sendEvent(userId, event)` pushes a change event to every connection of that user.
A heartbeat interval sends a ping and rereads the session after expiry, and closes the socket when the session has ended.
Clients use the pushed events and `change_number` to refetch changed data.

## External integrations

- The server has no calendar integration.
- Music sources are local folders on the server's file system.
- `music-location` stores the folders a user adds and browses folders through `file-system.repository.ts`.
- `library-scan` walks the folders and reads tags with `music-metadata`, and records unreadable files.
- `music-track/path-track.source.ts` serves audio files by path.
- The music controllers exist only in the `local-mac` deployment.
- Sentry receives errors and logs.

## Testing

- Vitest projects are defined in `vitest.projects.ts` and selected by file suffix:
  - `*.unit.test.ts` for pure functions (`npm run test:unit`).
  - `*.api.test.ts` for HTTP tests with supertest against the app with fake repositories (`npm run test:api`).
  - `*.boundary.test.ts` for repositories against real Postgres in Testcontainers (`npm run test:boundary`, `vitest.postgres.config.ts`).
  - `*.external.test.ts` for external services (`npm run test:external`).
- Tests live in `apps/server/tests/<feature>/`, separate from `src/`.
- `tests/_fixtures/test-app-container.ts` builds the real container and replaces each repository with a fake from `tests/_fakes/`.
- `tests/_fixtures/database/` holds the Postgres global setup, test database, and test transaction helpers.
- Playwright end-to-end tests live in `e2e/`.
- Maestro flows test the Android app in `apps/mobile/.maestro/`.
- `npm run check:all` runs format, lint, typecheck, build, and every test project.

```ts
// apps/server/tests/_fixtures/test-app-container.ts
export function createTestAppContainer(config: ServerConfig = TEST_CONFIG): AppContainer {
  const appContainer = createAppContainer(config);
  appContainer.register({
    taskRepository: asClass(TaskRepositoryFake).singleton(),
    // one fake per repository
  });
  return appContainer;
}
```

## Build and deploy

- The server has no compile step and runs TypeScript with `tsx --import ./src/instrument.ts src/main.ts`.
- `npm run dev` builds the web app and starts the server with the `dev` Doppler config.
- `npm run local-prd` runs "local production" on the author's Mac with the `loc` Doppler config, from a separate clone that stays on `main`.
- Tailscale Serve (`npm run share:dev`, `npm run share:local-prd`) gives the Mac an HTTPS address for phones.
- Releases go from `develop` to `main` with `npm run release -- <patch|minor|major>`.
- `docker/check-all/Dockerfile` runs `check:all` in a container.
- The repository has no `.github` folder and no cloud deploy configuration.
- Husky hooks run lint-staged, secretlint, and commit message checks.

## Architecture docs in the repository

The repository has no `docs/` folder and no decision records.
`README.md` documents setup, settings with Doppler and Varlock, repo copies, local production, Tailscale sharing, manual tests, and the track-count benchmark.
`.claude/skills/` holds workflow skills, such as `settings-schema`, `testing`, and `implementation`.

## Patterns worth keeping

- One folder per feature with fixed file suffixes for controller, request, service, domain, and repository.
- Repository interfaces in the domain file, with in-memory fakes in `tests/_fakes/`.
- A typed awilix container in strict mode, with the test container that swaps repositories.
- One shared Zod contract package for server and clients, with a contract version header.
- Domain error classes mapped to HTTP status codes by small error middlewares.
- `AsyncLocalStorage` request context for the request id and the signed-in user.
- Zod parsing of all environment variables at start, with one error that lists every problem.
- Standard columns and sync columns (`change_number`, `deleted`, `deleted_at`) shared by tables.
- Test types separated by file suffix, with real Postgres in Testcontainers for repository tests.

## Problems seen

- Drizzle runs on a release candidate, `drizzle-orm` and `drizzle-kit` 1.0.0-rc.5.
- `BetterAuthClient` detects a disallowed host by matching the error text `is not in the allowed hosts list`, which is a workaround around the library's error type.
- The sign-up rate limit uses `storage: 'memory'`, so the limit resets on each restart.
- WebSocket connections and scan progress live in process memory, so the design supports one server process only (not verified beyond the code).
- The daily cleanup uses `setInterval` inside the server process, with no persistence of the last run.
- The `src/api/` error middlewares have no handler for unexpected errors, so those use the Express default response (not verified at runtime).
- `README.md` states that `npm run test:track-count` has not run past the step of 100,000 tracks.
- The free Doppler plan allows four environments per project, which the README documents as a limit.
- No `TODO`, `FIXME`, or `HACK` comments appear in `apps/server` or `packages/api-contract`.
