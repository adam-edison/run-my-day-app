# DR-0002: Adoption of OpenSpec

**Status:** Accepted  
**Date:** 2026-11-24

## Context

The project will leverage AI-assisted software development. A structured approach is needed to ensure requirements, architectural decisions, and implementation remain aligned as the product evolves without requiring extensive upfront planning.

As a solo developer, minimizing process overhead, token consumption, and development friction is a priority.

Three spec-driven development tools are candidates:

- OpenSpec gives each change its own folder with a proposal, specs, design, and tasks. Archiving a change merges its delta specs into the main specs.
- GitHub Spec Kit carries a specification through planning, implementation, and convergence for each feature. It runs the same workflow regardless of task size.
- BMAD Method uses role-based agent personas and workflows to plan and build software.

An independent benchmark ran 50 production Python tickets through each tool. Per ticket, OpenSpec had the lowest median spec-phase tokens, the shortest median spec phase, and the lowest cost per merged ticket of the three. Merge rates of the three fall within the benchmark's noise band.

## Decision

Adopt OpenSpec as the framework for specification-driven development, using incremental change proposals rather than comprehensive upfront specifications.

Small changes skip OpenSpec. A small change touches one or two files, has few acceptance criteria, and has one owner.

## Alternatives Considered

- **GitHub Spec Kit:** Provides a comprehensive specification-driven workflow. Rejected for solo development. Spec Kit runs the same full workflow for every task, with rigid phase gates. For small tasks, that workflow generates a large amount of scaffolding and uses more tokens than necessary. In an independent benchmark, Spec Kit used about 40% more median spec-phase tokens than OpenSpec and cost more per merged ticket. Practitioner reviews describe Spec Kit as rigid and as overkill for tiny changes.
- **BMAD Method:** Provides structured, role-based AI development workflows. Rejected for rapid solo development. In an independent benchmark, BMAD used more than twice the median spec-phase tokens of OpenSpec. BMAD had the longest median time to merge and the highest cost per merged ticket of the three. Practitioner reviews describe BMAD as rigid, heavy to learn, and excessive for small changes. More agents mean more tokens per cycle.

## Rationale

- **Lightweight approach:** OpenSpec is lighter, more flexible, and more incremental than Spec Kit and BMAD Method. A practitioner review rates OpenSpec as the least ceremony per change of the tools it compares.
- **Token efficiency:** Minimize AI context consumption and unnecessary artifact generation. In an independent benchmark, OpenSpec used the fewest spec-phase tokens and wrote the shortest specs of the three tools.
- **Development velocity:** Prioritize rapid iteration with minimal process overhead. In the same benchmark, OpenSpec had the shortest median spec phase and time to merge of the three tools.
- **AI-assisted development:** Provide coding agents with structured requirements and persistent project context.
- **Incremental delivery:** Define and implement features individually as the product evolves.
- **Living documentation:** Maintain specifications that reflect current system behavior. In the benchmark, OpenSpec had the lowest spec drift of the three tools.
- **Traceability:** Preserve the history and intent behind implemented changes.
- **Developer flexibility:** Avoid dependence on a specific AI coding agent or provider.
- **Reduced rework:** Clarify requirements and design decisions before implementation.

## Consequences

- Feature development will generally follow the OpenSpec change lifecycle.
- Specifications must be maintained alongside implementation.
- Some additional planning and documentation overhead is introduced.
- For one- or two-file changes, an independent benchmark finds no merge-rate gain from any spec workflow, including OpenSpec, and a higher cost than no spec.
- For small changes such as bug fixes, the full OpenSpec cycle can be heavier than the change needs.
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

### [Proposal: speckit.tinySpec: a lightweight workflow for small tasks (github/spec-kit#1174)](https://github.com/github/spec-kit/issues/1174)

Retrieved 2026-11-28.

- Fact: Spec Kit runs the same workflow for every task.
  > "However, this workflow behaves the same regardless of **task complexity**, and this becomes inefficient for very small tasks."
- Fact: For small tasks, Spec Kit generates a large amount of scaffolding and uses more tokens than necessary.
  > "For trivial changes like adding a simple button (I'm exaggerating, but you got the point) the current workflow generates a large amount of scaffolding."
  > "This also results in significantly more token usage and compute cost than necessary, which makes spec-kit impractical for small tasks that developers perform multiple times per day."

