# Architecture Principles

## Single source of truth

Each piece of information is defined once. Every other place refers to that definition. Near-copies are not allowed.

## Zero-trust validation

Data is validated at every boundary with a zero-trust policy.

## Database integrity

The database enforces data integrity.

## Static enforcement

Architecture patterns are enforced by static code analysis.

## Generated documentation

Documentation is generated from code where possible.

## Fast development and testing

Development and testing are both fast.

## Minimal tooling

A tool or abstraction is added only when a current need requires it.

## Discoverable code

The codebase is organized so that the code for any behavior is easy to find.

## Human review

Every line of code and documentation is reviewed by a human, with no exceptions.

## One codebase

One codebase serves Android, iOS, and web, with as close to feature parity as possible.

## Event-driven client state

Client state changes only in response to events. Components only display state.
