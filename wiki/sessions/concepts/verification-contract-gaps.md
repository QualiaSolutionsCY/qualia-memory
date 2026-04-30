---
title: "Verification Contract Gaps"
aliases: [db-smoke-test, builder-sandbox-limits, plan-checker-revision, verification-missing-db]
tags: [qualia-framework, verification, methodology, supabase, builder-agents]
sources:
  - "daily/2026-04-27.md"
  - "daily/2026-04-28.md"
created: 2026-04-27
updated: 2026-04-28
---

# Verification Contract Gaps

Verification contracts in the Qualia Framework can pass without validating critical infrastructure prerequisites. Discovered during the Kids Festive M2 build (2026-04-27): M1 "verification" had passed without a real Supabase project existing because nothing in M1 was actually data-driven. When M2's auto-build chain attempted to apply migrations, it halted at a hard gate — no Supabase project to connect to.

## Key Points

- **M1 verification passed without a real DB:** Because M1 had no data-driven features, all verification checks passed despite no Supabase project existing — verification contracts should include a "DB connection smoke test" for any milestone that creates migration files
- **Builder sandbox can't reach Supabase CLI:** Builder agents running inside Claude Code's sandbox cannot execute `npx supabase` commands — migration application must happen at the orchestrator level, not delegated to builders
- **Plan-checker revision budget validated:** P1, P2, and P4 plans all needed one revision cycle (vague fields, missing wiring contracts) but resolved within the 2-cycle budget — P3 passed first try, confirming the budget is sufficient
- **Auto-chain halted correctly:** The `/qualia-plan 1 --auto` auto-chain correctly stopped at the infrastructure gate rather than proceeding with broken state — fail-fast is the right behavior for missing prerequisites
- **Human gates identified:** Phase 4 has 2 hard human gates: (a) Squarespace export file from client, (b) client review of Greek translations — these cannot be automated

## Details

The Kids Festive project ran `/qualia-plan 1 --auto` to plan and auto-build all four phases of Milestone 2 (Component Library + CMS + Content Migration). Phase 1 Wave 1 built successfully with 3 parallel builders producing UI primitives, SEO helpers, and a Supabase migration file. However, when the auto-chain attempted to apply the migration, it discovered that no Supabase project had been created for Kids Festive. The "eventmaster" Supabase project that existed was confirmed to be a separate project (cities/vendors/listings), not Kids Festive.

This exposed a gap in the Qualia Framework's verification contract model: if a milestone produces migration files but doesn't actually connect to a database, the verification checks (file exists, TypeScript compiles, exports are wired) all pass without catching the missing infrastructure. The fix is to add a "DB connection smoke test" as a verification gate for any milestone whose plan includes database migrations. This could be as simple as `npx supabase db diff --linked` — if it fails with "no project linked," the verification should fail.

The builder sandbox limitation is a practical constraint of the Claude Code execution environment. Builder subagents spawned by `/qualia-build` can read and write files, but they cannot execute Supabase CLI commands that require network access to a linked project. This means migration application (pushing `.sql` files to the remote database) must be handled by the orchestrator after builder agents finish writing the migration files. This is consistent with the framework's principle that destructive database operations should not be automated without human review.

The plan-checker's revision loop proved effective: three of four phase plans needed corrections on the first pass (vague acceptance criteria, missing wiring contracts, unspecified test commands), but all resolved within one revision cycle. No plan required the maximum of 2 revision cycles, suggesting the 2-cycle budget provides adequate margin for typical planning errors.

Region recommendation for the Kids Festive Supabase project was `eu-central-1` (Frankfurt) — closest to Cyprus and matching Vercel's default region for optimal latency. The project would cost approximately $10/month on Pro tier.

**Update (2026-04-28):** Kids Festive M2 (Marketing+CMS) was successfully shipped to production, completing content seeding, Resend wiring, admin user setup, and a full deploy pipeline verified at `kidsfestive-lswhjx5k1-qualiasolutionscy.vercel.app`. The session also produced 17 feature commits across 7 phases and 2 milestones. However, a new verification gap emerged: after M2 shipped, the user invoked `/qualia-handoff` prematurely despite M3 (Commerce) and M4 (Handoff) still remaining. The framework's state machine suggested handoff as the next command because it's the only post-shipped state transition, but JOURNEY.md clearly defines 4 milestones. This is documented as a state-routing gotcha in [[concepts/claude-code-operational-gotchas]].

## Related Concepts

- [[concepts/qualia-erp-phase-25]] — Demonstrated the same lesson from the other direction: RPC functions existed as local migrations but weren't pushed to production Supabase
- [[concepts/claude-code-operational-gotchas]] — Builder sandbox limitations are in the same category as CWD invalidation and hook directory assumptions
- [[concepts/stacked-pr-workflow]] — Plan-checker revision loop is part of the same quality infrastructure as the PR stacking workflow

## Sources

- [[daily/2026-04-27.md]] — Session at 04:13 (Kids Festive): "M1 'verification' passed without a real Supabase project because nothing was actually data-driven yet"
- [[daily/2026-04-27.md]] — Session at 04:13 (Kids Festive): "Builder agents can't reach Supabase CLI from within their sandbox — migration application must happen at orchestrator level"
- [[daily/2026-04-27.md]] — Session at 04:13 (Kids Festive): "Plan-checker caught issues in P1/P2/P4 on first pass but all resolved in one revision cycle"
- [[daily/2026-04-27.md]] — Session at 04:13 (Kids Festive): "'eventmaster' Supabase project is NOT Kids Festive — it's a separate project (cities/vendors/listings)"
- [[daily/2026-04-27.md]] — Session at 04:13 (Kids Festive): "Region recommendation: eu-central-1 (Frankfurt) — closest to Cyprus + Vercel default"
- [[daily/2026-04-28.md]] — Session at 11:59: "M2 shipped to production; user invoked `/qualia-handoff` prematurely — 2 milestones remain (M3 Commerce, M4 Handoff)"
