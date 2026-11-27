# DR-0014: Parallel Worktrees, with One Big Task at a Time

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Challenges:

* Every line of code is reviewed by one human.
* AI coding agents write pull requests faster than one person can review them.
* Pull requests that wait for review go stale against `main`.

## Decision

Work runs in parallel git worktrees, with these limits:

* One big task at a time.
* Up to 3 small tasks at the same time as the big one.
* At most 4 worktrees have work in progress at once.

A big task is one pull request in a vertical slice by feature, or in the walking skeleton.

A small task is any other change that adds no feature behavior, such as a docs fix, a bug fix, or a dependency update.

Example: the routine endpoints pull request is the big task in worktree 1. At the same time, worktree 2 fixes a typo in the domain model, and worktree 3 updates a dependency.

## Alternatives Considered

One pull request at a time, rejected:

* Small fixes would wait behind the big task.

Several big tasks at once, rejected:

* Big pull requests would queue up for review and go stale against `main`.

## Rationale

* Review sets the pace, and only one big pull request waits for review at a time.
* Small tasks are reviewed quickly, so they don't slow the big one.
* The limit of 4 keeps the number of tasks one reviewer tracks low enough to avoid overwhelm.

## Consequences

* A second feature can't start until the big task in progress is merged.
