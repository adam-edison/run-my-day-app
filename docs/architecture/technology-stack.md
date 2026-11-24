# Technology Stack

## Guiding Principles

- Single source of truth for schemas and contracts.
- Static, deterministic enforcement as much as possible.
- Runtime validation at boundaries.
- Database-level enforcement of data integrity.
- Explicit architectural and module boundaries.
- Generated documentation over manually maintained documentation.
- End-to-end verification of critical user workflows.
- Minimal tooling and abstraction overhead.
- Efficient context discovery for AI coding agents.
- Code-enforced constraints over instructional conventions.

## Language & Type Safety

- [TypeScript (Strict Mode)](https://www.typescriptlang.org/tsconfig/#strict) - Compile-time type safety, strict type checking, and typed interfaces across all application tiers.
- [Zod 4](https://zod.dev/) - Runtime schema validation, data contracts, type inference, and JSON Schema generation.

## API & Communication

- [Fastify](https://fastify.dev/) - High-performance HTTP server framework, plugin encapsulation, request lifecycle management, and backend infrastructure.
- [oRPC](https://orpc.dev/) - Contract-first, end-to-end type-safe APIs, request/response validation, and OpenAPI generation.
- [TanStack Query](https://tanstack.com/query/latest) - Asynchronous server state management, caching, mutations, request deduplication, and cache invalidation with type-safe oRPC integration.

## Architecture & Code Quality

- [ESLint](https://eslint.org/) - Static code analysis, coding standards, and deterministic rule enforcement.
- [TypeScript ESLint](https://typescript-eslint.io/) - Type-aware linting and TypeScript-specific code quality enforcement.
- [ESLint Boundaries](https://www.jsboundaries.dev/) - Architectural boundary enforcement, module isolation, and dependency restrictions.
- [Knip](https://knip.dev/) - Detection of unused files, exports, dependencies, and dead code.
- [Prettier](https://prettier.io/) - Deterministic code formatting and consistent source code style across the repository.

## Development Workflow

- [Husky](https://typicode.github.io/husky/) - Git hook management and automated local development quality gates.
- [lint-staged](https://github.com/lint-staged/lint-staged) - Targeted linting, formatting, and validation of staged files to provide fast pre-commit feedback.
- [commitlint](https://commitlint.js.org/) - Deterministic commit message validation and Conventional Commits enforcement.

## Environment & Configuration

- [Varlock](https://varlock.dev/) - Declarative environment variable schemas, configuration validation, type generation, and sensitive configuration handling.
- [Doppler CLI](https://docs.doppler.com/docs/cli) - Centralized secret management, environment-specific configuration retrieval, and secure secret injection for development and deployment workflows.

## Database & Persistence

- [Drizzle ORM](https://orm.drizzle.team/) - Type-safe SQL queries, declarative database schemas, relational modeling, and migration management.
- [PostgreSQL](https://www.postgresql.org/) - Relational data storage, database-level constraints, transactions, indexing, and data integrity enforcement.

## Testing & Verification

- [Vitest](https://vitest.dev/) - Unit, integration, and contract testing for business logic, schemas, and application components.
- [fast-check](https://fast-check.dev/) - Property-based unit testing with generated inputs, edge case discovery, reproducible failures, and automatic shrinking. Restricted to fast, isolated unit tests, excluding integration and end-to-end testing.
- [Playwright](https://playwright.dev/) - Browser-based end-to-end testing, user workflow verification, and regression testing.
- [Maestro](https://docs.maestro.dev/) - Declarative mobile end-to-end testing, native iOS and Android user workflow verification, and regression testing.

## Presentation & Client Applications

- [Expo](https://docs.expo.dev/) - Universal React Native application development targeting iOS, Android, and web from a shared TypeScript codebase.

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
- [Expo EAS](https://docs.expo.dev/eas/) - Managed application builds, code signing, app store submission, over-the-air updates, and deployment automation for Expo applications.

## Text-to-Speech

- [kokoro-js](https://www.npmjs.com/package/kokoro-js) - Client-side neural text-to-speech synthesis using Kokoro-82M with WebGPU or WASM inference, enabling on-demand audio generation in the browser without server-side processing or centralized audio storage.
- [ONNX Runtime](https://onnxruntime.ai/docs/get-started/with-javascript/react-native.html) - On-device model inference for native iOS and Android applications, enabling local text-to-speech synthesis, offline operation, and ephemeral audio playback without centralized audio storage.