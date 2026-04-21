# Operation Log

> Append-only history of all wiki operations.

---

## 2026-04-14

- **INIT** — Scaffolded LLM Wiki structure around existing Qualia vault
  - Created: `raw/`, `wiki/`, `memory/`, `_templates/`
  - Created: `wiki/index.md`, `wiki/hot.md`, `wiki/log.md`, `wiki/overview.md`
  - Created: `memory/soul.md`, `memory/user.md`, `memory/memory.md`, `memory/daily_log.md`
  - Created: `CLAUDE.md` (master schema)
  - Preserved: All existing folders (Clients/, Projects/, Team/, Finances/, Research/)
  - Integrated existing content into wiki index
- **INGEST** — Processed all 34 GitHub repos (Qualiasolutions org) into `wiki/sources/`
  - 12 active repos (batch 1): qualiafinal, ALKEMY, kronospan, giannino-mayfair, qualia-erp, etc.
  - 11 mid-activity repos (batch 2): shai, mpm, theranote, chainwise, carton, etc.
  - 11 older repos (batch 3): boardroom1, blackgoldunited, loca-noche, Vuet, etc.
  - Sources: README.md, CLAUDE.md, PROJECT.md, package.json per repo
  - Updated wiki/index.md with all 34 source entries, categorized

## 2026-04-22

- **DEEP RESEARCH** — 3 parallel agents over Framework, ERP codebase, ERP live data
  - **Framework (Explore):** produced `wiki/sources/qualia-framework-deep.md` (422 lines). v1 at 4.1.0 (26 skills / 8 agents, full-featured) + v2 at 2.7.0 (lean, team-hardened). **Correction:** v1 is NOT deprecated — both are active, projects choose at init.
  - **ERP codebase (Explore):** produced `wiki/sources/qualia-erp-deep.md` (376 lines). 76 tables, 60 action modules, 6 integrations (Zoho daily 04:00 UTC, VAPI, GitHub `.planning/` sync, Vercel, Gemini+OpenRouter, Upstash Redis). Phase 10 shipped 2026-04-19 (Cache Components `'use cache'` + god-module split + Redis rate-limiting).
  - **ERP live snapshot (general-purpose):** produced `wiki/sources/qualia-erp-live-snapshot.md` (210 lines). Surfaced 6 past-due DRAFT invoices totalling ~€7.5k (Craft Bakery x3, ODC Journeys x2, Peta x1), CSC Zyprus INV-0001211 222 days overdue. GSC Underdog Sales = #1 customer (€18.8k across 4 invoices). Dormant surfaces: blog_posts, client_feature_requests, client_invitations, ai_user_memory all empty.
- **DISCOVERY:** wiki conflated TWO GitHub accounts. Fixed:
  - `Qualiasolutions` (personal user) — 34 repos, home of qualia-erp, qualia-framework, qualiafinal, and most older client work
  - `QualiasolutionsCY` (org) — 25 repos, home of current client pipeline + `obs` (this vault)
  - `SakaniQualia` (org) — 1 repo (sakani)
- **UPDATED:** `wiki/index.md`, `wiki/hot.md`, `memory/memory.md` — added deep reference pointers, new finance action items, dormant surfaces note, corrected v1 deprecation claim

---

## 2026-04-21

- **REFRESH** — ERP + GitHub + local checkouts diff'd against 2026-04-16 snapshot
  - ERP: 46→51 clients (+5), 38→41 projects (+3)
  - New clients (all hot): Andreas - AI EXPO CONFERENCE, Panel Disscusion Conference, Geppetto AI, Ghapetto AI, Qualia Solutions (internal duplicate)
  - New projects: Geppetto (Active), Qualia Solutions x2, Bio Pharma (Demos)
  - New GitHub repos: `qualiasolutions` (2026-04-21), `geppetto` (2026-04-20), `bio` (2026-04-19), `mipantry` (2026-04-17)
  - Finance: draft invoices €12,738.12; overdue €3,788.14 (2 invoices)
