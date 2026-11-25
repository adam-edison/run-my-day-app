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

- [ ] **OPEN: conditions on steps.** A step that runs only when a condition holds, so a task carries no unnecessary steps. DM has unlock conditions on tasks only.

## 2. Extend

- [ ] **MISSING: Extend and nagging.** F587, F589: at the end of extended time, "Time is up for <task>." plays, whether nagging is on or off. Nags stop until the extended time is up.

## 3. Skip

- [ ] **MISSING: skip on a one-time task or a task with no due date.** F525: "the task becomes unfinished." F18–24: an unfinished task is "treated as due on the day it is first put off, so from the next day it is overdue". DM defines skip only for recurring tasks and sessions.
- [ ] **CONFLICT: block end counts as a skip.** F27–29, F527: the skip count goes up on every skip "and every time a block ends with the task unfinished", including a task the block never reached. DM: the number of times you skipped it since you last completed it.
- [ ] **CONFLICT: skip on a recurring task.** KIV-50.13: a skip "does not change status or due date". DM: the due date moves forward each time you complete or skip it.
- [ ] **MISSING: Dismiss resets the skip count.** KIV-50.13 #6. DM computes the skip count from history and has no reset.
- [ ] **MISSING: skip in history.** KIV-51.2 #7: history records the step as skipped. KIV-162.7 #6: a skip adds nothing to Time spent or Completions.

## 4. Undo

- [ ] **MISSING: Undo.** F533–554:
  - Undo reverses only the latest done or skip, with no time limit (F537).
  - The undone task's countdown continues as if the done never happened. The task that started after it goes back to not started, and its start is removed from history (F539–540).
  - Undoing a skip reverses the skip-count increase (F541).
  - Undo after the block ended starts nothing (F543).

## 5. Pause

- [ ] **MISSING: pause.** F560–565: pause is available at any time. Warnings and nags stop while paused. A done or skip while paused ends the pause, and the next task starts at once.
- [ ] **MISSING: pause reminder.** F569–572: a vibration every 15 minutes while paused, on by default (D48–49).
- [ ] **MISSING: timed pause.** F576–580: "pause for N minutes" ends by itself, then "Resuming <task>." plays.
- [ ] **MISSING: block-end warning during a pause.** F1106: it plays on resume, with the minutes actually left.
- [ ] **MISSING: paused time and view limits.** F987: paused time does not count toward a view's minutes limit.
- [ ] **OPEN: does a step's elapsed time keep counting during an appointment pause?**
- [ ] **OPEN: a block that ends while paused.**

## 6. Nagging, warnings, and announcements

- [ ] **MISSING: nagging.** F484–489, F459: nags for a task waiting past its estimate, on or off with an interval. Off by default, 60-second interval (D24–25).
- [ ] **MISSING and CONFLICT between sources: warnings.**
  - F599–600, D18–23: remaining warning at 5 minutes, halfway warning, next-up warning 30 seconds before the end. All off by default. No halfway warning for estimates of 10 minutes or less.
  - KIV-159 #2, #3, #5, #6: halfway warning, plus a 1-minute warning that says the next step. All on by default. Halfway only on steps longer than 2 minutes.
- [ ] **MISSING: announcement kinds with an on/off switch each.** F347: block start, task start, item start, warning, nag, routine complete, free time, block end, prompt, moved-past step. KIV-159 #5.
- [ ] **MISSING: where guidance settings live.** F271–273, F342: "Every sound setting is on the routine… no per-task exception." "One global mode… always wins."
- [ ] **MISSING: announcements never overlap.** F312–316: a due announcement waits for the one playing. Fixed priority for ties: task start, item start, block-end warning, remaining warning, halfway warning, next-up warning, nagging.
- [ ] **CONFLICT between sources: estimate end.** F603: "Nothing is announced when a task's estimate ends; that is nagging's job." KIV-51.2 #4: a sound plays at zero.
- [ ] **MISSING: routine complete announcement.** KIV-159 #4, F1438: "Morning routine complete."
- [ ] **MISSING: loop number.** KIV-436 #1–3: "1 of 15" at the start of each loop.
- [ ] **MISSING: sound mode during an appointment.** F317–320, F1140–1142: an appointment sets vibrate mode until you report that you are back.

