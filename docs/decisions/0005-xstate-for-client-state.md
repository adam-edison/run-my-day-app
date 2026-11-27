# DR-0005: XState for Client State

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Client state changes only in response to events. Inputs arrive from many sources at once: user actions, audio events, timer ticks, server messages, and online and offline changes. Race conditions between these inputs must be prevented.

## Decision

Client state is held in XState v5 actors, the current stable version. Each actor is a statechart with nested and parallel states. Actors communicate only through events. An event that is not allowed in the current state is ignored.

The actors are Day, Block run, Task run, Waits, Announcer, and Sync. Each actor's module is built around one type. The actors are kept in a shared core package used by every platform. Components only display state.

Only the core package imports XState. Screens and the platform shell read state and send events through the core package's own API. A static boundary check rejects any other import of XState.

## Alternatives Considered

- **Redux Toolkit, Zustand, or a hand-written reducer:** Rejected because states, allowed events, nested states, parallel states, and timers would be built by hand.
- **Smaller statechart libraries, such as statecharts.sh and superstate:** Rejected because their adoption is small, and their support for actors and parallel states is not confirmed.
- **XState v6:** Deferred because it is in beta, and its APIs may still change, including parallel state behavior.

## Rationale

- **Fewer race conditions:** Each state defines which events it accepts.
- **Testable logic:** Actors run without a screen, so most logic is covered by fast unit tests.
- **Shared by every platform:** The same actors run on Android, iOS, and web.

## Migration to XState v6

A later migration changes only the core package. The protections are:

- **Behavior tests through the core API:** Tests send events and check the resulting state and effects through the core package's own API. The same tests run unchanged before and after the migration.
- **Parallel and nested state tests:** Each parallel actor has tests that check that an event in one region leaves the other regions unchanged. Model-based tests generate a path through every state.
- **Recorded behavior traces:** Stately's baseline tool records each machine's behavior under v5. Its verify tool replays the traces under v6 and reports any difference.
- **One machine at a time:** v5 and v6 are installed side by side. Machines move one at a time, and the tests run after each move.
- **Saved state:** v6 cannot read state saved by v5. A test restores a real saved v5 snapshot through the conversion and checks that the day continues from where it stopped.

## Consequences

- Each developer needs to learn statecharts and XState.
- Effects such as audio playback, storage, and network calls are carried out by the platform shell, outside the actors.
- The core package's API is maintained as a separate layer over XState.
