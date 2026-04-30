---
title: "Claude Code Operational Gotchas"
aliases: [claude-code-gotchas, session-gotchas, hook-gotchas]
tags: [claude-code, operational, gotchas, hooks]
sources:
  - "daily/2026-04-26.md"
  - "daily/2026-04-27.md"
  - "daily/2026-04-28.md"
  - "daily/2026-04-29.md"
created: 2026-04-26
updated: 2026-04-29
---

# Claude Code Operational Gotchas

A collection of non-obvious operational pitfalls discovered while using Claude Code in production across the Qualia ecosystem. These are tool-level behaviors that don't fit neatly into any single feature article but repeatedly cause friction if not anticipated.

## Key Points

- **CWD invalidation:** Deleting the current working directory mid-session (`rm -rf /path/to/project`) invalidates the Bash tool's CWD — all subsequent shell commands fail until a fresh session is started or `cd` redirects to a valid path
- **Hook directory assumption:** Hooks fire in environments where `~/.claude/` may not yet exist — `logEvent` in the Stop hook silently swallowed ENOENT until a defensive `mkdir` guard was added
- **CronCreate auto-expiry:** Claude Code `CronCreate` recurring tasks auto-expire after 7 days — cannot rely on them for permanent schedules; use system crontab directly for anything that must persist
- **Hook restart requirement:** The Stop hook (and other newly installed hooks) won't fire until the Claude Code session is restarted after framework install — the session loads hooks at startup, not dynamically
- **Obsidian graph.json race:** Obsidian holds graph configuration in memory and flushes to disk when the graph view closes — external edits to `graph.json` are silently overwritten if the graph tab is open
- **`next build` zombie processes:** `next build` can leave zombie processes that hold ports and block subsequent Vercel deploys — kill stale `next build` PIDs before retrying `vercel --prod`
- **Framework state drift:** When `.planning/` bookkeeping drifts (missing `tracking.json`, `JOURNEY.md`, stale `STATE.md`), `state.js` returns `NO_PROJECT` and framework commands fail — bootstrap state manually first, then commands work going forward
- **Git rebase silently reverts fixes:** During deploy, rebasing against upstream can silently drop committed fixes if upstream redesigned the same files — always verify changes survived after rebase before shipping
- **Turbopack stale `.next` directory:** Dev server can fail with permission errors if `.next/` is stale from a previous build — clearing it (`rm -rf .next`) fixes it immediately; common after switching branches or interrupted builds
- **Supabase CLI migration drift:** When local and remote migration histories diverge too far, `supabase db push` refuses to apply — escape via MCP `apply_migration` tool or Dashboard SQL editor instead of trying to reconcile the drift
- **MCP query size limits:** Large table dumps (e.g., full `auth.users` table) via MCP tools fail due to token/payload limits — pivot to file-based extraction or chunked queries
- **Premature handoff state routing:** After shipping a milestone, the framework's `state.js` router may suggest `/qualia-handoff` as the next command because it's the only post-shipped path — but the real state may have remaining milestones per JOURNEY.md; always cross-reference JOURNEY.md before executing handoff
- **Vercel env var caching:** Rotating an API key (e.g., OpenRouter) requires explicitly removing and re-adding the Vercel env var (`vercel env rm` + `vercel env add`) — the old key remains cached in the production environment even after the key is rotated at the provider; `vercel env pull` alone does not push new values to production
- **Vercel multi-account CLI blocking:** When a Vercel org has multiple members, the CLI authenticates per-user. If that user's account has restrictions (spend caps, blocked status), CLI deploys fail with "Blocked" badge even though the project itself is healthy. Workaround: `vercel promote` on a successful Preview build, which routes through the project owner's identity
- **`NEXT_PUBLIC_*` env vars baked at build time:** `vercel redeploy` reuses the existing build artifact and does NOT pick up new `NEXT_PUBLIC_*` environment variable values — a fresh `vercel --prod` build is required. This is distinct from server-side env vars which are read at runtime
- **Supabase Edge Function JWT key requirement:** The newer `sb_publishable_*` anon key format is not accepted by Supabase Edge Functions — must use the classic JWT-format `eyJ...` anon key. The edge function silently rejects the new format with no useful error

