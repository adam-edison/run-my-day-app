# Planning Status

Repository: `/Users/aedison/repositories/run-my-day-app`, branch `main`. This is planning only: no code yet.

## 1. Decisions made

### Domain model (written into `docs/domain-model.md`)

- **Task view filters** follow the Notion model. A filter is a group that matches all or any of its conditions. A condition is a field condition or another group. Groups nest at most 3 levels deep. One condition type matches tasks whose ID is in a chosen list.
- **Routines take no limit.** Each run of a routine runs it from start to end. Only task views take limits.
- **Lower level for one run.** Right before a session starts, you can choose a lower level, never a higher one, and then pick one of that level's sessions. The run records metrics like any other run. Level conditions are metric conditions, so the run counts toward a condition only if its metrics meet it. The active level and its rotation slot don't change.
- **Level limits.** Move-down conditions are optional. The top level has no move-up conditions, and the bottom level has no move-down conditions. This applies to both progressions and habits.
- **Entries that can't run.** A task entry that can't run is passed over, with no announcement: a task in backlog, canceled, or complete; a task with an unmet unlock condition; or a project task with an earlier task in the project not complete.
- **Waiting status.** A task in the Waiting status plays like any other task when it's due. Its due date is the follow-up date.
- **History** records only actions. Passing over an entry that isn't due isn't recorded.
- **Calendar changes.** A block's times always come from its event as last read, and the existing block start and end rules apply. When an event is extended past now after its block ended, the block starts again from the point where it ended.
- **Calendar reads** cover 24 hours back to 14 days ahead. A failed read keeps the last good read.
- **Loop numbers.** There's no separate loop number announcement. Loop number and loop total are variables in the step start template, so you add a step to speak the count wherever it belongs. Examples: "Round 1 of 3" at the start of a circuit round, or a "Count" step after In, Hold, Out in breathing.
- **Guidance is always on.** There's no guided or manual mode, and no on/off.
- **Project tasks** run in order. A task can run only when every earlier task in the project is complete. Every project task stays visible.
- **Terms:** a block list has *positions*, and each time a task view's position runs is a *run*.

### Non-functional requirement (written)

Run My Day reacts to a calendar change within 1 minute.

### Process and architecture (agreed, not yet written as decision records)

- **Work tracking (open):** GitHub Issues. A milestone holds parent issues, and each sub-issue is one pull request. How OpenSpec is used, if at all, is still open.
- **No custom process tooling.** Use off-the-shelf tools only. Kivaro failed partly because maintaining its factory, tickets, scripts, and test runners replaced building the product.
- **One repository with git worktrees,** no repository copies. Making Varlock work with worktrees is still open.
- **Client:** one Expo codebase for Android, iOS, and web, with feature parity, so the phone can do both planning and running.
  - The server stays a custom Fastify server with oRPC, WebSockets, and Postgres.
  - Expo's web output is static files, and Expo API routes aren't used.
  - Status: proposed, until a trial of one screen plus a service worker in Expo web passes.
- **Client state:** an event-driven state model, using the Elm Architecture with XState actors.
  - Every input is an event: user actions, audio events, timer ticks, server messages, online and offline.
  - Pure update logic returns the new state plus effects, and a thin shell carries out the effects.
  - The state is split into small actors that talk only through events: Day, Block run, Task run, Waits, Announcer, and Sync.
  - Each actor uses nested and parallel states (statecharts), and each module is built around one type.
  - Actors live in a shared core package. Components display state only.
  - This targets the race conditions that were most of kivaro's front-end bugs.
- **Offline is required** on every platform.
- **Test tiers:**

| Tier | Tool | When it runs |
| --- | --- | --- |
| Unit and display | Vitest, React Testing Library | Every commit |
| Boundary: web, service worker, APIs, database | Vitest, Playwright (`--changed`, `--only-changed`, Actions `paths` filters) | When that boundary changes, before a release, and on a schedule |
| Device: emulator | Maestro in CI (stock emulator action) | When native code changes, and before a release |
| Device: real phone | Maestro, `phone` tag, run by the user | Before a release |
| Perception: sound, speech, timing by ear | Manual checklist in the GitHub issue | When that behavior changes |

