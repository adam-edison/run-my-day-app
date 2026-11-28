# DR-0015: GitHub Issues and OpenSpec Changes for Work Tracking

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Challenges:

* Work needs an order, and a record of which pull request did what.
* Behavior must be planned and reviewed before code is written.
* Each work item must be listed in one place only.

Tools:

* GitHub Issues and a GitHub Project board order the work.
* OpenSpec holds the feature specs and the changes to them.

## Decision

Board:

* A milestone groups the parent issues for a release, such as the MVP.
* One parent issue covers one feature, or the walking skeleton.
* The order of parent issues is set by hand on the GitHub Project board.

Requirements:

* The parent issue holds a general description of the feature's requirements.
* The OpenSpec change's proposal fleshes out those requirements and lists the parent issue as its source.
* After the proposal is reviewed, the parent issue's description is not edited. The OpenSpec change, and then the feature spec, hold the current requirements.

OpenSpec changes:

* Each feature is built through one OpenSpec change.
* The change's proposal, design, and spec changes are reviewed before any code is written.
* The change's `tasks.md` is the only list of work items for that feature.
* `tasks.md` is grouped by pull request. Each group is one pull request.
* Each apply run builds one group, then opens one pull request.
* Each pull request links the parent issue and the group it completes.
* The change is archived after its last pull request merges. Archiving adds its spec changes to the feature specs.

Walking skeleton:

* The walking skeleton is one small OpenSpec change.
* Its `tasks.md` has many small groups.

Small tasks:

* A small task needs no OpenSpec change.
* A small task gets its own issue only when it needs to be tracked, such as a bug.

Example: issue #12, "Routines," says "A routine is a list of steps that are played in order, with timed announcements." The proposal for the OpenSpec change `add-routines` starts with "Source: #12" and turns that description into requirements with scenarios. Its `tasks.md` has 4 groups: the routines table, the routine endpoints, the routine steps in the Task run actor, and the routine screens. The pull request "Routines: 2. Routine endpoints" completes group 2. After group 4 merges, `add-routines` is archived, and `openspec/specs/routines/spec.md` describes routines.

## Alternatives Considered

Sub-issues for each pull request, rejected:

* Each work item would be listed both in `tasks.md` and in a sub-issue.

OpenSpec specs without changes, rejected:

* Behavior would not be reviewed before code.
* Specs would be updated by hand in each pull request and would drift.

## Rationale

* Each work item is listed once, in `tasks.md`.
* Behavior is reviewed before code, when a wrong assumption costs least.
* The feature specs are updated when a change is archived.
* The walking skeleton's setup runs to thousands of lines. Many small groups keep each of its pull requests small.

## Consequences

* The feature specs show a feature's new behavior only after its whole change is archived.
* "One group per apply run" is a project convention. OpenSpec's apply skill works through every task unless told otherwise.

## Sources

### [About milestones](https://docs.github.com/en/issues/using-labels-and-milestones-to-track-work/about-milestones)

Retrieved 2026-11-27.

- Fact: A milestone groups issues.
  > "You can use milestones to track progress on groups of issues or pull requests in a repository."

### [Adding sub-issues](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/adding-sub-issues)

Retrieved 2026-11-27.

- Fact: GitHub issues can have sub-issues under a parent issue.
  > "You can add sub-issues to an issue to break down larger pieces of work into tasks. Your sub-issues show their relationship to the parent issue allowing you to track your work across GitHub."

### [Customizing the board layout](https://docs.github.com/en/issues/planning-and-tracking-with-projects/customizing-views-in-your-project/customizing-the-board-layout)

Retrieved 2026-11-27.

- Fact: Items on a GitHub Project board can be ordered by hand when the board is not sorted.
  > "When a board is sorted, you cannot manually reorder items within a column. You can continue to move items from column to column."

### [OpenSpec](https://github.com/Fission-AI/OpenSpec)

Retrieved 2026-11-27.

- Fact: An OpenSpec change has a proposal, spec changes, a design, and `tasks.md`.
  > "each change gets its own folder with proposal, specs, design, and tasks"
- Fact: The change is reviewed before any code is written.
  > "Your AI writes these; you review the plan before any code is written."

### [Commands](https://github.com/Fission-AI/OpenSpec/blob/main/docs/commands.md)

Retrieved 2026-11-27.

- Fact: OpenSpec's apply skill works through every task unless told otherwise.
  > "Reads `tasks.md` and identifies incomplete tasks"
  > "Works through tasks one by one"
- Fact: Archiving adds the change's spec changes to the feature specs.
  > "Offers to sync delta specs if not already synced"
  > "Merges changes into main `openspec/specs/` directory"
