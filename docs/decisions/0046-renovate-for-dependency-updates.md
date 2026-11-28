# DR-0046: Renovate for Dependency Updates

**Status:** Draft  
**Date:** 2026-11-28

## Context

The repository is public on GitHub, as recorded in DR-0001.

A dependency update is a small task. Up to 3 small tasks run beside the big task, as recorded in DR-0014.

Renovate finds package files automatically, including in monorepos. Its latest version, 45.3.2, was published on 2026-11-27. A Renovate schedule can be set per dependency or per package manager.

Dependabot is built into GitHub.

Example: Expo releases a new SDK. Renovate opens one pull request that updates `expo` and every related `expo-*` package together.

## Decision

* Renovate opens the dependency update pull requests.
* The `group:monorepos` preset groups the packages of one monorepo, such as Expo or typescript-eslint, into one pull request.
* The Dependency Dashboard lists every pending update in one issue.

## Alternatives Considered

Dependabot, rejected:

* Dependabot groups only by groups the user writes.
* Dependabot does not update the packages of one monorepo in one pull request.
* Dependabot has no dashboard.

## Rationale

* Renovate documents monorepo grouping, a dashboard, and per-dependency schedules.
* These features keep the number of update pull requests low.
* Few update pull requests fit the limit of 3 small tasks.

## Consequences

* The Mend Renovate app is hosted by Mend, an outside company.
* The price of the Mend Renovate app is not verified.

## Sources

### [Bot comparison | Renovate Docs](https://docs.renovatebot.com/bot-comparison/)

Retrieved 2026-11-27.

- Fact: Renovate groups monorepo packages in one pull request.
  > "Renovate has a group:monorepos preset, that upgrades common monorepo packages in a single PR."
- Fact: Dependabot does not.
  > "Dependabot does not update common monorepo packages in a single PR."
- Fact: Dependabot needs user-written groups.
  > "Dependabot can group dependencies into a single PR too, but you must set your own groups first."
- Fact: Renovate has a dashboard, and Dependabot does not.
  > "One big feature of Renovate is the Dependency Dashboard, which is enabled by default."
  > "Dependabot does not have a similar feature."
- Fact: Renovate schedules can be set per dependency.
  > "You can set a schedule for Renovate, per dependency, manager, or even a global schedule."

### [Renovate Docs](https://docs.renovatebot.com/)

Retrieved 2026-11-27.

- Fact: Renovate finds package files in monorepos.
  > "Renovate finds relevant package files automatically, including in monorepos."
- Fact: The Mend Renovate app is hosted by Mend.
  > "Or you can use the Mend Renovate app which is hosted by Mend."

### [npm registry: renovate](https://www.npmjs.com/package/renovate)

Retrieved 2026-11-27.

- Fact: Renovate's latest version is 45.3.2.
  > "version = '45.3.2'"
  > "time.modified = '2026-11-27T17:48:50.669Z'"