There are no end-to-end screen flows. Kivaro's Maestro flows and lessons can be reused, but not its 42 runner scripts or its manual-testing app.

## 2. Documents to write, one at a time

- [x] `docs/architecture/principles.md`
- [x] Add offline use to `docs/architecture/non-functional-requirements.md`
- [ ] Decision records in `docs/decisions/`, one per technology, in the format of DR-0003:
  - [x] 0004 Expo for Android, iOS, and web (proposed)
  - [x] 0005 XState for client state
  - [x] 0006 Vitest for core and server tests
  - [x] 0007 Jest, jest-expo, and React Native Testing Library for display tests
  - [x] 0008 Chromium-based desktop browsers only
  - [x] 0009 Playwright for web boundary tests
  - [ ] 0010 Maestro for device tests
  - [ ] 0011 Git worktrees, including Varlock
  - [ ] Later: work tracking with GitHub Issues, and whether OpenSpec is used
  - [ ] Later: one record for each remaining technology in `technology-stack.md`, such as Fastify, oRPC, Zod, Drizzle, PostgreSQL, TanStack Query, Better Auth, kokoro-js, ONNX Runtime, Sentry, Pino, Renovate, Doppler, ESLint, Knip, Prettier, Husky, commitlint, fast-check, and rrule. Each needs a rationale.

## 3. Domain model wrap-up (step 2 of planning)

- [ ] **Create `docs/glossary.md`:** every term, with a definition and an example. Definitions use only glossary terms.
- [ ] **Shrink `docs/domain-model.md` to the overview:** the diagrams of how items relate. Behavior rules move into OpenSpec capability specs as requirements with scenarios, and most existing examples become scenarios.
- [ ] **Open proofread questions:**
  - [ ] A repeating block list that contains a routine plays the routine on every pass. Confirm this. The recommendation is yes, as written.
  - [ ] Fixed numbers that might need to be settings: appointment warnings (10 and 5 minutes), an appointment pause lasting until you resume, the 7-day window for unscheduled tasks, and the 5 and 3 loop counts for learned estimates.
- [ ] **Open question:** two blocks whose events overlap. Options: the later block takes over, the running block finishes first, or overlaps are refused.
- [ ] **`docs/domain-model-review.md`** has about 60 remaining items. Most are edge cases, and they should be decided inside each capability's OpenSpec change rather than all up front. The new features in it aren't needed for an MVP:
  - seasonal tasks, conditions on steps, and custom metric units
  - guidance channels (music cues, vibration, notifications)
  - timers, background music and playlists, and summaries
  - QR scan tasks, Sleep events, and the return reminder after an appointment
  - on-demand runs
  - more repeat options, projects in any order, and project dependencies
  - editing past runs, spacing between steps, and draft or paused progressions

## 4. Remaining planning steps

- [ ] **MVP scope:** pick the capabilities for the first version.
- [ ] **Architecture:** finish principles, constraints, security, and performance.
- [ ] **Technology decisions:** the decision records above, plus the Expo web trial and service worker.
- [ ] **Feature roadmap:** an ordered list of GitHub issues.
- [ ] **First OpenSpec change.**

## 5. How the user wants to work

- Write in plain, technical, passive-voice English. Items never act on their own: not "a view picks" but "the view's filter matches." No shorthand ("waits," "decides," "lives on"). Use existing terms and never invent labels.
- Every rule gets an example drawn from the real use case. Show a concrete example of every change made.
- Prefer existing data structures over special cases. Keep every setting independent: no setting changes what another setting does.
- Ask one focused question per message, with options and an example in a narrow ASCII block. Check earlier decisions, in past conversations, kivaro, and the archived prototype, before asking anything.
- Confirm before writing files other than `docs/domain-model.md` and `docs/task-settings-guide.md`. Don't commit or push without approval.
