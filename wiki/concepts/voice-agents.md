---
type: concept
created: 2026-04-21
updated: 2026-04-21
tags: [voice, agents, retell, vapi, elevenlabs, telnyx]
---

# Voice Agents

> The Qualia default for SME client acquisition. Voice beats chat for SMEs because it maps onto a job role (receptionist, sales rep, coach) the client already knows how to budget for.

## Stack we use

- **Retell AI** — primary orchestration for production voice agents (`RETELL_API_KEY`).
- **VAPI** — historically used on Qualia ERP voice features; Retell is preferred for new deployments.
- **ElevenLabs** — voice synthesis, cloning, streaming (`ELEVENLABS_API_KEY`).
- **Telnyx** — telephony / SIP for PSTN numbers in Cyprus and UK (`TELNYX_API_KEY`).
- **OpenRouter** — LLM routing (GPT-4 / Claude / Gemini interchangeable).

## Shipped / shipping voice agents

- **Sophia — Zyprus** (real estate lead qualifier, bookings)
- **Boss Brainz — Giulio / Underdog Sales** (€1,700→€2,500/mo retainer)
- **Alfred / Aifred — Giulio / Underdog Sales** (voice sales agent)
- **Alexis — Armenius** (internal ops agent for Armenius IT)
- **Carton — UK medical practices** (lead follow-up voice)
- **Geppetto — Arijit Sircar** (real-time multilingual orchestrator, Whisper + TTS + Textual state machine — see `~/Projects/geppetto`)
- **Shai** — speech therapy for children (AI voice + safeguarding)

## Design patterns

- **One job, named intent.** "Sophia books viewings" — not "Sophia does real-estate stuff." Scope creep kills voice agents faster than bad audio.
- **Handoff loop.** Every agent must be able to escalate to a human with full transcript context. Clients don't buy "never needs a human," they buy "handles the 80% that did need one."
- **Silence and barge-in tuned to the client's accent.** Cyprus accents, Arabic code-switching, and UK regional accents all need different VAD thresholds. Geppetto's `all_language_probs` threshold code is the reference.
- **Transcripts → CRM.** Every call writes a summary + action items into the client CRM (or ERP) so sales can follow up the next morning.
- **Record consent.** Cyprus GDPR requires in-call consent. Bake it into the opening line.

## Pricing pattern

Voice agents price as retainers, not projects. Client expectation: "it's like paying for a part-time employee." That frame closes deals; project quotes don't.

## Where voice fails

- High-compliance phone lines (banking, healthcare diagnostics) — regulatory risk outweighs revenue.
- Cultures where voicemail is normal and cold-call pickup rates are already low — agent doesn't help if no one answers.
- Clients who can't define the "one job" clearly. Walk away politely.

## Related

- [[wiki/analysis/qualia-ai-thesis]]
- [[wiki/analysis/ai-expo-2026-talking-points]]
- [[Sophia AI]]
- [[Boss Brainz]]
- [[Aifred]]
- [[Alexis AI]]
