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

## Rationale

* Zod 4 is stable.
* Zod 4 converts schemas to JSON Schema without another library.
* Zod Mini covers the case where client bundle size matters.

## Consequences

* The full Zod API does not tree-shake well.
* No official source states that Valibot brings no benefit over Zod for this case. This point is own reasoning.
* ArkType is not yet researched as an option.

## Sources

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
