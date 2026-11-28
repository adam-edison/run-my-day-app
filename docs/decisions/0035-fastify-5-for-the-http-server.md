# DR-0035: Fastify 5 for the HTTP Server

**Status:** Draft  
**Date:** 2026-11-28

## Context

Sync runs on a Fastify server through oRPC, as recorded in DR-0017. Requests use oRPC over HTTP, and push uses server-sent events (SSE) through oRPC streaming, as recorded in DR-0018. The stack uses Pino logging through Fastify and uses @fastify/rate-limit.

Fastify facts:

* The npm latest version is 5.12.8.
* Fastify supports each major release for at least six months from its release date.
* A major line gets six more months of security updates after the next major line comes out.
* Fastify v5 has no end-of-support date set.
* oRPC ships a Fastify adapter.
* Request bodies that oRPC handles skip Fastify's `bodyLimit`.

Example: a client sends a sync upload of 50 MB. oRPC reads the body. Fastify's `bodyLimit` does not stop the upload.

## Decision

* The server uses Fastify 5 as its HTTP framework.
* The server sets a request body limit through oRPC.

## Alternatives Considered

Hono, rejected:

* Hono runs on many runtimes, but the server runs only on Node.js.
* Pino logging, rate limiting, and the sync design in DR-0017 assume Fastify.

## Rationale

* Fastify 5 is on an active major line with a published support policy.
* oRPC documents a Fastify adapter.
* Pino logging through Fastify, @fastify/rate-limit, and the sync design in DR-0017 all fit a Fastify server.

## Consequences

* Fastify's `bodyLimit` does not cover oRPC request bodies. The server needs a separate body limit through oRPC.
* No official source states that Hono's support for many runtimes adds nothing for a Node.js-only server. This point is own reasoning.
* Express is not yet researched as an option.

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
