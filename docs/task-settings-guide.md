# Task Settings Guide

This guide explains each task setting with examples, and how settings combine. Every setting should be easy to predict by intuition. When a setting needs this guide to make sense, its explanation belongs in the app next to the setting.

## Repeats

A recurring task is one task. Each time you complete or skip it, its due date moves forward. A missed recurring task stays overdue until you complete it, skip it, or move its due date.

### Pattern kinds

| Pattern kind | Example | Good for |
| --- | --- | --- |
| Every N days, weeks, months, or years | weekly on Monday, Wednesday, and Friday; monthly on the 1st | most repeating work |
| Cycle of slots | push-ups, rest, push-ups, rest, rest | rotations and fixed rest days |
| Specific dates | March 3, April 19, June 2 | dates with no pattern |
| Quota | 4 times in any 7 days | flexible targets with no fixed days |

Presets such as daily, weekly, monthly, yearly, and every weekday are shortcuts for the first kind.

Quota and cycle overlap. Pick whichever takes less work to set up. A cycle fixes the order of work and rest days. A quota lets you choose the days.

### By due date or by completion date

Both settings behave the same when you finish on the due date. They differ only when you finish late or early.

Example: "Water plants", every 7 days, due Monday the 1st.

| | Finished on time, Monday the 1st | Finished late, Thursday the 4th | Finished early, Saturday the 30th |
| --- | --- | --- | --- |
| By due date | next: Monday the 8th | next: Monday the 8th, 4 days later | next: Monday the 8th, 9 days later |
| By completion date | next: Monday the 8th | next: Thursday the 11th, 7 days later | next: Saturday the 6th, 7 days later |

- **By due date keeps the rhythm.** Mondays stay Mondays. A late finish shortens the gap before the next one.
- **By completion date keeps the gap.** There are always 7 days between waterings. A late finish moves everything later.

| Rhythm matters: by due date | Gap matters: by completion date |
| --- | --- |
| Trash out Monday night, because the truck comes Tuesday | Water plants, because watering Thursday then Monday is too soon |
| Team report every Friday | Haircut every 6 weeks |
| Rent on the 1st | New toothbrush every 3 months |

### If missed: jump to today or every date is owed

This setting applies only to repeats by due date. It decides where the due date moves after a late completion or a skip.

Example: "Push-ups", weekly on Monday, Wednesday, and Friday. You were sick, and on Friday the task is still due Monday.

- **Jump to today** (default): the due date moves to the next pattern date after today, which is next Monday. Wednesday and Friday are not owed.
- **Every date is owed:** the due date moves to the next pattern date after the old due date, which is Wednesday. That date has passed, so the task is overdue again, and you work through each missed date.

| Practices: jump to today | Obligations: every date is owed |
| --- | --- |
| Push-ups: three sessions on Friday help nobody | Rent: March and April are both still owed |
| Water plants: watering twice today drowns them | Client invoice: each month bills different hours |
| Clean kitchen: one clean covers the missed weeks | Timesheet: each week is a separate record |
| Vitamins: never double up on a missed dose | Quarterly tax estimate: Q1 does not go away when Q2 comes |

### Quota rest days

A quota can require rest days between sessions. Without rest days, "4 times in any 7 days" allows Monday through Thursday in a row. With 1 rest day, the sessions spread out, such as Monday, Wednesday, Friday, and Sunday.

Some combinations can never be met. 5 sessions with 10 rest days between need at least 45 days, so a 30-day window cannot work. Run My Day refuses such a quota and shows the minimum: "5 sessions with 10 rest days between need at least 45 days. Lengthen the window, lower the count, or shorten the rest."

### Ends

A repeat ends never, on a date, or after a number of repeats.
