# DR-0036: oRPC for the API

**Status:** Draft  
**Date:** 2026-11-28

## Context

Sync runs through oRPC on the Fastify server, as recorded in DR-0017. Requests use oRPC's RPC protocol over HTTP, as recorded in DR-0018. Push uses SSE through oRPC streaming. Each client fetches every change since its last sync after it reconnects.

oRPC facts:

* The npm latest version of `@orpc/server` is 1.16.7.
* `@orpc/server` was first published on npm on 16 November 2024. `@trpc/server` was first published on 25 January 2021. oRPC is about four years younger than tRPC.
* oRPC combines typed RPC with first-class OpenAPI support.
* The same procedures serve the RPC protocol and OpenAPI endpoints.
* oRPC supports contract-first APIs and typed SSE.
* On reconnect, the client sends the last event ID it received in `lastEventId`.
* A stream ends when its handler returns.
* EventSource clients do not recognize this end and try to reconnect.

tRPC facts:

* The npm latest version of `@trpc/server` is 11.32.2.
* tRPC supports typed SSE.
* oRPC's comparison page lists no contract-first support in tRPC and only partial OpenAPI support.

Example: the laptop loses Wi-Fi for 30 seconds. The Workout block moves during that time. After the reconnect, the laptop must receive the moved Workout block.

## Decision

* The API uses oRPC.
* The push handler keeps running while the client is connected.
* The push stream resumes from `lastEventId` after a reconnect.

## Alternatives Considered

tRPC, rejected:

* oRPC's comparison page lists no contract-first support in tRPC.
* oRPC's comparison page lists only partial OpenAPI support in tRPC.

ts-rest, not chosen:

* ts-rest is no longer maintained. It has no planned releases, bug fixes, or security updates.
* ts-rest defines a contract first.
* ts-rest supports Fastify and any validation library with Standard Schema support.

## Rationale

* oRPC's docs list contract-first support, typed SSE, and first-party OpenAPI.
* Resuming a stream from `lastEventId` matches the reconnect design in DR-0018.

## Consequences

* An oRPC stream ends when its handler returns. EventSource clients then try to reconnect. A push handler that returns early causes repeated reconnects.

## Open Questions

1. Do you want to consider ts-rest in place of oRPC? Example: a fork of ts-rest takes over maintenance, and the sync contract moves to it.

## Sources

### [@orpc/server on the npm registry](https://registry.npmjs.org/@orpc/server)

Retrieved 2026-11-28.

- Fact: `@orpc/server` was first published on 16 November 2024.
  > "created": "2024-11-16T06:14:34.679Z"

### [@trpc/server on the npm registry](https://registry.npmjs.org/@trpc/server)

Retrieved 2026-11-28.

- Fact: `@trpc/server` was first published on 25 January 2021.
  > "created": "2021-01-25T22:34:03.428Z"

### [ts-rest](https://ts-rest.com/)

Retrieved 2026-11-28.

- Fact: ts-rest is no longer maintained.
  > "There are no planned releases, bug fixes, security updates, or support."
- Fact: ts-rest defines a contract first.
  > "Contract-First API"
- Fact: ts-rest supports Fastify.
  > "We support Nest, Next.js, Express, Fastify and more"
- Fact: ts-rest supports any validation library with Standard Schema support.
  > "We support any Typescript validation library that supports Standard Schema."

### [Comparison | oRPC](https://orpc.dev/docs/comparison)

Retrieved 2026-11-27.

- Fact: oRPC combines typed RPC with OpenAPI.
  > "combines typesafe RPC with first-class OpenAPI support."
- Fact: The same procedures serve both the RPC protocol and OpenAPI endpoints.
  > "serves the same procedures over its RPC protocol and RESTful OpenAPI endpoints."

### [Event Iterator | oRPC](https://orpc.dev/docs/event-iterator)

Retrieved 2026-11-27.

- Fact: On reconnect, the client sends the last event ID it received.
  > "the last received event ID is sent back to the server in `lastEventId`"
- Fact: A stream ends when its handler returns.
  > "oRPC marks the stream as completed when the handler returns."
- Fact: EventSource clients reconnect after that signal.
  > "do not recognize this completion signal and will automatically attempt to reconnect."
