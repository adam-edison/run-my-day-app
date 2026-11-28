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

zizmor, not chosen:

* zizmor is an MIT-licensed static analysis tool for GitHub Actions.
* Its checks target security problems, such as template injection, credential exposure, and broad permissions.
* Its README does not list expression type checks or shellcheck integration.

jactionlint, not chosen:

* jactionlint started as a fork of actionlint and covers actionlint's checks.
* It adds security rules that follow the audits of zizmor.
* Packages called actionlint, such as the one in Homebrew core, install the original project, not this fork.

GitHub Actions extension for VS Code, not chosen:

* It validates workflow files and GitHub Actions expressions in the editor.
* It runs in the editor only, not in a git hook or CI.

## Rationale

* The actionlint README documents expression type checks.
* The actionlint README documents shellcheck integration.

## Consequences

* The latest actionlint release is v1.7.12, from 2026-03-30. No release came in the 8 months since.
* The latest commit on the actionlint `main` branch is from 2026-04-19.

## Assumptions

* actionlint is still maintained despite 8 months without a release. Not found in official sources as of 2026-11-28.
* Without a workflow linter, a broken workflow is found only when it runs on GitHub. A source contradicts this.

## Open Questions

* Should zizmor run beside actionlint? Example: a workflow step puts `${{ github.event.pull_request.title }}` straight into a `run:` script. zizmor reports the template injection.
* Should jactionlint replace actionlint? Example: one jactionlint run reports both the `${{ matrix.devise }}` typo and a template injection.
* Should the GitHub Actions extension for VS Code be recommended in the editor? Example: it marks an invalid workflow key while the file is being edited.

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

### [actionlint commits | GitHub](https://github.com/rhysd/actionlint/commits/main)

Retrieved 2026-11-28.

- Fact: The latest commit on `main` is from 2026-04-19.
  > "Merge pull request #651 from attehuhtakangas/fix-shellcheck-deadlock-macos"

### [zizmor | GitHub](https://github.com/zizmorcore/zizmor)

Retrieved 2026-11-28.

- Fact: zizmor is a static analysis tool for GitHub Actions.
  > "Static analysis for GitHub Actions (and more)"
- Fact: zizmor is MIT licensed.
  > "licensed under the MIT License."
- Fact: Its checks target security problems. The page was read as a summary, so no verbatim quote is available for the list of checks.

### [jactionlint | GitHub](https://github.com/jdx/jactionlint)

Retrieved 2026-11-28.

- Fact: jactionlint covers actionlint's checks and adds security rules from zizmor.
  > "It started as a fork of actionlint and covers its checks, and its security rules follow the audits of zizmor."
- Fact: Packages called actionlint install the original project.
  > "Packages named actionlint, as in Homebrew core, install the original project, not this fork."

### [GitHub Actions for VS Code | GitHub](https://github.com/github/vscode-github-actions)

Retrieved 2026-11-28.

- Fact: The extension validates workflow files and expressions in the editor.
  > "Validation and code completion for the YAML schema and GitHub Actions Expressions."