## Details

The CWD invalidation gotcha surfaced during the aandnglobal project housekeeping session at 19:04. After pushing `CLAUDE.md` to `origin/main` (commit `a0a4673`), the entire `/home/qualia-new/Projects/aandnglobal` directory was deleted. This left the Bash tool pointing at a non-existent directory, rendering all subsequent shell commands broken. The user's final message ("cd ,,") was a typo attempting to recover. The practical rule is: never delete the directory you're currently working in during a Claude Code session. If cleanup requires directory deletion, either `cd` out first or close and reopen the session.

The hook directory issue was caught during the v4.3.0 release cycle. The `logEvent` function in the Stop hook assumed `~/.claude/` existed and failed silently when it didn't, which meant early sessions after a fresh install produced no event logs. The fix was a one-line `mkdir -p` guard, but the broader lesson applies to all hooks: never assume directories exist in hook context. This is especially relevant for hooks that fire in CI, Docker containers, or fresh user environments.

The CronCreate limitation was discovered when setting up weekly wiki-lint and knowledge flush schedules. Claude Code's built-in cron system is designed for short-term reminders (7-day max), not persistent automation. The workaround for permanent schedules is to use the system crontab directly, as was done for the weekly knowledge flush: `0 3 * * 0 node ~/.claude/bin/knowledge-flush.js`.

The `next build` zombie process issue was discovered during the claude-memory-compiler Dependabot vulnerability sweep at 19:20. After running `next build` (either directly or via `vercel build`), stale Node processes can remain alive holding ports, which causes subsequent `vercel --prod` deploys to hang or fail. The fix is to check for and kill any lingering `next build` PIDs before retrying the deploy. This is particularly insidious because the zombie process may not be visible without explicitly checking `ps aux | grep next`.

Framework state drift was encountered in the same session when the claude-memory-compiler project's `.planning/` directory had fallen out of sync: `tracking.json` was missing entirely, `JOURNEY.md` didn't exist, and `STATE.md` was stale by approximately one day. The framework's `state.js` router returned `NO_PROJECT`, which meant all `/qualia-*` commands would either fail or misroute. Rather than running `/qualia-milestone` (which would have failed due to missing prerequisites), the solution was to bootstrap all state files manually — creating `tracking.json`, writing `JOURNEY.md`, updating `STATE.md` — after which framework commands functioned normally. The lesson: when bookkeeping files drift, don't force framework commands that expect valid state; reconstruct the state first.

The git rebase gotcha was discovered on 2026-04-27 during a deploy of video loading fixes to qualiasolutions.net/work. A fix to `DeviceMockup3D.tsx` (removing the `opacity: ready ? 1 : 0` pattern) was committed and verified locally, but a subsequent `git rebase` against upstream silently dropped the fix because upstream had redesigned the same file. The fix had to be re-verified and re-applied after the rebase. The practical rule: after any rebase during a deploy pipeline, explicitly verify (`git diff`) that all intended changes survived — especially when upstream has been actively editing the same components.

The Turbopack stale `.next` directory issue was discovered on 2026-04-27 during the UnderdogSales webinar hotfix session. The Next.js dev server failed to start with permission errors, caused by a stale `.next/` directory left over from a previous build (possibly a different branch or an interrupted `next build`). Deleting `.next/` entirely (`rm -rf .next`) and restarting the dev server resolved it immediately. This is a common issue with Turbopack's incremental compilation cache — when the cache becomes inconsistent (branch switches, interrupted builds, or permission changes), the dev server can fail cryptically rather than regenerating the cache. The fix is always the same: delete `.next/` and restart.

