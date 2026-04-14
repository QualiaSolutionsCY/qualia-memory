# BMAD (Breakthrough Method for Agile AI-Driven Development)

**Type:** Agentic AI development framework with agile principles
**Full Name:** Breakthrough Method for Agile AI-Driven Development
**License:** MIT -- 100% free and open source, no paywalls, no gated content
**Repo:** [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD)
**Docs:** [docs.bmad-method.org](https://docs.bmad-method.org/)

---

## What It Is

BMAD is a structured, persona-driven framework that brings agile principles and "Agent-as-Code" practices to AI-assisted development. Instead of talking to a generic AI assistant, you work with specialized agent personas (Product Manager, Architect, Developer, etc.) -- each defined as a Markdown file describing expertise, responsibilities, constraints, and expected outputs.

At its core, BMAD tames the chaos of vibe coding by replacing it with a structured, repeatable workflow where each agent produces persistent, reviewable artifacts (PRDs, architecture docs, test plans) rather than ephemeral chat responses.

## The Problem It Solves

- **Context loss** between AI interactions
- **Inconsistent architecture** from unstructured prompting
- **Fragmented workflows** where planning, design, and coding blur together
- **Hallucinations** reduced by providing explicit behavioral specifications as contracts

---

## Agent Personas (Agent-as-Code)

Each agent is a Markdown file defining expertise, responsibilities, constraints, and expected outputs. Six default agents ship with BMAD:

| Agent | Name | Skill ID | Responsibilities |
|-------|------|----------|------------------|
| **Analyst** | Mary | `bmad-analyst` | Brainstorm project, research, create brief, PRFAQ challenge, document project |
| **Product Manager** | John | `bmad-pm` | Create/validate/edit PRD, create epics and stories, implementation readiness, course corrections |
| **Architect** | Winston | `bmad-architect` | Create architecture, validate implementation readiness |
| **Developer** | Amelia | `bmad-agent-dev` | Dev stories, quick dev, QA test generation, code review, sprint planning, epic retrospectives |
| **UX Designer** | Sally | `bmad-ux-designer` | Create UX design |
| **Technical Writer** | Paige | `bmad-tech-writer` | Document project, write docs, update standards, Mermaid diagrams, validate docs, explain concepts |

Additional roles referenced in some sources: **BMAD Master** (orchestrator), **Scrum Master**, **QA Engineer** -- likely available through modules or custom configuration.

---

## Four-Phase Workflow

### 1. Analysis
- Captures problem and constraints in a **one-page PRD**
- Analyst agent conducts research, gathers requirements, documents project foundations
- Output: Project brief, research findings, requirements document

### 2. Planning
- Breaks PRD into **user stories with explicit acceptance criteria**
- Product Manager creates epics and stories
- Scrum Master prioritizes by value and risk
- Output: Prioritized backlog with acceptance criteria

### 3. Solutioning
- Architect drafts **minimal design specifications**
- Developer proposes implementation steps
- UX Designer creates user flows and interfaces
- Output: Architecture document, UX specs, implementation plan

### 4. Implementation
- Iterative execution with small stories
- Artifacts updated rather than rebuilt from scratch
- Developer handles code, code review, QA test generation
- Sprint-based execution with retrospectives
- Output: Working code, test results, updated documentation

---

## Quality Gates

Stage-transition checkpoints that verify each artifact meets defined criteria before the next agent begins work. They operate at every workflow transition and catch:

- Inconsistencies between artifacts
- Missing requirements
- Coverage gaps
- AI-induced issues

Quality gates prevent problems from propagating downstream -- catching them early when they're cheap to fix.

---

## Three Operational Tracks

| Track | Use Case | Speed |
|-------|----------|-------|
| **Quick Flow** | Bug fixes, small features | ~5-minute tech specs |
| **Standard (BMAD Method)** | MVPs, new products | Full PRD/architecture cycle |
| **Enterprise** | Compliance-heavy projects | Audit trails, full governance |

---

## Two Project Modes

- **Greenfield:** Sequential design-first pipeline (Setup -> Analysis -> Planning -> Architecture -> UX -> Stories -> Dev -> Validation -> Iteration)
- **Brownfield:** Code-first or PRD-first for existing codebases

---

## Installation

```bash
npx bmad-method@next install
```

**Prerequisites:** Node.js v20+, Python 3.10+, uv package manager

**Non-interactive (CI/CD):**
```bash
npx bmad-method install --directory /path/to/project --modules bmm --tools claude-code --yes
```

---

## Official Modules

| Module | Code | Purpose |
|--------|------|---------|
| BMad Method | BMM | Core framework |
| BMad Builder | BMB | Custom agent/workflow creation |
| Test Architect | TEA | Risk-based testing strategy |
| Game Dev Studio | BMGD | Game development workflows |
| Creative Intelligence Suite | CIS | Innovation and design thinking |

---

## Key Artifacts Produced

- One-page PRD (Product Requirements Document)
- Architecture specifications
- User stories with acceptance criteria
- UX design documents
- Agent-as-Code persona definitions (Markdown)
- Implementation task lists / sprint plans
- Test plans
- Technical documentation

---

## Compatibility

**LLMs:** Claude Opus/Sonnet, Gemini Pro, GPT-5, Grok -- model-agnostic, works with any frontier LLM capable of following structured prompts.

**IDEs/Tools:** VS Code, Cursor, Windsurf, JetBrains, [[Claude Code]], GitHub Copilot, OpenCode.

**MCP Integrations:** SonarQube (code quality), Atlassian/Jira (tickets), Azure (infrastructure), GitHub (PRs), Figma (design components).

---

## Key Features

- **Scale-Domain-Adaptive** intelligence -- adjusts complexity from bug fixes to enterprise systems
- **34+ workflows** in the core framework
- **Party Mode** -- multiple agents collaborating simultaneously
- **AI Intelligent Help** via `bmad-help` skill for real-time guidance
- **12+ domain experts** available across modules

---

## Comparison to [[GSD]]

| Dimension | BMAD | GSD |
|-----------|------|-----|
| **Target User** | Teams, complex multi-stakeholder projects | Solo developers, technical founders |
| **Approach** | Role-based agent personas | Context-isolated phases |
| **Context Strategy** | Specialized agents with scoped artifacts | Fresh sub-agent context per task |
| **Overhead** | Higher (full agile lifecycle) | Lower (execution-focused) |
| **Ceremony** | PRDs, epics, stories, sprints | Plan -> Execute -> Review |
| **Best For** | Production-grade software, team workflows | Ship fast as a solo builder |
| **Flexibility** | Three operational tracks (quick/standard/enterprise) | Quick mode + full pipeline |

Both solve the same core problem: context degradation in AI-assisted development. BMAD solves it with specialized agent personas and artifact-driven handoffs. GSD solves it with fresh sub-agent contexts per task.

---

## Sources

- [GitHub: bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD)
- [BMAD Docs: docs.bmad-method.org](https://docs.bmad-method.org/)
- [Reenbit: BMAD Method Overview](https://reenbit.com/the-bmad-method-how-structured-ai-agents-turn-vibe-coding-into-production-ready-software/)
- [DEV.to: BMAD Agile Framework](https://dev.to/extinctsion/bmad-the-agile-framework-that-makes-ai-actually-predictable-5fe7)
- [Medium: BMAD vs Vibe Coding](https://medium.com/@visrow/bmad-method-vs-vibe-coding-structured-ai-development-vs-intuitive-programming-6d57957a42d5)
- [Medium: What is BMAD-METHOD](https://medium.com/@visrow/what-is-bmad-method-a-simple-guide-to-the-future-of-ai-driven-development-412274f91419)
- [Medium: BMAD vs Vibe Coding Clash](https://xantygc.medium.com/vibe-coding-vs-bmad-method-the-clash-of-titans-in-ai-development-f5ba2c0a5dcc)
