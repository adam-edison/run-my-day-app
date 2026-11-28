# DR-0023: Temporal Plain Dates for Recurrence

**Status:** Draft  
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

RFC 5545 is the iCalendar standard for recurrence rules. Google Calendar and other calendars use it. Temporal is the new JavaScript date API with real time zone and plain-date types.

Example: "Water plants" repeats every 3 days by completion date. It is completed at 23:30 on Monday. The next due date is Thursday, on the phone and on the server alike.

## Decision

* The core package stores a repeat as the fields the domain model defines.
* The core package computes the next due date with Temporal `PlainDate` arithmetic.
* The "day starts at" setting and the time zone decide only which date is today.
* The next date itself is plain-date arithmetic.
* The supported patterns are every N units, chosen weekdays, a day of the month, and specific dates.
* If calendar-style patterns or iCalendar import are needed, rrule-temporal expands the patterns. The by-completion and if-missed rules stay in the core package.

Example: "Water plants" is due Thursday. It is missed, and the "if missed" setting is "jump to today". On Saturday the core package computes the next due date from Saturday's plain date.

## Alternatives Considered

rrule (jkbrzt/rrule), rejected:

* The last npm release is 2.8.1, published 2023-11-10.
* The README states the project is in maintenance mode, with no new features planned.
* The repository shows 188 issues.
* It works with JavaScript `Date` objects that must be read as "UTC dates meant as local time". This makes time zone handling easy to get wrong.

rrule-temporal for every pattern, rejected for the patterns in the domain model today:

* The domain model's rules for by-completion date, if missed, and specific dates are not RFC 5545 concepts. Code for them is written in every option.
* It returns `Temporal.ZonedDateTime` values, and due dates are calendar dates.
* It is maintained by one person.
* Running a Temporal polyfill inside React Native (Hermes engine) was not checked.

## Rationale

* Plain dates have no time zone. The device and the server give the same answer by construction.
* The code for the domain model's patterns is small.
* rrule is unmaintained.
* rrule-temporal stays available for richer patterns. It claims full RFC 5545 compliance. Version 2.3.0 was published 2026-11-18.

## Consequences

* Rules like "the last Friday of each month" must be written and tested by hand.
* No standard text format exists for import or export.
* No official source states that hand-written plain-date code is simpler than a library for these patterns.
* No official source states that plain dates give the same answer on every device for this case.
* No official source states that the domain model's repeat rules fall outside RFC 5545.
* Running a Temporal polyfill inside React Native (Hermes engine) is not checked.

## Open Questions

1. Do repeats need patterns beyond every N units and chosen weekdays? Example: "Pay credit card on the last business day of each month" or "Team report on the 2nd Tuesday".
2. Should a repeat be importable from or exportable to a calendar as an RRULE string? Example: copying a Google Calendar repeating event into a task.
3. Is a Temporal polyfill in the shared core acceptable on Android, iOS, and web? Example: every due-date calculation in the phone app would go through it.

## Sources

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
