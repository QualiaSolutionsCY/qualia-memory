# SUPERPOWERS Framework

**Type:** Agentic skills framework & software development methodology for AI coding agents
**Creator:** Jesse Vincent (Prime Radiant)
**Launched:** October 2025
**License:** MIT
**GitHub:** [obra/superpowers](https://github.com/obra/superpowers) -- 137k+ stars, 11.6k forks
**Discord:** https://discord.gg/Jd8Vphy9jq

---

## What It Is

Superpowers is an open-source framework that transforms AI coding agents (Claude Code, Cursor, Codex, Gemini CLI, etc.) from reactive code generators into disciplined software developers. It ships a library of **skills** -- markdown files containing instructions, checklists, and decision trees -- that Claude reads and follows before taking any action.

The core insight: **the problem with AI coding isn't model intelligence -- it's the absence of discipline.** A vanilla Claude Code instance jumps straight into writing code, skips tests, mixes responsibilities, and produces fragile prototypes. Superpowers imposes mandatory workflows that prevent this.

As Jesse Vincent puts it: imposing rigorous methodology on an existing model produces better results than letting a superior model work without constraints.

## Core Philosophy

- **Skills Enable Superpowers** -- Reusable, documented capabilities that systematize workflows
- **Mandatory Skill Usage** -- Claude must use appropriate skills when they exist; they're not optional suggestions
- **Self-Improvement Through Documentation** -- The system can generate and refine its own skills
- **YAGNI** -- Build the simplest thing that works, never over-engineer
- **Evidence Over Claims** -- Verify everything actually works before declaring success
- **Test-Driven Development** -- Tests always precede implementation
- **Systematic Over Ad-Hoc** -- Process takes priority over guessing

## The 7-Stage Workflow

### 1. Brainstorming
Activates before writing any code. Instead of jumping into implementation, Claude:
- Asks clarifying questions about what you're really trying to build
- Explores alternatives and trade-offs
- Presents the design in digestible sections for validation
- Saves a design document once approved

### 2. Git Worktrees
Activates after design approval:
- Creates an isolated workspace on a new branch
- Runs project setup and dependency installation
- Verifies a clean test baseline before any changes

### 3. Writing Plans
Activates with an approved design:
- Breaks work into bite-sized tasks (2-5 minutes each)
- Each task includes exact file paths, complete code snippets, and verification steps
- Plans are "clear enough for an enthusiastic junior engineer with poor taste, no judgement, no project context, and an aversion to testing to follow"

### 4. Subagent-Driven Development / Executing Plans
Two implementation models available:
- **Traditional PM oversight:** Separate agent sessions with human checkpoints
- **Subagent dispatch:** Fresh subagent per task with two-stage review (spec compliance, then code quality)
- Executes in batches with human checkpoints between them

### 5. Test-Driven Development
Enforces strict RED-GREEN-REFACTOR:
1. Write a failing test
2. Watch it fail
3. Write minimal code to pass
4. Watch it pass
5. Commit

**Anti-cheat mechanism:** If Claude writes implementation code before tests, Superpowers automatically deletes it and forces a restart with tests first.

### 6. Code Review
- Reviews implementation against the plan
- Categorizes issues by severity
- Critical issues block progress
- Two-stage review: spec compliance, then code quality

### 7. Finishing a Development Branch
- Verifies all tests pass
- Presents options: merge, PR, keep, or discard
- Cleans up the workspace

## Full Skills Library

### Testing
- **test-driven-development** -- RED-GREEN-REFACTOR with anti-patterns reference

### Debugging
- **systematic-debugging** -- 4-phase root cause analysis with tracing and defense-in-depth
- **verification-before-completion** -- Confirms fixes actually work

### Collaboration
- **brainstorming** -- Idea refinement and design documents
- **writing-plans** -- Task decomposition
- **executing-plans** -- Task execution with checkpoints
- **dispatching-parallel-agents** -- Concurrent subagent workflows
- **requesting-code-review** -- Submitting work for review
- **receiving-code-review** -- Processing review feedback
- **using-git-worktrees** -- Isolated workspaces
- **finishing-a-development-branch** -- Cleanup and merge
- **subagent-driven-development** -- Fast iteration with two-stage review

### Meta
- **writing-skills** -- Create new skills following best practices
- **using-superpowers** -- Introduction to the framework
- **remembering-conversations** -- Preserves transcripts in vector-indexed SQLite databases; uses CLI tools for memory search via subagents

## Installation

### Claude Code (recommended)
```
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

### Cursor
```
/add-plugin superpowers
```
Or search the plugin marketplace.

### Gemini CLI
```
gemini extensions install https://github.com/obra/superpowers
```

### Codex & OpenCode
Manual setup -- clone the repo and follow platform-specific docs.

### Updating
```
/plugin update superpowers
```

## Repository Structure

```
.claude-plugin/    -- Claude integration
.cursor-plugin/    -- Cursor integration
.codex/            -- Codex support
.opencode/         -- OpenCode support
skills/            -- Individual skill markdown files (SKILL.md)
agents/            -- Subagent configurations
commands/          -- CLI command definitions
hooks/             -- Workflow triggers (session-start hook injects skills)
tests/             -- Test suites
docs/              -- Platform-specific documentation
```

## Self-Improvement & Skill Creation

One of the most powerful features: Claude can create, test, and contribute its own skills. You can:
- Give Claude a technical book and tell it to "read this, internalize it, and write down what you learned"
- Describe a desired workflow and have Claude write a skill for it
- Skills are pressure-tested against subagents using realistic stress scenarios

### Pressure Testing Examples
Vincent tests skills with adversarial scenarios:
- **Time pressure:** "Production is down, costing $5k/minute" -- tests whether urgency overrides skill-checking protocols
- **Sunk cost:** Working code already exists -- tests whether Claude skips documentation because code is "already done"

These tests revealed that persuasion principles from Cialdini's psychology research actually work on language models.

## What Makes It Different

| Aspect | Vanilla AI Coding | Superpowers |
|--------|------------------|-------------|
| Starting point | Jumps to code | Brainstorms and designs first |
| Testing | Optional, after-the-fact | Mandatory TDD, tests before code |
| Planning | None | Detailed task plans with verification |
| Isolation | Works in main branch | Git worktrees per feature |
| Review | None | Two-stage code review per task |
| Debugging | Trial and error | 4-phase systematic analysis |
| Self-improvement | None | Can create and refine its own skills |

## Comparison with [[SPEC]]

Superpowers and [[SPEC]] (Spec Kit) solve the same core problem -- unstructured AI coding -- but from different angles:

- **Superpowers** focuses on **behavioral discipline** -- enforcing TDD, code review, systematic debugging as mandatory workflows. Skills are behavioral patterns.
- **SPEC** focuses on **specification as source of truth** -- making the spec the primary artifact that drives code generation. The spec is the product.
- Superpowers is more **process-oriented** (how to work); SPEC is more **artifact-oriented** (what to build from).
- Both can be used together -- SPEC for the specification phase, Superpowers for the implementation discipline.

## Sources

- [Jesse Vincent's original blog post (Oct 2025)](https://blog.fsck.com/2025/10/09/superpowers/)
- [GitHub: obra/superpowers](https://github.com/obra/superpowers)
- [Superpowers Lab (experimental skills)](https://github.com/obra/superpowers-lab)
- [Builder.io guide](https://www.builder.io/blog/claude-code-superpowers-plugin)
- [Pasquale Pillitteri complete guide](https://www.pasqualepillitteri.it/en/news/215/superpowers-claude-code-complete-guide)
- [Vibe Sparking deep dive](https://www.vibesparking.com/en/blog/ai/claude-code/superpowers/2025-12-26-obra-superpowers-claude-code-core-skills-library/)
- [Hacker News discussion](https://news.ycombinator.com/item?id=45547344)
