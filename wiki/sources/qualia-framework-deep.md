---
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [qualia-framework, claude-code, skills, agents, hooks, internal-tooling]
---

# Qualia Framework — Deep Reference

## At a Glance

### v1 (GitHub: `Qualiasolutions/qualia-framework`)
- **What it was:** The original Claude Code harness framework (v4.1.0 final)
- **Status:** ACTIVE. Latest release April 21, 2026. Not deprecated.
- **Public/Private:** Public (MIT license)
- **Version:** 4.1.0 (see `/tmp/v1-qualia/package.json`)
- **Install:** `npx qualia-framework@latest install`
- **Last release:** April 21, 2026 (commit `6800faf`)
- **Description:** "Claude Code workflow framework by Qualia Solutions. Plan, build, verify, ship."

### v2 (Local package: `qualia-framework-v2`)
- **What it is:** Simplified, production-hardened version of v1
- **Status:** ACTIVE. Version 2.7.0 (as cached in npx)
- **Public/Private:** Public (MIT license, repo: `qualia-solutions/qualia-framework-v2`)
- **Install:** Installed via npx cache from private or public v2 repo
- **Key difference:** Removed legacy skills (`qualia-discuss`, `qualia-help`, `qualia-map`, `qualia-milestone`, `qualia-optimize`, `qualia-research`, `qualia-test`). Simplified agent count (4 vs 8). Added environment guards (`block-env-edit.js` hook).
- **Purpose:** Cleaner, faster, team-hardened variant of v1. Intended for standardized team workflows.

---

## v2 Architecture

### Top-Level Structure
```
skills/           — 19 reusable command implementations
agents/           — 4 specialized subagents for planning, building, verifying, QA
hooks/            — 8 event handlers (Node.js, cross-platform)
rules/            — 4 documented constraints (security, frontend, design, deployment)
templates/        — Starter files for new projects
bin/cli.js        — CLI entrypoint
CLAUDE.md         — Context for Claude Code sessions
guide.md          — Quick-start guide for developers
```

