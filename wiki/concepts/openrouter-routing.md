---
type: concept
created: 2026-04-21
updated: 2026-04-21
tags: [openrouter, llm, routing, architecture]
---

# OpenRouter Routing

> Every Qualia project routes LLM calls through OpenRouter rather than calling OpenAI / Anthropic / Google directly. This is a deliberate architectural choice, not a price optimization.

## Why

- **Model shelf-life is ~90 days.** GPT-4o → GPT-4.1 → GPT-5 → Claude 4.6 → 4.7 in the time a client retainer runs. Direct provider SDKs churn faster than client relationships.
- **Per-task routing.** OCR-heavy tasks → Gemini. Long-context synthesis → Claude Opus. Cheap summarization → Haiku / GPT-4o-mini. One env var, multiple models.
- **Cost transparency.** OpenRouter itemizes per-call spend. Client-facing dashboards can show *cost per resolved call* which is the metric voice-agent retainers actually care about.
- **One API surface.** Client codebases stay identical across providers. Onboarding new engineers is a 10-minute job, not a 2-day job.

## Convention

- Env var: `OPENROUTER_API_KEY`
- Never hardcode a provider SDK (`openai`, `@anthropic-ai/sdk`, `@google/generative-ai`) unless the client explicitly requires direct integration for compliance.
- Model selection lives in a config file or feature flag, not the call site.
- Fallback chain: define a primary + 1 fallback for every task. If primary rate-limits, fallback fires automatically.

## When to go direct (exceptions)

- **Claude-specific features** (prompt caching, message batches, extended thinking, citations) — use `@anthropic-ai/sdk` directly to access the Claude API features that don't proxy through OpenRouter. Still keep the routing layer so non-Claude work stays provider-agnostic.
- **Enterprise compliance** — Kronospan-style on-prem needs direct weights, not a router.
- **Vercel AI Gateway** — equivalent product for Vercel-hosted apps. Interchangeable with OpenRouter for most cases; prefer it inside a Next.js app on Vercel for the native observability.

## How this shows up in shipped code

- `qualia-erp` uses OpenRouter for AI assistant (`ai_conversations`, `ai_messages` tables).
- `Boss Brainz`, `Sophia`, `Alfred` all route their thinking step through OpenRouter.
- `DawaDose`, `Sigatalachana`, `Chainwise` use OpenRouter for vision + synthesis tasks.

## Related

- [[wiki/analysis/qualia-ai-thesis]]
- [[wiki/concepts/voice-agents]]
- [[wiki/concepts/sme-ai-automation]]
