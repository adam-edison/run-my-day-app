# Non-Functional Requirements

These constraints shape the architecture. Each one gets worked through when the architectural principles are planned.

## Performance

- **Announcement timing.** The first sound of each step plays within 100 milliseconds of the moment the step is due to start. Timing is metronomic: every step boundary in a run keeps the same accuracy, with no drift that builds up over a run. Example: a posture held for 4 seconds, looped, starts each loop on the 4-second mark. The first sound may be a cue sound or an announcement, depending on the step's settings. An earlier app, Timer Machine, sometimes played an announcement late, so steps that should each last 4 seconds came out uneven.

- **Calendar change latency.** Run My Day reacts to a calendar change within 1 minute of the edit. Example: at 10:05 you move the Workout event's end from 10:00 to 10:30, and the Workout block runs again by 10:06.

- **Push latency.** A notice from the server must reach an open client within 2 seconds. Example: at 10:20 the laptop takes over "Morning," and the phone must stop running it by 10:20:02.

- **History volume.** History records every action, including every edit to a definition, for every user every day. Its growth must not slow down Run My Day. Reads such as streaks, conditions, skip counts, and learned estimates scan history, so storage and queries need a design that keeps them fast as history grows.

## Offline

- **Offline use.** Run My Day works fully offline on every platform. Changes made offline sync when the connection returns. Example: on a hike with no signal, you complete a task on the phone, and the web app shows it complete once the phone reconnects.
