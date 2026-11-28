# Technology Stack

## Language & Type Safety

- [TypeScript (Strict Mode)](https://www.typescriptlang.org/tsconfig/#strict) - Compile-time type safety, strict type checking, and typed interfaces across all application tiers.
- [Zod 4](https://zod.dev/) - Runtime schema validation, data contracts, type inference, and JSON Schema generation.

## API & Communication

- [Fastify](https://fastify.dev/) - High-performance HTTP server framework, plugin encapsulation, request lifecycle management, and backend infrastructure.
- [oRPC](https://orpc.dev/) - Contract-first, end-to-end type-safe APIs, request/response validation, and OpenAPI generation. Requests use oRPC's RPC protocol over HTTP. Push to open clients uses server-sent events through oRPC streaming, with expo/fetch on Android and iOS. Sync of the outbox runs through oRPC on the Fastify server.
## Architecture & Code Quality

- [ESLint](https://eslint.org/) - Static code analysis, coding standards, and deterministic rule enforcement.
- [TypeScript ESLint](https://typescript-eslint.io/) - Type-aware linting and TypeScript-specific code quality enforcement.
- [ESLint Boundaries](https://www.jsboundaries.dev/) - Architectural boundary enforcement, module isolation, and dependency restrictions. Rejects any import of XState outside the core package.
- [Knip](https://knip.dev/) - Detection of unused files, exports, dependencies, and dead code.
- [Prettier](https://prettier.io/) - Deterministic code formatting and consistent source code style across the repository.

## Development Workflow

- [Husky](https://typicode.github.io/husky/) - Git hook management and automated local development quality gates.
- [lint-staged](https://github.com/lint-staged/lint-staged) - Targeted linting, formatting, and validation of staged files to provide fast pre-commit feedback.
- [commitlint](https://commitlint.js.org/) - Deterministic commit message validation and Conventional Commits enforcement.

## Environment & Configuration

- [Varlock](https://varlock.dev/) - Declarative environment variable schemas, configuration validation, type generation, and sensitive configuration handling.
- [Doppler CLI](https://docs.doppler.com/docs/cli) - Centralized secret management, environment-specific configuration retrieval, and secure secret injection for development and deployment workflows. `doppler run` passes secrets as environment variables, and reads its latest encrypted snapshot when Doppler can't be reached. The token is stored in the macOS Keychain.

## Database & Persistence

- [Drizzle ORM](https://orm.drizzle.team/) - Type-safe SQL queries, declarative database schemas, relational modeling, and migration management, on the server and on each device.
- [PostgreSQL](https://www.postgresql.org/) - Relational data storage, database-level constraints, transactions, indexing, and data integrity enforcement on the server.
- [expo-sqlite](https://docs.expo.dev/versions/latest/sdk/sqlite/) - Offline client storage on Android, iOS, and web, with one SQLite schema and one set of queries for every platform. Holds definitions, the last calendar read, 90 days of history, history snapshots, and the outbox.

## Testing & Verification

- [Vitest](https://vitest.dev/) - Unit tests for the core package and the server, and the server's boundary tests against the API and the database. Runs on every commit.
- [Jest](https://jestjs.io/) with [jest-expo](https://docs.expo.dev/develop/unit-testing/) - Display tests of React Native components, with the default jest-expo preset. Runs on every commit.
- [React Native Testing Library](https://oss.callstack.com/react-native-testing-library/) - Rendering and querying components in display tests, by text and role.
- [fast-check](https://fast-check.dev/) - Property-based unit testing with generated inputs, edge case discovery, reproducible failures, and automatic shrinking. Restricted to fast, isolated unit tests, excluding integration and end-to-end testing.
- [Playwright](https://playwright.dev/) - Boundary tests of the exported Expo web build in Chromium, against the real server: the service worker, offline use, sync after reconnecting, and code written only for the web. Runs on a schedule and before a release.
- [Maestro](https://docs.maestro.dev/) - Device tests on an Android emulator and an iOS simulator in GitHub Actions. Runs on a schedule and before a release.

## Presentation & Client Applications

- [Expo](https://docs.expo.dev/) - Universal React Native application development targeting iOS, Android, and web from a shared TypeScript codebase. The web build is exported as static files. Expo API routes are not used.
- [Workbox](https://developer.chrome.com/docs/workbox) - Generates the service worker for offline use of the web build, after the static export.
- Supported web browsers: Chromium-based desktop browsers only, namely Google Chrome, Microsoft Edge, Opera, and Brave.

## Client State

- [XState v5](https://stately.ai/docs/xstate) - Client state in actors, each a statechart with nested and parallel states. The actors are Day, Block run, Task run, Waits, Announcer, and Sync. Only the shared core package imports XState.

## Alerts

- [AlarmKit](https://developer.apple.com/documentation/alarmkit) - Block-start alerts on iOS when the app is not open.
- [Android exact alarms](https://developer.android.com/develop/background-work/services/alarms) - Block-start alerts on Android when the app is not open. The app requires the exact alarm permission before it can be used.
- [Push API](https://developer.mozilla.org/en-US/docs/Web/API/Push_API) - Web push from the server at each block start when the web app's tab is closed.

## Domain Utilities

- [rrule](https://github.com/jkbrzt/rrule) - RFC 5545-based recurrence rule parsing, serialization, and occurrence generation for recurring tasks, events, and schedules.

## Security

- [Better Auth](https://better-auth.com/docs) - Self-hosted, TypeScript-native authentication and identity management, session management, API keys, OAuth, and integration with Drizzle, Expo, and oRPC.
- [@fastify/rate-limit](https://github.com/fastify/fastify-rate-limit) - HTTP request rate limiting, abuse prevention, configurable rate limit policies, and support for shared storage when scaling across multiple backend instances.
- [Betterleaks](https://github.com/betterleaks/betterleaks) - Automated secret detection across source code and Git history, with configurable detection rules and CI enforcement to prevent credential exposure.

## Logging

- [Pino](https://getpino.io/) - High-performance structured JSON logging, contextual child loggers, sensitive data redaction, and request-correlated logging through Fastify integration.

## Observability & Monitoring

- [Sentry](https://docs.sentry.io/) - Application error tracking, exception reporting, performance monitoring, distributed tracing, and release regression detection across web, mobile, and backend services.

## Dependency Management

- [Renovate](https://docs.renovatebot.com/) - Automated dependency discovery, version updates, configurable update policies, and dependency maintenance through pull requests.

## CI/CD

- [GitHub Actions](https://docs.github.com/en/actions) - Automated build, verification, and deployment workflows with integrations for cloud deployment services.
- [actionlint](https://github.com/rhysd/actionlint) - Static analysis of GitHub Actions workflows, including syntax validation, expression type checking, and configuration error detection.
- [Expo Build](https://docs.expo.dev/build/introduction/) - Managed builds and code signing for the Android and iOS apps.
- [Expo Submit](https://docs.expo.dev/submit/introduction/) - App store submission for the Android and iOS apps.

## Text-to-Speech

- [kokoro-js](https://www.npmjs.com/package/kokoro-js) - Client-side neural text-to-speech synthesis using Kokoro-82M with WebGPU or WASM inference, enabling on-demand audio generation in the browser without server-side processing or centralized audio storage.
- [ONNX Runtime](https://onnxruntime.ai/docs/get-started/with-javascript/react-native.html) - On-device model inference for native iOS and Android applications, enabling local text-to-speech synthesis, offline operation, and ephemeral audio playback without centralized audio storage.