### Skills (19 total, v2)
1. **qualia** — Smart router (state-driven next-command suggester)
2. **qualia-build** — Execute current phase (builder subagents, wave-based parallelization)
3. **qualia-debug** — Structured debugging (symptom → diagnosis → root cause → fix)
4. **qualia-design** — One-shot design transformation (critique, polish, hardening)
5. **qualia-handoff** — Client delivery (credentials, handover doc, final update)
6. **qualia-idk** — Alias for `/qualia` (handles 'idk', 'I'm stuck', 'what now' queries)
7. **qualia-learn** — Save learning, pattern, fix to persistent knowledge base
8. **qualia-new** — Project setup wizard (interactive step-by-step questioning)
9. **qualia-pause** — Save session context to `.continue-here.md`
10. **qualia-plan** — Plan current phase (planner agent, fresh context)
11. **qualia-polish** — Design/UX pass (critique, polish, harden, accessible)
12. **qualia-quick** — Fast path for small tasks (skips planning)
13. **qualia-report** — Generate session report + commit to repo (MANDATORY)
14. **qualia-resume** — Restore context from previous session (reads `.continue-here.md` or `STATE.md`)
15. **qualia-review** — Production audit & code review (general, `--web`, `--ai` flags)
16. **qualia-ship** — Deploy to production (quality gates → deploy → verify)
17. **qualia-skill-new** — Author new skill or agent (reusable command creator)
18. **qualia-task** — Build single task (heavier than quick, lighter than build)
19. **qualia-verify** — Goal-backward verification (verifier agent, checks actual functionality)

### Skills Removed from v1 → v2 (7 total)
- **qualia-discuss** — Pre-planning decision capture (v1 feature, removed in v2)
- **qualia-help** — Open reference guide in browser (v1 feature, removed)
- **qualia-map** — Infer codebase architecture (v1 feature, removed)
- **qualia-milestone** — Close milestone, open next (v1 feature, removed)
- **qualia-optimize** — Deep optimization pass (v1 feature, removed)
- **qualia-research** — Deep research a domain (v1 feature, removed)
- **qualia-test** — Generate/run tests (v1 feature, removed)

### Agents (4 total, v2)
1. **builder** — Executes single task from plan. Fresh context per task.
2. **planner** — Creates executable phase plans (task breakdown, waves, verification contracts).
3. **qa-browser** — Real-browser QA. Navigates dev server, checks layout (mobile/tablet/desktop), clicks flows, captures console + a11y issues.
4. **verifier** — Goal-backward verification. Checks if phase ACTUALLY works.

### Agents in v1 (8 total, v2 removed 4)
- **builder**, **planner**, **qa-browser**, **verifier** (same as v2)
- **plan-checker** ✓ (removed in v2) — Validated plans before execution
- **researcher** ✓ (removed in v2) — Deep research one dimension in parallel
- **research-synthesizer** ✓ (removed in v2) — Merged 4 research outputs
- **roadmapper** ✓ (removed in v2) — Created JOURNEY.md, REQUIREMENTS.md, ROADMAP.md

### Hooks (8 total, v2)
1. **auto-update.js** — Auto-updates framework on session start
2. **block-env-edit.js** ⭐ — NEW in v2: Blocks `.env` file edits by non-OWNER (PreToolUse gate)
3. **branch-guard.js** — Prevents pushes to main/master (employees only; OWNER bypasses)
4. **migration-guard.js** — Catches dangerous SQL (DROP, DELETE without WHERE, unRLS tables)
5. **pre-compact.js** — Saves state before context compression
6. **pre-deploy-gate.js** — Quality gates before deploy (tsc, lint, build, tests, service_role scan)
7. **pre-push.js** — Stamps `tracking.json` via bot commit
8. **session-start.js** — Loads project context automatically

### Rules (4 in v2, 6 in v1)
**v2 rules:**
- **security.md** — Auth patterns, RLS, Zod, secret management
- **frontend.md** — Design standards, responsive, accessible
- **deployment.md** — Deploy checklist
- **design-reference.md** — Visual/brand guidelines

**v1 additional rules (removed from v2):**
- **grounding.md** — Removed (consolidated into agent prompts)
- **infrastructure.md** — Removed (references moved to CLAUDE.md)

---

## Key Concepts

### "The Road" (Project Lifecycle Flow)

**Linear progression from kickoff to handoff:**
```
/qualia-new          ← Set up project once. Creates initial PROJECT.md, maps journey.
     ↓
For each phase of the current milestone:
  /qualia-plan       ← Plan the phase (fresh planner context)
  /qualia-build      ← Build tasks (parallel waves, fresh builder per task)
  /qualia-verify     ← Verify it works (goal-backward checks)
     ↓
(Implicit: phases repeat until milestone complete)
     ↓
/qualia-polish       ← Design pass (part of Handoff milestone, phase 1)
/qualia-ship         ← Deploy to production
/qualia-handoff      ← Deliver 4 mandatory artifacts
     ↓
Done.
```

**v1 additions (removed in v2):**
- `/qualia-milestone` — Explicit close-milestone gate (v2 removes; auto-advances)
- `/qualia-discuss` — Pre-planning decision lock (v2 removes)
- `/qualia-research` — Deep research before planning (v2 removes; `/qualia-new` researches upfront)
- `/qualia-map` — Infer brownfield codebase (v2 removes)
- `/qualia-optimize` — Comprehensive optimization audit (v2 removes)

### Context Isolation (Core Architecture)
- **Goal:** Task N gets same quality as Task 1 (no context rot)
- **Mechanism:** Fresh subagent spawned per task with inlined `.planning/PROJECT.md` + phase requirements
- **Planner:** Receives PROJECT.md + phase goal
- **Builder:** Receives single task from plan + PROJECT.md
- **Verifier:** Receives success criteria + read-only codebase access
- **No accumulation:** No garbage from previous tasks

### Quality Gates (Always Active in v2)
1. **Frontend guard** — Must read `.planning/DESIGN.md` before frontend changes
2. **Deploy guard** — `tsc`, `lint`, `build`, `tests` must pass before `vercel --prod`
3. **Branch guard** — Employees cannot push to main (OWNER can)
4. **Env guard** ⭐ — Employees cannot edit `.env` (OWNER can; `block-env-edit.js` hook)
5. **Sudo guard** ⭐ — Employees cannot run `sudo` (OWNER can)
6. **Intent verification** — Confirm before modifying 3+ files (OWNER: auto-approve)
7. **Migration guard** — Block dangerous SQL
8. **Service-role leak scan** — Block client-code exposure of Supabase `service_role`

**v1 additional gates (removed/simplified in v2):**
- **Migration guard** still present, but references to `grounding.md` removed
- **Intent verification** simplified from v1 rubric to binary confirm/skip

### Tracking — `.planning/tracking.json`
**Structure:**
```json
{
  "project": "",
  "client": "",
  "type": "",
  "assigned_to": "",
  "team_id": "",
  "project_id": "",
  "git_remote": "",
  "milestone": 1,
  "milestone_name": "",
  "milestones": [],
  "phase": 0,
  "phase_name": "",
  "total_phases": 0,
  "status": "setup",
  "wave": 0,
  "tasks_done": 0,
  "tasks_total": 0,
  "verification": "pending",
  "gap_cycles": {},
  "blockers": [],
  "session_started_at": "",
  "last_updated": "",
  "last_pushed_at": "",
  "last_commit": "",
  "build_count": 0,
  "deploy_count": 0,
  "deployed_url": "",
  "report_seq": 0,
  "notes": "",
  "submitted_by": "",
  "lifetime": {
    "tasks_completed": 0,
    "phases_completed": 0,
    "milestones_completed": 0,
    "total_phases": 0,
    "last_closed_milestone": 0
  }
}
```
- **Updated on every push** via `pre-push.js` hook
- **ERP reads it automatically** for status dashboards
- **Never edit manually** — hooks manage it from STATE.md
- **Milestone tracking** — `milestones[]` stores metadata for each closed milestone

### Wave-Based Parallelization
- **Mechanism:** Planner assigns wave numbers to tasks based on file dependency graph
- **Algorithm:** Topological sort by reads/writes adjacency (deterministic, not ML)
- **Safety:** No two tasks in same wave share write targets
- **Execution:** Builder spawns agents per wave; waves execute sequentially, tasks within wave in parallel

---

## Stack It Assumes

**Frontend:**
- Next.js 16+
- React 19
- TypeScript
- Tailwind CSS (inferred from rules/frontend.md)

**Backend:**
- Supabase (PostgreSQL, Auth, RLS)
- Next.js API Routes / Edge Functions
- Server Actions (App Router)

**Services:**
- **Deployment:** Vercel (primary)
- **Voice:** VAPI (v2), ElevenLabs, Telnyx, Retell AI (v1)
- **AI/LLMs:** OpenRouter
- **Compute/Agents:** Railway (background jobs)

**Documented in:**
- v1: `rules/infrastructure.md` (detailed service list)
- v2: `CLAUDE.md` stack section (simplified)

---

## v1 → v2 Migration / Rationale

### Why v2 Exists
1. **Simplified workflow** — Removed research/discuss/optimize phases; `/qualia-new` does research upfront
2. **Fewer agents** — Removed plan-checker, researcher, research-synthesizer, roadmapper (4 → 8 agent contracts hardened)
3. **Stricter team guards** — Added `block-env-edit.js` to prevent accidental config leaks
4. **Faster iteration** — No explicit milestone boundary; phases auto-advance until manual gate
5. **v1 bloat cleaned** — Removed 7 skills; v2 is ~26% smaller

### What Broke / Changed
- **Removed skills** — `/qualia-discuss`, `/qualia-help`, `/qualia-map`, `/qualia-milestone`, `/qualia-optimize`, `/qualia-research`, `/qualia-test` no longer available
- **Auto mode removed** — v1's `--auto` chaining deleted; v2 uses manual phase progression
- **Agents consolidated** — v2 removed plan-checker (planner output trusted), researcher/synthesizer/roadmapper (v2-new does research inline)
- **Rules removed** — `grounding.md`, `infrastructure.md` (consolidated into prompts + CLAUDE.md)
- **Guard changes** — Added `block-env-edit.js` + `sudo-guard`, sharpened intent verification

### Backward Compatibility
- **Not compatible.** v1 projects cannot use v2 CLI directly. Would require rewriting phase plans, STATE.md format.
- **v1 is NOT deprecated** — Both versions coexist; projects pick one at init.

---

## Roadmap / Open Questions

### v1 Recent Activity (Last 2 months)
- **2026-04-21:** v4.1.0 release — "command quality + build workflow hardening"
- **2026-04-20:** PR #6 merged — feat/command-quality-quickwins
- **2026-04-19:** feat(skills) round 4 — parallel fan-out in design; investigative qualia-debug; review speed fixes
- **2026-04-17:** feat(agents) — harden 7 agent contracts (Rule 8, tool budgets, frontend gate, dep graph, typed inputs)
- **2026-04-10:** release v4.0.5 — statusline refresh

### v2 Recent Activity
- **Unknown** — No git history available in cached npx package (version 2.7.0 frozen)
- **Last recorded change:** April 10 (based on cache date)

### Known Open Questions
1. **v2 repository location** — Is it `qualia-solutions/qualia-framework-v2` or private? (Assumed public per package.json)
2. **v2 roadmap** — Are there planned re-additions of removed skills (qualia-optimize, qualia-research)?
3. **Unified future?** — Will v1 and v2 merge or stay parallel?
4. **License migration** — Both MIT; no changes expected.

---

## How to Run / Use

### Quick Command Reference

| Command | When | What |
|---------|------|------|
| `/qualia-new` | Start | Interactive wizard: project name, type, stack, milestones |
| `/qualia-plan` | Building | Plan current phase (planner agent, fresh context) |
| `/qualia-build` | Building | Build tasks in waves (builder subagents) |
| `/qualia-verify` | Building | Verify phase works (goal-backward, real code checks) |
| `/qualia-quick` | Small tasks | Skip planning, just code (bug fix, tweak, hot fix) |
| `/qualia-task` | Medium tasks | Build one focused task (heavier than quick, lighter than build) |
| `/qualia-debug` | Bugs | Structured debugging (symptom → root cause → fix + DEBUG report) |
| `/qualia-design` | UI/UX issues | One-shot design pass (no choices, just make it professional) |
| `/qualia-polish` | Handoff | Design/UX pass (critique, polish, harden, accessible) |
| `/qualia-ship` | Ready | Deploy to production (gates, commit, push, deploy, verify) |
| `/qualia-handoff` | Final | Deliver 4 artifacts: credentials, doc, final update, ERP report |
| `/qualia` | Lost | Smart router (reads STATE.md, returns next command) |
| `/qualia-report` | End of day | Generate session report + commit (MANDATORY) |
| `/qualia-pause` | Pausing | Save context to `.continue-here.md` for seamless handoff |
| `/qualia-resume` | Resuming | Restore from `.continue-here.md` or STATE.md |
| `/qualia-review` | QA | Production audit (general, `--web`, `--ai` flags) |
| `/qualia-learn` | Learning | Save pattern/fix to persistent knowledge base |
| `/qualia-skill-new` | New skill | Author reusable command/agent |

### Typical Workflow (v1)
```
1. /qualia-new
   → Deep research (4 parallel researchers, 1 synthesizer)
   → JOURNEY.md written (all milestones upfront)
   → Approval gate

2. For each milestone:
   a. For each phase:
      /qualia-plan → /qualia-build → /qualia-verify
   b. /qualia-milestone (close, archive, prep next)

3. Final milestone (Handoff):
   /qualia-polish → /qualia-ship → /qualia-handoff → /qualia-report → Done
```

### Typical Workflow (v2)
```
1. /qualia-new
   → Simple interactive Q&A
   → PROJECT.md created

2. For each phase (manual progression):
   /qualia-plan → /qualia-build → /qualia-verify → /qualia (next?)

3. When ready to finish:
   /qualia-polish → /qualia-ship → /qualia-handoff → /qualia-report → Done
```

### When to Use Which Skill
- **Setup:** `/qualia-new` (both v1, v2)
- **Planning:** `/qualia-plan` (both)
- **Execution:** `/qualia-build` (both)
- **Verification:** `/qualia-verify` (both)
- **Quick fix:** `/qualia-quick` (both)
- **Single task:** `/qualia-task` (both)
- **Design issue:** `/qualia-design` (both)
- **Bug investigation:** `/qualia-debug` (both)
- **Confused (about command):** `/qualia` (both)
- **Stuck (about situation):** `/qualia-idk` (v1 only; v2 aliases to `/qualia`)
- **Design polish:** `/qualia-polish` (both)
- **Ready to deploy:** `/qualia-ship` (both)
- **Client handoff:** `/qualia-handoff` (both)
- **End of day:** `/qualia-report` (both)
- **Need research:** `/qualia-research` (v1 only)
- **Map codebase:** `/qualia-map` (v1 only)
- **Optimize:** `/qualia-optimize` (v1 only)
- **Test generation:** `/qualia-test` (v1 only)
- **Run tests:** `/qualia-test` (v1 only)

---

## Install & Upgrade

### v1
```bash
npx qualia-framework@latest install
# Installs to ~/.claude/
# Prompts for team code (get from Fawzi)

npx qualia-framework@latest version     # Check version + updates
npx qualia-framework@latest update      # Upgrade (remembers team code)
npx qualia-framework@latest uninstall   # Clean removal
npx qualia-framework@latest team list   # Show team members
npx qualia-framework@latest team add    # Add member
npx qualia-framework@latest traces      # View hook telemetry
```

### v2
- Installed from npx cache or private repo
- Same pattern as v1 (exact commands unknown; infer same structure)

### Cache Management
**Important:** `npx` caches at `~/.npm/_npx/` with no TTL. Always use `@latest`:
```bash
npx qualia-framework@latest install  # Good
npx qualia-framework install         # BAD — uses stale cache
npx clear-npx-cache                  # Manual cache clear
```

---

## Related

- [[wiki/concepts/agency-delivery-model]] — Agency operating model (Qualia's service delivery framework)
- [[Qualia Framework]] — Existing wiki page (Projects/)
- [[Qualia Framework v2]] — Existing wiki page (Projects/)

---

## File References

### v1 Key Files
- `/tmp/v1-qualia/CHANGELOG.md` — Release notes
- `/tmp/v1-qualia/README.md` — Full documentation
- `/tmp/v1-qualia/guide.md` — Developer quick-start
- `/tmp/v1-qualia/CLAUDE.md` — Session context
- `/tmp/v1-qualia/skills/*/SKILL.md` — Skill definitions
- `/tmp/v1-qualia/agents/*.md` — Agent contracts
- `/tmp/v1-qualia/hooks/*.js` — Event handlers
- `/tmp/v1-qualia/rules/*.md` — Constraints
- `/tmp/v1-qualia/V4_REVIEW.md` — v4 audit findings

### v2 Key Files
- `/home/qualia/.npm/_npx/fd11f2e48791b9cf/node_modules/qualia-framework-v2/package.json`
- `/home/qualia/.npm/_npx/fd11f2e48791b9cf/node_modules/qualia-framework-v2/guide.md`
- `/home/qualia/.npm/_npx/fd11f2e48791b9cf/node_modules/qualia-framework-v2/CLAUDE.md`
- `/home/qualia/.npm/_npx/fd11f2e48791b9cf/node_modules/qualia-framework-v2/skills/*/SKILL.md`

---

**Report generated:** April 22, 2026 from GitHub clones and local npx caches.
