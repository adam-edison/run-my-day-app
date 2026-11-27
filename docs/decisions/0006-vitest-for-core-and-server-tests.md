# DR-0006: Vitest for Core and Server Tests

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Most logic is plain TypeScript: the client state actors in the core package, the schemas, and the server. Most tests cover this logic, and they run on every commit. Vitest has no official support for React Native components.

## Decision

Vitest runs the unit tests for the core package and the server. It also runs the server's boundary tests against the API and the database. React Native component tests use Jest, as recorded in DR-0007.

## Alternatives Considered

- **Jest everywhere:** Rejected because Jest's ESM support is experimental, and Jest runs slower.
- **Vitest everywhere, with vitest-native:** Rejected because Expo documents only Jest with jest-expo, and Vitest support for Expo comes only from new community plugins.

## Rationale

- **Fast feedback:** Tests run on every commit, so speed matters most here.
- **TypeScript and ESM:** Both work without extra setup.
- **Official support:** Vitest officially supports plain TypeScript code.

## Consequences

- Two test runners are maintained: Vitest and Jest.
- Shared test helpers can't be imported by both runners without care, because their mock APIs differ (`vi` against `jest`).
