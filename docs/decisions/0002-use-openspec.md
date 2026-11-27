# DR-0002: Adoption of OpenSpec

**Status:** Accepted  
**Date:** 2026-11-24

## Context

The project will leverage AI-assisted software development. A structured approach is needed to ensure requirements, architectural decisions, and implementation remain aligned as the product evolves without requiring extensive upfront planning.

As a solo developer, minimizing process overhead, token consumption, and development friction is a priority.

## Decision

Adopt OpenSpec as the framework for specification-driven development, using incremental change proposals rather than comprehensive upfront specifications.

## Alternatives Considered

- **GitHub Spec Kit:** Provides a comprehensive specification-driven workflow but was considered too token-intensive and process-heavy for solo development.
- **BMAD Method:** Provides structured, role-based AI development workflows but appeared better suited to organizational and team-based development than rapid solo development.

OpenSpec was selected for its comparatively lightweight, flexible, and incremental approach.

## Rationale

- **Token efficiency:** Minimize AI context consumption and unnecessary artifact generation.
- **Development velocity:** Prioritize rapid iteration with minimal process overhead.
- **AI-assisted development:** Provide coding agents with structured requirements and persistent project context.
- **Incremental delivery:** Define and implement features individually as the product evolves.
- **Living documentation:** Maintain specifications that reflect current system behavior.
- **Traceability:** Preserve the history and intent behind implemented changes.
- **Developer flexibility:** Avoid dependence on a specific AI coding agent or provider.
- **Reduced rework:** Clarify requirements and design decisions before implementation.

## Consequences

- Feature development will generally follow the OpenSpec change lifecycle.
- Specifications must be maintained alongside implementation.
- Some additional planning and documentation overhead is introduced.
- Product vision, roadmap, and architectural decisions will be maintained separately and inform individual change proposals.
- The development workflow can evolve as project complexity and collaboration requirements increase.

## References

- [OpenSpec](https://github.com/Fission-AI/OpenSpec)
- [GitHub Spec Kit](https://github.com/github/spec-kit)
- [BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD)
