---
type: concept
created: 2026-04-21
updated: 2026-04-21
tags: [agency, delivery, qualia-framework, subagents, ops]
---

# Agency Delivery Model

> Why Qualia ships ~40 projects across 6 countries with a team of 5 — the AI-native delivery layer.

## The stack underneath

- **Qualia Framework v2** — Claude Code harness with 26 skills, 8 agents, 7 hooks. This is the codebase equivalent of an SOP manual for shipping software.
- **Fresh-context subagents** — planner, builder, verifier all run in isolated contexts so there's no "context rot" on project #50.
- **Qualia ERP** — operational source of truth (portal.qualiasolutions.net). Clients, projects, invoices, phases, tasks, session reports, voice AI — all in one Supabase-backed Next.js 16 app.
- **Git-backed tracking** — `.planning/tracking.json` updated by hooks on every push. ERP reads it via git pulls. No manual status updates.

## How work actually moves

```
/qualia-new           → kickoff + parallel research
/qualia-plan          → plan the phase (planner + plan-checker loop)
/qualia-build         → build it (builder subagents, wave-based parallel)
/qualia-verify        → goal-backward check (verifier agent)
/qualia-milestone     → close milestone, archive artifacts
/qualia-ship          → deploy to production
/qualia-handoff       → credentials, docs, update, report
```

Human gates: journey approval + one per milestone. `--auto` runs everything in between.

## Why this beats "hire more people"

- New hire ramp ≈ 3 months. New Claude Code session ramp ≈ 30 seconds.
- Humans degrade on task #50 of a repetitive build. Subagents with fresh context do not.
- Agency margin is a function of time-to-ship. Subagents compress time-to-ship by ~60% on well-scoped work.
- The rare hire (like Sally, full-time June 2026) does strategic work, not scaffolding.

## What humans do in this model

- Scope and price. The first conversation with the client.
- Design taste. `.planning/DESIGN.md` is a human artifact.
- Trust and handshakes. No agent is going to close a retainer.
- Security review. RLS policies, secrets handling, auth gates — human-checked every time.
- Writing on behalf of the company. Voice, tone, positioning.

## What subagents do

- Planning, building, verifying, testing, deploying.
- Code review (vercel-plugin agents), UI review (qualia-design).
- Research (context7 / webfetch / websearch for stack & pitfalls).
- Documentation.

## Related

- [[wiki/analysis/qualia-ai-thesis]]
- [[wiki/concepts/sme-ai-automation]]
- [[Qualia Framework v2]]
- [[Qualia ERP]]
