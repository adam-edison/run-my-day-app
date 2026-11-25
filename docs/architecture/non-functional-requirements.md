# Non-Functional Requirements

These constraints shape the architecture. Each one gets worked through when the architectural principles are planned.

## Performance

- **History volume.** History records every action, including every edit to a definition, for every user every day. Its growth must not slow down Run My Day. Reads such as streaks, conditions, skip counts, and learned estimates scan history, so storage and queries need a design that keeps them fast as history grows.
