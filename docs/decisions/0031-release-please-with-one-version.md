# DR-0031: release-please with One Version

**Status:** Draft  
**Date:** 2026-11-28

## Context

A new version of Run My Day must be cut and numbered. The app stores need build numbers.

Facts from earlier decisions:

* commitlint enforces Conventional Commits on every commit.
* A GitHub milestone groups the parent issues for a release, such as the MVP, as recorded in DR-0015.
* Playwright, Maestro, and hand checks run before a release, as recorded in DR-0009 and DR-0010.
* Expo Build builds the Android and iOS apps.
* The client, the core package, and the server live in one repository, as recorded in DR-0004 and DR-0013.

SemVer raises MAJOR for breaking changes, MINOR for features, and PATCH for fixes. Conventional Commits map onto SemVer: `fix` to PATCH, `feat` to MINOR, and `BREAKING CHANGE` to MAJOR.

App stores use two numbers. `version` is shown to users. `ios.buildNumber` and `android.versionCode` are build numbers. Google Play rejects a reused `versionCode`.

Example: the "Routines" milestone closes. The release pull request lists its `feat` commits. Merging it raises the version from `0.2.0` to `0.3.0` and tags `v0.3.0`.

## Decision

* release-please keeps an open release pull request that collects changes from Conventional Commits.
* Merging the release pull request updates `CHANGELOG.md`, bumps the version, and tags the commit.
* The repository has one SemVer version for the app and the server.
* The first version is `0.1.0`, tagged `v0.1.0`.
* The Expo `version` is set from the release version.
* Build numbers use Expo's `remote` version source with `autoIncrement` under the `production` build profile.

Example: a `fix` commit for the "Stretch" task lands on `main`. The release pull request proposes `0.3.1`. Merging it tags `v0.3.1`, and the next production build gets a higher build number from Expo.

## Alternatives Considered

release-please with separate versions for the app and the server, rejected:

* Two numbers must be tracked, such as `app-v1.2.0` and `server-v1.4.0`.
* The app and the server must stay compatible across versions.

semantic-release, rejected:

* Every merge with a `feat` or `fix` commit can publish a release. A phone app release needs hand checks first, as recorded in DR-0010.
* The documentation site read is marked as discontinued.

Changesets, rejected:

* Each pull request adds a file that states the version bump and a changelog line. This is a second record of each change next to the Conventional Commit.

## Rationale

* The human decides when to release by merging one pull request. This fits review of every change, as recorded in DR-0013.
* release-please writes the changelog and the Git tag.
* release-please reads the same Conventional Commits that commitlint already enforces.
* Expo recommends the `remote` version source from EAS CLI version `12.0.0`.

## Consequences

* release-please does not publish anything. The app build and the server deploy are separate steps.
* A server-only change raises the app's version too.
* SemVer requires a declared public API.
* `autoIncrement` does not change the user-facing version.
* No official source supports one version over separate versions for this case.
* No official source states that semantic-release conflicts with hand checks before a release.
* No official source states that Changesets duplicates Conventional Commits.
* No Apple source on `CFBundleVersion` or `CFBundleShortVersionString` is quoted. No Apple format rules are recorded.

## Open Questions

1. One version for the app and server, or one for each? Example: a server-only fix to Google Calendar push would raise the app's version from `0.4.0` to `0.4.1` under one version, though the app did not change.
2. Does a release happen when the release pull request is merged, or on a fixed rhythm? Example: release `0.3.0` when the "Routines" milestone closes.
3. Does `0.x` end with the MVP? Example: the MVP is tagged `v1.0.0`.
4. Does an over-the-air JavaScript update get its own PATCH version? Example: a text fix shipped through Expo Update raises `1.2.0` to `1.2.1` with no new store build.

## Sources

### [Semantic Versioning 2.0.0](https://semver.org/)

Retrieved 2026-11-27.

- Fact: a breaking change raises MAJOR.
  > "MAJOR version when you make incompatible API changes"
- Fact: SemVer requires a declared public API.
  > "Software using Semantic Versioning MUST declare a public API."
- Fact: `0.y.z` is for initial development.
  > "Major version zero (0.y.z) is for initial development. Anything MAY change at any time."

### [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/)

Retrieved 2026-11-27.

- Fact: `fix` maps to PATCH.
  > "`fix` type commits should be translated to `PATCH` releases."
- Fact: `feat` maps to MINOR.
  > "`feat` type commits should be translated to `MINOR` releases."
- Fact: a breaking change maps to MAJOR.
  > "Commits with `BREAKING CHANGE` in the commits, regardless of type, should be translated to `MAJOR` releases."

### [Release Please](https://github.com/googleapis/release-please)

Retrieved 2026-11-27.

- Fact: release-please keeps a release pull request.
  > "Rather than continuously releasing what's landed to your default branch, release-please maintains Release PRs:"
- Fact: merging the release pull request tags the commit.
  > "Tags the commit with the version number"
- Fact: it supports several packages in one repository.
  > "Release Please also supports releasing multiple artifacts from the same repository."
- Fact: it does not publish.
  > "It does not handle publication to package managers or handle complex branch management."

### [Release Please Action](https://github.com/googleapis/release-please-action)

Retrieved 2026-11-27.

- Fact: the action runs release-please in GitHub Actions.
  > "Automate releases with Conventional Commit Messages."

### [semantic-release](https://semantic-release.gitbook.io/semantic-release/)

Retrieved 2026-11-27.

- Fact: it runs after every successful build on the release branch.
  > "**semantic-release** is meant to be executed on the CI environment after every successful build on the release branch."
- Fact: this documentation site is discontinued.
  > "This documentation site is no longer maintained and has been discontinued."

### [changesets](https://github.com/changesets/changesets)

Retrieved 2026-11-27.

- Fact: Changesets focuses on repositories with several packages.
  > "A tool to manage versioning and changelogs with a focus on monorepos"

### [App version management (Expo)](https://docs.expo.dev/build-reference/app-versions/)

Retrieved 2026-11-27.

- Fact: `version` is shown in the stores.
  > "The user-facing version visible in stores."
- Fact: the `remote` version source is recommended.
  > "The `remote` version source is the recommended behavior from EAS CLI version `12.0.0`."
- Fact: `autoIncrement` raises build numbers.
  > "Then, under the `production` build profile, you can set the `autoIncrement` property to `true`."
- Fact: `autoIncrement` does not change the user-facing version.
  > "`autoIncrement` does not support the `version` option."

### [Version your app (Android)](https://developer.android.com/studio/publish/versioning)

Retrieved 2026-11-27.

- Fact: each release needs a greater `versionCode`.
  > "make sure that each successive release of your app uses a greater value."
- Fact: `versionName` is the number shown to users.
  > "A string used as the version number shown to users."

Apple's `CFBundleVersion` and `CFBundleShortVersionString` pages did not render, so no Apple format rules are quoted.
