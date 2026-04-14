# SPEC Framework (Spec Kit / Spec-Driven Development)

**Type:** Specification-driven development toolkit for AI coding agents
**Creator:** GitHub (Den Delimarsky, Principal Product Manager)
**Launched:** September 2025
**License:** MIT
**GitHub:** [github/spec-kit](https://github.com/github/spec-kit)
**Also known as:** SDD (Specification-Driven Development), Spec Kit

---

## What It Is

Spec Kit is GitHub's open-source toolkit for **Specification-Driven Development (SDD)** -- a methodology where specifications become the primary artifact and code becomes a generated expression of those specs. Instead of coding first and documenting later, you start with a spec that serves as an executable contract for how code should behave.

The core insight: **the problem with AI coding isn't the model's capabilities -- it's the lack of clear, unambiguous instructions.** Vague prompts force models to make thousands of unstated assumptions, many incorrect. Explicit specifications eliminate this guesswork.

As GitHub puts it: "Specifications don't serve code -- code serves specifications."

## Why It Exists

The "vibe coding" pattern: describe your goal, get code back, it looks right but doesn't quite work. This works for prototypes but fails for production applications. One study found that **1 iteration with structure was as accurate as 8 iterations with unstructured prompts.**

Three converging trends enable SDD:
1. **AI Capability** -- LLMs can reliably translate specs into working code
2. **Complexity Growth** -- Modern systems with dozens of integrated services need systematic alignment
3. **Rapid Change** -- Product pivots demand systematic regeneration, not manual rewrites

## Core Principles

- **Specifications as Lingua Franca** -- The spec is the primary artifact; code expresses it in a particular language/framework
- **Executable Specifications** -- Specs must be precise, complete, and unambiguous enough to generate working systems
- **Continuous Refinement** -- Validation happens continuously, not as one-time gates
- **Research-Driven Context** -- Research agents gather technical context throughout spec development
- **Bidirectional Feedback** -- Production metrics and incidents inform specification evolution
- **Branching for Exploration** -- Generate multiple implementation approaches to explore different optimization targets

## The 6-Phase Workflow

### Phase 0: Constitution
Establish immutable project principles and development guidelines. The constitution defines architectural laws that cannot be violated.

Key constitutional articles include:
- **Library-First** -- Every feature begins as a standalone library
- **CLI Interface Mandate** -- All functionality exposed through CLI
- **Test-First Imperative** -- Non-negotiable strict TDD
- **Simplicity** -- Max 3 projects for initial implementation
- **Anti-Abstraction** -- Use framework features directly, don't wrap them
- **Integration-First Testing** -- Prefer real databases over mocks

### Phase 1: Specify (`/speckit.specify` or `/speckit-specify`)
Transform a high-level feature description into a complete structured specification:
- Focuses on **what** users need and **why**, not how to implement
- Captures user journeys, experiences, and success criteria
- Forces `[NEEDS CLARIFICATION]` tags for unknowns -- prevents assumptions
- Auto-numbers features sequentially
- Creates semantic branch names
- Output: **spec.md**

### Phase 2: Plan (`/speckit.plan` or `/speckit-plan`)
Technical constraints enter here. Create a comprehensive implementation plan:
- Developer specifies tech stack, architecture, compliance requirements
- Analyzes feature requirements against constitutional principles
- Translates business needs into technical architecture
- Generates supporting docs: data models, API contracts, test scenarios
- **Pre-Implementation Gates** enforce constitutional compliance:
  - Simplicity Gate (max 3 projects, no premature optimization)
  - Anti-Abstraction Gate (direct framework usage)
  - Integration-First Gate (contracts defined, contract tests written)
- Output: **plan.md**

### Phase 3: Tasks (`/speckit.tasks` or `/speckit-tasks`)
Decompose spec + plan into actionable work units:
- Each task solves a specific, isolated piece
- Can be implemented and tested independently
- Marks independent tasks for parallelization
- Includes verification steps
- Output: **tasks.md**

### Phase 4: Implement (`/speckit.implement` or `/speckit-implement`)
Execute tasks according to plan:
- Strict TDD: no implementation before tests are written, validated, and confirmed to fail
- Code generation begins when specs are stable enough (don't need to be 100% complete)
- Developers review focused changes per task, not massive code dumps
- Output: working code, tests

### Phase 5: Post-Implementation (via extensions)
Review, verify, and refine:
- Production metrics and incidents update specifications for next regeneration
- Feedback loop closes: change a core requirement in the spec, affected implementation plans update automatically

## File Structure

```
project/
  constitution.md          -- Immutable development principles
  spec.md                  -- Feature specification (what + why)
  plan.md                  -- Technical implementation plan (how)
  tasks.md                 -- Actionable task breakdown
  .speckit/
    config.json            -- Configuration settings
    agents.json            -- Agent definitions
    metadata.json          -- Project metadata
```

## Installation

### Persistent (recommended)
```bash
# Specific stable release
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@vX.Y.Z

# Latest from main
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
```

### One-time usage
```bash
uvx --from git+https://github.com/github/spec-kit.git@vX.Y.Z specify init <PROJECT_NAME>
```

### CLI Commands
```bash
specify init <PROJECT_NAME>        # Create new project
specify init . --ai claude         # Initialize in existing directory
specify check                      # Verify installed dependencies
```

### Enterprise/Air-Gapped
Supports `pip download`-based portable wheel bundles for restricted networks.

## AI Agent Integration

### Slash Commands (Claude Code)
Installed as skills in `.claude/skills/`:
- `/speckit-constitution` -- Create project principles
- `/speckit-specify` -- Describe the feature
- `/speckit-plan` -- Define tech stack and architecture
- `/speckit-tasks` -- Generate implementation tasks
- `/speckit-implement` -- Execute all tasks

### Supported Agents
- Claude Code (verified)
- GitHub Copilot CLI (verified)
- Qoder CLI, Kiro CLI, Amp, Auggie CLI, CodeBuddy CLI, Codex CLI (verified)
- Any terminal-based agent can use `specify` CLI commands directly

## Template-Driven Quality

Templates constrain LLM behavior through 7 mechanisms:

1. **Preventing Premature Implementation** -- Focus on WHAT/WHY before HOW
2. **Forcing Uncertainty Markers** -- Mandatory `[NEEDS CLARIFICATION]` tags
3. **Structured Thinking** -- Checklists as "unit tests" for specs
4. **Constitutional Compliance** -- Phase gates enforce principles before code
5. **Hierarchical Detail Management** -- Main docs stay readable; complexity extracted to separate files
6. **Test-First Thinking** -- File creation order enforces contracts before implementation
7. **Preventing Speculation** -- Explicit discouragement of "might need" features

## Technology Agnosticism

A key feature: the spec-to-plan separation means tech stack decisions live entirely in the plan. The **same spec** can generate:
- A .NET/Blazor implementation on one branch
- A Vite/vanilla-JS implementation on another

Change the plan, regenerate the code. The spec stays the same.

## Extensions & Presets

- **Extensions** add functionality: docs, code review, Jira/Azure DevOps sync, QA testing, security review, release automation. 50+ community extensions.
- **Presets** customize terminology and templates without changing tooling.
- Both discoverable via community catalogs.

## Use Cases

- **Greenfield projects** -- Upfront specs ensure the AI builds what you intend
- **Feature development** -- Clear specs define how new features integrate with existing code
- **Legacy modernization** -- Capture original business logic in specs, rebuild without tech debt
- **Portability** -- Moving between AI tools is as simple as moving a markdown file

## What Makes It Different

| Aspect | Vibe Coding | Spec-Driven Development |
|--------|------------|------------------------|
| Primary artifact | Code | Specification |
| Starting point | "Build me X" | Structured spec with user journeys |
| Assumptions | Thousands of implicit guesses | Explicit, documented, clarified |
| Tech stack decisions | Baked into code | Isolated in plan, swappable |
| Iteration cost | Rewrite everything | Regenerate from updated spec |
| Knowledge management | Lost in chat history | Persisted in markdown files |
| Portability | Locked to one tool | Specs move anywhere |

## Comparison with [[SUPERPOWERS]]

Both frameworks solve unstructured AI coding, from different angles:

- **SPEC** focuses on the **artifact** -- making the specification the source of truth that drives everything downstream. Specs are the product; code is derived.
- **SUPERPOWERS** focuses on **behavioral discipline** -- enforcing TDD, code review, systematic debugging as mandatory agent workflows. Skills are behavioral patterns.
- SPEC is more **what-to-build-oriented**; Superpowers is more **how-to-work-oriented**.
- SPEC is backed by **GitHub** with enterprise features (air-gapped install, VS Code extension, multi-agent support).
- Both can complement each other: SPEC for specification and planning, Superpowers for implementation discipline and TDD enforcement.

## Related Projects

- **[SpecPulse](https://github.com/specpulse/specpulse)** -- SDD framework with Claude Code integration
- **[OpenSpec](https://github.com/Fission-AI/OpenSpec)** -- Lighter alternative, no rigid phase gates
- **[IBM IaC Spec Kit](https://github.com/IBM/iac-spec-kit)** -- SDD for infrastructure-as-code

## Sources

- [GitHub Blog: Spec-driven development with AI](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
- [GitHub: github/spec-kit](https://github.com/github/spec-kit)
- [Spec-Driven Development methodology doc](https://github.com/github/spec-kit/blob/main/spec-driven.md)
- [Microsoft Developer: Diving into SDD with Spec Kit](https://developer.microsoft.com/blog/spec-driven-development-spec-kit)
- [Heeki Park: Using SDD with Claude Code](https://heeki.medium.com/using-spec-driven-development-with-claude-code-4a1ebe5d9f29)
- [Agent Factory: SDD with Claude Code](https://agentfactory.panaversity.org/docs/General-Agents-Foundations/spec-driven-development)
- [CC for Everyone: GSD Framework](https://ccforeveryone.com/gsd)
- [Visual Studio Magazine: GitHub open sources kit for SDD](https://visualstudiomagazine.com/articles/2025/09/03/github-open-sources-kit-for-spec-driven-ai-development.aspx)
