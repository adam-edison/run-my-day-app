# DR-0028: Pino Logs and Optional Sentry

**Status:** Accepted  
**Date:** 2026-11-28

## Context

The server writes a Pino log. Errors from the server and the apps can go to Sentry. Some conditions must alert the operator. A self-hoster's install must not send personal data to a third party without consent.

Facts from earlier decisions:

* The technology stack lists Pino for structured JSON logging on the server, with redaction and request-correlated logs through Fastify.
* The technology stack lists Sentry for error tracking on web, mobile, and backend.
* The repository is public under the MIT license, as recorded in DR-0001.
* Sync runs on the Fastify server through oRPC, with an outbox on each device, as recorded in DR-0017.
* The server renews each Google Calendar watch before it expires, and checks each calendar every 5 minutes as a backup, as recorded in DR-0021.
* Every secret is an environment variable from Doppler or a local file, checked by Varlock, as recorded in DR-0011 and DR-0024.

The DSN is the Sentry project address that the SDK sends errors to.

Example: a self-hoster installs Run My Day and sets no `SENTRY_DSN`. A crash in the "Morning" block run is written to the Pino log and sent nowhere else.

## Decision

Sentry:

* Sentry is optional.
* The server reads the DSN from an environment variable such as `SENTRY_DSN`.
* The app build reads the DSN from a build-time variable.
* No DSN is set by default. With no DSN, nothing is sent.
* The project's own deployment sets the project's DSN.
* A self-hoster may set the DSN of their own Sentry account.
* Fastify uses the Sentry Node SDK.
* The Expo app uses `@sentry/react-native`.

Logging and redaction:

* Pino's `redact` option removes the `authorization` header, cookies, session tokens, Google access and refresh tokens, and passwords.
* The log holds no task titles, step text, or calendar event titles.
* Sentry reports follow the same rule. A report holds IDs, such as the task ID, and never titles or step text.

Log levels:

* `error` is for failures that need action, such as a failed watch renewal.
* `warn` is for retried failures, such as a Google request that succeeded on retry.
* `info` writes one line per request and per sync batch, with user ID and request ID only.
* `debug` is off in production.

Alert conditions:

* Any new server error in Sentry.
* A calendar watch renewal fails, or a watch expires without renewal.
* The 5-minute backup calendar check misses its schedule or fails. Sentry Crons watches this check.
* No new database dump exists for 2 hours, or a dump check fails. The server reports this error itself, as recorded in DR-0027.
* A device is blocked because the server cannot apply an outbox entry, as recorded in DR-0017. The alert fires on the first such entry. Network failures and server errors that are retried raise no alert.
* Alerts reach the operator by email.

Example: the watch on the Workout calendar fails to renew at 03:00. The server logs an `error` line, and Sentry emails an alert.

## Alternatives Considered

Self-hosters report to the project's Sentry by default, rejected:

* The free plan's 5,000 errors would be shared by every installation.
* Error reports may contain personal data from people who never agreed to send it. OWASP lists personal data among the data to exclude from logs.
* The DSN would ship in the public code.

No Sentry, logs only, rejected:

* Errors on phones and in browsers never reach the server log.

## Rationale

* With no DSN, the SDK sends no events. A self-hoster's data never leaves their server by default.
* The free Developer plan covers one user and 5,000 errors. This fits one developer.
* Config in environment variables keeps credentials out of the public code.
* OWASP lists session values, access tokens, passwords, and personal data as data to exclude from logs.
* Sentry also scrubs data on its own servers. Advanced Data Scrubbing can redact more before data is saved.
* The Node SDK instruments Fastify and skips errors with 3xx and 4xx status codes.
* `@sentry/react-native` uploads source maps during the release build.
* Sentry's Expo SDK captures errors on phones and in browsers once `Sentry.init()` is called.
* Every Sentry plan includes one cron monitor. That monitor covers the 5-minute backup calendar check.
* A missed check-in also shows that the whole server is down.

## Consequences

* A self-hoster who sets no DSN has no error reports. They rely on the Pino log only.
* Self-hosted Sentry needs at least 16 GB of RAM with 16 GB of swap. This is heavy for one user.
* No official source states the log levels for this case.
* No official source states the alert conditions for this case. Sentry Alerts act when issues match rules, and Sentry Crons watches scheduled jobs.
* No official source states that a DSN in public code is a problem.
* Sentry sends some notifications by email by default. The pages read do not state whether alert rules send email by default, or whether email alerts are on the free Developer plan.

## Sources

