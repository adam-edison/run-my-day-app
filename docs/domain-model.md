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

A one-time task can hold one-time subtasks, nested to any depth you need. A recurring task never has subtasks and never sits inside another task.

### Project

A project is one-time work that you create or mark as a project yourself. A one-time task with subtasks stays a plain task until you mark it. A project grows as you learn: you add parts you could not foresee, reorder them, and break them down across as many levels as you need. A project never contains a recurring task.

Subtasks run in their set order. A later subtask waits for the earlier ones without any stored condition. Waiting subtasks stay visible as "up next" and are never hidden.

### Condition

A condition is a check against your history of completions, metric values, and dates. Unlock conditions, habits, goals, and progression levels all use the same condition concept.

### Unlock conditions

Any task, one-time or recurring, can have a list of unlock conditions. Most tasks have none: order covers the common case, and unlock conditions handle exceptions such as a dependency on a task in another project, a date, or a metric. Each task chooses whether it unlocks when all of its conditions are met or when any one of them is met. A locked task stays hidden until it unlocks. A condition can be:

- another task completed a set number of times, including once
- a metric condition
- a start date: the task stays hidden until that date

A condition lives on the thing whose state it changes. A task carries its own unlock conditions. Anything that chooses between tasks belongs to a progression.

Circular conditions are impossible. Run My Day refuses any condition that would make a task wait on itself.

### Progression

A progression is an ordered list of levels, such as "run 1 km, then 3 km, then 5 km". Each level points at a task. Exactly one level is active at a time. Each level has move-up and move-down conditions, and the progression changes levels automatically when they pass. The tasks inside a progression carry no level logic.

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

- **Goal, achievement:** not yet reviewed. Whether habits also have levels.
- **Task fields:** custom status names over fixed status categories, and fixed or custom priority levels. Deferred to the task spec.
