# GSD (Get Shit Done) Framework

**Type:** Context engineering / spec-driven development framework for AI coding agents
**Creator:** TACHES (developer handle)
**Target User:** Solo developers and technical founders
**License:** Open source
**Repo:** [gsd-build/get-shit-done](https://github.com/gsd-build/get-shit-done)
**v2 Repo:** [gsd-build/gsd-2](https://github.com/gsd-build/gsd-2)

---

## What It Is

GSD is a meta-prompting and context engineering orchestration layer that sits on top of [[Claude Code]] (and other AI coding tools). It structures complex projects into phases, sub-plans, and atomic tasks -- each executed in a fresh sub-agent context window to prevent context rot.

The core insight: mixing planning, coding, and review in one long session degrades LLM output quality. GSD enforces isolation between cognitive tasks so each gets Claude's full attention with a clean 200k token window.

## The Problem It Solves

**Context Rot** -- the longer a conversation goes, the worse the model performs. Traditional frameworks (BMAD, SpecKit, Taskmaster) execute planning, research, development, and verification in a single context window. GSD eliminates this by spawning fresh sub-agent instances per task. Task 50 gets the same quality as Task 1.

## Core Philosophy

- Structure over speed
- Repeatability over raw throughput
- Context isolation is non-negotiable
- Every task = one atomic git commit (isolated, reversible, reviewable)
- Specification-driven: plan before you code

---

## Three Foundational Phases

### Phase 1: Plan
- Produce a written specification before any code
- Output: structured plan document
- Deliverables: numbered implementation steps, files to create/modify, function signatures, edge cases, unresolved questions
- Key rule: "Do not write any code yet"
- Fresh conversation session with only problem statement + constraints

### Phase 2: Execute
- Implement the plan exactly as specified
- Input: plan document from Phase 1
- Can be broken into sub-phases (2a, 2b, 2c) for large tasks
- Dense XML prompts sent to Claude specifying exact requirements
- Verification criteria run before marking complete
- Human verification required before proceeding
- Automatic git commit after every task completion
- Key rule: "Do not deviate from the plan without flagging it first"

### Phase 3: Review
- Stress-test code against **original requirements** (not the plan -- catches plan-level errors)
- Fresh context session with clean perspective
- Review criteria: correctness, edge cases, security, performance at scale, code quality
- Output: structured review report with severity ratings

---

## Six-Step Workflow (v1)

| Step | Command | Purpose |
|------|---------|---------|
| 1. Initialize | `/gsd-new-project` | Questions -> Research -> Requirements -> Roadmap approval |
| 2. Discuss | `/gsd-discuss-phase {N}` | Capture design preferences before planning |
| 3. Plan | `/gsd-plan-phase {N}` | Researcher investigates, planner creates 2-3 atomic tasks with XML, verifier validates |
| 4. Execute | `/gsd-execute-phase {N}` | Runs plans in dependency-sorted waves (parallel when independent, sequential when dependent) |
| 5. Verify | `/gsd-verify-work {N}` | Walk through testable deliverables, auto-diagnose failures, generate fix plans |
| 6. Ship | `/gsd-ship {N}` | Ship phase -> `/gsd-complete-milestone` -> `/gsd-new-milestone` |

**Quick mode:** `/gsd-quick` for ad-hoc tasks without full phase planning.
**Auto-detect:** `/gsd-next` picks the next logical step.

---

## File Structure (`.planning/` directory)

```
.planning/
├── PROJECT.md           # Vision & overview (high-level source of truth)
├── REQUIREMENTS.md      # Scoped v1/v2 features with phase mapping
├── ROADMAP.md           # Progress tracking across phases
├── STATE.md             # Decisions, blockers, cross-session memory
├── research/            # Domain analysis & ecosystem knowledge
├── todos/               # Captured ideas for later work
├── threads/             # Persistent context for cross-session work
├── seeds/               # Forward-looking concepts
├── quick/               # Ad-hoc task tracking
└── {phase_num}/
    ├── CONTEXT.md       # User preferences & gray area decisions
    ├── RESEARCH.md      # Implementation investigation
    ├── {N}-PLAN.md      # XML-structured atomic tasks
    ├── {N}-SUMMARY.md   # Execution results & changes
    ├── VERIFICATION.md  # Quality gates & testing
    └── UAT.md           # User acceptance testing
```

### Three Core Documents

1. **PROJECT.md** -- The high-level source of truth. Core values, key requirements, auth status.
2. **ROADMAP.md** -- Tactical. Exact phases and deliverables required. The "how."
3. **STATE.md** -- Tracks progress. When you spin up a new session, this file tells the AI exactly where it left off.

### Task Hierarchy

```
Project
  └── Phases (7 distinct phases per milestone)
       └── Sub-plans (e.g., Plan 0601, 0602)
            └── Atomic Tasks (max 2-3 per sub-plan)
```

---

## GSD v2 (Major Evolution)

v2 transforms GSD from a prompt-injection system into a **standalone CLI** built on the Pi SDK. It has programmatic control over the agent harness itself.

### Installation

```bash
npm install -g gsd-pi@latest
```

### What Changed

| Aspect | v1 | v2 |
|--------|----|----|
| Runtime | Claude Code slash commands | Standalone CLI (Pi SDK) |
| Context | Hope LLM doesn't overflow | Fresh session per task (guaranteed) |
| Auto mode | LLM self-loop | State machine reading `.gsd/` files |
| Recovery | None | Lock files + session forensics |
| Git | LLM writes commands | Worktree isolation + sequential commits |
| Cost tracking | None | Per-unit token ledger |
| Stuck detection | None | Retry once, then diagnostics |

### v2 Task Hierarchy

```
Milestone -> shippable version (4-10 slices)
  Slice -> demoable capability (1-7 tasks)
    Task -> context-window-sized unit
```

**Iron rule: A task must fit in one context window.**

### v2 File Structure (`.gsd/` directory)

**Core Documents:**
- `PROJECT.md` -- current project state
- `DECISIONS.md` -- append-only architecture log
- `KNOWLEDGE.md` -- cross-session rules
- `RUNTIME.md` -- API endpoints, env vars
- `STATE.md` -- quick-glance dashboard

**Milestone-Specific:**
- `M001-ROADMAP.md` -- slices with risk levels
- `M001-CONTEXT.md` -- user decisions
- `M001-RESEARCH.md` -- ecosystem research

**Task-Specific:**
- `S01-PLAN.md` -- slice decomposition
- `T01-PLAN.md` -- individual task with verification

### Two Execution Modes

- **Step Mode** (`/gsd` or `/gsd next`) -- one unit at a time with pauses. Hands-on.
- **Auto Mode** (`/gsd auto`) -- fully autonomous: researches, plans, executes, verifies, commits, repeats until milestone completion.

### Headless Mode (CI/CD)

```bash
gsd headless --timeout 600000    # Run auto in CI
gsd headless next                # One unit at a time (cron-friendly)
gsd headless query               # Instant JSON state snapshot (~50ms, no LLM call)
```

---

## Supported Tools

Claude Code, OpenCode, Gemini CLI, Kilo, Codex, Copilot, Cursor, Windsurf, Antigravity, Augment, Trae, Cline.

## Installation (v1)

```bash
npx get-shit-done-cc@latest
```

Choose runtime and installation scope (global or local). Installs as skills (`skills/gsd-*/SKILL.md`) for Claude Code 2.1.88+.

---

## Key Guarantees

- Fresh context per execution (zero accumulated garbage)
- Built-in quality gates (schema drift detection, security enforcement, scope validation)
- Atomic commits per task (clean git history)
- Wave-based parallel execution (dependency resolution)
- Verification loops (plans verified before execution, work verified after)

---

## Comparison to [[BMAD]]

GSD is streamlined for **solo developers**. It strips away enterprise overhead and agile ceremony to focus on execution for one person building an app. BMAD is more comprehensive with role-based agents and full agile lifecycle -- better for teams or complex multi-stakeholder projects.

Both solve the same core problem: context degradation in AI-assisted development. GSD solves it with fresh sub-agent contexts. BMAD solves it with specialized agent personas and artifact-driven handoffs.

---

## Sources

- [MindStudio: What Is the GSD Framework](https://www.mindstudio.ai/blog/gsd-framework-claude-code-clean-context-phases)
- [Chase AI: GSD for Solo Developers](https://www.chaseai.io/blog/gsd-claude-code-solo-developer)
- [GitHub: gsd-build/get-shit-done](https://github.com/gsd-build/get-shit-done)
- [GitHub: gsd-build/gsd-2](https://github.com/gsd-build/gsd-2)
- [Neon Nook: Rise of GSD AI Frameworks](https://neonnook.substack.com/p/the-rise-of-get-shit-done-ai-product)
- [The New Stack: Beating Context Rot with GSD](https://thenewstack.io/beating-the-rot-and-getting-stuff-done/)
