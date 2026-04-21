# Memory

> Key decisions and lessons promoted from daily logs. Always loaded into context.

Last updated: 2026-04-21

---

## Key Decisions
- **2026-04-14** — Use Karpathy's LLM Wiki pattern
- **2026-04-14** — Preserve existing vault structure, build wiki layer on top
- **2026-04-14** — Install Obsidian plugins: Bases, Templater, Git, Web Clipper, Obsidian CLI

- **2026-04-14** — Adopted LLM Wiki pattern (Karpathy) for knowledge management. Using Obsidian as IDE, Claude Code as programmer, markdown wiki as codebase.
- **2026-04-14** — Installed notebooklm-mcp-cli for querying NotebookLM research notebooks directly from Claude Code.
- **2026-04-14** — Built second brain vault structure around existing Obsidian data rather than starting from scratch.

## Lessons Learned

*(Will be populated as sessions are captured and promoted)*

## Important Facts

- Qualia ERP is the operational source of truth (Next.js 16+, Supabase). Project ref: `vbpzaiqovffpsroxaulv`
- Team spans Cyprus and Jordan — timezone-aware scheduling matters
- Three financial systems were consolidated into one unified architecture
- Qualia Framework runs in TWO parallel tracks: v1 (GitHub, 4.1.0, 26 skills/8 agents, full-featured) and v2 (npx cache, 2.7.0, leaner, team-hardened with env-edit guard). Neither is deprecated — projects choose at init. Update notice "4.0.5 → 4.1.0" refers to v1.
- Team roles: Moayad/Hasan/Sally = Employees, Rama = Trainee. Sally becomes full-time June 2026
- Sakani contract = 12,000 JOD but invoiced in EUR (€14,520 across 4 milestones). Don't confuse currencies
- Boss Brainz (Giulio): currently €1,700/mo, bumps to €2,500/mo from May 2026
- Vault is denormalized read view of ERP — refresh by querying Supabase REST with service role key (in `~/Projects/qualia/qualia-erp/.env.local`)

## Vault Refresh History

- **2026-04-16** — Full refresh: 46 clients, 38 projects, 28 profiles synced from ERP. Added 24 client files + 8 project files. Deleted Aris/Sofia (not clients).
- **2026-04-21** — Delta refresh: 51 clients (+5), 41 projects (+3). New hot leads: Andreas AI Expo Conference, Panel Discussion Conference, Geppetto AI (Arijit Sircar), Ghapetto AI. New repos: `qualiasolutions`, `geppetto`, `bio`, `mipantry`. Seeded `wiki/analysis/` + `wiki/concepts/` + `wiki/entities/` (all were empty). AI Expo Conference is Thu 2026-04-23 + Fri 2026-04-24 — speech prep file at `wiki/analysis/ai-expo-2026-talking-points.md`.
- **2026-04-22** — Deep research pass. 3 parallel agents. New pages: `sources/qualia-framework-deep.md` (422 L), `sources/qualia-erp-deep.md` (376 L), `sources/qualia-erp-live-snapshot.md` (210 L). Corrected "v1 deprecated" claim. Documented the two-GitHub-account split (Qualiasolutions user = 34 repos incl. all internal tools; QualiasolutionsCY org = 25 repos for current client pipeline). Surfaced: 6 past-due DRAFT invoices (~€7.5k unsent), CSC Zyprus 222 days overdue (€2,146.50), Underdog = #1 customer (€18.8k), 4 ERP surfaces dormant (blog_posts, feature_requests, invitations, ai_user_memory all empty).

## Upcoming Events

- **2026-04-21 11:00** — Panel Discussion Conference meeting
- **2026-04-21 12:00** — Andreas (AI Expo Conference) meeting
- **2026-04-21 15:00** — Geppetto AI demo (Arijit Sircar, Zoom)
- **2026-04-23–24** — AI Expo Conference Cyprus, 09:00–18:00 Europe/Nicosia
- **2026-04-28 10:00** — AIFRED Planning kickoff
- **2026-05-01** — Geppetto v1 start (if demo closes)
