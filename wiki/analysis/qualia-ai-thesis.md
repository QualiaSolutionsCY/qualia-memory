---
type: analysis
created: 2026-04-21
updated: 2026-04-21
tags: [thesis, positioning, qualia-solutions, strategy]
---

# Qualia's AI Thesis

> The operating worldview behind Qualia Solutions — distilled from shipped projects, client conversations, and the current portfolio as of 2026-04-21.

## Short version

AI doesn't replace small businesses — it gives them the ops team they could never afford. Our job is to ship that ops team, one domain at a time, on an AI-native delivery layer that scales with 5 people, not 50.

## Four claims we actually operate by

### 1. The market is SMEs, not enterprise — with one carve-out
Enterprise AI is a slower sales cycle and a different product (on-prem, compliance, custom training). SMEs are faster, cheaper to close, and more grateful for incremental wins. **Exception:** Kronospan-style deployments (offline NL-to-SQL on NVIDIA DGX Spark) are where enterprise margin lives. Take them when they show up; don't build the agency around chasing them.

### 2. Voice is the SME front door
Text chatbots scared the Froutaria grocer. A voice agent that sounds like his cousin did not. Every SME deployment we land via voice has higher first-month retention. See [[Concepts/Voice Agents]].

### 3. Model-agnostic or bust
Every project routes through OpenRouter. When GPT-5 ships or Claude changes pricing, we swap one env var. Clients who wired directly to OpenAI in 2024 are rewriting in 2026. We never are. See [[Concepts/OpenRouter Routing]].

### 4. Agency > startup for this moment in AI
- A single-product AI startup bets on one thesis being right for 5 years.
- An agency ships ten theses per quarter and listens to which ones clients pay for twice.
- With AI-native delivery (fresh-context subagents, planner/builder/verifier loops), an agency can do the work of a 50-person team with 5 people.
- The agency *graduates* into products only when a thesis has paid itself back at least twice (e.g., Qualia ERP, Boss Brainz).

## What we don't believe

- **"LLM + vector DB = product."** No. That's a feature. The product is the workflow it replaces.
- **"Generic chatbot as MVP."** Every generic chatbot we've shipped was churned within 90 days. Specific agents with one job (Sophia = viewings, Alfred = cold-call triage) retain.
- **"Open-source self-host saves money."** For SMEs it costs more in ops hours than the API bill. Save self-host for clients with a compliance reason (Kronospan, pharmacy).
- **"AI replaces coders."** It replaces the glue — the repetitive scaffolding, the test writing, the docs. Coders who embrace that ship 5x more. We live this: see [[Qualia Framework v2]].

## Geography & positioning

Cyprus base, Jordan team, client work in UK, UAE, Saudi, Greece, KSA. Three structural advantages:
- **EU-compliant** from day one (GDPR, VAT handled).
- **MENA-adjacent** — Arabic-native team in Jordan, RTL projects ship natively (DawaDose, shai, Saydala POS).
- **English default** — no localization friction with US/UK clients.

This is the "Cyprus + Jordan" stack. Don't apologize for being small — it's the whole advantage.

## Pricing philosophy

- **Retainers** for things that run every day (voice agent, RAG ops, invoice AI). Currently €2,350/mo, €3,150/mo from May.
- **Milestone billing** for new builds (Sakani = 4 invoices of ~€3,600 EUR each).
- **Demos** for leads we want to close (Bio Pharma, The Pantry, AlJaber, Giannois UK, Dawadose all currently in demo status).
- Never quote hourly. Hourly rewards slowness; we reward shipping.

## What "winning" looks like in 2026

- €5k+/mo MRR by end of June (committed path: May hits €3,150, Geppetto v1 + Andreas Expo leads close = plausible).
- 2 more enterprise-adjacent deployments like Kronospan.
- Qualia Framework v2 stable, open-sourced, enough outside adoption that it's the reason clients call us.
- One hire (Sally full-time from June 2026, confirmed).

## Related

- [[wiki/analysis/ai-expo-2026-talking-points]]
- [[wiki/concepts/voice-agents]]
- [[wiki/concepts/openrouter-routing]]
- [[wiki/concepts/sme-ai-automation]]
- [[wiki/concepts/agency-delivery-model]]
- [[wiki/index]]
