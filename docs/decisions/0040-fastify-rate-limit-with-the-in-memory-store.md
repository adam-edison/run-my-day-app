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

## Rationale

* Plugin version 11 matches Fastify 5.
* One server instance needs no shared store.

## Consequences

* With the in-memory store, each server instance counts requests on its own.
* A second server instance needs Redis through ioredis.
* No official source recommends using the in-memory store until a second instance is added. This choice is own reasoning.
* Rate limits at a proxy or host are not yet researched as an option.

## Sources

### [fastify/fastify-rate-limit on GitHub](https://github.com/fastify/fastify-rate-limit)

Retrieved 2026-11-27.

- Fact: The plugin is a rate limiter for routes.
  > "A low overhead rate limiter for your routes."
- Fact: The default store is in memory.
  > "by default, this plugin uses an in-memory store."
- Fact: Plugin version 10 and later supports Fastify 5.
  > "`>=10.x` | `^5.x`"
