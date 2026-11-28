# DR-0035: Keep Fastify 5 as the HTTP Framework

**Status:** Draft  
**Date:** 2026-11-28

## Context

Sync is built on the existing Fastify server through oRPC, as recorded in DR-0017. Requests use oRPC over HTTP, and push uses server-sent events (SSE) through oRPC streaming, as recorded in DR-0018. The stack uses Pino logging through Fastify and uses @fastify/rate-limit.

Fastify facts:

* The npm latest version is 5.12.8.
* Fastify supports each major release for at least six months from its release date.
* A major line gets six more months of security updates after the next major line comes out.
* Fastify v5 has no end-of-support date set.
* oRPC ships a Fastify adapter.
* Request bodies that oRPC handles skip Fastify's `bodyLimit`.

Example: a client sends a sync upload of 50 MB. Fastify's `bodyLimit` does not stop it, because oRPC reads the body. A limit set through oRPC rejects it.

## Decision

* The server keeps Fastify 5 as its HTTP framework.
* The server sets a request body limit through oRPC.

## Alternatives Considered

Hono, rejected:

* Hono runs on many runtimes, but the server runs only on Node.js.
* Pino logging, rate limiting, and the sync design in DR-0017 already assume Fastify.

Express, rejected:

* Express was not checked against sources.

## Rationale

* Fastify 5 is on an active major line with a published support policy.
* oRPC documents a Fastify adapter.
* The existing logging, rate limiting, and sync design stay as they are.

## Consequences

* Fastify's `bodyLimit` does not cover oRPC request bodies. The server needs a separate body limit through oRPC.
* No official source states that Hono's support for many runtimes adds nothing for a Node.js-only server. This point is own reasoning.
* Express was not compared against sources.

## Sources

### [Long Term Support | Fastify](https://fastify.dev/docs/latest/Reference/LTS/)

Retrieved 2026-11-27.

- Fact: Fastify supports each major release for at least six months.
  > "are supported for a minimum period of six months from their release date."
- Fact: An old major line gets six more months of security updates after the next major line comes out.
  > "Major releases will receive security updates for an additional six months from the release of the next major release."

### [Fastify adapter | oRPC](https://orpc.dev/docs/adapters/fastify)

Retrieved 2026-11-27.

- Fact: oRPC runs on Fastify.
  > "Use oRPC with Fastify servers via RPCHandler or OpenAPIHandler"
- Fact: Fastify's body size limit does not cover the bodies that oRPC handles.
  > "bodies it receives skip Fastify's bodyLimit"

### [Hono docs](https://hono.dev/docs/)

Retrieved 2026-11-27.

- Fact: Hono is built on Web Standards.
  > "is a small, simple, and ultrafast web framework built on Web Standards."
- Fact: Hono runs on many runtimes, including Node.js.
  > "AWS Lambda, Lambda@Edge, and Node.js."
