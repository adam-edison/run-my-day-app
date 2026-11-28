# DR-0030: GitHub Actions Pull Request, Nightly, and Release Workflows

**Status:** Draft  
**Date:** 2026-11-28

## Context

GitHub Actions runs the checks for Run My Day. Some checks run on each pull request, some on a schedule, and some before a release. Some checks must pass before a pull request can be merged.

Facts from earlier decisions:

* The repository is public under the MIT License, as recorded in DR-0001.
* Standard GitHub-hosted runners are free for public repositories.
* Vitest runs the core and server unit tests and the server boundary tests on every commit, as recorded in DR-0006.
* Jest with jest-expo runs the display tests on every commit, as recorded in DR-0007.
* Playwright tests the exported web build in Chromium on a schedule and before a release, as recorded in DR-0009.
* Maestro runs device tests on an Android emulator and an iOS simulator in GitHub Actions on a schedule and before a release, as recorded in DR-0010.
* Hand checks on an Android phone and an iPad happen before a release, as recorded in DR-0010.
* A worktree with only non-code changes, such as docs, runs its checks in GitHub Actions on the pull request, as recorded in DR-0011.
* The technology stack lists ESLint, Knip, Prettier, commitlint, actionlint, Betterleaks, and Renovate.
* Secrets come from Doppler, as recorded in DR-0011.

Betterleaks ships no GitHub Action. It runs as a CLI or as the Docker image `ghcr.io/betterleaks/betterleaks:v2`.

Example: a pull request changes the "Morning" routine screen. The fast checks run on the pull request. Playwright and Maestro test the change that night on `main`.

## Decision

Workflows:

* A pull request workflow runs the fast checks on each pull request.
* A scheduled workflow runs Playwright and Maestro each night.
* A release workflow runs the same slow checks before a release, started by hand through `workflow_dispatch`.
* The pull request workflow skips the code jobs at the job level when only `docs/` or `openspec/` files change. The workflow itself always starts.

Pull request checks:

* ESLint, `tsc` type check, Prettier check, Knip, Vitest, and Jest.
* actionlint checks the workflow files.
* commitlint checks every commit in the pull request.
* Betterleaks scans for secrets. The job runs the Betterleaks CLI or the Docker image `ghcr.io/betterleaks/betterleaks:v2`.
* `actions/setup-node` caches the package manager's download folder.

Required status checks on `main`:

* lint, type check, Prettier, Knip, Vitest, Jest, actionlint, commitlint, and Betterleaks.
* Each job has a unique name across all workflows.

Scheduled and release checks:

* Playwright runs on `ubuntu-latest`.
* Maestro Android runs on Linux.
* Maestro iOS runs on a macOS runner.

Example: a typo fix in `docs/domain-model.md` starts the pull request workflow. The code jobs report "skipped", and the required checks pass.

## Alternatives Considered

Everything on each pull request, rejected:

* It reverses DR-0009 and DR-0010.
* macOS jobs are the slowest and most limited runners.
* GitHub states that macOS costs more than Linux on paid plans.

## Rationale

* The slow tests stay off the pull request path, as recorded in DR-0009 and DR-0010.
* Public repositories use standard runners for free. The nightly runs cost nothing.
* A skipped required check counts as passing.
* A workflow skipped by path or branch filters leaves its required checks pending and blocks the merge.
* Maestro iOS tests need a macOS runner because the iOS Simulator runs only on macOS with Xcode.
* commitlint documents a GitHub Actions job that checks all commits in a pull request.
* GitHub advises unique job names for required checks.

## Consequences

* A scheduled run can start late.
* Scheduled runs test only `main`.
* GitHub turns off scheduled runs in a public repository with no activity for 60 days.
* Schedules use UTC.
* `actions/setup-node` does not cache `node_modules`.
* The cache limit is 10 GB for each repository. Entries unused for 7 days are deleted.
* Betterleaks v2 is still in development. Until the first stable v2 release, package managers and the Docker `latest` tag may still provide v1.

## Open Questions

1. When does the scheduled run start? Example: every night at 03:00 UTC, so failures are visible each morning.
2. Does a failed nightly run open a GitHub issue automatically, or only send the usual email? Example: a Maestro failure on the iOS simulator opens an issue on the board.
3. Does the Vitest boundary test against Postgres run on each pull request in a Postgres service container? Example: the routine endpoint tests start a `postgres` service in the job.

## Sources

### [Troubleshooting required status checks](https://docs.github.com/en/pull-requests/how-tos/merge-and-close-pull-requests/troubleshooting-required-status-checks)

