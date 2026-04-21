# Hot Cache

> Recent context — updated at the end of every session.

Last updated: 2026-04-22 (deep-research pass: Framework v1+v2, ERP codebase, live Supabase snapshot)

## 🚨 This Week — AI EXPO CONFERENCE CYPRUS
- **Thursday 2026-04-23 + Friday 2026-04-24**, 09:00–18:00 Europe/Nicosia
- Contact: **Andreas** (ERP client "Andreas - AI EXPO CONFERENCE", hot lead)
- Prep meeting **today 2026-04-21 12:00** with Andreas — confirm slot length, audience, demo policy
- Panel discussion meeting **today 2026-04-21 11:00** (ERP client "Panel Disscusion Conference")
- Speech source material: [[wiki/analysis/ai-expo-2026-talking-points]], [[wiki/analysis/qualia-ai-thesis]]

## Current Focus
- Vault refreshed against ERP (51 clients, 41 projects) — **+5 clients / +3 projects since 2026-04-16**
- Geppetto AI demo **today 2026-04-21 15:00** (Arijit Sircar, Zoom) — v1 budget €5-10k, start 2026-05-01
- Sakani still the most active build (Phase 10 shipped 2026-04-19: Cache Components + god-module split + Upstash Redis)
- Qualia ERP Phase 10 shipped 2026-04-19 (`qualia-erp` / portal.qualiasolutions.net)

## New / Changed Since 2026-04-16
- **New clients (ERP hot):** Andreas - AI EXPO CONFERENCE, Panel Disscusion Conference, Geppetto AI, Ghapetto AI
- **New projects (ERP):** Geppetto (Active, linked to Geppetto AI), Qualia Solutions (internal), Bio Pharma demo
- **New GitHub repos:** `qualiasolutions` (2026-04-21), `geppetto` (2026-04-20), `bio` (2026-04-19), `mipantry` (2026-04-17)
- **Shipped:** ERP Phase 10 (cache components, god-module split, Redis rate-limit), Sakani M9 polish, Geppetto v0 demo build (22 commits)

## Recent Decisions
- ERP is source of truth for clients/projects/finances
- Vault is denormalized read view of ERP, refreshed manually (+ on request)
- Team roles confirmed: Moayad/Hasan/Sally = Employees, Rama = Trainee
- Voice-agents-first pitch for SMEs (see [[wiki/concepts/voice-agents]])
- Always route LLMs via OpenRouter unless compliance requires direct (see [[wiki/concepts/openrouter-routing]])

## Active Pipeline (hot leads)
- Andreas AI Expo, Panel Discussion, Geppetto AI, Ghapetto AI, Boussias, Concept Carton, DawaDose, Giannino Mayfair, Giannis UK, Kronospan, Lauren-Zyprus, Mothana, SSlaw, temo

## Active Retainers (MRR)
- Zyprus: €400/mo
- Armenius: €250/mo
- Boss Brainz: €1,700/mo (→ €2,500 from May 2026)
- **Total now: €2,350/mo → €3,150/mo from May**

## Outstanding (action items — 2026-04-22 live snapshot)
- **CSC Zyprus INV-0001211 is 222 days overdue** (€2,146.50 since Sept 2025) — dead receivable, chase or write off
- **6 draft invoices with past due dates totalling ~€7.5k** sitting unsent: Craft Bakery (3), ODC Journeys (2), Peta (1)
- Sakani-003 + Sakani-004 (€2,904 each) still draft
- Draft invoices total: €12,738.12 EUR
- Overdue (non-draft): €3,788.14 EUR (2 invoices)
- **60-day collected: €29.7k vs €16.3k still out** — healthy ratio but draft leak is real money

## Revenue Concentration
- **GSC Underdog Sales is #1 customer** — 4 of top 10 all-time invoices (~€18.8k paid). Two Underdog projects currently `is_building`. Losing Giulio would hurt.
- Sakani second tier (~€11.6k paid across 2 of 4 milestones).

## Dormant ERP Surfaces (shipped but unused)
- `blog_posts`, `client_feature_requests`, `client_invitations`, `ai_user_memory` — **all empty**
- `dashboard_notes` has one junk entry ("d")
- `portal_messages`: 2 one-word client messages in 10 days
- Translation: finance/projects/meetings layer is alive; community/content/AI-memory layer is not

## Active Context
- Owner: Fawzi Goussous, Nicosia, Cyprus
- Stack: Next.js 16+, React 19, TypeScript, Supabase, Vercel
- Voice: VAPI, ElevenLabs, Telnyx, Retell AI
- AI: OpenRouter (model-agnostic routing — see [[wiki/concepts/openrouter-routing]])
- ERP Supabase: `vbpzaiqovffpsroxaulv.supabase.co`
