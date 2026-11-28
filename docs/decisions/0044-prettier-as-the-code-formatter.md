# DR-0044: Prettier as the Code Formatter

**Status:** Draft  
**Date:** 2026-11-28

## Context

Prettier's main purpose is to end debates over code style. Its latest version is 3.10.1, updated on npm on 2026-11-12.

The repository has Markdown in `docs/` and YAML in GitHub Actions workflows.

The linter is ESLint, as proposed in DR-0042. With ESLint as the linter, Biome would be used only as a formatter. Biome's latest version is 2.6.9.

Biome's formatter follows the same approach as Prettier.

Example: a decision record in `docs/decisions/` and a TypeScript file in the core package are both formatted by one `prettier --write` run.

## Decision

* The code formatter is Prettier 3.

## Alternatives Considered

Biome formatter, rejected:

* With ESLint as the linter, Biome would add only formatting. Prettier does that.
* Biome follows the same approach as Prettier. A switch would bring no new formatting style.
* Biome's Markdown and YAML formatter support could not be confirmed from its language support page.

## Rationale

* Prettier is maintained.

## Consequences

* No official source was read that states Prettier formats Markdown and YAML. The Prettier language list was not read.
* No benchmark was read that compares Prettier with formatters written in Rust. Prettier being slower is not verified.
* The choice of Prettier is not verified by an official source.

## Sources

### [Why Prettier? | Prettier](https://prettier.io/docs/why-prettier)

Retrieved 2026-11-27.

- Fact: Prettier's main purpose is to end style debates.
  > "By far the biggest reason for adopting Prettier is to stop all the on-going debates over styles."

### [npm registry: prettier, @biomejs/biome](https://www.npmjs.com/package/prettier)

Retrieved 2026-11-27.

- Fact: Prettier's latest version is 3.10.1.
  > "version = '3.10.1'"
  > "time.modified = '2026-11-12T07:06:22.685Z'"
- Fact: Biome's latest version is 2.6.9.
  > "version = '2.6.9'"

### [Formatter | Biome](https://biomejs.dev/formatter/)

Retrieved 2026-11-27.

- Fact: Biome's formatter follows Prettier's approach.
  > "It follows a similar philosophy to Prettier"
