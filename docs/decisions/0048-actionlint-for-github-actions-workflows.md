# DR-0048: actionlint for GitHub Actions Workflows

**Status:** Draft  
**Date:** 2026-11-28

## Context

The repository is public, so GitHub Actions minutes on standard runners are free, as recorded in DR-0001.

Checks of a docs-only worktree run in GitHub Actions on the pull request, as recorded in DR-0011. Maestro device tests run in GitHub Actions, as recorded in DR-0010.

actionlint checks the types of `${{ }}` expressions. It checks shell scripts in `run:` steps with shellcheck. It runs with one command and finds workflow files by itself.

Example: the Maestro workflow refers to `${{ matrix.devise }}` in place of `${{ matrix.device }}`. actionlint reports the error before the workflow runs on GitHub.

## Decision

* actionlint checks the GitHub Actions workflow files.

## Alternatives Considered

No workflow linter, rejected:

* A broken workflow is found only when it runs on GitHub.

## Rationale

* The actionlint README documents expression type checks.
* The actionlint README documents shellcheck integration.

## Consequences

* The latest actionlint release is v1.7.12, from 2026-03-30. No release came in the 8 months since.
* The actionlint repository was not checked for recent commits.
* No other workflow linter was researched.
* No official source states that a broken workflow is found only when it runs on GitHub. This point is not verified.

## Sources

### [actionlint | GitHub](https://github.com/rhysd/actionlint)

Retrieved 2026-11-27.

- Fact: actionlint checks expression types.
  > "Strong type check for `${{ }}` expressions."
- Fact: It checks `run:` scripts with shellcheck and pyflakes.
  > "shellcheck and pyflakes integrations"
- Fact: It runs with one command.
  > "Basically all you need to do is run the `actionlint` command in your repository."

### [GitHub API: actionlint latest release](https://api.github.com/repos/rhysd/actionlint/releases/latest)

Retrieved 2026-11-27.

- Fact: The latest release is v1.7.12.
  > "{\"published_at\":\"2026-03-30T17:49:21Z\",\"tag_name\":\"v1.7.12\"}"
