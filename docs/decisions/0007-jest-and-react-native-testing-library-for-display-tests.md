# DR-0007: Jest and React Native Testing Library for Display Tests

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Components only display state. Display tests check that each component shows the right content for a given state and sends the right event when it is used. The components are React Native components, which Expo renders on Android, iOS, and web. Expo documents only Jest with jest-expo for testing them.

## Decision

Display tests run in Jest with the default jest-expo preset. Components are rendered and queried with React Native Testing Library. Each shared component is tested once, because its code is the same on every platform. Each test renders a component from a fixed state and checks what a user sees or does: text, roles, and the events sent on a press.

Display tests run on every commit, with the unit tests.

## Alternatives Considered

- **Vitest with vitest-native:** Rejected because Expo documents only Jest with jest-expo, and Vitest support for Expo comes only from new community plugins.
- **React Testing Library:** Rejected because it renders to the browser DOM, and the components are React Native components.

## Rationale

- **Official support:** jest-expo mocks the native part of the Expo SDK and handles most of the setup.
- **Tests match use:** Components are queried the way a user finds them, by text and role.
- **Thin components:** Components only display state, so display tests need no client state actors.

## Consequences

- Two test runners are maintained: Vitest and Jest.
- Code for one platform, such as `Player.android.tsx`, is not covered by display tests. Maestro covers it on Android and iOS, and Playwright covers it on the web.
