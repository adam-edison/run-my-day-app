# DR-0042: ESLint with typescript-eslint and ESLint Boundaries

**Status:** Draft  
**Date:** 2026-11-28

## Context

The code is TypeScript in strict mode.

Only the core package imports XState. A static boundary check rejects any other import of XState, as recorded in DR-0005.

typescript-eslint runs type-aware rules. These rules use the TypeScript type checker to find bugs that plain syntax rules miss.

ESLint Boundaries sorts files into element types, such as "core" and "app". It rejects imports that break a policy. A policy can also match an external package, such as `xstate`.

Latest npm versions:

* ESLint 10.16.0
* typescript-eslint 8.74.0
* eslint-plugin-boundaries 7.4.5, updated 2026-11-25

Example: a display component in the Expo app adds `import { createActor } from "xstate"`. ESLint Boundaries reports an error in the editor and in CI.

## Decision

* The linter is ESLint 10 with typescript-eslint 8 and ESLint Boundaries 7.
* Type-aware linting runs in CI and before push, as the typescript-eslint docs suggest.

## Alternatives Considered

Oxlint, rejected:

* Support for ESLint plugins written in JavaScript is in alpha. An alpha feature can change or break.

Biome, rejected:

* The Biome linter page shows no rule that restricts imports by folder.
* The XState boundary check would need another tool.

dependency-cruiser for the boundary check, rejected:

* It checks import rules from the command line.
* Its page shows no editor feedback, unlike an ESLint rule.

## Rationale

* typescript-eslint documents type-aware rules.
* ESLint Boundaries documents import policies by element type and by external package.
* All three packages are actively released.

## Consequences

* Type-aware linting is slower, because TypeScript builds the whole project first.
* Oxlint is 50 to 100 times faster than ESLint, by its own benchmarks.
* Oxlint has type-aware linting, and ESLint Boundaries lists Oxlint as supported.
* Biome v2 has some type-aware rules.
* No official source states whether type-aware linting is fast enough in a pre-commit hook for this repository.
* No official source states that `no-restricted-imports` with a per-folder override can reject `xstate` outside the core package. This idea is not verified.

## Open Questions

* Is one rule for XState the only boundary planned? If yes, ESLint's built-in `no-restricted-imports` rule alone would cover it, and ESLint Boundaries could be dropped. If more layer rules are planned, ESLint Boundaries fits. Example: a later rule that the server never imports from the Expo app would be a second boundary.

## Sources

### [npm registry: eslint, typescript-eslint, eslint-plugin-boundaries](https://www.npmjs.com/package/eslint)

Retrieved 2026-11-27.

- Fact: ESLint's latest version is 10.16.0.
  > "version = '10.16.0'"
- Fact: typescript-eslint's latest version is 8.74.0.
  > "version = '8.74.0'"
- Fact: eslint-plugin-boundaries' latest version is 7.4.5, updated 2026-11-25.
  > "version = '7.4.5'"
  > "time.modified = '2026-11-25T14:13:25.856Z'"

### [Linting with Type Information | typescript-eslint](https://typescript-eslint.io/getting-started/typed-linting/)

Retrieved 2026-11-27.

- Fact: Type-aware rules use the TypeScript type checker.
  > "Some typescript-eslint rules utilize TypeScript's type checking APIs to provide much deeper insights into your code."

### [JS Boundaries](https://www.jsboundaries.dev/)

Retrieved 2026-11-27.

- Fact: ESLint Boundaries reports an error when an import crosses a boundary.
  > "Classify your files, write policies for who may depend on whom, and get an ESLint error the moment a boundary is crossed."
- Fact: It supports ESLint 9, Oxlint, TypeScript, and monorepos.
  > "ESLint 9, Oxlint, TypeScript, monorepos"
- Fact: A policy can match external packages.
  > "Where the dependency resolves: local, external, core."

### [Linter | Oxc](https://oxc.rs/docs/guide/usage/linter.html)

Retrieved 2026-11-27.

- Fact: Oxlint claims to be much faster than ESLint.
  > "show Oxlint is 50 to 100 times faster than ESLint."
- Fact: Oxlint has type-aware linting.
  > "Oxlint supports two backends for type-aware linting."
- Fact: Oxlint's ESLint-compatible JavaScript plugins are listed as an alpha feature.
  > "Custom JS plugins compatible with the ESLint plugin ecosystem."

### [Linter | Biome](https://biomejs.dev/linter/)

Retrieved 2026-11-27.

- Fact: Biome v2 has type-aware rules.
  > "Since Biome v2, the toolchain is now able to use TypeScript to infer types and provide more powerful rules."

### [no-restricted-imports | ESLint](https://eslint.org/docs/latest/rules/no-restricted-imports)

Retrieved 2026-11-27.

- Fact: ESLint has a built-in rule that rejects named modules.
  > "Disallow specified modules when loaded by `import`"

### [dependency-cruiser | GitHub](https://github.com/sverweij/dependency-cruiser)

Retrieved 2026-11-27.

- Fact: dependency-cruiser checks rules from the command line.
  > "This will validate against your rules and shows any violations in an eslint-like format."
