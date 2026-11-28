# DR-0047: Betterleaks and GitHub Secret Scanning

**Status:** Draft  
**Date:** 2026-11-28

## Context

The repository is public. Secrets must stay outside the repository, as recorded in DR-0001.

Every secret comes from Doppler. No `.env` file with secret values exists in any worktree, as recorded in DR-0011.

Betterleaks:

* Betterleaks is a maintained secret scanner, MIT licensed, with about 3,100 GitHub stars.
* It is made by the people who made Gitleaks, including the original author.
* It scans files and git history.
* The latest stable release is v1.10.1, from 2026-11-12.
* Version 2 is in release candidates. v2.0.0-rc.16 came out on 2026-11-27.
* The `main` branch holds v2. Package managers may still install v1 until v2 is stable.

GitHub secret scanning runs for free on public repositories and scans the whole git history. Push protection for the user account is on by default. It blocks pushes of secrets to public repositories.

Example: a Google OAuth client secret is pasted into a test file by mistake. Betterleaks finds it on the machine. If it is missed there, GitHub push protection blocks the push.

## Decision

* Betterleaks scans for secrets, pinned to v1.10.1 until v2 is stable.
* GitHub secret scanning is turned on for the repository.
* GitHub push protection is turned on for the repository.

## Alternatives Considered

Gitleaks, rejected:

* Gitleaks receives only security patches.
* The Gitleaks author has stopped adding features to Gitleaks and moved to Betterleaks.

GitHub secret scanning and push protection alone, rejected:

* It runs on GitHub, so it does not check a commit before it leaves the machine.

## Rationale

* Betterleaks is under active development by the Gitleaks authors.
* Betterleaks can run on the machine before a push, which stops a secret before it reaches the public repository.
* GitHub secret scanning is free for public repositories.

## Consequences

* Version 2 of Betterleaks may change commands or config. The pin to v1.10.1 is not verified by an official source.
* The Betterleaks repository has a `.pre-commit-hooks.yaml` file, but its README does not document hook setup.
* The Betterleaks README does not document a GitHub Action.
* No official source states that GitHub scanning misses a commit before it leaves the machine. This point is not verified.
* No official source states that using both Betterleaks and GitHub scanning is better than one. This point is not verified.

## Open Questions

* Should Betterleaks run in a git hook, in CI, or both? Example: in a git hook, a commit that adds a Doppler token is stopped on the laptop. In CI only, the token is found after the push to the public repository.

## Sources

### [betterleaks | GitHub](https://github.com/betterleaks/betterleaks)

Retrieved 2026-11-27.

- Fact: Betterleaks is a secret scanner.
  > "a configurable, fast, and thorough secrets scanner."
- Fact: It is made by the Gitleaks authors.
  > "is maintained by the folks who made Gitleaks, including the original author."
- Fact: `main` holds version 2.
  > "The `main` branch is for v2 development. V1 maintenance and documentation live on the `v1.x` branch"

### [GitHub API: betterleaks repository and releases](https://api.github.com/repos/betterleaks/betterleaks/releases)

Retrieved 2026-11-27.

- Fact: The repository is MIT licensed, active, and not archived.
  > "{\"archived\":false,\"description\":\"Find leaked secrets everywhere.\",\"license\":\"MIT\",\"pushed_at\":\"2026-11-27T21:06:17Z\",\"stargazers_count\":3136}"
- Fact: The latest stable release is v1.10.1.
  > "{\"published_at\":\"2026-11-12T16:40:13Z\",\"tag_name\":\"v1.10.1\"}"
- Fact: The latest release candidate is v2.0.0-rc.16.
  > "{\"name\":\"v2.0.0-rc.16\",\"published_at\":\"2026-11-27T19:57:19Z\",\"tag_name\":\"v2.0.0-rc.16\"}"

### [gitleaks | GitHub](https://github.com/gitleaks/gitleaks)

Retrieved 2026-11-27.

- Fact: Gitleaks gets no new features and only security patches.
  > "Gitleaks is feature complete. I'm not merging new features into Gitleaks."
  > "Future releases will be security patches only."
- Fact: Its author moved to Betterleaks.
  > "shifting my focus to Betterleaks"

### [About secret scanning | GitHub Docs](https://docs.github.com/en/code-security/secret-scanning/introduction/about-secret-scanning)

Retrieved 2026-11-27.

- Fact: Secret scanning is free on public repositories.
  > "Public repositories: Secret scanning runs automatically for free."
- Fact: It scans the whole git history.
  > "scans your entire Git history on all branches of your repository for hardcoded credentials."

### [About push protection | GitHub Docs](https://docs.github.com/en/code-security/secret-scanning/introduction/about-push-protection)

Retrieved 2026-11-27.

- Fact: Push protection for the user account is on by default.
  > "Is enabled by default"
  > "Stops you from pushing secrets to public repositories on GitHub."
