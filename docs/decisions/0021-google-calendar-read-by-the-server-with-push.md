# DR-0021: Google Calendar, Read by the Server with Push

**Status:** Accepted  
**Date:** 2026-11-27

## Context

Run My Day reads the calendar and never changes it. It reads events from 24 hours back to 14 days ahead.

Requirement: Run My Day reacts to a calendar change within 1 minute of the edit.

A locked phone app, or a closed browser tab, runs no code. The hosted version must serve thousands of users.

Google Calendar API limits:

* 10,000 requests per minute for each Google Cloud project, shared by every user of that project.
* 600 requests per minute for each user.
* Google advises against checking every calendar every minute, because that uses up the per-minute limit.

Example: at 10:05 the Workout event's end moves from 10:00 to 10:30. The phone is locked, and the laptop's tab is closed. The Workout block runs again by 10:06.

## Decision

Calendars:

* The first version supports Google Calendar only.
* Outlook and iCloud are added later.

Reading:

* The server reads the calendar. Devices do not call Google.
* Each user connects Google Calendar once, on Google's consent screen. The server stores that user's refresh token.
* Calendar events reach each device through the Sync actor, like any other change.

Changes:

* Google sends a push notification to the server when a watched calendar changes.
* The server renews each watch before it expires.
* The server also checks each calendar every 5 minutes, in case a push notification is lost. A server environment variable sets the interval.
* The server checks the user's calendars when the app is opened.
* Each check uses incremental sync, which returns only the changes since the last check.

Self-hosted version:

* Push is used in the self-hosted version too.
* The self-hoster creates their own Google Cloud project, so their requests count only against their own limits.
* The server needs a public HTTPS address for Google's push notifications, such as one from Tailscale Funnel.

Example: at 10:05 the Workout event is moved. Google sends a push notification to the server. The server reads the change, and the Sync actor brings it to the laptop and the phone by 10:06. If the push notification is lost, the 5-minute check finds the change by 10:10.

## Alternatives Considered

Checking every calendar every minute, rejected:

* 5,000 users with 2 calendars each would use all 10,000 requests per minute.
* Google advises against it.

Each device reads the calendar, rejected:

* A locked phone or a closed tab runs no code, so a moved event would be missed.
* Requests from devices most likely count against the same project limit.

Push for the hosted version and checking every minute for the self-hosted version, rejected:

* Two ways to read changes would be built and tested.

## Rationale

* Push uses requests only when a calendar changes.
* The server reacts to changes while every app is closed.
* One way of reading changes serves the hosted and self-hosted versions.

## Consequences

* Google's push notifications are not 100% reliable. A lost push notification delays a change until the next check, 5 minutes at most by default.
* Reading Google Calendar events is a sensitive scope. Google must verify the hosted app before more than 100 users can connect.
* The self-hosted version needs a public HTTPS address, which the hosting and remote access decisions must provide.
* A Google Cloud project in Testing status gets refresh tokens that expire after 7 days. A self-hoster's project in Personal mode needs no verification. Whether its refresh tokens last longer than 7 days is not confirmed.

## Sources

### [Manage quotas | Google Calendar](https://developers.google.com/workspace/calendar/api/guides/quota)

Retrieved 2026-11-27.

- Fact: Each Google Cloud project can make 10,000 requests per minute.
  > "Per minute per project | 10,000 requests"
  > "The maximum number of requests your Cloud project can make in one minute."
- Fact: Each user can make 600 requests per minute.
  > "Per minute per user per project | 600 requests"
- Fact: Google advises against checking every calendar every minute.
  > "Avoid repeatedly polling every calendar of interest, which quickly consumes your quota."
  > "if your application has 5,000 users and polls each user's calendar once a minute, it requires a per-minute quota of at least 5,000 before doing any work."
- Fact: Push notifications use requests only when something changes.
  > "Push notifications take more effort to set up, but they use your quota far more efficiently and provide a better user experience."

### [Push notifications | Google Calendar](https://developers.google.com/workspace/calendar/api/guides/push)

Retrieved 2026-11-27.

- Fact: Google sends a push notification to the server when a watched calendar changes.
  > "Whenever a watched resource changes, the Google Calendar API notifies your application."
  > "Each notification channel is associated both with a particular user and a particular resource (or set of resources)."
- Fact: The address for push notifications must use HTTPS.
  > "This is your webhook callback URL, and it must use HTTPS."
- Fact: Push notifications are not 100% reliable.
  > "Notifications are not 100% reliable."
- Fact: The server must renew each watch before it expires.
  > "Currently, there's no automatic way to renew a notification channel."

### [Synchronize resources efficiently | Google Calendar](https://developers.google.com/workspace/calendar/api/guides/sync)

Retrieved 2026-11-27.

- Fact: Incremental sync returns only the changes since the last check.
  > "Incremental sync allows you to retrieve all the resources that have been modified since the last sync request."

### [Using OAuth 2.0 for Web Server Applications](https://developers.google.com/identity/protocols/oauth2/web-server)

Retrieved 2026-11-27.

- Fact: The server reads the calendar with the user's consent, while the user is not using the app.
  > "A properly authorized web server application can access an API while the user interacts with the application or after the user has left the application."
  > "This value instructs the Google authorization server to return a refresh token *and* an access token"

### [Using OAuth 2.0 to Access Google APIs](https://developers.google.com/identity/protocols/oauth2)

Retrieved 2026-11-27.

- Fact: A Google Cloud project in Testing status gets refresh tokens that expire after 7 days.
  > "A Google Cloud Platform project with an OAuth consent screen configured for an external user type and a publishing status of "Testing" is issued a refresh token expiring in 7 days"

### [Sensitive scope verification](https://developers.google.com/identity/protocols/oauth2/production-readiness/sensitive-scope-verification)

Retrieved 2026-11-27.

- Fact: Reading Google Calendar events is a sensitive scope.
  > "Examples of sensitive scopes include reading events stored in Google Calendar"

### [Unverified apps - Google Cloud Platform Console Help](https://support.google.com/cloud/answer/7454865)

Retrieved 2026-11-27.

- Fact: An unverified app is limited to 100 new users.
  > "will be limited to 100 new users until it is verified."
- Fact: An app in Personal mode needs no verification.
  > "does not display the unverified app screen, and does not need to go through verification."

### [Tailscale Funnel](https://tailscale.com/kb/1223/funnel)

Retrieved 2026-11-27.

- Fact: Tailscale Funnel gives a home server a public HTTPS address.
  > "exposes a local resource to the internet through a unique Funnel URL."
  > "Funnel only works over TLS-encrypted connections."
