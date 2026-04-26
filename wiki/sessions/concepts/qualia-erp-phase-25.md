---
title: "Qualia ERP Phase 25 Performance"
aliases: [erp-phase-25, erp-performance, portal-optimizations]
tags: [qualia-erp, performance, supabase, deployment]
sources:
  - "daily/2026-04-26.md"
created: 2026-04-26
updated: 2026-04-26
---

# Qualia ERP Phase 25 Performance

Phase 25 of the Qualia ERP (portal.qualiasolutions.net) delivered four performance optimization tasks: auth deduplication, chat parallel writes, per-client project stats RPC, and team status RPC. The phase also included a design transformation of the projects gallery after the user identified the full-width single-column card layout as visually poor.

## Key Points

- Four builder tasks executed: auth dedup, chat parallel writes, clients RPC (`get_per_client_project_stats`), team-status RPC (`get_latest_session_per_profile`)
- Two Supabase RPCs were not in the live database — caught and applied before deploy (migrations must be explicitly pushed to production)
- Parallel builder agents in Wave 1 (T1, T3, T4) had their changes bundled into a single commit `317234f` by lint-staged's stash/restore mechanism — code correct but git history misleading
- Projects gallery design was flagged as "ugly" after an earlier UI tweak made stage columns single-column — cards became full-width sparse blocks
- Deployed to production via `vercel --prod` at commit `431e12e`

## Details

The Phase 25 build revealed an important lesson about Supabase migration workflows: RPC functions created as local migrations only exist locally until explicitly pushed with `npx supabase db push` or applied via the dashboard SQL editor. The `get_per_client_project_stats` and `get_latest_session_per_profile` functions were referenced in the deployed code but missing from the live database, which would have caused runtime errors. Always verifying the live Supabase state before deploying RPC-dependent code is now an established practice.

The lint-staged wave collision issue is a side effect of parallel builder agents. When multiple agents commit simultaneously, lint-staged stashes uncommitted changes, runs linting on staged files, then restores. If multiple agents trigger this process concurrently, unrelated changes can be bundled into a single commit. The code integrity is preserved — only the git history suffers. For phases where clean commit history matters, sequential commits within each wave are recommended.

The projects gallery design issue illustrated a common layout trap: switching from multi-column to single-column grid without redesigning the card component creates oversized, sparse cards. Layout changes require matching card density adjustments — a card designed for a 3-column grid looks wrong when stretched to fill a single column.

The ERP's framework state files (OPTIMIZE.md, STATE.md, JOURNEY.md) are pre-v4 format. A decision on whether to initialize v4 state or continue with legacy format is pending.

## Related Concepts

- [[concepts/stacked-pr-workflow]] — The lint-staged wave collision discovered during this phase
- [[concepts/qualia-os-unification]] — ERP tracking field extension running in parallel
- [[concepts/qualia-framework-v4.3.0-release]] — Framework version that ERP will adopt

## Sources

- [[daily/2026-04-26.md]] — Session at 03:09 (second instance: Phase 25 build, RPC migrations, lint-staged collision, gallery design critique, deploy verification)
