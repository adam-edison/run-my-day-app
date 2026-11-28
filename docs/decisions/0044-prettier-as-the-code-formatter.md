# DR-0044: Prettier as the Code Formatter

**Status:** Draft  
**Date:** 2026-11-28

## Context

Prettier's main purpose is to end debates over code style. Its latest version is 3.10.1, updated on npm on 2026-11-12.

The repository has Markdown in `docs/` and YAML in GitHub Actions workflows.

The linter is ESLint, as proposed in DR-0042. With ESLint as the linter, Biome would be used only as a formatter. Biome's latest version is 2.6.9.

Biome's formatter follows the same approach as Prettier.

Prettier formats Markdown and YAML. Biome's home page reports Biome as about 35 times faster than Prettier on one benchmark of 171,127 lines.

Example: a decision record in `docs/decisions/` and a TypeScript file in the core package are both formatted by one `prettier --write` run.

## Decision

* The code formatter is Prettier 3.

## Alternatives Considered

Biome formatter, rejected:

* With ESLint as the linter, Biome would add only formatting. Prettier does that.
* Biome follows the same approach as Prettier. A switch would bring no new formatting style.

## Rationale

* Prettier is maintained.

## Assumptions

* Biome's formatter does not format Markdown and YAML. A source contradicts this.

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

### [What is Prettier? | Prettier](https://prettier.io/docs/)

Retrieved 2026-11-28.

- Fact: Prettier formats Markdown and YAML.
  > "Prettier is an opinionated code formatter with support for:"
  > "Markdown, including GFM and MDX v1"
  > "YAML"

### [Biome](https://biomejs.dev/)

Retrieved 2026-11-28.

- Fact: Biome reports about 35 times the speed of Prettier on one benchmark.
  > "~35x"
  > "Faster than Prettier when formatting 171,127 lines of code in 2,104 files with an Intel Core i7 1270P."

### [Biome v2.6 | Biome](https://biomejs.dev/blog/biome-v2-6/)

Retrieved 2026-11-28.

- Fact: Biome 2.6 supports Markdown and YAML.
  > "Biome 2.6 ships support for three new languages: Markdown, YAML and SCSS."
- Fact: Biome's Markdown formatter is about 92% compatible with Prettier.
  > "The Markdown formatter is ~92% compatible with Prettier"
- Fact: Biome's YAML formatter is fully compatible with Prettier.
  > "the formatter has 100% compatibility with Prettier"
