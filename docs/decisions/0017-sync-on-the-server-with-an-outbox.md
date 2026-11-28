# DR-0017: Sync on the Server, with an Outbox

**Status:** Accepted  
**Date:** 2026-11-27

## Context

A change made offline has to reach the server and every other device. Two devices can edit the same task while offline.

## Decision

* Sync is built on the existing Fastify server, through oRPC.
* Each change is saved on the device with an outbox entry. Each outbox entry is sent to the server in order.
* Conflicts are resolved per field. The change the server receives last wins.

Example: offline, the phone moves the "Stretch" task's due date to Friday, and the laptop changes its title to "Stretch hips." After both reconnect, the task is "Stretch hips," due Friday.

## Alternatives Considered

PowerSync, rejected:

* Its React Native SDK runs on op-sqlite, not expo-sqlite.
* Its React Native Web support is in beta.
* Self-hosting adds a separate service with its own storage database.

Whole-row last write wins, rejected:

* In the example, one of the two edits would be lost.

## Rationale

* No sync service is added.
* Server receipt order avoids device clocks, which drift.

## Consequences

* The sync code is built and maintained in this codebase.
* When two devices edit the same field, only the edit the server receives last is kept.

## Sources

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
