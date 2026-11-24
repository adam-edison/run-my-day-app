# Domain Model

The task is the center of the domain. Blocks, routines, and task views arrange tasks. Steps inside tasks record metrics. Habits apply rules to metrics.

```
 CALENDAR (read-only) --title match--> BLOCK
                                         | holds, in order
                         +---------------+---------------+
                         v                               v
                      ROUTINE                        TASK VIEW
                      ordered tasks, repeats         filter + sort,
                         |                           e.g. "not in a
                         v                           routine"
                       TASK  <---------------------------+
                         | ordered steps
                         v
                       STEP --records--> METRIC <-- free entries
                                           |
                                         HABIT (rules, automatic)

 MODES: guided | manual.  Run anything on demand, calendar or not.
```

## Settled

### Calendar

The calendar holds your schedule. Run My Day reads it and never changes it.

### Block

A block attaches work to the calendar from the top down. A calendar event starts the block whose title matches the event title. A block holds an ordered list of routines and task views. A block never holds a task directly.

### Routine

A routine is an ordered list of tasks that repeats. A routine holds no steps of its own.

### Task view

A task view is a saved filter and sort over tasks. It is the only way a task outside a routine reaches a block. A filter can include or exclude tasks that belong to a routine.

### Task

A task is the center of the domain. A task holds an ordered list of steps. A task with no steps runs as a single step. A recurring task comes back on its own schedule.

### Step

A step is a small unit of work inside a task. Steps run in order. A step can record a metric value.

### Metric

A metric is a measured value. A step can record a metric value. You can also add, edit, or remove metric entries directly.

### Habit

A habit is a set of rules over metrics. A habit updates automatically from metric values. You never tick off a habit directly.

### Modes

- **Guided mode:** the clock and your rules advance the day. Guidance comes through your choice of voice, sound cues, music cues, vibration, notifications, or silence.
- **Manual mode:** nothing advances by itself. You start and finish everything. Manual mode is also the way to take back control when the system gets something wrong.

You can start any routine or task on demand, outside the calendar, in either mode. Run My Day works without a blocks calendar.

### Transitions

Work runs in small steps, so nothing gets cut off midway. Transition time between steps lets you catch your breath. Before something ends, Run My Day tells you what comes next and asks you to wrap up and leave the work in a good state. Run My Day encourages this practice and never enforces it.

## Open

- **Project:** one-time ordered tasks. Open question: a flat list, or projects inside projects.
- **Goal, achievement, progression, level:** not yet reviewed.
- **Task fields:** custom status names over fixed status categories, and fixed or custom priority levels. Deferred to the task spec.
