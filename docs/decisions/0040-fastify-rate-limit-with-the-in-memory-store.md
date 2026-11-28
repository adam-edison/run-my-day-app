# DR-0040: @fastify/rate-limit with the In-Memory Store

**Status:** Draft  
**Date:** 2026-11-28

## Context

The server runs on Fastify 5.

@fastify/rate-limit facts:

* The npm latest version is 11.3.0.
* It is the official Fastify plugin for route rate limits.
* Plugin version 10 and later supports Fastify 5.
* Clients over the limit get a 429 response.
* The default store is in memory. Each server instance counts requests on its own.
* More than one instance needs a shared Redis store through ioredis.

Example: a script floods the sync endpoint with outbox uploads. The server must limit the flood.

## Decision

* The server uses @fastify/rate-limit.
* The server uses the in-memory store while one server instance runs.
* Redis is added when a second server instance is added.

Example: a script floods the sync endpoint with outbox uploads. After the limit, the server answers each request with a 429 response.

## Alternatives Considered

Rate limits at a proxy or host, not chosen:

* Tailscale Funnel has bandwidth limits that cannot be configured. Its docs do not describe request rate limits.
* nginx limits the request rate per key, such as the client IP address, with a built-in module.
* Caddy needs a plugin built into a custom binary for rate limits.
* Cloudflare offers rate limiting rules that match requests by an expression.

## Rationale

* Plugin version 11 matches Fastify 5.
* One server instance needs no shared store. The plugin's README states that an external store is needed when the app runs on multiple servers.

## Consequences

* With the in-memory store, each server instance counts requests on its own.
* A second server instance needs Redis through ioredis.

## Open Questions

1. Do you want to consider rate limits at a proxy or host as well as, or in place of, the plugin? Example: a self-hoster puts nginx in front of the server and limits each IP address to 10 requests per second.

## Sources

### [Tailscale Funnel](https://tailscale.com/kb/1223/funnel)

Retrieved 2026-11-28.

- Fact: Tailscale Funnel has bandwidth limits that cannot be configured.
  > "Traffic sent over a Funnel is subject to non-configurable bandwidth limits."

### [Module ngx_http_limit_req_module](https://nginx.org/en/docs/http/ngx_http_limit_req_module.html)

Retrieved 2026-11-28.

- Fact: nginx limits the request rate per key, such as the client IP address.
  > "The `ngx_http_limit_req_module` module (0.7.21) is used to limit the request processing rate per a defined key, in particular, the processing rate of requests coming from a single IP address."

### [mholt/caddy-ratelimit on GitHub](https://github.com/mholt/caddy-ratelimit)

Retrieved 2026-11-28.

- Fact: Caddy rate limits come from a plugin module.
  > "This module implements both internal and distributed HTTP rate limiting."

### [Rate limiting rules | Cloudflare](https://developers.cloudflare.com/waf/rate-limiting-rules/)

Retrieved 2026-11-28.

- Fact: Cloudflare rate limiting rules match requests by an expression.
  > "Rate limiting rules allow you to define rate limits for requests matching an expression"

### [fastify/fastify-rate-limit on GitHub](https://github.com/fastify/fastify-rate-limit)

Retrieved 2026-11-27.

- Fact: The plugin is a rate limiter for routes.
  > "A low overhead rate limiter for your routes."
- Fact: The default store is in memory.
  > "by default, this plugin uses an in-memory store."
- Fact: An app on multiple servers needs an external store.
  > "if an application runs on multiple servers, an external store will be needed"
- Fact: Plugin version 10 and later supports Fastify 5.
  > "`>=10.x` | `^5.x`"
