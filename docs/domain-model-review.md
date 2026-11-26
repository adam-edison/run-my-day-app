# Domain Model Review

Every item left to decide before the domain model is complete. Each item comes from comparing the domain model with the earlier prototype spec and the kivaro tickets. Remove an item once it is decided and written into the domain model, captured-for-later, or dropped.

Sources:

- **F** = `~/repositories/_archived/prototype-run-my-day-deprecated/bdd/FEATURES.md`, with line number
- **D** = the prototype's `bdd/DEFAULTS.md`
- **KIV** = a ticket in `~/repositories/kivaro/backlog/tasks/`
- **DM** = `docs/domain-model.md`

Kinds:

- **CONFLICT**: a source says something different from the domain model
- **MISSING**: a source settled a rule that the domain model does not have
- **OPEN**: no source settles it

## 1. How steps advance and when a task completes

- [ ] **OPEN: seasonal tasks.** The user stated this case. "Wipe condensation off the windows" repeats daily, but only from October through March. From April through September the task should not appear at all. The task must show what its condition is, and you must be able to set it. DM's unlock conditions are only "another task completed" and "a metric condition". The task start date was removed, so DM has no date-based condition. To decide: how a task can be active only in part of each year, and how it moves between hidden and active, for example between a backlog status and Ready.
- [ ] **OPEN: conditions on steps.** A step that runs only when a condition holds, so a task carries no unnecessary steps. DM has unlock conditions on tasks only.



## 5. Pause


## 6. Nagging, warnings, and announcements


## 7. Prompts and metrics on done





## 12. View limits


## 13. Routine entries that cannot run


## 14. Calendar changes during a run

- [ ] **MISSING: calendar warnings.** F953: announced at the next task boundary, once per new problem, and never blocks anything.
- [ ] **CONFLICT: overlaps.** F1090: no overlaps allowed: two appointments, two blocks, or an appointment and a block. KIV-158.2 #3–4: a block with an overlap does not start until the overlap is fixed. DM: an appointment takes its time out of a block. Block-to-block overlap is not covered.
- [ ] **MISSING: return reminder.** F1146–1148: a vibration every 10 minutes after an appointment ends, until you report that you are back.

## 15. Manual mode and on-demand runs

- [ ] **MISSING: starting a block by hand.** F927–929: "Start Housework Block for 60 minutes". Refused if it overlaps any event.
- [ ] **MISSING: an on-demand run creates a block.** F935–938: its length is the total estimate plus the transition buffer, and every block rule applies.
- [ ] **MISSING: a running routine cannot be edited.** F1182–1183.
- [ ] **OPEN: can an on-demand run overlap a block or an appointment?**

## 16. Day boundary and downtime

- [ ] **MISSING: changing "day starts at".** F1004–1009: applies at once to today's end. A day that has started keeps its start. Past days keep their boundaries. Each start is recorded as a "Day started" event.
- [ ] **MISSING: travel and daylight saving.** F1022–1024: a date is never skipped or repeated. Daylight saving gives 23- and 25-hour days.
- [ ] **MISSING: Sleep event.** F1037–1042: a "Sleep" event silences everything, and a built-in Sleep block exists. May have been dropped.
- [ ] **MISSING: app downtime.** F810–823: while no device runs the day, nothing is announced and nothing goes into history. That time does not count toward the current task. A block that started meanwhile starts when a device runs again. A block whose time ended meanwhile ends when the app stopped. A clock jump over 2 minutes counts as a sleep.
- [ ] **MISSING: one active device.** F805–809: one device runs the day, and "Run here" moves it. May belong to architecture.
- [ ] **MISSING: first day of the week.** KIV-314 #1, #3: weekly windows count from a chosen first day, Monday by default.

## 17. Estimates and history

- [ ] **MISSING: editing and deleting runs.** KIV-162.10 #2–4: editing a Time spent value changes the source run, and a run can be deleted.
- [ ] **MISSING: automatic metrics.** KIV-162.7 #1–3: Time spent and Completions per task, step, or routine, computed from history.
- [ ] **MISSING: what a step record holds.** KIV-164 #1: planned length, real length, and done or skipped.
- [ ] **MISSING: a task done by hand with no recorded start.** F1358: drawn with its estimate, ending at its done time.
- [ ] **MISSING: spacing between steps.** KIV-167 #1–3: a routine can require a minimum and maximum time between two steps. Saving refuses a routine that breaks it. The run does not check it.

## 18. Progressions, habits, and goals

