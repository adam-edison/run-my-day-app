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
- **Smaller statechart libraries, such as statecharts.sh and superstate:** Rejected because their adoption is small, and neither documents actors.
- **XState v6:** Deferred because it is in beta, and its APIs may still change. v6 also changes how a transition inside one region of a parallel state behaves.

## Rationale

- **Fewer race conditions:** Each state defines which events it accepts.
- **Testable logic:** Actors run without a screen, so most logic is covered by fast unit tests.
- **Shared by every platform:** The same actors run on Android, iOS, and web.

## Migration to XState v6

A later migration changes only the core package. The protections are:

- **Behavior tests through the core API:** Tests send events and check the resulting state and effects through the core package's own API. The same tests run unchanged before and after the migration.
- **Parallel and nested state tests:** Each parallel actor has tests that check that an event in one region leaves the other regions unchanged. Model-based tests generate a path through every state.
- **Recorded behavior traces:** Stately's baseline tool records each machine's behavior under v5. Its verify tool replays the traces under v6 and reports any difference.
- **One version installed:** XState has no supported way to install v5 and v6 together. The core package moves from v5 to v6 in one step, and only v6 is installed afterward.
- **Saved state:** v6 cannot read state saved by v5. Before the migration, real v5 snapshots are saved to test fixture files. After the migration, a test converts each fixture with `machineVersions`, restores it, and checks that the day continues from where it stopped.

## Consequences

- Each developer needs to learn statecharts and XState.
- Effects such as audio playback, storage, and network calls are carried out by the platform shell, outside the actors.
- The core package's API is maintained as a separate layer over XState.

## Sources

### [Frequently asked questions (XState v6)](https://stately.ai/docs/xstate/v6/faq)

Retrieved 2026-11-27.

- Fact: XState v6 is in beta.
  > "Not yet. v6 is in beta and is published under the `beta` dist-tag, so `npm install xstate@beta` gets the current release."
- Fact: v6 APIs may still change.
  > "APIs may still change before the stable release."
- Fact: XState v5 is the current stable version.
  > "XState v5 is still supported; use it if you need a stable version today"
- Fact: XState has no supported way to install v5 and v6 together.
  > "There is no supported dual-install story."
- Fact: v6 cannot read state saved by v5.
  > "The internal snapshot shape changed and v5 persisted snapshots are not binary-compatible with v6"

### [Migrate from XState v5 to v6](https://stately.ai/docs/xstate/v6/xstate-v5-to-v6)

Retrieved 2026-11-27.

- Fact: XState v6 is in beta.
  > "XState v6 is currently in beta (`npm install xstate@beta`)."
- Fact: v6 changes how a transition inside one region of a parallel state behaves.
  > "A transition whose targets stay inside one region of a parallel state exits and reenters that region only."
- Fact: Saved v5 state is converted with `machineVersions`.
  > "v6 does not read that shape, so convert those entries with"
- Fact: Machines move one at a time, and the tests run after each move.
  > "Upgrade one machine at a time and run its tests after each change."
- Fact: Stately's baseline and verify tools compare a migrated machine with the v5 original.
  > "`baseline` and `verify` check that a migrated machine behaves like the v5 original."

### [Migrate from XState v5 to v6 (prod.stately.ai)](https://prod.stately.ai/docs/xstate/v6/xstate-v5-to-v6)

Retrieved 2026-11-27.

- Fact: The baseline tool records each machine's behavior under v5.
  > "`baseline` loads the v5 module under XState v5, generates event sequences, and records a trace of each one."
- Fact: The verify tool replays the traces under v6 and reports any difference.
  > "`verify` loads the migrated module under XState v6, replays the same sequences, and compares the traces step by step."

### [Graph & Paths](https://stately.ai/docs/graph)

Retrieved 2026-11-27.

- Fact: Model-based tests generate a path through every state.
  > "Returns the shortest path from the initial state to every reachable state."
  > "Path generation enables model-based testing - generating test cases directly from your state machine."

### [Statecharts.sh](https://statecharts.sh)

Retrieved 2026-11-27.

- Fact: statecharts.sh is a small statechart library. Its page does not mention actors.
  > "An open state chart specification and library."

### [npm downloads API: statecharts.sh](https://api.npmjs.org/downloads/point/last-month/statecharts.sh)

Retrieved 2026-11-27.

- Fact: statecharts.sh adoption is small.
  > "{"downloads":16,"start":"2026-10-28","end":"2026-11-26","package":"statecharts.sh"}"

### [GitHub - kossnocorp/superstate: Type-safe JavaScript statecharts library](https://github.com/kossnocorp/superstate)

Retrieved 2026-11-27.

- Fact: superstate is a statechart library. Its README does not mention actors.
  > "Type-safe JavaScript statecharts library"

### [npm downloads API: superstate](https://api.npmjs.org/downloads/point/last-month/superstate)

Retrieved 2026-11-27.

- Fact: superstate adoption is small.
  > "{"downloads":88,"start":"2026-10-28","end":"2026-11-26","package":"superstate"}"
