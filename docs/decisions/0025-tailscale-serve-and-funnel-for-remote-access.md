# DR-0025: Tailscale Serve and Funnel for Remote Access

**Status:** Draft  
**Date:** 2026-11-28

## Context

The phone and the laptop must reach the self-hosted server from outside the home network. Google must reach the server's push webhook at a public HTTPS address.

Facts from earlier decisions:

* Google sends calendar push notifications to an HTTPS webhook address, as recorded in DR-0021.
* DR-0021 gives Tailscale Funnel as an example of a public HTTPS address.
* A 5-minute backup check runs in case a push notification is lost, as recorded in DR-0021.
* Push to open clients uses SSE, a long-lived HTTP response, as recorded in DR-0018.
* A notice must reach an open client within 2 seconds.
* The web build registers a service worker. Browsers allow a service worker only on HTTPS or `localhost`.

Tailscale is a private network (a "tailnet") between a person's own devices. `tailscale serve` shares the server with those devices over HTTPS. Funnel makes a chosen address reachable from the whole internet.

Example: at 10:05 the Workout event moves while the phone is on mobile data. Google sends a push notification to the home server, and the phone gets the change over the tailnet by 10:06.

## Decision

* Tailscale Serve shares the server with the phone and the laptop.
* Tailscale Funnel publishes only the Google webhook path to the internet.
* The app and the API stay private to the user's devices.
* Before the status moves past Draft, a test checks SSE through Tailscale Serve.

Example: the laptop opens the app at its tailnet address. Google posts a push notification to the Funnel address of the webhook path. No other path is public.

## Alternatives Considered

Cloudflare Tunnel for everything, rejected:

* It needs a domain on Cloudflare.
* The whole app is public, so sign-in (Better Auth) and rate limiting are the only protection.
* Cloudflare returns error 524 when the server sends no response for 125 seconds. The SSE stream needs a regular heartbeat.

Tailscale for devices plus Cloudflare Tunnel for the webhook only, rejected:

* Two tools must be installed and configured.

A VPS with a public IP address and a normal certificate, rejected:

* It applies only to a VPS, not to a home machine.

## Rationale

* DR-0021 already gives Funnel as the example of a public HTTPS address.
* Serve provisions TLS certificates for the tailnet DNS name automatically.
* No router port is opened, and no domain is bought.
* The beta status and port limits of Funnel are acceptable for one low-traffic webhook.
* The 5-minute backup check covers a lost push notification.

## Consequences

* Funnel is in beta.
* Funnel listens only on ports 443, 8443, and 10000.
* Funnel traffic has bandwidth limits that cannot be changed.
* Funnel addresses must be under `ts.net`.
* Every phone and laptop needs the Tailscale app installed and signed in.
* No official source describes SSE through Tailscale Serve or Funnel. The test checks it.
* No official source compares these options for this case. The choice is not verified.

## Open Questions

1. Must the app stay private to your devices, or may it be public behind sign-in? Example: with Cloudflare Tunnel, `runmyday.example.com` is reachable by anyone who finds it.
2. Is the Funnel beta acceptable for the webhook? Example: if Funnel stops, the moved Workout event arrives by the 5-minute check, by 10:10 instead of 10:06.
3. Is installing Tailscale on every phone acceptable? Example: a phone without Tailscale cannot sync on mobile data.
4. Do you own a domain to use with Cloudflare?

## Sources

### [Tailscale Funnel](https://tailscale.com/kb/1223/funnel)

Retrieved 2026-11-27.

- Fact: Funnel is in beta.
  > "Tailscale Funnel is currently in beta."
- Fact: Funnel uses only three ports.
  > "Funnel can only listen on ports `443`, `8443`, and `10000`."
- Fact: Funnel uses TLS only.
  > "Funnel only works over"
- Fact: Funnel has fixed bandwidth limits.
  > "Traffic sent over a Funnel is subject to non-configurable bandwidth limits."
- Fact: Funnel addresses are under the tailnet's domain.
  > "Funnel can only use DNS names in your"

### [Tailscale Serve](https://tailscale.com/kb/1312/serve)

Retrieved 2026-11-27.

- Fact: Serve shares a service with your other devices.
  > "Tailscale Serve lets you route traffic from other devices on your Tailscale network"
- Fact: Serve gets TLS certificates automatically.
  > "Tailscale Serve requires that you enable HTTPS in your tailnet to automatically provision TLS certificates for your unique tailnet DNS name."
- Fact: Serve forwards requests to a local server.
  > "you can proxy requests to a web server running at"

### [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/)

Retrieved 2026-11-27.

- Fact: Tunnel uses outbound connections only.
  > "Cloudflare Tunnel uses an outbound-only connection model to enable bidirectional communication."
- Fact: Tunnel needs no public IP address.
  > "without a publicly routable IP address"

### [Cloudflare Tunnel common errors](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/troubleshoot-tunnels/common-errors/)

Retrieved 2026-11-27.

- Fact: Tunnel buffers responses unless the server sends the event-stream content type.
  > "Proxied traffic through Cloudflare Tunnel is buffered by default unless the origin server includes the"
  > "This header tells `cloudflared` to stream data as it arrives instead of buffering the entire response."

### [Error 524 | Cloudflare](https://developers.cloudflare.com/support/troubleshooting/http-status-codes/cloudflare-5xx-errors/error-524/)

Retrieved 2026-11-27.

- Fact: Cloudflare waits 125 seconds for the server by default.
  > "the origin did not provide an HTTP response before the default 125 seconds"

### [ServiceWorkerContainer.register() | MDN](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerContainer/register)

Retrieved 2026-11-27.

- Fact: Service workers need HTTPS or localhost.
  > "The `scriptURL` is not a potentially trustworthy origin, such as `localhost` or an `https` URL."