## 7. Prompts and metrics on done

- [ ] **MISSING: prompts repeat.** F430–435: a prompt that waits for an answer repeats every 30 seconds until answered. The interval is a global setting.
- [ ] **MISSING: metric recording modes.** F503–521, KIV-162.2 #2–6: each metric on a task or step records Automatic, Default with prompt, or Prompt. Silence for 5 seconds records the default. "done with N" records N. Every way of completing records the metrics (F517). The next task is announced after the last metric (F521).
- [ ] **OPEN: metrics per loop or after the last loop.** KIV-51.5.

## 8. Waits

- [ ] **MISSING: waits, such as laundry.** F129–136, F493–499: "A wait counts from the done of the task before it. During a wait, the routine waits and the block continues: the block's next view starts." A wait sits between tasks, never inside one. If nothing else is left, free time runs until the wait ends. DM routine entries cannot express a wait.

## 9. Block start

- [ ] **MISSING: overstuffing prompt.** F1119–1131: when a block starts with more content than time, no task starts. "Tasks overflow by N minutes. Continue, or fix?" The check runs again at every task boundary, including a skip. After Continue, it does not ask again in that block.
- [ ] **MISSING: transition buffer.** F1109: every block has a transition buffer, 5 minutes by default, counted in the overstuffing check. DM's Transitions section gives no amount.
- [ ] **MISSING: block start announcement.** F958, KIV-158.2 #6: a block's name is announced every time it starts.
- [ ] **MISSING: title match ignores case.** F925, KIV-158.2 #1.
- [ ] **MISSING: all-day events never start blocks.** KIV-158.2 #8.
- [ ] **MISSING: minimum block length.** F1082–1084: an event shorter than 15 minutes is ignored, with a warning.
- [ ] **MISSING: events that match no block.** F946–947: the event still reserves its time and nothing runs. KIV-158.2 #7: it does not count as free time.

## 10. Block end

- [ ] **MISSING: five-minute warning.** F1103–1105: global setting, on by default. It does not play once free time was announced.
- [ ] **CONFLICT: what happens at block end.** KIV-158.5 #2–5: "Finish routine or Stop at block end", and with no answer the routine finishes. F1102: the routine stops. KIV-158.2 #5: a block starting while a routine overruns waits for it. DM: a running step stops at the block end.
- [ ] **CONFLICT: Catch up block.** F1111–1113: every unfinished task becomes unfinished at block end, and a "Catch up" block appears. DM: the task stays due, with no leftovers list.
- [ ] **MISSING: a routine cut short is incomplete in history.** F1110.
- [ ] **OPEN: a block that ends while a step is in overtime.**
- [ ] **OPEN: what a non-repeating block does when its list finishes early.**

## 11. Free time

- [ ] **MISSING: free time.** KIV-158.7 #1–4, F960–963: time without a running routine, a running block, or an appointment. It is announced with its end and the next event, or "for the rest of the day".
- [ ] **MISSING and CONFLICT between sources: a block that finishes early.** KIV-158.7 #4: the block ends early and free time starts. F1114: free time is announced, and nothing plays at the event's end.
- [ ] **MISSING: free time never runs unfinished tasks.** F1241.

## 12. View limits

