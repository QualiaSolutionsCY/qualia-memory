# Qualia Framework v2

**Status:** Built
**Repo:** `/home/qualia/Projects/qualia-framework-v2/`
**Replaces:** Old qualia-framework (200+ files → 30 files)

## What It Is
Claude Code workflow framework for [[Qualia Solutions]]. Guides [[Moayad]], [[Hasan]], [[Sally]], [[Rama]] from project setup to client handoff.

## The Road
1. `/qualia-new` — set up project
2. `/qualia-plan` → `/qualia-build` → `/qualia-verify` (repeat per phase)
3. `/qualia-polish` → `/qualia-ship` → `/qualia-handoff`
4. `/qualia-report` — mandatory session log
5. `/qualia` — "what's next?" router

## Core Concepts
- **Context isolation** — fresh AI context per task (from [[GSD]])
- **Goal-backward verification** — greps code, doesn't trust claims (from old Qualia)
- **Plans-as-prompts** — plan files ARE builder instructions (from old Qualia)
- **Wave execution** — parallel independent tasks (from old Qualia)
- **ERP integration** — `tracking.json` synced via git

## Architecture
- 10 skills, 3 agents (planner/builder/verifier), 6 hooks, 3 rules, 4 templates
- ~3,000 lines total (old: 64,000)

## Related
- [[GSD]] — context isolation inspiration
- [[SUPERPOWERS]] — discipline enforcement inspiration
- [[SPEC]] — spec-first approach inspiration
- [[BMAD]] — quality gates inspiration
- [[Qualia ERP]] — reads tracking.json from git
