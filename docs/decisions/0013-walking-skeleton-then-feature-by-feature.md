# DR-0013: Walking Skeleton, then Feature by Feature

**Status:** Accepted  
**Date:** 2026-11-27

## Context

The layers of Run My Day are:

* database
* server API
* core package
* screens
* service worker

Challenges:

* Every line of code must be reviewed by a human, to maintain quality and prevent architectural drift. 
* Large pull requests can't be reviewed well. 

Problems with building in layers:

* When the whole app is built one layer at a time, the layers first run together near the end, which catches problems too late.
* A requirement found while building a screen can change the layers below it.

Example: the task view screen is built last, after every table, endpoint, and actor is done. While building it, each task's skip count turns out to be needed on screen. A history query, a new endpoint field, and new actor state must then be added to layers that were already reviewed.

## Decision

Development runs in two stages.

Stage 1, walking skeleton:

* One user story of the Tasks feature is built through all five layers first.
* That user story works in the web build with the network turned off.
* The skeleton is split into small pull requests. Each pull request adds one layer or one tool.
* Each pull request is merged to `main` with passing tests.

Example: "As a user, I can create a task with a title and see it in my list, with the network turned off." When the network returns, the task is saved on the server.

Stage 2, feature by feature, one layer per pull request:

* The app is built one feature at a time, such as Tasks, Routines, Calendar blocks, and Progressions.
* Each feature is built in every layer it needs before the next feature starts.
* Each pull request covers one layer of one feature.

Example: Routines is built before Progressions starts. The Routines pull requests add the routines table, the routine endpoints, the Task run actor's routine steps, and the routine screens. While building the routine screens, a step's spoken count turns out to be needed. The change is made in the Routines work that is still open.

## Alternatives Considered

One layer at a time for the whole app, rejected:

* The layers first run together near the end.
* A requirement found in a higher layer reopens lower layers that were already reviewed.

Vertical slices of one user story each, rejected:

* Each feature would be split into many tickets.

Example: Tasks would become separate slices for creating a task, completing a task, skipping a task, and editing a due date, each with its own ticket.

## Rationale

Walking skeleton:

* All five layers are shown to work together before any feature depends on them.

Feature by feature, one layer per pull request:

* A requirement found while building a screen changes only the feature in progress.
* Each pull request stays small enough to review.

## Consequences

* No feature is able to be built until the skeleton is complete.

## Sources

### [Alistair.Cockburn.us | Walking skeleton](https://web.archive.org/web/20160210222727/http://alistair.cockburn.us/Walking+skeleton)

Retrieved 2026-11-27.

- Fact: A walking skeleton is one small end-to-end function built through all the main layers first.
  > "A Walking Skeleton is a tiny implementation of the system that performs a small end-to-end function. It need not use the final architecture, but it should link together the main architectural components."
- Fact: The walking skeleton shows that all layers work together before features depend on them.
  > "The architecture and the functionality can then evolve in parallel."

### [INVEST in Good Stories, and SMART Tasks - XP123](https://xp123.com/invest-in-good-stories-and-smart-tasks/)

Retrieved 2026-11-27.

- Fact: A vertical slice builds one user story in every layer it needs.
  > "We want to give the customer the essence of the whole cake, and the best way is to slice vertically through the layers."
- Fact: When the whole app is built one layer at a time, a finished lower layer has no use until the layers above it exist.
  > "Developers often have an inclination to work on only one layer at a time (and get it “right”); but a full database layer (for example) has little value to the customer if there’s no presentation layer."

### [Small CLs | eng-practices](https://google.github.io/eng-practices/review/developer/small-cls.html)

Retrieved 2026-11-27.

- Fact: Large pull requests can't be reviewed well.
  > "With large changes, reviewers and authors tend to get frustrated by large volumes of detailed commentary shifting back and forth—sometimes to the point where important points get missed or dropped."
- Fact: Small pull requests are easier to review.
  > "It’s easier for a reviewer to find five minutes several times to review small CLs than to set aside a 30 minute block to review one large CL."