- [ ] **CONFLICT: a minutes limit cuts the running task.** F972: "the running view ends on time, and the task continues when the same view appears again." DM: a view limit never interrupts a step.
- [ ] **CONFLICT: fit by task or by step.** F971 fits whole tasks by task estimate. DM fits by the next step's estimate.
- [ ] **MISSING: a task picked by two views runs in the view listed first.** F977.
- [ ] **MISSING: a task runs once per block.** F969.
- [ ] **MISSING: a cut task finished elsewhere is passed over.** F981.
- [ ] **MISSING: a task cut at its view's last appearance waits for the block's end.** F983.
- [ ] **MISSING: resuming says "Resuming Write report." and does not repeat the step.** F973, F979.
- [ ] **MISSING: planning counts what each appearance plans to run.** F985–986.
- [ ] **MISSING: a view's end is not a task boundary and plays no warning.** F988.
- [ ] **MISSING: a task view can include specific tasks.** KIV-50.6 #2.
- [ ] **OPEN: does a resumed partial task count toward a tasks limit?**
- [ ] **OPEN: Extend or overtime against a minutes limit.**
- [ ] **OPEN: can routines take part in alternation?** KIV-362.

## 13. Routine entries that cannot run

- [ ] **MISSING: moved-past entries.** F1164–1173: a routine moves past a backlog or blocked task and announces the reason. Complete or canceled tasks are passed silently (F1165). DM does not say what a routine does with an entry that is locked, backlog, Waiting, canceled, or a later project task.
- [ ] **CONFLICT: Waiting tasks.** KIV-50.12 #2–3: blocks and routines never pick a waiting task. On the follow-up date it shows as "Follow up with plumber". DM: Waiting's due date is the follow-up date, so the task would play on that date.
- [ ] **CONFLICT: steps that always run.** KIV-51: "A step always runs and always logs." KIV-173 adds a "Run only when the linked task is due" switch. DM: an entry plays only when due, with no switch.
- [ ] **MISSING: history records an entry as not due.** KIV-173 #4.

## 14. Calendar changes during a run

- [ ] **MISSING: changes apply at the next task boundary.** F1064–1070: "A running task is never interrupted." A new end time moves the block end. A renamed or deleted event ends the block at the next task boundary. An event renamed to another block's title starts that block. KIV-158.9 leaves the rest open.
- [ ] **MISSING: overflow check after a change.** F1066.
- [ ] **MISSING: read cadence.** F1056–1061, KIV-158.3 #3, #6–7: read 14 days ahead and 24 hours back. Re-read after every done and every 5 minutes. A failed read keeps the saved events.
- [ ] **MISSING: calendar warnings.** F953: announced at the next task boundary, once per new problem, and never blocks anything.
- [ ] **CONFLICT: overlaps.** F1090: no overlaps allowed: two appointments, two blocks, or an appointment and a block. KIV-158.2 #3–4: a block with an overlap does not start until the overlap is fixed. DM: an appointment takes its time out of a block. Block-to-block overlap is not covered.
- [ ] **CONFLICT: appointment during a routine.** F593, F1077: an appointment cannot interrupt a running routine. KIV-158.4 #2, the newer source, and DM: the routine pauses.
- [ ] **MISSING: return reminder.** F1146–1148: a vibration every 10 minutes after an appointment ends, until you report that you are back.

## 15. Manual mode and on-demand runs

- [ ] **CONFLICT: manual mode.** F926: "The calendar always drives the day. There is no manual mode." DM has manual mode.
- [ ] **MISSING: starting a block by hand.** F927–929: "Start Housework Block for 60 minutes". Refused if it overlaps any event.
- [ ] **MISSING: an on-demand run creates a block.** F935–938: its length is the total estimate plus the transition buffer, and every block rule applies.
- [ ] **MISSING: a running routine cannot be edited.** F1182–1183.
- [ ] **OPEN: "wait for done" in manual mode.**
- [ ] **OPEN: can an on-demand run overlap a block or an appointment?**

## 16. Day boundary and downtime

