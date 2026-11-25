# Domain Model

The task is the center of the domain. Blocks, routines, task views, and progressions arrange tasks. Steps inside tasks record metrics. Habits measure metrics after the fact.

## Overview

### How work reaches your day

```mermaid
flowchart LR
    Calendar["Calendar<br/>(read-only)"] -->|title match| Block
    Block -->|holds, in order| Routine
    Block -->|holds, in order| TaskView["Task view"]
    Routine -->|entry, in order| Task
    Routine -->|entry, in order| Progression
    TaskView -->|picks| Task
```

You can also run any routine or task on demand, without the calendar.

### Inside a progression

```mermaid
flowchart LR
    Progression -->|one active at a time| Level["Level<br/>(a full plan)"]
    Level -->|contains| Session
    Level -->|schedules by| Rotation
    Session -->|ordered, at least one| Step
```

### Inside a project and a task

```mermaid
flowchart LR
    Project -->|ordered, one-time only| Task
    Task -->|ordered, at least one| Step
```

### Metrics and habits

```mermaid
flowchart LR
    Step -->|records| Metric
    FreeEntry["Free entry"] -->|records| Metric
    Habit -->|measures| Metric
```

### What conditions drive

```mermaid
flowchart LR
    Condition["Condition<br/>(checks history)"] -->|unlocks| Task
    Condition -->|moves level up / down| Progression
    Condition -->|moves level up / down| Habit
    Condition -->|completes| Goal
```

Modes: guided or manual.

## Settled

### Calendar

The calendar holds your schedule. Run My Day reads it and never changes it.

### Block

A block attaches work to the calendar from the top down. A calendar event starts the block whose title matches the event title. A block holds an ordered list of routines and task views. A block never holds a task directly.

### Routine

A routine is an ordered list of entries that repeats. Each entry is a task or a progression. A routine holds no steps of its own.

### Task view

A task view is a saved filter and sort over tasks. It is the only way a task outside a routine reaches a block. A filter can include or exclude tasks that belong to a routine.

### Task

A task is the center of the domain. A task holds an ordered list of steps and always has at least one step. A task's estimate is the sum of its steps' estimates. A recurring task comes back on its own schedule.

A recurring task is one task whose due date moves forward each time you complete or skip it. A missed recurring task stays overdue until you complete it, skip it, or move its due date yourself.

A recurring task's repeat builds on TickTick's repeat model. The [task settings guide](task-settings-guide.md) explains each setting with examples.

A repeat has one pattern kind:

- **Every N days, weeks, months, or years**, repeating by due date or by completion date.
- **Specific dates**, repeating by due date.

A repeat by due date sets the next due date from the old due date. A repeat by completion date sets it from when you finished.

A repeat by due date has an "if missed" setting. **Jump to today**, the default, moves the due date to the next pattern date after today. **Every date is owed** moves it to the next pattern date after the old due date.

A repeat ends never, on a date, or after a number of repeats.

Tasks have no subtasks. To break work down, you split a task into several tasks in the same place.

### Project

A project is an ordered list of one-time tasks that you create as a project yourself. A project grows as you learn: you add tasks you could not foresee, reorder them, and split tasks into smaller tasks. A project never contains a recurring task. The depth is always project, then task, then step.

A project's tasks run in their set order. A later task waits for the earlier ones without any stored condition. Waiting tasks stay visible as "up next" and are never hidden. A project's estimate is the sum of its tasks' estimates.

### History

History is the record of every action taken in Run My Day, such as completing a task, skipping a task, moving a due date, or logging a metric value. History belongs to no single entity.

### Condition

A condition is a check against your history of completions, metric values, and dates. Unlock conditions, habits, goals, and progression levels all use the same condition concept.

### Unlock conditions

Any task, one-time or recurring, can have a list of unlock conditions. Most tasks have none: order covers the common case, and unlock conditions handle exceptions such as a dependency on a task in another project, a date, or a metric. Each task chooses whether it unlocks when all of its conditions are met or when any one of them is met. A locked task stays hidden until it unlocks. A condition can be:

- another task completed a set number of times, including once
- a metric condition
- a start date: the task stays hidden until that date

A condition lives on the thing whose state it changes. A task carries its own unlock conditions. Anything that chooses between tasks belongs to a progression.

Conditions can never be circular: no task can wait on itself.

### Progression

A progression is an ordered list of levels. Each level is a whole training plan: a set of sessions and one rotation for them, over any span of days or weeks. For example, a push-up progression starts at 10 push-ups twice a week. It moves to four times a week, then to changing sets and reps, then to a two-week rotation of push-up types such as handstand push-ups one day and triangle push-ups another. Each level is a full plan on its own and lists all of its sessions and its rotation. Exactly one level is active at a time.

### Session