### [Sentry Pricing documentation](https://docs.sentry.io/pricing/)

Retrieved 2026-11-28.

- Fact: Every Sentry plan includes one cron monitor.
  > "All Sentry plans include one cron monitor and one uptime monitor. Additional monitors can only be purchased with your PAYG budget."

### [Notifications | Sentry](https://docs.sentry.io/product/notifications/)

Retrieved 2026-11-28.

- Fact: Sentry sends some notifications by email by default.
  > "By default, Sentry sends these notifications by email to members who are subscribed to the issue"

### [Job Monitoring | Sentry Crons](https://docs.sentry.io/product/monitors-and-alerts/monitors/crons/job-monitoring/)

Retrieved 2026-11-28.

- Fact: Sentry Crons can alert when a scheduled job does not run.
  > "**Missed check-in:** The job didn't execute in the predicted timeframe or frequency."

### [Sentry Pricing](https://sentry.io/pricing/)

Retrieved 2026-11-27.

- Fact: The free Developer plan includes one user.
  > "1 User"
- Fact: The free Developer plan includes 5,000 errors.
  > "5k errors"

### [Fastify | Sentry for Node.js](https://docs.sentry.io/platforms/javascript/guides/fastify/)

Retrieved 2026-11-27.

- Fact: Fastify needs no separate Sentry SDK.
  > "Fastify doesn't need its own Sentry SDK."
- Fact: The runtime SDK instruments Fastify.
  > "Set up Sentry for the runtime your app runs on, and the SDK instruments Fastify automatically."
- Fact: Errors with 3xx and 4xx status codes are skipped.
  > "Errors with a 3xx or 4xx status code are skipped."

### [Expo | Sentry for React Native](https://docs.sentry.io/platforms/react-native/manual-setup/expo/)

Retrieved 2026-11-27.

- Fact: Expo uses the React Native SDK.
  > "The same `@sentry/react-native` SDK powers bare React Native and Expo apps."
- Fact: Error monitoring starts when the SDK is initialized.
  > "**Error Monitoring** starts working as soon as you call `Sentry.init()`."
- Fact: Source maps are uploaded during the release build.
  > "Source maps for the `Release` version of your application are uploaded automatically during the native application build."

### [Configuration Options | Sentry for Node.js](https://docs.sentry.io/platforms/javascript/guides/node/configuration/options/)

Retrieved 2026-11-27.

- Fact: With no DSN, the SDK sends nothing.
  > "If this is not set, the SDK will not send any events."

### [Scrubbing Sensitive Data | Sentry](https://docs.sentry.io/security-legal-pii/scrubbing/)

Retrieved 2026-11-27.

- Fact: Sentry can redact data on its servers before saving it.
  > "Advanced Data Scrubbing as an alternative way to redact sensitive information just before it is saved in Sentry."

### [Alerts | Sentry](https://docs.sentry.io/product/alerts/)

Retrieved 2026-11-27.

- Fact: Sentry alerts act when issues match rules.
  > "Sentry's **Alerts** take action when issues in your organization match pre-defined rules."

### [Crons | Sentry](https://docs.sentry.io/product/crons/)

Retrieved 2026-11-27.

- Fact: Sentry Crons monitors scheduled jobs.
  > "Sentry's Cron Monitoring allows you to monitor the uptime and performance of any scheduled, recurring job in Sentry."

### [Self-Hosted Sentry](https://develop.sentry.dev/self-hosted/)

Retrieved 2026-11-27.

- Fact: Self-hosted Sentry recommends 32 GB of RAM, and needs at least 16 GB with swap.
  > "We recommend using 32 GB RAM, but it works with 16 GB RAM + 16 GB swap as well."

### [Pino Redaction](https://github.com/pinojs/pino/blob/main/docs/redaction.md)

Retrieved 2026-11-27.

- Fact: Pino redacts the keys given in the `redact` option.
  > "To redact sensitive information, supply paths to keys that hold sensitive data using the `redact` option."

### [OWASP Logging Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html)

Retrieved 2026-11-27.

- Fact: Access tokens should be excluded from logs.
  > "Access tokens"
- Fact: Passwords should be excluded from logs.
  > "Authentication passwords"
- Fact: Session values should be excluded or hashed.
  > "Session identification values (consider replacing with a hashed value if needed to track session specific events)"

### [The Twelve-Factor App: Config](https://12factor.net/config)

Retrieved 2026-11-27.

- Fact: Config belongs in environment variables.
  > "The twelve-factor app stores config in environment variables (often shortened to env vars or env)."
