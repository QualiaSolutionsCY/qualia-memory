---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia, internal-tool]
source_url: https://github.com/Qualiasolutions/qualia-framework
---

# qualia-framework

> Harness engineering framework for Claude Code — structured planning, execution, verification, and deployment gates for AI-assisted development.

## Overview

Qualia Framework v3 is an opinionated workflow layer that installs into `~/.claude/` and wraps AI-assisted development with structured planning, execution, verification, and deployment gates. It is not an application framework — it tells Claude Code how to plan, build, and verify projects. Applies lessons from Anthropic's "Harness Design for Long-Running Apps" article with scored evaluator rubrics, verification contracts, and hook telemetry.

## Tech Stack

- JavaScript / Node.js (hooks and state machine)
- Claude Code (slash commands / skills)
- Cross-platform (Windows, macOS, Linux)
- npm (distribution via `npx qualia-framework install`)

## Status

- **Language:** JavaScript
- **Last updated:** 2026-04-14
- **Visibility:** Public

## Key Features

- 26 skills (slash commands) from setup to handoff
- 8 agents: planner, builder, verifier, qa-browser, researcher, research-synthesizer, roadmapper, plan-checker
- 7 hooks: session start, branch guard, pre-push tracking, migration guard, deploy gate, pre-compact state save, auto-update
- Goal-backward verification (scores Correctness, Completeness, Wiring, Quality 1-5)
- Agent separation with fresh context per task (prevents context rot)
- Wave-based parallel task execution
- Enforced state machine with atomic STATE.md + tracking.json updates
- Plans-as-prompts (plan files ARE the prompts, no translation loss)
- Team management with role-based access (owner vs employee guards)
- Knowledge base persistence across projects and sessions

## Related

- [[Qualia ERP]]
- [[qualiafinal]]
