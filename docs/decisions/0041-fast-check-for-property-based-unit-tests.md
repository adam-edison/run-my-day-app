# DR-0041: fast-check for Property-Based Unit Tests

**Status:** Draft  
**Date:** 2026-11-28

## Context

Vitest runs the unit tests for the core package and the server, as recorded in DR-0006.

Property-based testing runs one test against many generated inputs. fast-check is a property-based testing library for JavaScript and TypeScript. Its latest version on npm is 4.11.0.

fast-check needs no special test runner integration. It works with Vitest.

Example: a recurrence rule repeats a task every second Tuesday. A property test generates hundreds of start dates and checks that each next date falls on a Tuesday 14 days later.

## Decision

* The unit tests keep fast-check.
* fast-check is used only in fast unit tests that run in Vitest.

## Alternatives Considered

No property-based testing, rejected:

* Edge cases would depend on hand-picked examples.

## Rationale

* fast-check runs in Vitest with no extra setup.
* Logic with many input cases, such as recurrence rules, per-field conflict resolution, and streak counts, suits property tests.

## Consequences

* fast-check adds another library and test style to learn.
* No official source states which Run My Day logic benefits from property tests. The choice of recurrence rules, conflict resolution, and streak counts is not verified.
* No official source states that hand-picked examples miss edge cases in this codebase.

## Sources

### [fast-check](https://fast-check.dev/)

Retrieved 2026-11-27.

- Fact: fast-check is a property-based testing library for JavaScript and TypeScript.
  > "Property-based testing for JavaScript and TypeScript"
- Fact: It needs no special test runner integration.
  > "fast-check can be used within any test runner without any specific integration needed."
- Fact: It works with Vitest.
  > "It works well with Jest, Mocha, Vitest, and others."
