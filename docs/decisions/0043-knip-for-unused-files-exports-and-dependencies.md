# DR-0043: Knip for Unused Files, Exports, and Dependencies

**Status:** Draft  
**Date:** 2026-11-28

## Context

The repository is a monorepo with an Expo client, a shared core package, and a Fastify server.

Vitest tests the core package and the server, as recorded in DR-0006. Jest with jest-expo tests the display, as recorded in DR-0007.

Knip finds unused dependencies, exports, and files. Knip has over 200 plugins. The list includes ESLint, Jest, and Vitest. The Expo plugin turns on when `expo` is a dependency.

Knip's latest version, 6.49.0, was published on npm on 2026-11-27.

Example: the core package exports a `formatStreak` helper that no file imports anymore. Knip reports the unused export.

## Decision

* Knip finds unused files, exports, and dependencies in every package of the monorepo.

## Alternatives Considered

ts-prune and depcheck, rejected:

* Each covers only part of the work.

TypeScript's `noUnusedLocals`, rejected:

* It reports unused local variables.
* It finds unused code inside one file only.
* It does not find unused exports or dependencies.

## Rationale

* Knip documents checks for unused files, exports, and dependencies.
* Knip documents plugins for Expo, Vitest, Jest, and ESLint.
* Knip is actively released.

## Consequences

* Knip reports a file as unused when no entry file reaches it. A plugin that misses an entry file pattern causes false reports of unused files.

## Assumptions

* TypeScript's `noUnusedLocals` finds unused code inside one file only and does not find unused exports or dependencies. Not found in official sources as of 2026-11-28.

## Sources

### [Knip](https://knip.dev/)

Retrieved 2026-11-27.

- Fact: Knip finds unused dependencies, exports, and files.
  > "Knip finds and fixes unused dependencies, exports and files."
- Fact: Knip has plugins for ESLint, Jest, and Vitest.
  > "comes with over 200 plugins for tools and frameworks like Astro, Cypress, ESLint, Jest, GitHub Actions, Next.js, Nx, Remix, Storybook, Svelte, Vite, Vitest and Webpack."

### [Expo | Knip](https://knip.dev/reference/plugins/expo)

Retrieved 2026-11-27.

- Fact: The Expo plugin turns on when Expo is a dependency.
  > "This plugin is enabled if `\"expo\"` is listed in `\"dependencies\"` or `\"devDependencies\"` in `package.json`."

### [Configuring Project Files | Knip](https://knip.dev/guides/configuring-project-files)

Retrieved 2026-11-28.

- Fact: Knip reports a file as unused when no entry file reaches it.
  > "Files are reported as unused if they are in the set of `project` files, but are not resolved from the `entry` files:"

### [Handling Issues | Knip](https://knip.dev/guides/handling-issues)

Retrieved 2026-11-28.

- Fact: A plugin that misses an entry file pattern causes false reports of unused files.
  > "Files may be reported as unused if existing plugins do not include that entry file pattern yet."

### [noUnusedLocals | TSConfig Reference](https://www.typescriptlang.org/tsconfig/#noUnusedLocals)

Retrieved 2026-11-28.

- Fact: `noUnusedLocals` reports unused local variables.
  > "Report errors on unused local variables."

### [npm registry: knip](https://www.npmjs.com/package/knip)

Retrieved 2026-11-27.

- Fact: Knip's latest version is 6.49.0, updated 2026-11-27.
  > "version = '6.49.0'"
  > "time.modified = '2026-11-27T20:21:35.201Z'"
