# DR-0023: rrule-temporal for Recurrence

**Status:** Accepted  
**Date:** 2026-11-28

## Context

Run My Day computes the next due date of a recurring task. The same code must give the same answer on the phone, on the web, and on the server, while offline.

The technology stack lists rrule for recurring tasks, events, and schedules. No decision record has chosen it yet.

The domain model defines the repeat rules:

* A pattern is "every N days, weeks, months, or years" or "specific dates".
* A repeat moves forward by due date or by completion date.
* A repeat by due date has an "if missed" setting. The default jumps to today. The other choice makes every date owed.
* A repeat ends never, on a date, or after a number of repeats.
* A due date is a calendar date with an optional clock time.
* A "day starts at" setting, 4:00 by default, decides which date counts as today.
* Every day rule uses the time zone stored in the user's settings.

The domain logic lives in a shared core package. The core package runs on the devices and on the server, as recorded in DR-0005, DR-0016, and DR-0017.

Moments, such as a completion time, are stored in UTC. A rule in local clock time, such as "every day at 07:00," needs the user's time zone. UTC does not follow daylight saving time, and the user's clock does.

React Native runs on the Hermes engine. Hermes on iOS rejects some time zone aliases, such as "US/Eastern," and treats an alias and its canonical name, such as "America/New_York," as different zones. `temporal-polyfill` fails on those aliases. Node resolves aliases correctly.

RFC 5545 is the iCalendar standard for recurrence rules. Google Calendar and other calendars use it. Temporal is the new JavaScript date API with real time zone and plain-date types.

Example: "Water plants" repeats every 3 days by completion date. It is completed at 23:30 on Monday. The next due date is Thursday, on the phone and on the server alike.

## Decision

* rrule-temporal expands repeat patterns in the core package.
* Patterns include calendar-style patterns, such as "the 2nd Tuesday" or "the last business day of each month."
* The core package adds the repeat options that RFC 5545 does not define: repeating by completion date, the "if missed" setting, and specific dates.
* The "day starts at" setting and the time zone decide which date is today.
* The core package converts each date that rrule-temporal returns to a calendar date.
* The user's time zone is stored only as its canonical IANA identifier, in IANA's exact case, such as "America/New_York."
* The server converts any alias to its canonical identifier before the time zone reaches a device.
* Device tests on iOS and Android cover every supported time zone, including "Asia/Kolkata."

Example: "Team report" repeats on the 2nd Tuesday of each month, by due date, with "if missed" set to "jump to today." rrule-temporal lists the 2nd Tuesdays. The report due on Tuesday is missed, and on Thursday the core package picks the next 2nd Tuesday after today.

## Alternatives Considered

rrule (jkbrzt/rrule), rejected:

* The last npm release is 2.8.1, published 2023-11-10.
* It works with JavaScript `Date` objects that must be read as "UTC dates meant as local time". This makes time zone handling easy to get wrong.

Hand-written patterns on Temporal plain dates only, rejected:

* Calendar-style patterns, such as "the last Friday of each month," would be written and tested by hand.

## Rationale

* rrule-temporal reduces the code written for repeat patterns.
* It claims full RFC 5545 compliance.
* It is built on Temporal, with time zone aware results.
* It is maintained. Version 2.3.0 was published 2026-11-18.
* Every identifier that failed on Hermes in the bug report is accepted in its exact canonical form. Canonical identifiers avoid the failure.
* Node resolves aliases correctly, so the server can convert them.

## Consequences

* The core package still contains the code for repeating by completion date, the "if missed" setting, and specific dates.
* rrule-temporal is maintained by one person.
* rrule-temporal uses native Temporal when present and otherwise bundles `temporal-polyfill`.
* The bugs in `temporal-polyfill` and Hermes are open, with no fix from the maintainers. A time zone picker that offers an alias would break repeats on iOS.

## Assumptions

* `temporal-polyfill` works on Hermes when it receives only canonical identifiers. The device tests check this.
* "Asia/Kolkata" failed in the bug report, although IANA treats it as canonical. Apple's list may hold "Asia/Calcutta" as the primary name. The device tests check this.

## Open Questions

1. Should a repeat be importable from or exportable to a calendar as an RRULE string? Example: copying a Google Calendar repeating event into a task.

## Sources

### [Zone identifiers fail on React Native's Hermes | fullcalendar/temporal-polyfill issue 133](https://github.com/fullcalendar/temporal-polyfill/issues/133)

Retrieved 2026-11-28.

- Fact: On Hermes, `temporal-polyfill` 1.0.8 throws for some time zone identifiers, and a zone never equals its alias.
  > "On React Native's Hermes engine, some zone identifiers throw and a zone never equals its alias. On Node 22 with the same version neither happens."
- Fact: On iOS, 21 of 439 tried identifiers throw. All 21 are IANA links.
  > "The same happens for 21 of 439 identifiers tried, all IANA links"
- Fact: Every identifier that failed is accepted in its exact case.
  > "In their exact case all 21 are accepted."

### [Intl.DateTimeFormat on iOS matches some timeZone identifiers case-sensitively, and does not canonicalize links | Hermes issue 2323](https://github.com/meta/hermes/issues/2323)

Retrieved 2026-11-28.

- Fact: Hermes on iOS treats an alias and its canonical name as different zones.
  > "Intl.DateTimeFormat on iOS matches some timeZone identifiers case-sensitively, and does not canonicalize links"

### [jkbrzt/rrule on GitHub](https://github.com/jkbrzt/rrule)

Retrieved 2026-11-27.

- Fact: The README tells users to treat returned dates as local time.
  > "THE BOTTOM LINE: Returned "UTC" dates are always meant to be interpreted as dates in your local timezone."
- Fact: The README tells users to pass only UTC dates.
  > "make sure to only use JS `Date` objects that are represented in UTC"
- Fact: The repository shows 188 issues.
  > "Issues188 (188)"

### [rrule on npm (npm view output)](https://www.npmjs.com/package/rrule)

Retrieved 2026-11-27.

- Fact: The latest version is 2.8.1, last modified 2023-11-10.
  > "time.modified = '2023-11-10T20:05:55.900Z' version = '2.8.1'"
- Fact: It ships TypeScript types.
  > "types = 'dist/esm/index.d.ts'"

### [ggaabe/rrule-temporal on GitHub](https://github.com/ggaabe/rrule-temporal)

Retrieved 2026-11-27.

- Fact: It claims full compliance and uses Temporal.
  > "The first and only fully compliant Recurrence rule processing JS/TS library built on the Temporal API."
- Fact: It returns zoned date-times.
  > "returns `Temporal.ZonedDateTime` instances for easy time‑zone aware scheduling."
- Fact: It skips dates that do not exist.
  > "Following RFC 5545, generated dates that do not exist are omitted and do not consume `COUNT`."

### [rrule-temporal on npm (npm view output)](https://www.npmjs.com/package/rrule-temporal)

Retrieved 2026-11-27.

- Fact: Version 2.3.0 was published 2026-11-18.
  > "time.modified = '2026-11-18T19:01:25.436Z' version = '2.3.0'"

### [RFC 5545, section 3.3.10 Recurrence Rule](https://www.rfc-editor.org/rfc/rfc5545#section-3.3.10)

Retrieved 2026-11-27.

- Fact: RFC 5545 defines a value type for recurrence rules.
  > "This value type is used to identify properties that contain a recurrence rule specification."
- Fact: A rule ends after a count or on a date, never both.
  > "The UNTIL or COUNT rule parts are OPTIONAL, but they MUST NOT occur in the same 'recur'."
