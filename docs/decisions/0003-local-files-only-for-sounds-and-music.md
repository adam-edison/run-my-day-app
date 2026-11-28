# DR-0003: Local Files Only for Sounds and Music

**Status:** Accepted  
**Date:** 2026-11-25

## Context

Run My Day plays sounds and music during routines, such as a start sound on a step or background music for a workout. Earlier plans connected cloud storage, such as Google Drive, OneDrive, and Dropbox, as music sources.

Reading all of a user's Google Drive files requires restricted OAuth scopes, such as `drive.readonly`. The non-sensitive `drive.file` scope reaches only the files that the user opens with the app or picks in the Google Picker. Google requires each app that accesses restricted scope data from or through a server to pass a third-party CASA security assessment every 12 months. The developer pays the assessor's fees. Streaming from cloud storage APIs adds latency. The first sound of each step must play within 100 milliseconds of its start mark.

## Decision

Play only files stored on the device that runs the day. Run My Day ships its own free sounds and music as samples. Users can play any sound or music file, as long as the file is on the device.

Each device has its own folders for sounds and music. Run My Day searches only those folders. A routine refers to a sound or song by its file, and each device resolves that reference to the closest match in its own folders by file name and metadata. In the browser, the files come from the user's local machine.

Run My Day suggests sync and download apps for getting files to and from Google Drive, OneDrive, and similar services.

## Alternatives Considered

- **Cloud storage connections:** Play files directly from Google Drive, OneDrive, Dropbox, and similar services. Rejected because of the security and content-rights exposure, streaming latency, and Google's scope rules. Reading a user's music folders in Google Drive needs restricted scopes and the yearly CASA assessment. The non-sensitive `drive.file` scope avoids the assessment, but the user must pick each file one at a time in the Google Picker.

## Rationale

- **Security and liability:** Run My Day never holds access to a user's cloud storage, and never hosts or streams content it does not own.
- **No CASA assessment:** No restricted Google scopes are needed.
- **Timing accuracy:** Local playback avoids streaming delays, so sounds can play on their start mark.

## Consequences

- Run My Day must locate files on each device: phones, tablets, and the browser's local machine.
- A user with several devices keeps a copy of their files on each one.
- Matching by file name and metadata can pick a different file than intended when two files are similar.
- Users rely on separate sync apps to move files between devices and cloud storage.
- Earlier plans for cloud music sources are dropped.

## Sources

### [Choose Google Drive API scopes](https://developers.google.com/workspace/drive/api/guides/api-specific-auth)

Retrieved 2026-11-27.

- Fact: Reading all of a user's Google Drive files requires restricted OAuth scopes, such as `drive.readonly`.
  > "Restricted scopes"
  > "https://www.googleapis.com/auth/drive.readonly"
  > "View and download all your Drive files."
- Fact: The non-sensitive `drive.file` scope reaches only the files that the user opens with the app or picks in the Google Picker.
  > "Create new Drive files, or modify existing files, that you open with an app or that the user shares with an app while using the Google Picker API or the app's file picker."
  > "Since drive.file is non-sensitive, it allows for a more streamlined verification process."

### [Restricted scope verification](https://developers.google.com/identity/protocols/oauth2/production-readiness/restricted-scope-verification)

Retrieved 2026-11-27.

- Fact: Google requires each app that accesses restricted scope data from or through a server to pass a third-party security assessment.
  > "Every app that requests access to Google users' restricted data and has the ability to access data from or through a third-party server must go through a security assessment from Google-empanelled security assessors."
- Fact: The assessment repeats every 12 months.
  > "apps must be reverified for compliance and complete a security assessment at least every 12 months after your assessor's Letter of Assessment (LOA) approval date."

### [Security Assessment - Google Cloud Platform Console Help](https://support.google.com/cloud/answer/13465431)

Retrieved 2026-11-27.

- Fact: The security assessment uses the CASA framework.
  > "To improve and standardize our security assessment process, we are leveraging the industry standard App Defense Alliance and its Cloud App Security Assessment framework (CASA)."
- Fact: The assessment repeats every year.
  > "All applications must be revalidated every year."

### [App verification | Google Health API](https://developers.google.com/health/app-verification)

Retrieved 2026-11-27.

- Fact: The developer pays the assessor's fees.
  > "The security assessment can take 2-3 weeks for tier-2 applications and 4-6 weeks for tier-3 applications to complete and involves fees payable to the third-party assessor, ranging from $500 to $4,500 USD, depending on your app's complexity."
