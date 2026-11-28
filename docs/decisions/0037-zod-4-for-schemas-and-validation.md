# DR-0037: Zod 4 for Schemas and Validation

**Status:** Draft  
**Date:** 2026-11-28

## Context

The stack lists Zod 4 for runtime validation and JSON Schema generation. oRPC validates requests and responses with these schemas.

Zod facts:

* The npm latest version is 4.6.7.
* Zod 4 is stable.
* Zod 4 is faster and slimmer than Zod 3.
* Zod 4 converts schemas to JSON Schema itself with `z.toJSONSchema()`.
* The regular Zod API is hard to tree-shake. Tree shaking removes unused code from a bundle.
* Zod Mini has the same features as Zod, with a tree-shakable API.

Example: the server validates a sync upload that ticks off a routine step with a Zod schema. The same schema produces the JSON Schema for the OpenAPI document.

## Decision

* The app uses Zod 4 for schemas and validation.
* When client bundle size matters, the clients use Zod Mini.

## Alternatives Considered

Valibot, rejected:

* No stated need calls for Valibot in place of Zod 4.

ArkType, not chosen:

* ArkType supports Standard Schema. oRPC accepts any Standard Schema library.
* ArkType converts schemas to and from JSON Schema.
* ArkType's site states that ArkType validates 20 times faster than Zod 4.
* ArkType needs TypeScript 5.1 or later.

## Rationale

* Zod 4 is stable.
* Zod 4 converts schemas to JSON Schema without another library.
* Zod Mini covers the case where client bundle size matters.

## Consequences

* The full Zod API does not tree-shake well.

## Assumptions

* Valibot brings no benefit over Zod for this app. A source contradicts this.

## Open Questions

1. Do you want to consider ArkType in place of Zod 4? Example: the sync upload schema is written in ArkType, and oRPC validates it through Standard Schema.

## Sources

### [Comparison with Zod and others | Valibot](https://valibot.dev/guides/comparison/)

Retrieved 2026-11-28.

- Fact: Valibot's bundle for a login form is 3 to 5 times smaller than Zod Mini's.
  > "Zod Mini requires approximately 6.88 kB with esbuild and 3.94 kB with Rolldown, still about 3 to 5x larger than Valibot's 1.37 kB"

### [Integrations | ArkType](https://arktype.io/docs/integrations)

Retrieved 2026-11-28.

- Fact: ArkType supports Standard Schema.
  > "ArkType is proud to support and co-author the new Standard Schema API with Valibot and Zod."
- Fact: oRPC accepts ArkType through Standard Schema.
  > "oRPC has built-in support for Standard Schema, so ArkType works seamlessly right out of the box"
- Fact: ArkType converts schemas to and from JSON Schema.
  > "ArkType supports bidirectional conversion with JSON Schema."

### [ArkType](https://arktype.io/)

Retrieved 2026-11-28.

- Fact: ArkType's site states that ArkType validates 20 times faster than Zod 4.
  > "20x faster than Zod 4 and 2,000x faster than Yup at runtime"

### [Setup | ArkType](https://arktype.io/docs/intro/setup)

Retrieved 2026-11-28.

- Fact: ArkType needs TypeScript 5.1 or later.
  > "TypeScript version `>=5.1`"

### [Release notes | Zod](https://zod.dev/v4)

Retrieved 2026-11-27.

- Fact: Zod 4 is stable.
  > "Zod 4 is now stable! It's faster, slimmer, more `tsc`-efficient, and implements some long-requested features."
- Fact: Zod 4 converts schemas to JSON Schema itself.
  > "Zod 4 introduces first-party JSON Schema conversion via `z.toJSONSchema()`."
- Fact: The regular Zod API is hard to tree-shake.
  > "Zod's method-heavy API is fundamentally difficult to tree-shake."
- Fact: Zod Mini has the same features with a tree-shakable API.
  > "It's a Zod variant with a functional, tree-shakable API that corresponds one-to-one with `zod`."
