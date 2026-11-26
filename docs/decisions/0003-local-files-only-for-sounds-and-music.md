# DR-0003: Local Files Only for Sounds and Music

**Status:** Accepted  
**Date:** 2026-11-25

## Context

Run My Day plays sounds and music during routines, such as a start sound on a step or background music for a workout. Earlier plans connected cloud storage, such as Google Drive, OneDrive, and Dropbox, as music sources.

Reading a user's Google Drive files requires restricted OAuth scopes. Google requires every app with restricted scopes to pass a third-party CASA security assessment every 12 months, at the developer's cost. Streaming from cloud storage APIs also adds latency, which conflicts with the requirement that the first sound of each step plays within 100 milliseconds of its start mark.

## Decision

Play only files stored on the device that runs the day. Run My Day ships its own free sounds and music as samples. Users can play any sound or music file, as long as the file is on the device.

Each device has its own folders for sounds and music. Run My Day searches only those folders. A routine refers to a sound or song by its file, and each device resolves that reference to the closest match in its own folders by file name and metadata. In the browser, the files come from the user's local machine.

Run My Day suggests sync and download apps for getting files to and from Google Drive, OneDrive, and similar services.

## Alternatives Considered

- **Cloud storage connections:** Play files directly from Google Drive, OneDrive, Dropbox, and similar services. Rejected because of the yearly CASA assessment, the security and content-rights exposure, and streaming latency.

## Rationale

- **Security and liability:** Run My Day never holds access to a user's cloud storage, and never hosts or streams content it does not own.
- **No CASA assessment:** No restricted Google scopes are needed.
- **Timing accuracy:** Local playback avoids streaming delays, so sounds can play on their start mark.

## Consequences

- Run My Day must locate files on each device: phones, tablets, and the browser's local machine.
- A user with several devices keeps a copy of their files on each one.
- Matching by file name and metadata can pick a different file than intended when two files are similar.
- Users rely on separate sync apps to move files between devices and cloud storage.
- Earlier plans for cloud music sources, such as kivaro tickets KIV-48.1 to KIV-48.5, are dropped.

## References

- [Google Cloud: Security Assessment](https://support.google.com/cloud/answer/13465431)
- [Google Identity: Restricted scope verification](https://developers.google.com/identity/protocols/oauth2/production-readiness/restricted-scope-verification)
