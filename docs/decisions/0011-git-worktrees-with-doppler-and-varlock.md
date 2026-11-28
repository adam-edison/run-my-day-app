# DR-0011: Git Worktrees, with Secrets from Doppler and Variables from Varlock

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Work happens in one repository, with a git worktree for each branch. No copies of the repository are made. A new worktree contains only committed files, so gitignored `.env` files with secret values are missing from it. Several worktrees sometimes run at the same time, and each one needs its own ports and database. Work sometimes happens without a network connection.

`doppler run` passes secrets to the app as environment variables. When Doppler can't be reached, `doppler run` reads its latest encrypted snapshot. The Doppler CLI stores its token in the macOS Keychain. `doppler setup` at the repository root covers every folder inside it. Varlock checks environment variables passed into the process against `.env.schema`.

## Decision

Each branch is worked on in its own git worktree of the one repository.

Every secret value comes from Doppler. The app is started through `doppler run`. Varlock checks the secrets against the committed `.env.schema`. No `.env` file with secret values exists in any worktree. The developer signs in once with `doppler login`. `doppler setup` is run once at the repository root. Every worktree is created inside the repository folder.

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
- **One Doppler setup:** Worktrees inside the repository folder are covered by the one `doppler setup` at the repository root.

## Consequences

- By default, the snapshot can be decrypted only with the Doppler token that created it.
- Worktrees must be created inside the repository folder to use the one Doppler setup.
- The first start on a new machine needs a network connection to Doppler.

## Sources

### [Git - git-worktree Documentation](https://git-scm.com/docs/git-worktree)

Retrieved 2026-11-27.

- Fact: One repository can have several worktrees, one for each branch.
  > "A git repository can support multiple working trees, allowing you to check out more than one branch at a time."
- Fact: A new worktree contains only the files of the commit checked out into it.
  > "Create a worktree at <path> and checkout <commit-ish> into it."

### [CLI Guide](https://docs.doppler.com/docs/cli)

Retrieved 2026-11-27.

- Fact: `doppler run` passes secrets to the app as environment variables.
  > "You can fetch the latest versions of your secrets for your project and selected config using the run command, which injects them as environment variables into the running process from your command or script."
- Fact: `doppler setup` at the repository root covers every folder inside it.
  > "You can perform an operation that assigns a specific directory (and its subdirectories) to a particular config."

### [Secret Fallback Files](https://docs.doppler.com/docs/automatic-fallbacks)

Retrieved 2026-11-27.

- Fact: `doppler run` saves an encrypted snapshot of the secrets.
  > "When using the `doppler run` command, the CLI automatically creates a fallback file containing an encrypted snapshot of the current secrets in JSON format to Doppler's system configuration directory."
- Fact: By default, the snapshot can be decrypted only with the Doppler token that created it.
  > "By default, a secret fallback file can only be decrypted when using the same Doppler token that was used when it was generated."
- Fact: The snapshot works offline with no time limit.
  > "No, the fallback file can be used indefinitely."

### [cli/pkg/cmd/run.go at master · DopplerHQ/cli](https://github.com/DopplerHQ/cli/blob/master/pkg/cmd/run.go)

Retrieved 2026-11-27.

- Fact: When Doppler can't be reached, `doppler run` reads its latest encrypted snapshot.
  > "encrypted secrets are written to this file after each successful fetch. secrets will be read from this file if subsequent connections are unsuccessful."
- Fact: The first start on a new machine needs a network connection to Doppler.
  > "encrypted secrets are written to this file after each successful fetch."

### [December 2020 Product Updates | Doppler Changelog](https://www.doppler.com/changes/december-2020-updates)

Retrieved 2026-11-27.

- Fact: The Doppler CLI stores its token in the macOS Keychain.
  > "Starting in v3.16.0, CLI Tokens are now securely stored in the OS Keyring instead of the file system for macOS, Linux, and Windows."

### [cli/pkg/configuration/config.go at master · DopplerHQ/cli](https://github.com/DopplerHQ/cli/blob/master/pkg/configuration/config.go)

Retrieved 2026-11-27.

- Fact: The Doppler CLI stores its token in the macOS Keychain.
  > "Saving %s to system keyring"

### [Environments | varlock](https://varlock.dev/guides/environments/)

Retrieved 2026-11-27.

- Fact: Varlock checks the environment variables passed in by `doppler run` against `.env.schema`.
  > "`varlock` will always treat environment variables passed into the process with the most precedence."
  > "you could rely entirely on process overrides to inject all config values, but still benefit from having a clear schema with validation applied to them."

### [Doppler Plugin | varlock](https://varlock.dev/plugins/doppler/)

Retrieved 2026-11-27.

- Fact: The Varlock Doppler plugin has no documented fallback when Doppler can't be reached. Its docs describe only a cache that keeps values for a set duration.
  > "cache resolved values for the specified duration"
