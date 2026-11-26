# Non-Functional Requirements

These constraints shape the architecture. Each one gets worked through when the architectural principles are planned.

## Performance

- **Announcement timing.** The first sound of each step plays within 100 milliseconds of the moment the step is due to start. Timing is metronomic: every step boundary in a run keeps the same accuracy, with no drift that builds up over a run. Example: a posture held for 4 seconds, looped, starts each loop on the 4-second mark. The first sound may be a cue sound or an announcement, depending on the step's settings. An earlier app, Timer Machine, sometimes played an announcement late, so steps that should each last 4 seconds came out uneven.

- **History volume.** History records every action, including every edit to a definition, for every user every day. Its growth must not slow down Run My Day. Reads such as streaks, conditions, skip counts, and learned estimates scan history, so storage and queries need a design that keeps them fast as history grows.