The Supabase CLI migration drift issue was discovered on 2026-04-28 during a portal hardening session. The Qualia ERP portal's local migration history had diverged from the remote Supabase project to the point where `supabase db push` refused to apply new migrations. Rather than trying to reconcile the tangled migration history (which risks data loss or schema corruption), the solution was to bypass the CLI entirely and apply the migration SQL directly via the Supabase MCP `apply_migration` tool. The Dashboard SQL editor serves the same purpose. The broader lesson: Supabase CLI migration tracking is optimistic — it assumes a clean linear history, and when that assumption breaks, the escape hatch is raw SQL application rather than history reconstruction.

MCP query size limits were discovered during the Underdog Academy user migration on 2026-04-28. Attempting to extract the full `auth.users` table (329 users with bcrypt hashes, metadata, and timestamps) via MCP tools exceeded the token/payload limit, causing the query to fail. The workaround was file-based extraction: dump the data to a JSON file via a script, then process the file. This is a practical constraint for any MCP-based workflow that involves bulk data access — always plan for chunked extraction when table sizes exceed a few hundred rows with wide columns.

The premature handoff state routing was encountered on 2026-04-28 with the Kids Festive project. After M2 shipped to production, the framework's state machine suggested `/qualia-handoff` as the next command because it was the only defined post-shipped state transition. However, the project's JOURNEY.md defined 4 milestones (M1 through M4, with M4 being the actual Handoff milestone), and M3 (Commerce) had not yet started. Executing handoff at that point would have incorrectly closed the project with 2 milestones remaining. The rule: always cross-reference JOURNEY.md before executing handoff — the state machine's suggestion is based on local state, not the full project plan.

The Vercel env var caching gotcha was discovered on 2026-04-28 during the portal hardening session. After rotating the OpenRouter API key at the provider, the Qualia ERP portal's Knowledge assistant continued using the old key because the production environment variable on Vercel still held the previous value. Vercel environment variables are not live-synced with external providers — they are static key-value pairs set at deploy time. Updating a key requires an explicit `vercel env rm OPENROUTER_API_KEY production` followed by `vercel env add OPENROUTER_API_KEY production` with the new value, then a redeploy. Simply rotating the key at OpenRouter's dashboard does nothing to the Vercel environment. This applies to any secret stored as a Vercel env var — Supabase keys, Stripe keys, Resend keys, etc.

The Vercel multi-account CLI blocking issue was discovered on 2026-04-29 during the Underdog Academy quiz fix deployment. CLI deploys were authenticating as the "Qualia Framework" user, which had a "Blocked" status (likely due to spend caps or org policy restrictions). Meanwhile, git-triggered preview deploys from the "Qualiasolutions" account succeeded normally. The workaround is `vercel promote` on a successful Preview deployment, which promotes the existing build to production without triggering a new build through the blocked CLI user. This is distinct from both the transient "Unexpected error" (which resolves on retry) and the integration provisioning failure (which requires `--prebuilt`). The permanent fix is to re-authenticate the Vercel CLI with the Qualiasolutions account (`vercel login`) or resolve the billing/spend restriction on the Qualia Framework user.

The `NEXT_PUBLIC_*` env var baking gotcha was discovered on 2026-04-29 during the Underdog Sales webinar signup fix. After adding new environment variables on Vercel, `vercel redeploy` was used expecting the new values to take effect. However, `NEXT_PUBLIC_*` variables are inlined into the client-side JavaScript bundle at build time by Next.js. A `vercel redeploy` reuses the existing build artifact (which has the old values compiled in), so the new env var values are invisible to the running application. Only a fresh `vercel --prod` build recompiles the bundle with the updated values. This does NOT affect server-side-only env vars (those without the `NEXT_PUBLIC_` prefix), which are read at runtime from the environment. The practical rule: after changing any `NEXT_PUBLIC_*` variable, always do a fresh build, never a redeploy.