Retrieved 2026-11-28.

- Fact: A workflow skipped by path or branch filters leaves its required checks pending and blocks the merge.
  > "Associated checks stay in a "Pending" state and block merging"

### [iOS | Maestro](https://docs.maestro.dev/get-started/supported-platform/ios)

Retrieved 2026-11-28.

- Fact: Maestro runs iOS tests on a simulator managed by Xcode.
  > "Run tests on any iOS Simulator managed by Xcode."
- Fact: iOS tests depend on macOS hardware.
  > "Scaling iOS tests locally can be difficult due to macOS hardware requirements."

### [GitHub Actions billing](https://docs.github.com/en/billing/concepts/product-billing/github-actions)

Retrieved 2026-11-27.

- Fact: standard GitHub-hosted runners are free in public repositories.
  > "The use of standard GitHub-hosted runners is free:"
- Fact: larger runners are billed even for public repositories.
  > "Larger runners are billed for public repositories."
- Fact: macOS runners cost more than Linux runners on paid usage.
  > "For example, jobs that run on Windows and macOS runners hosted by GitHub cost more to run than jobs on Linux runners."

### [About protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)

Retrieved 2026-11-27.

- Fact: a skipped required check counts as passing.
  > "Required status checks must have a `successful`, `skipped`, or `neutral` status"
- Fact: job names must be unique when they are required checks.
  > "make sure that job names are unique across all workflows."

### [Events that trigger workflows](https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/events-that-trigger-workflows)

Retrieved 2026-11-27.

- Fact: scheduled runs can be delayed.
  > "The `schedule` event can be delayed during periods of high loads of GitHub Actions workflow runs."
- Fact: scheduled runs use only the default branch.
  > "Scheduled workflows will only run on the default branch."
- Fact: quiet public repositories lose their scheduled runs after 60 days.
  > "In a public repository, scheduled workflows are automatically disabled when no repository activity has occurred in 60 days."
- Fact: schedules use UTC.
  > "By default, scheduled workflows run in UTC."

### [Dependency caching reference](https://docs.github.com/en/actions/reference/workflows-and-actions/dependency-caching)

Retrieved 2026-11-27.

- Fact: unused cache entries are removed after 7 days.
  > "GitHub will remove any cache entries that have not been accessed in over 7 days."
- Fact: the cache limit is 10 GB per repository.
  > "By default, the limit is 10 GB per repository, but this limit can be increased by enterprise owners"

### [setup-node](https://github.com/actions/setup-node)

Retrieved 2026-11-27.

- Fact: the action caches package manager downloads.
  > "Supported package managers are `npm`, `yarn`, `pnpm` (v6.10+). The `cache` input is optional."
- Fact: the action does not cache `node_modules`.
  > "The action does not cache `node_modules`"

### [GitHub-hosted runners](https://docs.github.com/en/actions/using-github-hosted-runners/using-github-hosted-runners/about-github-hosted-runners)

Retrieved 2026-11-27.

- Fact: GitHub hosts Linux, Windows, and macOS runners.
  > "GitHub-hosted runners are available with Ubuntu Linux, Windows, or macOS operating systems."

### [actionlint](https://github.com/rhysd/actionlint)

Retrieved 2026-11-27.

- Fact: actionlint checks workflow files statically.
  > "is a static checker for GitHub Actions workflow files."
- Fact: running it needs only the command.
  > "Basically all you need to do is run the `actionlint` command in your repository."

### [Guide: CI Setup (commitlint)](https://commitlint.js.org/guides/ci-setup.html)

Retrieved 2026-11-27.

- Fact: commitlint documents a GitHub Actions job for all commits in a pull request.
  > "An example of how a GitHub Actions workflow could validate the last commit message or all commit messages inside a Pull Request:"

### [Betterleaks](https://github.com/betterleaks/betterleaks)

Retrieved 2026-11-27.

- Fact: Betterleaks is a secrets scanner.
  > "Betterleaks is a configurable, fast, and thorough secrets scanner."
- Fact: version 2 is still in development.
  > "Until the first stable v2 release, package managers and the Docker `latest` tag may still provide v1."

### [Maestro Cloud overview](https://docs.maestro.dev/maestro-cloud/readme.md)

Retrieved 2026-11-27.

- Fact: Maestro Cloud is a paid hosted option with a GitHub Actions integration.
  > "Ready-made integrations for GitHub Actions, Bitrise, Bitbucket Pipelines, and CircleCI."
