---
title: "Claude Code Operational Gotchas"
aliases: [claude-code-gotchas, session-gotchas, hook-gotchas]
tags: [claude-code, operational, gotchas, hooks]
sources:
  - "daily/2026-04-26.md"
created: 2026-04-26
updated: 2026-04-26
---

# Claude Code Operational Gotchas

A collection of non-obvious operational pitfalls discovered while using Claude Code in production across the Qualia ecosystem on 2026-04-26. These are tool-level behaviors that don't fit neatly into any single feature article but repeatedly cause friction if not anticipated.

## Key Points

- **CWD invalidation:** Deleting the current working directory mid-session (`rm -rf /path/to/project`) invalidates the Bash tool's CWD — all subsequent shell commands fail until a fresh session is started or `cd` redirects to a valid path
- **Hook directory assumption:** Hooks fire in environments where `~/.claude/` may not yet exist — `logEvent` in the Stop hook silently swallowed ENOENT until a defensive `mkdir` guard was added
- **CronCreate auto-expiry:** Claude Code `CronCreate` recurring tasks auto-expire after 7 days — cannot rely on them for permanent schedules; use system crontab directly for anything that must persist
- **Hook restart requirement:** The Stop hook (and other newly installed hooks) won't fire until the Claude Code session is restarted after framework install — the session loads hooks at startup, not dynamically
- **Obsidian graph.json race:** Obsidian holds graph configuration in memory and flushes to disk when the graph view closes — external edits to `graph.json` are silently overwritten if the graph tab is open
- **`next build` zombie processes:** `next build` can leave zombie processes that hold ports and block subsequent Vercel deploys — kill stale `next build` PIDs before retrying `vercel --prod`
- **Framework state drift:** When `.planning/` bookkeeping drifts (missing `tracking.json`, `JOURNEY.md`, stale `STATE.md`), `state.js` returns `NO_PROJECT` and framework commands fail — bootstrap state manually first, then commands work going forward

## Details

The CWD invalidation gotcha surfaced during the aandnglobal project housekeeping session at 19:04. After pushing `CLAUDE.md` to `origin/main` (commit `a0a4673`), the entire `/home/qualia-new/Projects/aandnglobal` directory was deleted. This left the Bash tool pointing at a non-existent directory, rendering all subsequent shell commands broken. The user's final message ("cd ,,") was a typo attempting to recover. The practical rule is: never delete the directory you're currently working in during a Claude Code session. If cleanup requires directory deletion, either `cd` out first or close and reopen the session.

The hook directory issue was caught during the v4.3.0 release cycle. The `logEvent` function in the Stop hook assumed `~/.claude/` existed and failed silently when it didn't, which meant early sessions after a fresh install produced no event logs. The fix was a one-line `mkdir -p` guard, but the broader lesson applies to all hooks: never assume directories exist in hook context. This is especially relevant for hooks that fire in CI, Docker containers, or fresh user environments.

The CronCreate limitation was discovered when setting up weekly wiki-lint and knowledge flush schedules. Claude Code's built-in cron system is designed for short-term reminders (7-day max), not persistent automation. The workaround for permanent schedules is to use the system crontab directly, as was done for the weekly knowledge flush: `0 3 * * 0 node ~/.claude/bin/knowledge-flush.js`.

The `next build` zombie process issue was discovered during the claude-memory-compiler Dependabot vulnerability sweep at 19:20. After running `next build` (either directly or via `vercel build`), stale Node processes can remain alive holding ports, which causes subsequent `vercel --prod` deploys to hang or fail. The fix is to check for and kill any lingering `next build` PIDs before retrying the deploy. This is particularly insidious because the zombie process may not be visible without explicitly checking `ps aux | grep next`.

Framework state drift was encountered in the same session when the claude-memory-compiler project's `.planning/` directory had fallen out of sync: `tracking.json` was missing entirely, `JOURNEY.md` didn't exist, and `STATE.md` was stale by approximately one day. The framework's `state.js` router returned `NO_PROJECT`, which meant all `/qualia-*` commands would either fail or misroute. Rather than running `/qualia-milestone` (which would have failed due to missing prerequisites), the solution was to bootstrap all state files manually — creating `tracking.json`, writing `JOURNEY.md`, updating `STATE.md` — after which framework commands functioned normally. The lesson: when bookkeeping files drift, don't force framework commands that expect valid state; reconstruct the state first.

## Related Concepts

- [[concepts/memory-loop-architecture]] — Where the ENOENT hook bug and CronCreate limitation were discovered in production
- [[concepts/llm-wiki-infrastructure]] — Where the CronCreate auto-expiry and Obsidian graph.json race were documented
- [[concepts/qualia-framework-v4.3.0-release]] — The release cycle that exposed multiple hook-related gotchas
- [[concepts/npm-vulnerability-triage]] — The vuln sweep session where `next build` zombies and framework state drift were discovered

## Sources

- [[daily/2026-04-26.md]] — Session at 19:04: CWD invalidation after deleting aandnglobal project directory
- [[daily/2026-04-26.md]] — Session at 02:06: `logEvent` ENOENT guard needed for Stop hook
- [[daily/2026-04-26.md]] — Session at 02:31: CronCreate 7-day auto-expiry discovered
- [[daily/2026-04-26.md]] — Session at 03:35: Stop hook won't fire until session restart
- [[daily/2026-04-26.md]] — Session at 03:52: Obsidian graph.json overwritten when graph tab is open
- [[daily/2026-04-26.md]] — Session at 19:20: `next build` zombie processes blocking Vercel deploys
- [[daily/2026-04-26.md]] — Session at 19:20: Framework state drift — `tracking.json` missing, `JOURNEY.md` absent, `state.js` returning `NO_PROJECT`