A session is a planned bout of practice inside a progression level, such as "Tree pose" or "Wide push-ups". A session has a title, steps, and an estimate, and its steps record metrics, the same as a task. A session is not a task: it has no repeat, no due date, no unlock conditions, and no place in a project or a task view. The level decides when a session is due. You can also run a session on demand.

A session belongs to exactly one level of one progression. When two levels or two progressions use the same exercise, each has its own session with its own history. Progress across them is tracked through a shared metric, such as "Tree hold, seconds".

### Rotation

A level's schedule is a rotation: an ordered list of slots, where each slot plays one of the level's sessions or is a rest day. For example, a 14-day rotation of standing postures with 2 rest days. Rotations exist only on progression levels.

A rotation starts at its first slot on the day its level becomes active. A rotation waits for you: a missed slot stays due until you do it, and every later slot shifts by a day. A rest slot passes when a day passes. A skip on a session means "not today": the rotation stays on the same slot, and history records the skip.

### Levels in your day

- The active level's sessions come due when its rotation says, and their completions count toward the progression's conditions.
- Inactive levels' sessions are hidden everywhere except the progression editor. A move back down to a level makes that level's same sessions active again.

Blocks offer chances, and schedules decide whether something plays at each chance. Blocks reserve time, and schedules decide what to do.

A progression reaches your day only through a routine. A routine entry can be a progression. That entry plays the active level's session due that day and is skipped on a rest slot. The routine itself does not change when the level changes.

Each level has move-up and move-down conditions, and the progression changes levels automatically when they pass. Sessions carry no level logic.

A move up waits while the new level would not fit in the blocks that run it. The move happens once the blocks have room.

### Step

A step is a small unit of work inside a task or a session. Steps run in order. Every step has an estimate. A step can record a metric value.

### Metric

A metric is a measured value. A step can record a metric value. You can also add, edit, or remove metric entries directly.

### Habit

A habit measures unplanned behavior after the fact. You log it when it happens, such as logging 24 oz each time you finish a water bottle. A habit's metric values come mostly from free entries, and sometimes from steps in tasks. A habit creates no tasks. A habit updates automatically from metric values. You never tick off a habit directly.

A habit can nudge you during the day based on how you are doing against its target, such as "30 of 60 oz, and it's 3 pm". Each habit sets how often it nudges, including never.

A habit differs from a progression in when the doing happens. A habit tracks behavior that life triggers. A progression plans scheduled work as tasks.

A habit can have levels. Each level sets a higher target, and conditions move the habit up or down automatically. For example, a water habit starts at 60 oz a day. After 7 days in a row at or above the target, it moves to 70 oz, then 80 oz, then 100 oz, which is the top level. A habit stays at its top level once it gets there.

A habit measures its target over a sliding window of N days, weeks, months, or years. For example, "gym 3 times in any 7 days".

Each day passes or misses, based on the window that ends that day. The streak is the number of passing days in a row. The percentages are the share of passing days.

A habit shows:

- its current streak
- the percentage of successful days this week, this month, and this year

A level change never breaks a streak. Any day the target is not met breaks the streak.

A habit never finishes. Archiving a habit stops tracking. Unarchiving it shows its full history again, because Run My Day computes that history from the metric values.

### Goal

A goal is reached once and is then done. It completes the first time its conditions pass. A deadline is optional: some goals are for a lifetime. A goal with a deadline shows its pace: ahead, on track, or behind.

### Modes

- **Guided mode:** the clock and your rules advance the day. Guidance comes through your choice of voice, sound cues, music cues, vibration, notifications, or silence.
- **Manual mode:** nothing advances by itself. You start and finish everything. Manual mode is also the way to take back control when the system gets something wrong.

You can start any routine or task on demand, outside the calendar, in either mode. Run My Day works without a blocks calendar.

### Transitions

Work runs in small steps, so nothing gets cut off midway. Transition time between steps lets you catch your breath. Before something ends, Run My Day tells you what comes next and asks you to wrap up and leave the work in a good state. Run My Day encourages this practice and never enforces it.

## Captured for later

These behaviors belong in OpenSpec specs once those exist.

- When a move up waits, Run My Day tells you why, such as "Push-ups is ready for L3, but the Workout block is 10 minutes short".
- A new task gets one step with the task's title and estimate, so a simple task needs only a title and an estimate.- When Run My Day refuses a setting, such as a circular condition, it shows a plain message that explains how to fix it.

## Open


- **Achievement:** not yet reviewed.
- **Waiting level-up:** how to make it easy to give a waiting level-up room. To be designed later.
- **Gap check:** how Run My Day finds tasks that need doing but have no place to run, and how far ahead it looks. Warnings about calendar availability belong here, not in schedules.
- **Task fields:** custom status names over fixed status categories, and fixed or custom priority levels. Deferred to the task spec.
