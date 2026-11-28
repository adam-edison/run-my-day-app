# DR-0006: Vitest for Core and Server Tests

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Most logic is plain TypeScript: the client state actors in the core package, the schemas, and the server. Most tests cover this logic, and they run on every commit. Vitest has no official support for React Native components.

## Decision

Vitest runs the unit tests for the core package and the server. It also runs the server's boundary tests against the API and the database. React Native component tests use Jest, as recorded in DR-0007.

## Alternatives Considered

- **Jest everywhere:** Rejected because Jest's ESM support is experimental, and Jest runs slower.
- **Vitest everywhere, with vitest-native:** Rejected because Expo documents only Jest with jest-expo, and Vitest support for Expo comes only from a community plugin that has not reached version 1.0.

## Rationale

- **Fast feedback:** Tests run on every commit, so speed matters most here.
- **TypeScript and ESM:** Both work without extra setup.
- **Official support:** Vitest officially supports plain TypeScript code.

## Consequences

- Two test runners are maintained: Vitest and Jest.
- Shared test helpers can't be imported by both runners without care, because their mock APIs differ (`vi` against `jest`).

## Sources

### [ECMAScript Modules · Jest](https://jestjs.io/docs/ecmascript-modules)

Retrieved 2026-11-27.

- Fact: Jest's ESM support is experimental.
  > "Jest ships with experimental support for ECMAScript Modules (ESM)."
  > "The implementation may have bugs and lack features."

### [Comparisons with Other Test Runners | Guide | Vitest](https://vitest.dev/guide/comparisons)

Retrieved 2026-11-27.

- Fact: Jest runs slower than Vitest.
  > "it gives you a faster run for your unit tests and a jump in DX thanks to the default watch mode"

### [Features | Guide | Vitest](https://vitest.dev/guide/features)

Retrieved 2026-11-27.

- Fact: TypeScript and ESM work in Vitest without extra setup.
  > "Out-of-the-box TypeScript / JSX support"
  > "ESM first, top level await"

### [Migrating from Jest | Vitest](https://vitest.dev/guide/migration/jest)

Retrieved 2026-11-27.

- Fact: The mock APIs of Vitest and Jest differ.
  > "Vitest doesn't have an equivalent to `jest` namespace, so you will need to import types directly from `vitest`"
  > "In Vitest, the factory argument has to return an object with each export explicitly defined."

### [Unit testing with Jest - Expo Documentation](https://docs.expo.dev/develop/unit-testing/)

Retrieved 2026-11-27.

- Fact: Expo documents only Jest with jest-expo. The page does not mention Vitest.
  > "Jest is the most widely used unit and snapshot JavaScript testing framework."
  > "which is a Jest preset that mocks the native part of the Expo SDK"

### [Testing · React Native](https://reactnative.dev/docs/testing-overview)

Retrieved 2026-11-27.

- Fact: Vitest has no official support for React Native components. React Native ships Jest and does not mention Vitest.
  > "The default template of React Native ships with Jest testing framework."

### [vitest-native — Test React Native as it ships](https://vitest-native.danfry.dev/)

Retrieved 2026-11-27.

- Fact: Vitest support for Expo comes only from a community plugin that has not reached version 1.0.
  > "It's pre-1.0. Some options may still change before 1.0."
  > "Maintained successor to"
