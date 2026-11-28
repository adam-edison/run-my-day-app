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
* Hono was not designed for Node.js at first. It runs on Node.js through an adapter.

Express, not chosen:

* Express 5 was released on 15 October 2024.
* Express 5 passes a rejected promise in a route handler to the error handler.
* oRPC has an Express adapter.
* Fastify's benchmark page shows Fastify 5 handling about 79,000 GET requests per second and Express 5 about 54,000. The page calls these figures illustrative.

## Rationale

* Fastify 5 is on an active major line with a published support policy.
* oRPC documents a Fastify adapter.
* Pino logging through Fastify, @fastify/rate-limit, and the sync design in DR-0017 all fit a Fastify server.

## Consequences

* Fastify's `bodyLimit` does not cover oRPC request bodies. The server needs a separate body limit through oRPC.

## Assumptions

* Hono's support for many runtimes adds nothing for a Node.js-only server. Not found in official sources as of 2026-11-28.

## Open Questions

1. Do you want to consider Express in place of Fastify? Example: the oRPC handler mounts as Express middleware, and an Express rate-limit package replaces @fastify/rate-limit.

## Sources

### [Node.js | Hono](https://hono.dev/docs/getting-started/nodejs)

Retrieved 2026-11-28.

- Fact: Hono was not designed for Node.js at first. It runs on Node.js through an adapter.
  > "Hono was not designed for Node.js at first, but with a Node.js Adapter, it can run on Node.js as well."

### [Introducing Express v5: A New Era for the Node.js Framework](https://expressjs.com/en/blog/2024-10-15-v5-release)

Retrieved 2026-11-28.

- Fact: Express 5 was released on 15 October 2024.
  > "October 15, 2024"
- Fact: Express 5 passes a rejected promise in a route handler to the error handler.
  > "the router will now catch the rejected promise and treat that as calling `next(err)`."

### [Express.js Adapter | oRPC](https://orpc.dev/docs/adapters/express)

Retrieved 2026-11-28.

- Fact: oRPC has an Express adapter.
  > "Use oRPC inside an Express.js project by mounting a handler as a middleware through the Node HTTP Adapter, without conflicts from body-parsing middleware."

### [Benchmarks | Fastify](https://fastify.dev/benchmarks/)

Retrieved 2026-11-28.

- Fact: Fastify 5 handles about 79,000 GET requests per second on the benchmark page.
  > "78,696"
- Fact: Express 5 handles about 54,000 GET requests per second on the benchmark page.
  > "53,785"

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
