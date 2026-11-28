# DR-0033: Email and Password Sign-In, with Google Calendar Connected Separately

**Status:** Draft  
**Date:** 2026-11-28

## Context

Better Auth is the authentication library. The server runs Fastify with oRPC, Drizzle, and PostgreSQL. Clients work offline. They store data in expo-sqlite and queue changes in an outbox. The server reads Google Calendar and stores each user's refresh token, as recorded in DR-0021. The first version is single-user and self-hosted. A paid multi-user hosted version comes later.

SSE (server-sent events) is the one-way push stream from the server to a device.

Better Auth facts:

* On phones, the Expo plugin stores the session data and cookies in expo-secure-store.
* The Expo client adds the cookie to auth requests itself.
* Custom requests must read the session cookie from SecureStore and add it to the request headers. This applies to oRPC calls and the SSE stream on phones.
* On phones, the session is cached in SecureStore. The app can read the cached session without a network request.
* Sessions expire after 7 days by default.
* Each use after `updateAge` moves the expiry 7 days forward. The docs' example sets `updateAge` to 1 day.
* With the cookie cache turned on, a revoked session may keep working until the cache expires.
* The docs do not describe offline behavior.

A Google Cloud project in Testing status issues refresh tokens that expire after 7 days.

Example: the phone is offline for 8 days while the user ticks off routine steps. Back online, the server rejects the session. The queued ticks must still reach the server.

## Decision

Sign-in:

* The first version offers email and password sign-in.
* Passkeys are added later.

Google Calendar:

* Connecting Google Calendar is a separate step after sign-in.
* The step asks Google for the Calendar read scope.

Sessions:

* Phones use Better Auth's Expo plugin with expo-secure-store.
* oRPC calls and the SSE stream on phones add the session cookie from SecureStore to the request headers.
* The web build uses same-site cookies.
* Sessions use the default 7-day expiry with a 1-day `updateAge`.

Offline expiry:

* While offline, the app keeps working on local data.
* The outbox keeps queued changes.
* When the device is back online and the server rejects the session, the app shows the sign-in screen.
* After sign-in, the outbox sends its queued changes.

Example: the user opens Run My Day on a new laptop and types an email and password. In settings, the user taps "Connect Google Calendar", and the Workout event appears.

## Alternatives Considered

Google sign-in only, with the Calendar scope added at sign-in or later, rejected:

* A self-hoster cannot sign in until their Google Cloud project works.
* A Google project in Testing status issues refresh tokens that expire after 7 days.
* Outlook and iCloud calendars come later, as recorded in DR-0021. Sign-in would stay tied to Google after those arrive.

Email and password plus passkeys in the first version, rejected:

* Passkeys on Expo native apps need extra setup.

Magic links, rejected:

* Magic links need an email sender. Self-hosters would have to configure one.

## Rationale

* Sign-in does not depend on a Google Cloud project. A self-hoster can sign in before setting up Google.
* Better Auth stores sessions on phones in expo-secure-store and sends them on each request.
* A device used at least weekly stays signed in with the 7-day sliding expiry.
* A separate calendar step leaves room for Outlook and iCloud.

## Consequences

* No official source supports email and password sign-in with a separate calendar step for this case.
* Passwords can be reused or phished.
* No official source states that passkeys resist phishing.
* The Better Auth docs for passkey setup on Expo native apps are not yet read.
* No official source states that an SSE stream from the same site sends the session cookie in the web build.
* No official source describes the offline expiry behavior.
* A device unused for more than 7 days asks for sign-in again.
* If the cookie cache is turned on, a revoked session may keep working until the cache expires.
* Better Auth has 30.9k GitHub stars, 8,169 commits, and an MIT license. Release dates were not visible on the page. The maintenance pace is not verified.

## Open Questions

1. Is signing out other devices needed in the first version? Example: you lose the phone and sign it out from the laptop.

## Sources

### [Expo Integration | Better Auth](https://www.better-auth.com/docs/integrations/expo)

Retrieved 2026-11-27.

- Fact: expo-secure-store stores the session data and cookies.
  > "This is used to store the session data and cookies securely."
- Fact: The Expo client adds cookies to auth requests itself.
  > "stores cookies securely and automatically adds them to the headers of your auth requests."
- Fact: Custom requests must add the cookie by hand.
  > "retrieve the session cookie from `SecureStore` and manually add it to your request headers."
- Fact: The session is cached on the device.
  > "On native, the session data will be cached in SecureStore."

### [Session Management | Better Auth](https://www.better-auth.com/docs/concepts/session-management)

Retrieved 2026-11-27.

- Fact: Sessions expire after 7 days by default.
  > "The session expires after 7 days by default."
- Fact: Each use after `updateAge` extends the session.
  > "the session expiration is updated to the current time plus the `expiresIn` value."
- Fact: With the cookie cache, a revoked session can keep working for a while.
  > "revoked sessions may remain active on other devices until the cookie cache expires."

### [better-auth/better-auth | GitHub](https://github.com/better-auth/better-auth)

Retrieved 2026-11-27.

- Fact: The project describes itself as a full authentication framework under the MIT license.
  > "The most comprehensive authentication framework"

### [Using OAuth 2.0 to Access Google APIs](https://developers.google.com/identity/protocols/oauth2)

Retrieved 2026-11-27.

- Fact: A Testing project gets refresh tokens that expire in 7 days.
  > "A Google Cloud Platform project with an OAuth consent screen configured for an external user type and a publishing status of \"Testing\" is issued a refresh token expiring in 7 days"