- [ ] **MISSING: when level checks run.** F1425: on finished days only, at the start of the day.
- [ ] **CONFLICT: when a level change takes effect.** KIV-302.1 #12: at the end of the current pass, starting the new level at the first slot. DM: a rotation starts at its first slot on the day its level becomes active.
- [ ] **CONFLICT: marking a slot missed.** KIV-302.1 #8–9: a missed slot advances to the next slot. DM: a skip keeps the rotation on the same slot.
- [ ] **MISSING: wrap-around.** KIV-302.1 #11: after the last slot, the rotation starts again at the first slot.
- [ ] **MISSING: pausing a progression.** KIV-302.1 #13: stops new sessions and the move-down conditions.
- [ ] **MISSING: draft and start date.** KIV-302.1 #5–6: a new progression is a draft. Start asks for a start date.
- [ ] **MISSING: lapse clock.** F1418: after a drop by lapse, the lapse clock restarts at the drop.
- [ ] **MISSING: re-checking a waiting move up.** KIV-302.2 #4, F1428: each day, and on a "Check again" button.
- [ ] **CONFLICT between sources: changing a level by hand.** KIV-162.8 #5: no button to change the level. F34: every field the app changes by itself can also be changed by hand.
- [ ] **MISSING: when celebrations play.** KIV-162.6 #1, KIV-162.8 #4: at the next block start or free time, never during a routine. F1404, F1434: at the next block start.
- [ ] **CONFLICT: streaks.** KIV-162.4 #5: habits do not show or store streaks. DM: a habit shows its current streak. F1451 also has a miss allowance.
- [ ] **MISSING: unfinished periods.** KIV-162.4 #4, F1445: only finished spans count. The current span never counts as a miss.
- [ ] **MISSING: empty periods.** F1446: a finished period with no entries is missed, for any target.
- [ ] **MISSING and CONFLICT between sources: goal pace.** KIV-162.5 #4–6: on track within 2% of the plan line, plus a 30-day projection within 7 days. F1402: no tolerance band. DM says ahead, on track, or behind without a definition.
- [ ] **MISSING: editing a condition.** KIV-162.3 #9: an edit applies from the edit time. Each past span shows the result of the version in force then.

## 19. Recurring tasks and projects

- [ ] **MISSING: more "if missed" options.** KIV-161 #4–7: "Optional: a missed repeat never shows as overdue", and "Make up each" as separate tasks. DM has only Jump to today and Every date is owed.
- [ ] **MISSING: history records every missed repeat.** KIV-161 #8.
- [ ] **MISSING: N times per span.** KIV-161 #10: "repeat N times per span… 1 of 3 this week".
- [ ] **CONFLICT: recurring tasks and unlock conditions.** KIV-50.5 #3: a repeating task cannot block or be blocked. DM: any task, one-time or recurring, can have unlock conditions.
- [ ] **MISSING: project in any order.** KIV-50.4 #7.
- [ ] **MISSING: a project blocked by a project.** KIV-50.5 #1.

## 20. Other

- [ ] **MISSING: QR scan tasks.** F898–912: a scan task completes only on the right QR code. An emergency exit appears at half the estimate. Paused time does not count toward it. May have been dropped.

## 21. Loops

- [ ] **OPEN: does Skip end one loop or all of them?** KIV-51.5.
- [ ] **OPEN: do Done and Extend act per loop?** KIV-51.5.
- [ ] **OPEN: one history record per loop or per step?** KIV-51.5.
- [ ] **OPEN: lowest and highest loop count.** KIV-51.5.
- [ ] **OPEN: warnings in loops.** KIV-436: whether a looped step's warnings play in each loop.

## 22. Remaining list items not yet reviewed

- [ ] **OPEN: guidance channels.** The Guidance section says guidance comes through your choice of voice, sound cues, music cues, vibration, notifications, or silence. Only voice announcements and start sounds are defined. To decide: what music cues, vibration, and notifications do, and how you choose among the channels.
- [ ] **OPEN: custom units for metrics.** The user stated this feature: you can define your own units, such as "bottle of water" = 16 fl oz and "large bottle of water" = 20 fl oz. A value entered in a custom unit converts to the metric's unit, here fluid ounces, when it is recorded. To decide: where custom units live, which metrics can use them, and what history keeps.
- [ ] Timers (KIV-49)
- [ ] Background music and playlists in routines (KIV-169, KIV-36)
- [ ] Routine run history (KIV-164)
- [ ] Summary or plan for any span (KIV-162.9)
- [ ] Metric target conditions: window, aggregation, and condition (KIV-162.3)
- [ ] Comments: can a comment be edited or deleted? (KIV-50.14)
