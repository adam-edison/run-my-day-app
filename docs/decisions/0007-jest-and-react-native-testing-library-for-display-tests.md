# DR-0007: Jest and React Native Testing Library for Display Tests

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Components only display state. Display tests check that each component shows the right content for a given state and sends the right event when it is used. The components are React Native components, which Expo renders on Android, iOS, and web. Expo documents only Jest with jest-expo for testing them.

## Decision

Display tests run in Jest with the default jest-expo preset. Components are rendered and queried with React Native Testing Library. Each shared component is tested once, because its code is the same on every platform. Each test renders a component from a fixed state and checks what a user sees or does: text, roles, and the events sent on a press.

Display tests run on every commit, with the unit tests.

## Alternatives Considered

- **Vitest with vitest-native:** Rejected because Expo documents only Jest with jest-expo, and Vitest support for Expo comes only from a community plugin that has not reached version 1.0.
- **React Testing Library:** Rejected because it renders to the browser DOM, and the components are React Native components.

## Rationale

- **Official support:** jest-expo mocks the native part of the Expo SDK and handles most of the setup.
- **Tests match use:** Components are queried the way a user finds them, by text and role.
- **Thin components:** Components only display state, so display tests need no client state actors.

## Consequences

- Two test runners are maintained: Vitest and Jest.
- Code for one platform, such as `Player.android.tsx`, is not covered by display tests. Maestro covers it on Android and iOS, and Playwright covers it on the web.

## Sources

### [Unit testing with Jest - Expo Documentation](https://docs.expo.dev/develop/unit-testing/)

Retrieved 2026-11-27.

- Fact: Expo documents only Jest with jest-expo for testing components. The page does not mention Vitest.
  > "Jest is the most widely used unit and snapshot JavaScript testing framework."
- Fact: jest-expo mocks the native part of the Expo SDK and handles most of the setup.
  > "which is a Jest preset that mocks the native part of the Expo SDK"
  > "handles most of the configuration required for your Expo project."
- Fact: Expo documents React Native Testing Library for rendering and querying components.
  > "a lightweight solution for testing React Native components."

### [vitest-native — Test React Native as it ships](https://vitest-native.danfry.dev/)

Retrieved 2026-11-27.

- Fact: Vitest support for Expo comes only from a community plugin that has not reached version 1.0.
  > "It's pre-1.0. Some options may still change before 1.0."
  > "Maintained successor to"

### [React Testing Library | Testing Library](https://testing-library.com/docs/react-testing-library/intro/)

Retrieved 2026-11-27.

- Fact: React Testing Library renders to the browser DOM.
  > "React Testing Library builds on top of DOM Testing Library by adding APIs for working with React components."
  > "So rather than dealing with instances of rendered React components, your tests will work with actual DOM nodes."

### [Queries - React Native Testing Library](https://oss.callstack.com/react-native-testing-library/docs/api/queries)

Retrieved 2026-11-27.

- Fact: React Native Testing Library queries components by role and by text.
  > "Returns a `TestInstance` with matching `role` or `accessibilityRole` prop."
  > "Returns a `TestInstance` with matching text – may be a string or regular expression."