- [ ] **MISSING: changing "day starts at".** F1004–1009: applies at once to today's end. A day that has started keeps its start. Past days keep their boundaries. Each start is recorded as a "Day started" event.
- [ ] **MISSING: travel and daylight saving.** F1022–1024: a date is never skipped or repeated. Daylight saving gives 23- and 25-hour days.
- [ ] **MISSING: Sleep event.** F1037–1042: a "Sleep" event silences everything, and a built-in Sleep block exists. May have been dropped.
- [ ] **MISSING: app downtime.** F810–823: while no device runs the day, nothing is announced and nothing goes into history. That time does not count toward the current task. A block that started meanwhile starts when a device runs again. A block whose time ended meanwhile ends when the app stopped. A clock jump over 2 minutes counts as a sleep.
- [ ] **MISSING: one active device.** F805–809: one device runs the day, and "Run here" moves it. May belong to architecture.
- [ ] **MISSING: first day of the week.** KIV-314 #1, #3: weekly windows count from a chosen first day, Monday by default.

## 17. Estimates and history

- [ ] **CONFLICT: learned estimate formula.** KIV-168, KIV-50.7: median of the latest 5, after 3. F1316–1317: average of the last 5, with the set estimate as one value until 5 real completions exist. DM: trimmed average of the latest 5, dropping the highest and the lowest.
- [ ] **MISSING: editing and deleting runs.** KIV-162.10 #2–4: editing a Time spent value changes the source run, and a run can be deleted.
- [ ] **MISSING: automatic metrics.** KIV-162.7 #1–3: Time spent and Completions per task, step, or routine, computed from history.
- [ ] **MISSING: what a step record holds.** KIV-164 #1: planned length, real length, and done or skipped.
- [ ] **MISSING: a task done by hand with no recorded start.** F1358: drawn with its estimate, ending at its done time.
- [ ] **MISSING: spacing between steps.** KIV-167 #1–3: a routine can require a minimum and maximum time between two steps. Saving refuses a routine that breaks it. The run does not check it.

## 18. Progressions, habits, and goals

- [ ] **MISSING: go easier.** F1421–1424, KIV-302.4: run today's session at a lower level, one level down by default, recorded like any session. DM levels have no matching sessions across levels.
- [ ] **MISSING: when level checks run.** F1425: on finished days only, at the start of the day.
- [ ] **CONFLICT: when a level change takes effect.** KIV-302.1 #12: at the end of the current pass, starting the new level at the first slot. DM: a rotation starts at its first slot on the day its level becomes active.
- [ ] **CONFLICT: marking a slot missed.** KIV-302.1 #8–9: a missed slot advances to the next slot. DM: a skip keeps the rotation on the same slot.
- [ ] **MISSING: wrap-around.** KIV-302.1 #11: after the last slot, the rotation starts again at the first slot.
- [ ] **MISSING: pausing a progression.** KIV-302.1 #13: stops new sessions and the move-down conditions.
- [ ] **MISSING: draft and start date.** KIV-302.1 #5–6: a new progression is a draft. Start asks for a start date.
- [ ] **MISSING: lapse clock.** F1418: after a drop by lapse, the lapse clock restarts at the drop.
- [ ] **MISSING: re-checking a waiting move up.** KIV-302.2 #4, F1428: each day, and on a "Check again" button.
- [ ] **CONFLICT between sources: changing a level by hand.** KIV-162.8 #5: no button to change the level. F34: every field the app changes by itself can also be changed by hand.
- [ ] **MISSING: first and last levels.** KIV-162.8 #6: the first level has no move-down conditions. The last level has no move-up conditions.
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
- [ ] **OPEN: loop announcements.** KIV-436: the full phrase, whether warnings play in each loop, and whether every step in a looped routine says the loop number.

## 22. Remaining list items not yet reviewed

- [ ] Timers (KIV-49)
- [ ] Background music and playlists in routines (KIV-169, KIV-36)
- [ ] Routine run history (KIV-164)
- [ ] Summary or plan for any span (KIV-162.9)
- [ ] Metric target conditions: window, aggregation, and condition (KIV-162.3)
- [ ] Comments: can a comment be edited or deleted? (KIV-50.14)
