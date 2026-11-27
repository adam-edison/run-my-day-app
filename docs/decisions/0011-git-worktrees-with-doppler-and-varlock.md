# DR-0011: Git Worktrees, with Secrets from Doppler and Variables from Varlock

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Work happens in one repository, with a git worktree for each branch. No copies of the repository are made. A new worktree contains only committed files, so gitignored `.env` files with secret values are missing from it. Several worktrees sometimes run at the same time, and each one needs its own ports and database. Work sometimes happens without a network connection.

## Decision

Each branch is worked on in its own git worktree of the one repository.

Every secret value comes from Doppler. The app is started through `doppler run`, which passes secrets as environment variables. Varlock checks them against the committed `.env.schema`. When Doppler can't be reached, `doppler run` reads its latest encrypted snapshot. No `.env` file with secret values exists in any worktree. The developer signs in once with `doppler login`, and the Doppler CLI stores its token in the macOS Keychain. `doppler setup` is run once at the repository root. Every worktree is created inside the repository folder, so that one setup covers every worktree.

A worktree with code changes runs the app and its tests locally. A worktree with only non-code changes, such as docs, doesn't run the app locally. Its checks run in GitHub Actions on the pull request.

Each worktree that runs the app locally at the same time as another has a worktree number, from 1 to 9. The number is set in a gitignored `.env.local` in that worktree as `RUN_MY_DAY_WORKTREE`, and defaults to 1. `.env.schema` builds every port and the Docker Compose project name from the number. Example: worktree 2 runs the server on port 3200, the Expo dev server on 8121, and its own Postgres container on 5522.

## Alternatives Considered

- **Repository copies:** Rejected because each copy has to be cloned, installed, and kept in sync by hand.
- **Copying or linking `.env` files into each worktree:** Rejected because secret values would be stored on disk, and each new worktree would need a setup step.
- **A Doppler service token in the shell profile:** Rejected because the token would be stored in a readable file.
- **The Varlock Doppler plugin:** Rejected because it has no documented fallback when Doppler can't be reached.

## Rationale

- **Nothing to copy:** A new worktree runs with only committed files. Only a worktree that runs alongside another needs a `.env.local`, with one number.
- **No secrets in files:** Secret values are stored on the developer machine only in Doppler's encrypted snapshot, and the Doppler token only in the macOS Keychain.
- **Works offline:** The latest snapshot is used when Doppler can't be reached.
- **One schema:** `.env.schema` defines every variable once, with its type and whether it's secret.

## Consequences

- The snapshot can be decrypted only with the Doppler token that created it.
- Worktrees must be created inside the repository folder to use the one Doppler setup.
- The first start on a new machine needs a network connection to Doppler.