- **DISCOVERY** — AI Expo Conference Cyprus confirmed for Thu 2026-04-23 + Fri 2026-04-24 via `meetings` table; contact is Andreas; panel discussion meeting scheduled 2026-04-21 11:00; Andreas meeting 2026-04-21 12:00; Geppetto demo 2026-04-21 15:00 (Arijit Sircar, €5-10k v1 budget)
- **NEW — wiki/analysis/** (was empty):
  - `ai-expo-2026-talking-points.md` — speech raw material (thesis, 7 war stories, numbers, pre-conference checklist)
  - `qualia-ai-thesis.md` — operating worldview, 4 claims we operate by
- **NEW — wiki/concepts/** (was empty):
  - `voice-agents.md` — stack, shipped agents, design patterns, pricing, failure modes
  - `openrouter-routing.md` — why model-agnostic, convention, when to go direct
  - `sme-ai-automation.md` — target market, vertical heatmap, 4-week delivery playbook
  - `agency-delivery-model.md` — fresh-context subagents, Qualia Framework v2 mechanics
- **NEW — wiki/entities/** (was empty):
  - `andreas-ai-expo.md`
  - `arijit-sircar-geppetto.md`
- **UPDATED:** `wiki/hot.md`, `wiki/index.md`, `memory/memory.md`
- **Source queries run:** `clients`, `projects`, `financial_invoices`, `meetings`, `client_activities` on ERP Supabase; `gh api orgs/QualiasolutionsCY/repos` and `orgs/SakaniQualia/repos`; `git log` on local `~/Projects/qualia/qualia-erp`, `~/Projects/sakani`, `~/Projects/geppetto`

---

## 2026-04-16

- **NEW** — Created `wiki/sakani-e2e-onboarding/` staging area for overnight autonomous E2E test agent
  - Files: README, scope-boundary, environment, test-credentials, checklist-source, bugs-to-verify, open-questions, selectors-map, reporting-template, run-log (10 files, ~1275 lines)
  - Source material: `.planning/knowledge-base/SRS-v1.14-digest.md`, `.planning/ONBOARDING-CHECKLISTS.md`, `.planning/codebase/ONBOARDING-MAP.md`, `.planning/M8-GAP-REPORT.md`, `.planning/CLIENT-FEEDBACK-2026-04-{11,15,15-AUDIT}.md`, `.planning/GAP-LOG.md`, post-M9 codebase map by Explore agent 2026-04-16
  - Key coordination: scope-boundary.md explicitly excludes finance flows since parallel Claude Code session is running finance work
  - Linked from: `Projects/Sakani App.md` (added "Agent staging areas" section)
  - Purpose: overnight agent reads README first, walks checklist top-to-bottom, appends to run-log, produces morning report per reporting-template

- **REFRESH** — Full vault refresh from Qualia ERP (Supabase) + GitHub (Qualiasolutions org) + local `~/Projects/` checkouts
  - **Sources queried:** ERP `clients` (46 records), `projects` (38), `profiles` (28), `financial_invoices`, `financial_payments`, GitHub repo metadata for 34 Qualiasolutions repos + 1 SakaniQualia repo
  - **Decisions locked with user:** ERP wins on conflicts; team roles Moayad/Hasan/Sally=Employee, Rama=Trainee; Sakani contract is 12,000 JOD (billed in EUR for accounting); Boss Brainz €1,700→€2,500 May 2026
  - **Deleted:** `Clients/Aris.md`, `Clients/Sofia.md` (confirmed not real clients)
  - **Updated existing files:** Index.md, Finances/Overview.md, all 5 Team/, all 12 (now 10) original Clients/, 9 of 13 Projects/
  - **Created — Clients (24):** Aquador, Dr Marco E, Froutaria Siga ta Lachana, Glluz, Luxury Barber UK, MPM Imports, Mr Morees Abawi, My Pearl, ZNSO Architecture, Alecci Media, Boussias, Concept Carton, DawaDose, Giannino Mayfair, Giannis UK, Lauren - Zyprus, Mothana, SSlaw, temo, Afifi, Honda Dealership, Jordan Olympic Committee, Orbit, Rasmus, A&N Global, Black Gold United, Breathe IV Therapies, Chrysanthos Fakas, Craft Bakery, LuxCars, Tamoil Overseas, Urbans Melons Kids Festive, Woodlocation
  - **Created — Projects (8):** Aquador, Marco Osteopata, Aifred, Qualia Dashboard, Qualiafinal, Tomas, Underdog, Sigatalachana AI Agent, Alexis AI
  - **Refreshed:** wiki/index.md, wiki/hot.md, memory/memory.md
  - **Source queries reproducible:** see `Finances/Overview.md` footer for ERP REST URL pattern