The Supabase Edge Function JWT key format issue was discovered in the same debugging session. Supabase has introduced a newer `sb_publishable_*` key format for anon keys, but Edge Functions do not yet support this format — they require the classic JWT-format anon key (`eyJ...`). When the new format key is provided, the edge function silently rejects it without a useful error message, causing the entire function to fail. This was the second layer in a four-layer stacked bug (see [[concepts/brevo-transactional-vs-campaign]] for the full stack). The practical rule: always use JWT-format anon keys for Supabase Edge Functions, even if the project dashboard shows the new `sb_publishable_*` format.

## Related Concepts

- [[concepts/memory-loop-architecture]] — Where the ENOENT hook bug and CronCreate limitation were discovered in production
- [[concepts/llm-wiki-infrastructure]] — Where the CronCreate auto-expiry and Obsidian graph.json race were documented
- [[concepts/qualia-framework-v4.3.0-release]] — The release cycle that exposed multiple hook-related gotchas
- [[concepts/npm-vulnerability-triage]] — The vuln sweep session where `next build` zombies and framework state drift were discovered
- [[concepts/underdog-sales-webinar-system]] — Where the Turbopack stale `.next` directory issue was discovered during a hotfix session
- [[concepts/sophia-lead-routing-debug]] — Supabase edge function log eventual consistency was discovered during this debugging session
- [[concepts/supabase-cross-project-migration]] — Where MCP query size limits were discovered during auth table extraction
- [[connections/supabase-tooling-escape-hatches]] — The pattern of needing escape hatches when standard Supabase tooling fails
- [[concepts/brevo-transactional-vs-campaign]] — The stacked bugs session where NEXT_PUBLIC baking and Supabase Edge Function key format were discovered

## Sources

- [[daily/2026-04-26.md]] — Session at 19:04: CWD invalidation after deleting aandnglobal project directory
- [[daily/2026-04-26.md]] — Session at 02:06: `logEvent` ENOENT guard needed for Stop hook
- [[daily/2026-04-26.md]] — Session at 02:31: CronCreate 7-day auto-expiry discovered
- [[daily/2026-04-26.md]] — Session at 03:35: Stop hook won't fire until session restart
- [[daily/2026-04-26.md]] — Session at 03:52: Obsidian graph.json overwritten when graph tab is open
- [[daily/2026-04-26.md]] — Session at 19:20: `next build` zombie processes blocking Vercel deploys
- [[daily/2026-04-26.md]] — Session at 19:20: Framework state drift — `tracking.json` missing, `JOURNEY.md` absent, `state.js` returning `NO_PROJECT`
- [[daily/2026-04-27.md]] — Session at 01:50: Git rebase during deploy silently reverted video fix — fix had to be re-verified after rebase
- [[daily/2026-04-27.md]] — Session at 19:19: Turbopack stale `.next` directory caused dev server permission errors — `rm -rf .next` fixed it
- [[daily/2026-04-28.md]] — Session at 10:49: Supabase CLI migration drift blocked `db push` — escaped via MCP `apply_migration`
- [[daily/2026-04-28.md]] — Session at 17:00: MCP query size limits hit during auth table extraction — pivoted to file-based chunked approach
- [[daily/2026-04-28.md]] — Session at 11:59: Premature handoff state routing — `/qualia-handoff` suggested after M2 ship despite M3 and M4 remaining
- [[daily/2026-04-28.md]] — Session at 10:49: OpenRouter key rotation required explicit `vercel env rm` + `vercel env add` — old key cached on production Vercel environment
- [[daily/2026-04-29.md]] — Session at 00:19: Vercel CLI deploys as "Qualia Framework" user getting Blocked status — `vercel promote` on Preview builds bypasses the blocked account
- [[daily/2026-04-29.md]] — Session at 20:24: `NEXT_PUBLIC_*` env vars baked at build time — `vercel redeploy` doesn't pick up new values, must do fresh `vercel --prod` build
- [[daily/2026-04-29.md]] — Session at 20:24: Supabase Edge Functions reject `sb_publishable_*` anon key format — must use classic JWT-format `eyJ...` key
