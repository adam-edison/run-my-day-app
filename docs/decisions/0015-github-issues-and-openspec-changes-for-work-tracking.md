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
* The OpenSpec change's proposal fleshes out those requirements and names the parent issue as its source.
* After the proposal is reviewed, the parent issue's description is not edited. The OpenSpec change, and then the feature spec, hold the current requirements.

OpenSpec changes:

* Each feature is built through one OpenSpec change.
* The change's proposal, design, and spec changes are reviewed before any code is written.
* The change's `tasks.md` is the only list of work items for that feature.
* `tasks.md` is grouped by pull request. Each group is one pull request.
* Each apply run builds one group, then opens one pull request.
* Each pull request names the parent issue and the group it completes.
* The change is archived after its last pull request merges. Archiving adds its spec changes to the feature specs.

Walking skeleton:

* The walking skeleton is one small OpenSpec change.
* Its `tasks.md` has many small groups, because setup runs to thousands of lines.

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

## Consequences

* The feature specs show a feature's new behavior only after its whole change is archived.
* "One group per apply run" is a project convention. OpenSpec's apply skill works through every task unless told otherwise.
