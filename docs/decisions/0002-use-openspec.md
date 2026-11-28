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

## Rationale

- **Lightweight approach:** OpenSpec is lighter, more flexible, and more incremental than Spec Kit and BMAD Method.
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

## Sources

### [OpenSpec](https://github.com/Fission-AI/OpenSpec)

Retrieved 2026-11-27.

- Fact: OpenSpec is lightweight and iterative.
  > "OpenSpec adds a lightweight spec layer so you agree on what to build before any code is written."
- Fact: OpenSpec works through incremental change proposals, one folder per change.
  > "each change gets its own folder with proposal, specs, design, and tasks"
- Fact: OpenSpec clarifies requirements and design before implementation.
  > "Your AI writes these; you review the plan before any code is written."
- Fact: OpenSpec does not depend on a specific AI coding agent.
  > "works with 30+ AI assistants via skills"
- Fact: Archiving a change updates the specs and keeps the change for history.
  > "Specs updated. Ready for the next feature."
- Fact: OpenSpec describes Spec Kit as heavier than OpenSpec.
  > "Thorough but heavyweight. Rigid phase gates, lots of Markdown, Python setup. OpenSpec is lighter and lets you iterate freely."

### [GitHub Spec Kit](https://github.com/github/spec-kit)

Retrieved 2026-11-27.

- Fact: Spec Kit provides a comprehensive specification-driven workflow.
  > "Build a feature or application | Spec-Driven Development | A specification carried through planning, implementation, and convergence"

### [BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD)

Retrieved 2026-11-27.

- Fact: BMAD Method provides structured AI development workflows with agents.
  > "BMad keeps you in control while its agents and workflows make the important decisions explicit and preserve them as context for the work that follows."

### [Skills and Agents | BMad Method](https://docs.bmad-method.org/reference/skills-and-agents/)

Retrieved 2026-11-27.

- Fact: BMAD Method agents are role-based personas.
  > "Load an agent when you want role-specific coaching or want to move across related tasks in one persona."
