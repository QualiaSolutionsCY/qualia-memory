# Knowledge Base Index

| Article | Summary | Compiled From | Updated |
|---------|---------|---------------|---------|
| [[concepts/qualia-framework-v4.3.0-release]] | v4.3.0 shipped: 6 stacked PRs, +5,119 lines, memory loop, cron flush, self-healing | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/memory-loop-architecture]] | Full auto-capture pipeline: SessionEnd → daily-log → cron flush → concepts → recall; day-4 flush error rate 50% (2 OK / 2 ERR, small sample) | daily/2026-04-26.md, daily/2026-04-27.md, daily/2026-04-28.md, daily/2026-04-29.md | 2026-04-29 |
| [[concepts/stacked-pr-workflow]] | 6 PRs is the practical ceiling; sequential merge order; lint-staged wave collision | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/llm-wiki-infrastructure]] | Karpathy-style LLM wiki at ~/qualia-memory/, Obsidian-native, index-guided retrieval | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/prestashop-category-search]] | Category-based filtering beats keyword search for PrestaShop; Armenius widget patterns | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/qualia-os-unification]] | Wiring framework + ERP + memory repos; tracking.json extension; Memory MCP read-only | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/qualia-erp-phase-25]] | Phase 25 perf: auth dedup, chat parallel writes, RPC migrations, gallery redesign | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/qualia-portal-employee-workflows]] | Moayad cron tasks, clock-in refinements, hardcoded daily research entry, clock-out name mismatch bug | daily/2026-04-26.md, daily/2026-04-27.md | 2026-04-27 |
| [[concepts/opt-in-skills-vs-auto-hooks]] | Auto-hooks must be lightweight+universal+non-blocking; everything else is an opt-in skill | daily/2026-04-26.md | 2026-04-26 |
| [[connections/memory-loop-and-framework-release]] | Bootstrap dependency between memory loop, framework release, and stacked PR workflow | daily/2026-04-26.md | 2026-04-26 |
| [[connections/cron-automation-patterns]] | Same cron+dedup pattern across knowledge flush, employee tasks, and skill activation | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/claude-code-operational-gotchas]] | CWD invalidation, hook directory assumptions, CronCreate expiry, Supabase CLI drift, MCP query limits, Vercel multi-account CLI blocking, NEXT_PUBLIC build-time baking, Supabase Edge Function JWT key requirement | daily/2026-04-26.md, daily/2026-04-27.md, daily/2026-04-28.md, daily/2026-04-29.md | 2026-04-29 |
| [[concepts/npm-vulnerability-triage]] | Exploitability over severity; build-time-only deps; upstream-bound residuals; postcss/uuid patterns | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/qualiafinal-public-booking-sync]] | Mirror trigger missing workspace_id caused silent data loss; fix at trigger layer + backfill | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/qualia-portal-mcp-server]] | Portal MCP server: 6 read + 3 write tools, qlt_* token auth, workspace-scoped, security audit hardened | daily/2026-04-26.md | 2026-04-26 |
| [[connections/mcp-server-design-patterns]] | Memory MCP vs Portal MCP design spectrum; workspace-scoping as universal data-access requirement | daily/2026-04-26.md | 2026-04-26 |
| [[concepts/video-poster-preload-patterns]] | opacity:0 anti-pattern hides poster; IntersectionObserver rootMargin prefetch; poster-first rendering | daily/2026-04-27.md | 2026-04-27 |
| [[concepts/supabase-client-access-patterns]] | Storage CRUD policy independence; assertAppEnabledForClient fail-closed; default allowlists for clients | daily/2026-04-27.md | 2026-04-27 |
| [[concepts/erp-clock-out-name-mismatch]] | Report project name variant vs session project name blocks clock-out; ilike exact match fails on em-dash | daily/2026-04-27.md | 2026-04-27 |
| [[concepts/verification-contract-gaps]] | M1 passed without real DB; builder sandbox can't reach Supabase CLI; M2 shipped but premature handoff triggered | daily/2026-04-27.md, daily/2026-04-28.md | 2026-04-28 |
| [[concepts/vercel-deploy-patterns]] | Transient errors, `--prebuilt` workaround, `vercel promote`, cold-cache latency, NEXT_PUBLIC build-time baking | daily/2026-04-27.md, daily/2026-04-29.md | 2026-04-29 |
| [[concepts/underdog-sales-webinar-system]] | Zoom webinar + Brevo + Supabase edge function registration; CSP hotfix; 4 stacked bugs in signup flow; `--prebuilt` deploy | daily/2026-04-27.md, daily/2026-04-29.md | 2026-04-29 |
| [[connections/data-identity-mismatches]] | Same invisible-data pattern across booking sync, clock-out mismatch, and client access — write path must satisfy read path | daily/2026-04-27.md | 2026-04-27 |
| [[concepts/urban-catering-design-transformation]] | Editorial design: white/cream/navy palette, Playfair Display, numbered services, light-gradient hero, minimal ScrollNav | daily/2026-04-27.md | 2026-04-27 |
| [[concepts/sophia-lead-routing-debug]] | Truthy-object trap swallowed failed Telegram forward; /start requirement; Supabase log eventual consistency; Evelina zero leads since Jan | daily/2026-04-27.md | 2026-04-27 |
| [[connections/silent-failure-swallowing]] | 5 instances: truthy-object routing, data-identity mismatches, schema-validation swallowing = same symptom: "I did the thing, system didn't react" | daily/2026-04-27.md, daily/2026-04-29.md | 2026-04-29 |
| [[concepts/supabase-cross-project-migration]] | auth.admin.listUsers() omits encrypted_password; UUID preservation mandatory; collision-merge tier logic; MCP query size limits | daily/2026-04-28.md | 2026-04-28 |
| [[concepts/postgres-security-definer-triage]] | 4-category SECURITY DEFINER audit; REVOKE FROM anon is no-op when PUBLIC has implicit grant; trigger functions need only service_role | daily/2026-04-28.md | 2026-04-28 |
| [[concepts/fotini-ai-legal-platform]] | AI legal case management: 6 categories, ~40 cases, invoicing with PDF+email, entity dedup for multi-case clients, Claude Sonnet via OpenRouter | daily/2026-04-28.md | 2026-04-28 |
| [[concepts/eventmaster-design-pivots]] | Three aesthetic pivots + /plan wizard + /browse refinement; state guard blocked ship from setup state; off-white/navy locked | daily/2026-04-28.md, daily/2026-04-29.md | 2026-04-29 |
| [[connections/supabase-tooling-escape-hatches]] | CLI drift, MCP limits, auth API omissions, Edge Function JWT key format — all require escaping standard tooling | daily/2026-04-28.md, daily/2026-04-29.md | 2026-04-29 |
| [[concepts/qualia-framework-v4.4.0-release]] | v4.4.0 published to npm: plan-contract validator, agent-runs tracking, CLI agents subcommand, 226 tests, PR #17 merged; v4.5.0 scoped as skill wiring | daily/2026-04-28.md | 2026-04-28 |
| [[concepts/underdog-quiz-validation-bug]] | Zod uuidSchema vs arbitrary string option IDs silently broke 100% of quizzes (163 questions); every paying user locked out of gated lessons | daily/2026-04-29.md | 2026-04-29 |
| [[concepts/qualia-email-routing]] | bookings@ via Resend (send-only, transactional), info@ via Zoho Mail (inbox); Resend API key in Vercel env vars | daily/2026-04-29.md | 2026-04-29 |
| [[concepts/elevenlabs-voice-agent-tool-design]] | Client vs webhook tools; URL-spelling bug; unified agent config; Gemini→Claude Haiku swap; tool response design for voice | daily/2026-04-29.md | 2026-04-29 |
| [[concepts/brevo-transactional-vs-campaign]] | Brevo transactional `/smtp/email` doesn't resolve merge tags; must use inline HTML; stacked bugs masking pattern | daily/2026-04-29.md | 2026-04-29 |
| [[concepts/ophthalmos-voice-agent-proposal]] | Healthcare voice AI: €11-15K setup, Cypriot Greek dialect scope multiplier, 200-300 calls/day, prestige client dynamics | daily/2026-04-29.md | 2026-04-29 |
| [[connections/voice-ai-platform-selection]] | ElevenLabs for website widgets, Retell AI + Telnyx for telephony; platform choice determined by interaction channel | daily/2026-04-29.md | 2026-04-29 |
