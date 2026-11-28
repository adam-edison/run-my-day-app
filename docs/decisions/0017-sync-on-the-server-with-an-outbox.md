# DR-0017: Sync on the Server, with an Outbox

**Status:** Accepted  
**Date:** 2026-11-27

## Context

A change made offline has to reach the server and every other device. Two devices can edit the same task while offline. Later changes can depend on earlier ones. A server bug can make the server unable to apply a change.

## Decision

* Sync is built on the existing Fastify server, through oRPC.
* Each change is saved on the device with an outbox entry. Each outbox entry is sent to the server in order.
* Conflicts are resolved per field. The change the server receives last wins.

Example: offline, the phone moves the "Stretch" task's due date to Friday, and the laptop changes its title to "Stretch hips." After both reconnect, the task is "Stretch hips," due Friday.

Failed outbox entries:

* The device checks each change with the same validation as the server, from the shared core package, before the change is saved.
* When the server cannot be reached, or answers with a server error, the entry stays at the head of the outbox. The device retries it with exponential backoff.
* The server never drops an outbox entry and never sets one aside. Entries are applied only in order.
* When the server cannot apply an entry because of a bug, the device stops sending at that entry. The device keeps that entry and every later entry in its outbox, in order.
* The device keeps working as it does offline.
* When the device's app version is no longer supported, the server answers "update required." The device keeps its outbox and asks the user to update the app.
* The server records each blocked device and the error. The record holds no outbox entries.
* The server emails the operator about each blocked device, as recorded in DR-0028.
* The app tells the user that the administrator has been emailed about a sync problem. It tells the user not to clear the app's data or the browser's storage.
* When a new server version starts, the server tells each blocked device to retry: through SSE to an open app, and through a visible push notification to a closed app.
* When the retry succeeds, the rest of the outbox follows in order. The app tells the user that the problem is fixed and the changes have synced. The server deletes the blocked-device record.
* When the retry fails again, the device stays blocked.

Example: on a train, the phone makes 40 changes offline. Change 12, a rename of "Notes" to "Ideas," hits a bug. Changes 13 and 14 move "Ideas" into "Work" and delete "Work." The phone stops at change 12, shows the sync problem message, and the operator gets an email. After the fix is deployed, the phone gets a push notification, retries change 12, and changes 13 to 40 follow in order.

## Alternatives Considered

Dropping a rejected entry and continuing, rejected:

* The user's change is lost, and the user must make it again.
* After hours of offline changes, the user cannot remember every change to redo.
* A user who repeats the change hits the same bug again.

Example: a calendar app cannot complete a change, reverts it, and tells the user to try again. On a 3-hour train ride, the phone makes 40 changes offline, and several of them are dropped. The user cannot remember which changes to make again.

Setting a rejected entry aside on the server and replaying it later, rejected:

* Later entries can depend on the rejected one. A replay out of order can apply to nothing.
* Each repeated attempt by the user adds another set-aside entry.

Example: the phone renames "Notes" to "Ideas," moves "Ideas" into "Work," and deletes "Work." The rename is set aside, and the move and the delete are applied. When the rename is replayed, "Ideas" no longer exists.

PowerSync, rejected:

* Its React Native SDK runs on op-sqlite, not expo-sqlite.
* Its React Native Web support is in beta.
* Self-hosting adds a separate service with its own storage database.

Whole-row last write wins, rejected:

* In the example, one of the two edits would be lost.

## Rationale

* No sync service is added.
* Server receipt order avoids device clocks. Device clocks drift.
* Per-field resolution keeps both edits when two devices change different fields of the same task.
* Later entries can depend on earlier ones. Applying entries only in order keeps each entry pointed at data that exists.
* Stopping at a failed entry and keeping the outbox loses no change. After the fix, the kept entries sync without the user redoing anything.
* Keeping the app's data on a blocked device lets its changes sync after the fix.

## Consequences

* The sync code is built and maintained in this codebase.
* When two devices edit the same field, only the edit the server receives last is kept.
* While a device is blocked by a bug, its changes do not reach the server or the user's other devices.
* Clearing the app's data on a blocked device loses its unsynced changes.
* Replicache warns that errors that hold the queue "block synchronization and thus should be used carefully."

## Sources

### [Push Endpoint | Replicache](https://doc.replicache.dev/reference/server-push)

Retrieved 2026-11-28.

- Fact: Network and HTTP errors are retried with exponential backoff.
  > "Replicache will exponentially back off sending pushes in the case of both network level and HTTP level errors."
- Fact: Errors that hold the queue block synchronization.
  > "Temporary errors block synchronization and thus should be used carefully."

### [Handling write validation errors | PowerSync](https://docs.powersync.com/handling-writes/handling-write-validation-errors)

Retrieved 2026-11-28.

- Fact: Replaying a set-aside entry later can conflict with newer writes.
  > "If you apply a dead-lettered write later, newer writes from the same client may already be in your source database"

### [React Native & Expo SDK - PowerSync](https://docs.powersync.com/client-sdks/reference/react-native-and-expo)

Retrieved 2026-11-27.

- Fact: The PowerSync React Native SDK runs on op-sqlite.
  > "`@op-engineering/op-sqlite` is the SQLite library used by the PowerSync SDK on React Native."

### [React Native Web Support - PowerSync](https://docs.powersync.com/client-sdks/frameworks/react-native-web-support)

Retrieved 2026-11-27.

- Fact: PowerSync's React Native Web support is in beta.
  > "Support for React Native Web is available since versions 1.12.1 of the PowerSync React Native SDK and 1.8.0 if the JavaScript Web SDK, and is currently in a beta release."

### [Deployment Architecture - PowerSync](https://docs.powersync.com/maintenance-ops/self-hosting/deployment-architecture)

Retrieved 2026-11-27.

- Fact: Self-hosting PowerSync adds a separate service with its own storage database.
  > "One PowerSync container running in unified mode (API and replication): 1 vCPU, 512 MiB memory."
  > "A MongoDB or Postgres bucket storage database."
  > "You can use MongoDB or Postgres for bucket storage, independently of your source database:"

### [Clock drift - Wikipedia](https://en.wikipedia.org/wiki/Clock_drift)

Retrieved 2026-11-27.

- Fact: Device clocks drift.
  > "clock drift refers to several related phenomena where a clock does not run at exactly the same rate as a reference clock."
