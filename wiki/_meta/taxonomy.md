---
type: meta
created: 2026-04-26
updated: 2026-04-26
tags: [meta, taxonomy]
---

# Taxonomy — Canonical Tag Vocabulary

> Controlled vocabulary for the Qualia Brain. Every wiki page's `tags:` frontmatter must draw from this list. New tags get added here first, then applied. `wiki-lint` flags pages using tags not listed here.

## Domain

- `client` — pages about a client company or contact
- `project` — pages about a delivered or in-flight client project
- `team` — pages about Qualia Solutions team members
- `competitor` — pages about other agencies / overlapping vendors
- `partner` — vendors / integrators we collaborate with (not competing)
- `event` — conferences, demos, talks, expos
- `prospect` — qualified leads, not yet clients

## Practice / Domain knowledge

- `framework` — Qualia Framework (v1, v2, road, milestones, phases, skills)
- `erp` — Qualia ERP system (tables, modules, integrations)
- `voice-agents` — Retell AI / ElevenLabs / Telnyx work
- `ai-automation` — SME automation playbooks, agentic workflows
- `infrastructure` — Vercel / Railway / Supabase / OpenRouter / GitHub orgs
- `design` — DESIGN.md patterns, brand standards, UX
- `security` — RLS, auth, secrets, compliance
- `compliance` — legal/regulatory constraints (GDPR, KSA PDPL, etc.)

## Knowledge type

- `concept` — recurring idea / pattern / principle
- `entity` — person / organization / system
- `source` — distilled summary of a raw document or session
- `analysis` — cross-domain synthesis, comparison, opinion
- `decision` — a choice made, with rationale (architecture, hiring, pricing)
- `gotcha` — a sharp edge / pitfall / "we got burned by X"
- `playbook` — step-by-step operational pattern
- `pricing` — quotes, deal sizes, what won/lost

## Lifecycle

- `active` — currently relevant, in use
- `deprecated` — superseded but kept for history
- `wip` — work in progress, not yet load-bearing
- `inferred` — page contents are mostly LLM synthesis, not source-extracted (review before trusting)

## Visibility

- `visibility/public` — shareable (talks, blog, marketing)
- `visibility/internal` — Qualia team only (default)
- `visibility/sensitive` — pricing, contracts, personnel — handle with care
- `visibility/pii` — contains personal data — never paste into external tools

## Stack-specific (use freely; promote to canonical when used 3+ times)

- `nextjs`, `react`, `supabase`, `vercel`, `railway`, `openrouter`
- `retell`, `elevenlabs`, `telnyx`
- `claude-code`, `notebooklm`

## Conventions

- Use lowercase-kebab-case (`voice-agents`, not `Voice Agents`)
- 3–6 tags per page is the sweet spot
- The first tag should be the page's `type` (concept / entity / source / analysis / decision)
- Combine domain + knowledge-type: `[client, decision]` for "the decision we made for client X"

## Related

- [[wiki/index.md]]
- [[CLAUDE.md]] — vault schema
