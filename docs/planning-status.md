# Planning Status

Repository: `/Users/aedison/repositories/run-my-day-app`, branch `main`. This is planning only: no code yet.

## 1. Open decisions, in order

Core architecture:

- [ ] Calendar integration: which calendars, sign-in, and push or polling within the 1-minute requirement
- [ ] Audio timing and text-to-speech in the web build
- [ ] Recurrence with rrule

Hosting and operations:

- [ ] Hosting of the server and Postgres for the single-user, self-hosted version
- [ ] Remote access from phones and other machines, such as Tailscale
- [ ] Hosting of the static web build
- [ ] Database backups and restore
- [ ] Observability: what is logged to Pino and reported to Sentry, and what raises an alert
- [ ] Choices that must not block the paid hosted version

CI/CD and release:

- [ ] GitHub Actions pipeline: checks on each pull request, on a schedule, and before a release
- [ ] Releases: how a version is cut and numbered
- [ ] App distribution: Expo Build, Expo Submit, TestFlight, Play internal testing, and Expo over-the-air updates

Security:

- [ ] Authentication with Better Auth: sign-in methods, sessions, and tokens on phones
- [ ] Threat model and security review

Remaining technology records:

- [ ] One record for each remaining technology in `technology-stack.md`: Fastify, oRPC, Zod, Drizzle, PostgreSQL, ESLint and its plugins, Knip, Prettier, Husky, lint-staged, commitlint, fast-check, Renovate, Betterleaks, actionlint, and @fastify/rate-limit. Each needs a rationale.

## 2. Domain model wrap-up (step 2 of planning)

- [ ] **Create `docs/glossary.md`:** every term, with a definition and an example. Definitions use only glossary terms.
- [ ] **Shrink `docs/domain-model.md` to the overview:** the diagrams of how items relate. Behavior rules move into OpenSpec feature specs as requirements with scenarios, and most existing examples become scenarios.
- [ ] **Open proofread questions:**
  - [ ] A repeating block list that contains a routine plays the routine on every pass. Confirm this. The recommendation is yes, as written.
  - [ ] Fixed numbers that might need to be settings: appointment warnings (10 and 5 minutes), an appointment pause lasting until you resume, the 7-day window for unscheduled tasks, and the 5 and 3 loop counts for learned estimates.
- [ ] **Open question:** two blocks whose events overlap. Options: the later block takes over, the running block finishes first, or overlaps are refused.
- [ ] **`docs/domain-model-review.md`** has about 60 remaining items. Most are edge cases, and they should be decided inside each feature's OpenSpec change rather than all up front. The new features in it aren't needed for an MVP:
  - seasonal tasks, conditions on steps, and custom metric units
  - guidance channels (music cues, vibration, notifications)
  - timers, background music and playlists, and summaries
  - QR scan tasks, Sleep events, and the return reminder after an appointment
  - on-demand runs
  - more repeat options, projects in any order, and project dependencies
  - editing past runs, spacing between steps, and draft or paused progressions

## 3. Remaining planning steps

- [ ] **MVP scope:** pick the features for the first version.
- [ ] **Architecture:** finish principles, constraints, security, and performance.
- [ ] **Technology decisions:** the decision records above, plus the Expo web trial and service worker.
- [ ] **Feature roadmap:** an ordered list of GitHub issues.
- [ ] **First OpenSpec change.**

## 4. How the user wants to work

- Write in plain, technical, passive-voice English. Items never act on their own: not "a view picks" but "the view's filter matches." No shorthand ("waits," "decides," "lives on"). Use existing terms and never invent labels.
- Every rule gets an example drawn from the real use case. Show a concrete example of every change made.
- Prefer existing data structures over special cases. Keep every setting independent: no setting changes what another setting does.
- Ask one focused question per message, with options and an example in a narrow ASCII block. Check earlier decisions, in past conversations, kivaro, and the archived prototype, before asking anything.
- Confirm before writing files other than `docs/domain-model.md` and `docs/task-settings-guide.md`. Don't commit or push without approval.