### [BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD)

Retrieved 2026-11-27.

- Fact: BMAD Method provides structured AI development workflows with agents.
  > "BMad keeps you in control while its agents and workflows make the important decisions explicit and preserve them as context for the work that follows."

### [Skills and Agents | BMad Method](https://docs.bmad-method.org/reference/skills-and-agents/)

Retrieved 2026-11-27.

- Fact: BMAD Method agents are role-based personas.
  > "Load an agent when you want role-specific coaching or want to move across related tasks in one persona."

### [Build Your First Change | BMad Method](https://docs.bmad-method.org/start/build-your-first-change/)

Retrieved 2026-11-28.

- Fact: BMAD Method describes itself as fit for work from a small bug fix to a multi-team product.
  > "BMad helps you plan and build anything from a small bug fix to a multi-team software product."

### [Spec-Driven Development Benchmark 2026: Spec Kit vs Kiro vs BMAD vs OpenSpec vs No Spec on 50 Production Python Tickets (vendor blog, Uvik Software)](https://uvik.net/?p=7598)

Retrieved 2026-11-28.

- Fact: The spec phase uses about a third of all tokens in spec workflows. The benchmark tables report median spec-phase tokens of 104,924 for OpenSpec, 147,206 for Spec Kit, and 227,212 for BMAD.
  > "The Uvik Software SDD Benchmark found that the spec phase used 35.1% of all tokens in the spec arms."
- Fact: OpenSpec had a shorter median spec phase than Spec Kit.
  > "The median spec phase took 12 minutes with OpenSpec and 18 minutes with Spec Kit."
- Fact: BMAD and Spec Kit wrote longer specs per ticket than OpenSpec.
  > "BMAD wrote a median of 188 lines of spec per ticket, and Spec Kit wrote 132."
- Fact: Merge rates of OpenSpec and Spec Kit are close.
  > "In the Uvik Software SDD Benchmark, OpenSpec merged 42 of 50 tickets and GitHub Spec Kit merged 40."
- Fact: The benchmark advises skipping a spec for small single-owner changes.
  > "Skip the spec when the change is small, has few acceptance criteria and has one owner."

### [Spec Kit vs BMAD vs OpenSpec: Choosing an SDD Framework in 2026 (blog, DEV Community)](https://dev.to/willtorber/spec-kit-vs-bmad-vs-openspec-choosing-an-sdd-framework-in-2026-d3j)

Retrieved 2026-11-28. Posted April 23.

- Fact: More BMAD agents use more tokens.
  > "more agents means more tokens per cycle"
- Fact: BMAD handoffs between personas cause failures.
  > "Handoff failures between personas are a real debugging surface."
- Fact: OpenSpec is lightweight and suits existing codebases.
  > "Lightweight, brownfield-first SDD."
- Fact: The full OpenSpec cycle can be too heavy for small changes.
  > "can feel like performing surgery with a forklift"
- Fact: The author recommends OpenSpec by default.
  > "If in doubt, start with OpenSpec."

### [BMAD, Spec Kit, Superpowers, OpenSpec: Which Agent Skill Framework Fits Your Project Size (blog)](https://www.stephanmiller.com/bmad-spec-kit-superpowers-openspec-which-agent-skill-framework-fits-your-project-size/)

Retrieved 2026-11-28. Updated October 29, 2026.

- Fact: OpenSpec has the least ceremony per change.
  > "is the least ceremony per change in the field."
- Fact: Spec Kit is too much for tiny changes.
  > "Overkill for tiny."
- Fact: A cited review finds the Spec Kit process rigid.
  > "the process rigid, the docs dense, and no qualitative benefit to justify the overhead."
- Fact: BMAD is the hardest tool to learn of those compared.
  > "It is the heaviest thing here to learn."

### [Limitations of BMAD Method (blog)](https://asantos2000.github.io/blog/post-3/)

Retrieved 2026-11-28. Posted January 10, 2026.

- Fact: BMAD is rigid.
  > "too rigid for dynamic or exploratory development"
- Fact: BMAD uses many tokens per request.
  > "Token consumption: high per request due to extensive context and metadata inclusion"
