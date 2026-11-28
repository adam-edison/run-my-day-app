# DR-0034: STRIDE Threat Model, with ASVS and MASVS Reviews

**Status:** Draft  
**Date:** 2026-11-28

## Context

The repository is public under the MIT license. Secrets come from Doppler and are checked by Varlock, as recorded in DR-0011. No `.env` file with secret values exists in any worktree. Betterleaks scans for leaked secrets. The server stores each user's Google refresh token and receives Google push notifications on a public HTTPS address, as recorded in DR-0021. Clients store data offline in expo-sqlite. The web build runs in desktop Chromium. @fastify/rate-limit is in the technology stack.

OWASP's threat modeling method answers four questions: what is being built, what can go wrong, what is done about it, and whether that was good enough. STRIDE is a list of six threat types used as prompts:

* Spoofing: pretending to be someone.
* Tampering: changing data.
* Repudiation: denying an action.
* Information disclosure: leaking data.
* Denial of service: making the system unavailable.
* Elevation of privilege: gaining extra rights.

OWASP facts:

* STRIDE is a mature and popular technique.
* Threat modeling is ideally done early, in the design phase.
* Threat modeling is repeated as the system changes.
* ASVS (Application Security Verification Standard) lists security requirements for web apps and APIs. Version 5.0.0 was published on 30 May 2025.
* MASVS (Mobile Application Security Verification Standard) has groups such as MASVS-STORAGE and MASVS-AUTH.
* MASVS lists sensitive data stored unencrypted in private storage as weakness MASWE-0001.

Main threats for Run My Day:

* Stolen device with offline data. The expo-sqlite database holds the schedule. The session sits in expo-secure-store.
* Stolen Google refresh token. The server holds a token that reads the user's calendar. A refresh token for a confidential client only works for the client it was issued to. A leaked token together with the Google client secret would still work.
* Sync endpoint abuse. Someone floods or replays outbox uploads. Rate limiting and per-user checks address this.
* Webhook spoofing. Someone posts fake notifications to the Google push address. Google sends a channel token in the `X-Goog-Channel-Token` header, and the server can check it. A fake notification only causes an extra incremental sync.
* XSS (cross-site scripting) in the web build. An injected script could act as the signed-in user. A Content Security Policy and React's escaping reduce this.
* Secrets in the public repository. Doppler, Varlock, and Betterleaks address this, as recorded in DR-0011.

Example: an unlocked phone is stolen. The thief opens Run My Day and sees tomorrow's appointments from the offline database.

## Decision

Threat model:

* The threat model is one document that answers OWASP's four questions.
* STRIDE types serve as prompts for the question "what can go wrong".
* The document is written now, in the design phase.
* The document is updated as the system changes.

Security review:

* A security review runs before the paid hosted version takes other people's data.
* ASVS serves as the checklist for the Fastify server and the web build.
* MASVS serves as the checklist for the Android and iOS apps.
* Each OpenSpec change that touches authentication, sync, or Google tokens includes a short security review.

Example: the change that adds the Google push address adds a "fake push notification" row to the threat model. The row records that the server checks the `X-Goog-Channel-Token` header.

## Alternatives Considered

ASVS as the only method, rejected:

* ASVS is a checklist of requirements, not a method for finding threats in the design phase.

MASVS as the only method, rejected:

* MASVS covers only the phone apps.

## Rationale

* OWASP advises threat modeling early, in the design phase, and updating it as the system changes.
* STRIDE is mature and widely used.
* ASVS and MASVS give stable, published checklists for a later review.

## Consequences

* STRIDE produces a list of threats, not a list of tests to pass.
* The choice of an ASVS level is not verified.
* No official source states when the security review should run. The timing before the hosted version and in each OpenSpec change is own reasoning.
* The threats listed for Run My Day are own reasoning, apart from the facts with a source.
* No official source states that MASVS covers only the phone apps.
* Whether the offline SQLite file is encrypted is open.
* Encrypting Google refresh tokens in the database is an option, not a decision.

## Open Questions

1. Is the offline SQLite database encrypted on phones? Example: a stolen unlocked phone shows tomorrow's appointments.
2. Are Google refresh tokens encrypted in PostgreSQL? Example: a database backup leaks, and the tokens in it cannot be used without a separate key.
3. Which ASVS level is used for the review before the hosted version? Example: the Fastify server is checked against one ASVS level before the hosted version takes other people's data.
4. Who runs the security review? Example: you with Claude's security-review skill, or an outside reviewer before launch.

## Sources

### [Threat Modeling Cheat Sheet | OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)

Retrieved 2026-11-27.

- Fact: STRIDE is a mature method.
  > "a mature and popular threat modeling technique and mnemonic originally developed by Microsoft employees."
- Fact: Threat modeling is best done in the design phase.
  > "is ideally performed early in the SDLC, such as during the design phase."
- Fact: It is repeated as the system changes.
  > "it is not something that is performed once and never again."
- Fact: The first two of the four questions.
  > "What are we working on?"
  > "What can go wrong?"

### [OWASP ASVS](https://asvs.owasp.org/)

Retrieved 2026-11-27.

- Fact: Version 5.0.0 is the current stable release.
  > "Published 30 May 2025"

### [OWASP MASVS](https://mas.owasp.org/MASVS/)

Retrieved 2026-11-27.

- Fact: MASVS has storage and authentication groups.
  > "MASVS-STORAGE: Storage"
  > "MASVS-AUTH: Authentication and Authorization"

### [MASVS-STORAGE | OWASP](https://mas.owasp.org/MASVS/05-MASVS-STORAGE/)

Retrieved 2026-11-27.

- Fact: Unencrypted on-device data is a listed weakness.
  > "MASWE-0001: Sensitive Data Stored Unencrypted in Private Storage"

### [Push notifications | Google Calendar](https://developers.google.com/workspace/calendar/api/guides/push)

Retrieved 2026-11-27.

- Fact: A channel token lets the server check that a notification is genuine.
  > "You can use the token to verify that each incoming message is for a channel that your application created"
  > "The token is included in the `X-Goog-Channel-Token` HTTP header in every notification message that your application receives for this channel."

### [RFC 9700: Best Current Practice for OAuth 2.0 Security](https://datatracker.ietf.org/doc/html/rfc9700)

Retrieved 2026-11-27.

- Fact: A confidential client's refresh token works only for that client.
  > "refresh tokens for confidential clients can only be used by the client for which they were issued."

### [Expo Integration | Better Auth](https://www.better-auth.com/docs/integrations/expo)

Retrieved 2026-11-27.

- Fact: Sessions on phones are stored in SecureStore.
  > "On native, the session data will be cached in SecureStore."
