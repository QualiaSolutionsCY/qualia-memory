# Knowledge Base Index

| Article | Summary | Compiled From | Updated |
|---------|---------|---------------|---------|
| [[concepts/qualia-framework-v4.3.0-release]] | v4.3.0 shipped: 6 stacked PRs, +5,119 lines, memory loop, cron flush, self-healing | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/memory-loop-architecture]] | Full auto-capture pipeline: SessionEnd → daily-log → cron flush → concepts → recall; day-2 flush error rate ~23% | daily/2026-04-26.md, daily/2026-04-27.md | 2026-04-27 |
| [[concepts/stacked-pr-workflow]] | 6 PRs is the practical ceiling; sequential merge order; lint-staged wave collision | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/llm-wiki-infrastructure]] | Karpathy-style LLM wiki at ~/qualia-memory/, Obsidian-native, index-guided retrieval | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/prestashop-category-search]] | Category-based filtering beats keyword search for PrestaShop; Armenius widget patterns | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/qualia-os-unification]] | Wiring framework + ERP + memory repos; tracking.json extension; Memory MCP read-only | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/qualia-erp-phase-25]] | Phase 25 perf: auth dedup, chat parallel writes, RPC migrations, gallery redesign | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/qualia-portal-employee-workflows]] | Moayad cron tasks, clock-in refinements, hardcoded daily research entry, clock-out name mismatch bug | daily/2026-04-26.md, daily/2026-04-27.md | 2026-04-27 |
| [[concepts/opt-in-skills-vs-auto-hooks]] | Auto-hooks must be lightweight+universal+non-blocking; everything else is an opt-in skill | daily/2026-04-26.md | 2026-04-26 |
| [[connections/memory-loop-and-framework-release]] | Bootstrap dependency between memory loop, framework release, and stacked PR workflow | daily/2026-04-26.md | 2026-04-26 |
| [[connections/cron-automation-patterns]] | Same cron+dedup pattern across knowledge flush, employee tasks, and skill activation | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/claude-code-operational-gotchas]] | CWD invalidation, hook directory assumptions, CronCreate expiry, graph.json race, next build zombies, git rebase silent revert, Turbopack stale .next | daily/2026-04-26.md, daily/2026-04-27.md | 2026-04-27 |
| [[concepts/npm-vulnerability-triage]] | Exploitability over severity; build-time-only deps; upstream-bound residuals; postcss/uuid patterns | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/qualiafinal-public-booking-sync]] | Mirror trigger missing workspace_id caused silent data loss; fix at trigger layer + backfill | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/qualia-portal-mcp-server]] | Portal MCP server: 6 read + 3 write tools, qlt_* token auth, workspace-scoped, security audit hardened | daily/2026-04-26.md | 2026-04-26 |
| [[connections/mcp-server-design-patterns]] | Memory MCP vs Portal MCP design spectrum; workspace-scoping as universal data-access requirement | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/video-poster-preload-patterns]] | opacity:0 anti-pattern hides poster; IntersectionObserver rootMargin prefetch; poster-first rendering | daily/2026-04-27.md | 2026-04-27 |
| [[concepts/supabase-client-access-patterns]] | Storage CRUD policy independence; assertAppEnabledForClient fail-closed; default allowlists for clients | daily/2026-04-27.md | 2026-04-27 |
| [[concepts/erp-clock-out-name-mismatch]] | Report project name variant vs session project name blocks clock-out; ilike exact match fails on em-dash | daily/2026-04-27.md | 2026-04-27 |
| [[concepts/verification-contract-gaps]] | M1 passed without real DB; builder sandbox can't reach Supabase CLI; plan-checker revision budget validated | daily/2026-04-27.md | 2026-04-27 |
| [[concepts/vercel-deploy-patterns]] | Transient errors, integration provisioning `--prebuilt` workaround, cold-cache latency, docs-only deploy safety | daily/2026-04-27.md | 2026-04-27 |
| [[concepts/underdog-sales-webinar-system]] | Zoom webinar + Brevo + Supabase edge function registration; CSP hotfix for edge functions; `--prebuilt` deploy; 7-vector e2e smoke test | daily/2026-04-27.md | 2026-04-27 |
| [[connections/data-identity-mismatches]] | Same invisible-data pattern across booking sync, clock-out mismatch, and client access — write path must satisfy read path | daily/2026-04-27.md | 2026-04-27 |
| [[concepts/urban-catering-design-transformation]] | Editorial design: white/cream/navy palette, Playfair Display, numbered services, light-gradient hero, minimal ScrollNav | daily/2026-04-27.md | 2026-04-27 |
| [[concepts/sophia-lead-routing-debug]] | Truthy-object trap swallowed failed Telegram forward; /start requirement; Supabase log eventual consistency; Evelina zero leads since Jan | daily/2026-04-27.md | 2026-04-27 |
| [[connections/silent-failure-swallowing]] | Truthy-object routing + data-identity mismatches = same user symptom: "I did the thing, system didn't react" | daily/2026-04-27.md | 2026-04-27 |
