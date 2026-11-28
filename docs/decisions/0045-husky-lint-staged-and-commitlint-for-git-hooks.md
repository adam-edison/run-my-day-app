# DR-0045: Husky, lint-staged, and commitlint for Git Hooks

**Status:** Draft  
**Date:** 2026-11-28

## Context

Each branch is worked on in its own git worktree of one repository. Worktrees are created inside the repository folder, as recorded in DR-0011. Up to 4 worktrees have work in progress at once, as recorded in DR-0014.

Git hooks in worktrees:

* The repository `config` file is shared by all worktrees. Husky sets `core.hooksPath` once, and every worktree reads it.
* Husky sets `core.hooksPath` to the relative path `.husky/_`.
* Git resolves a relative hooks path from the root of the worktree where the hook runs. Each worktree uses its own `.husky/_` folder.
* Husky generates `.husky/_` during install and gitignores it.
* lefthook writes its hooks into `.git/hooks/`. That folder is in the shared git directory. The hooks exist in every worktree at once.
* lint-staged backs up the original state with `git stash`. All refs under `refs/` are shared by worktrees, and the stash is a ref.

Tools:

* Husky is 2 kB with no dependencies, and it uses git's `core.hooksPath`. Its latest version is 9.1.7.
* lint-staged runs formatters and linters on staged files only. It supports one config per package in a monorepo. Its latest version is 17.6.2.
* commitlint checks commit messages against a convention when they are written. Its latest version is 21.2.4.

Example: a new worktree is created for the branch `feature/streaks`. Dependencies are installed in it. A commit with a badly formatted file runs Prettier on that file through lint-staged, and commitlint checks the message.

## Decision

* Husky 9 runs the git hooks.
* lint-staged 17 runs formatters and linters on staged files before each commit.
* commitlint 21 checks each commit message.

## Alternatives Considered

lefthook, rejected:

* It replaces lint-staged's staged-file handling with its own config.

simple-git-hooks, rejected:

* It allows only one command per hook.

## Rationale

* Husky is small.
* Husky works in a worktree after dependencies are installed in it.
* Each worktree that runs code has its dependencies installed, as recorded in DR-0011.

## Consequences

* A new worktree has no `.husky/_` until dependencies are installed in it. Until then, no hook runs in that worktree. This point is not verified by an official source.
* Two worktrees that commit at the same moment could touch the same stash list. No report of this problem was found. This point is not verified.
* No official source states that lefthook replaces lint-staged's staged-file handling. This point is not verified.
* The choice to keep Husky is not verified by an official source.
* Husky 9.1.7 is the only release that npm metadata shows in the last months. npm metadata was last updated on 2026-09-02.
* Husky's install script warns that hook files with the old `husky.sh` lines will fail in Husky v10.
* lefthook can run hook jobs in parallel. Its version 2.2.4 was updated on 2026-11-26.
* No alternative to lint-staged or commitlint was researched. Both work with Husky and lefthook.

## Open Questions

* Should a docs-only worktree still run commit hooks? DR-0011 does not install dependencies in a docs-only worktree. If yes, lefthook fits better, because its hooks sit in the shared `.git/hooks/`. Example: a worktree created only to edit `docs/planning-status.md` has no `node_modules`, so Husky runs no commitlint check on its commits.

## Sources

### [git-config Documentation](https://git-scm.com/docs/git-config)

Retrieved 2026-11-27.

- Fact: A relative `core.hooksPath` is resolved from where the hooks run.
  > "A relative path is taken as relative to the directory where the hooks are run"

### [githooks Documentation](https://git-scm.com/docs/githooks)

Retrieved 2026-11-27.

- Fact: Hooks run in the root of the working tree.
  > "Before Git invokes a hook, it changes its working directory to either $GIT_DIR in a bare repository or the root of the working tree in a non-bare repository."

### [git-worktree Documentation](https://git-scm.com/docs/git-worktree)

Retrieved 2026-11-27.

- Fact: The config file is shared by all worktrees.
  > "By default, the repository `config` file is shared across all worktrees."
- Fact: Refs under `refs/` are shared.
  > "In general, all pseudo refs are per-worktree and all refs starting with `refs/` are shared."

### [gitrepository-layout Documentation](https://git-scm.com/docs/gitrepository-layout)

Retrieved 2026-11-27.

- Fact: Worktrees use the hooks folder of the common git directory.
  > "This directory is ignored if $GIT_COMMON_DIR is set and \"$GIT_COMMON_DIR/hooks\" will be used instead."

### [Husky](https://typicode.github.io/husky/)

Retrieved 2026-11-27.

- Fact: Husky is small and uses `core.hooksPath`.
  > "Just `2 kB` (📦 _gzipped_) with no dependencies."
  > "Uses new Git feature (`core.hooksPath`)."

### [husky/index.js](https://raw.githubusercontent.com/typicode/husky/main/index.js)

Retrieved 2026-11-27.

- Fact: Husky sets `core.hooksPath` to `.husky/_` and writes a `.gitignore` of `*` into it. The page was read as a summary, so no verbatim quote is available.

### [Lefthook](https://lefthook.dev/)

Retrieved 2026-11-27.

- Fact: lefthook installs hooks into `.git/hooks/`.
  > "Lefthook installs the configured hooks into `.git/hooks/`."

### [simple-git-hooks | GitHub](https://github.com/toplenboren/simple-git-hooks)

Retrieved 2026-11-27.

- Fact: simple-git-hooks allows one command per hook.
  > "this package allows you to set only one command per git hook."

### [lint-staged | GitHub](https://github.com/lint-staged/lint-staged)

Retrieved 2026-11-27.

- Fact: lint-staged backs up the original state in a git stash.
  > "By default _lint-staged_ creates a `git stash` as a backup of the original state before running any configured tasks"
- Fact: lint-staged supports one config per package.
  > "Install _lint-staged_ on the monorepo root level and add separate configuration files in each package."

### [commitlint](https://commitlint.js.org/)

Retrieved 2026-11-27.

- Fact: commitlint checks commit messages against a convention.
  > "helps your team adhere to a commit convention"

### [npm registry: husky, lefthook, lint-staged, @commitlint/cli](https://www.npmjs.com/package/husky)

Retrieved 2026-11-27.

- Fact: Husky's latest version is 9.1.7, last updated 2026-09-02.
  > "version = '9.1.7'"
  > "time.modified = '2026-09-02T19:31:26.814Z'"
- Fact: lefthook's latest version is 2.2.4.
  > "version = '2.2.4'"
- Fact: lint-staged's latest version is 17.6.2.
  > "version = '17.6.2'"
- Fact: commitlint's latest version is 21.2.4.
  > "version = '21.2.4'"
