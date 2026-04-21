---
type: analysis
created: 2026-04-21
updated: 2026-04-21
tags: [conference, speech, ai-expo, cyprus, qualia-thesis]
---

# AI Expo Cyprus 2026 — Talking Points

> Synthesized from the whole Qualia portfolio — real shipped work, not slides. Use as raw material when drafting the speech. Conference is **Thursday 2026-04-23 and Friday 2026-04-24**, 09:00–18:00 Europe/Nicosia. Contact: Andreas (hot lead, meeting scheduled 2026-04-21 12:00). Panel discussion also on the agenda (client "Panel Disscusion Conference", meeting 2026-04-21 11:00).

---

## The one sentence

> *"AI doesn't replace small businesses — it gives them the ops team they could never afford."*

Everything below is evidence for that sentence.

## The thesis (three beats)

1. **Agencies + AI is a real category, not a trend.** Qualia ships ~40 projects, ~50 clients across Cyprus, Jordan, UK, UAE, Saudi with a team of 5. That ratio is only possible because the delivery layer is AI-operated.
2. **Voice is the interface that actually unlocks SMEs.** Text chat is still the founder's comfort zone. Voice is the employee's. Every business that hired us for voice agents (Sophia/Zyprus, Boss Brainz, Alexis/Armenius, Alfred/Underdog, Carton/UK medical) had the same motive: *I can't hire a person who'll answer the phone at 9pm or in Arabic at 3am — but I can afford an agent.*
3. **Model-agnostic routing is the only durable architecture.** We use OpenRouter on every project. The specific model underneath shifts every 6 weeks. If you wire directly to a provider, you ship debt. See [[Concepts/OpenRouter Routing]].

## War stories (pick 3 for the talk)

These are all real shipped projects — cite them by name, they're in `wiki/sources/`.

### 1. Sophia — Zyprus real estate voice agent
Zyprus Property Group is a €400/mo retainer. Sophia answers calls 24/7, qualifies leads, books viewings. The value isn't "AI agent" — it's *no missed lead at 10pm on a Sunday when Lauren is having dinner.* Source: [[sources/qualia-erp]], [[Sophia AI]].

### 2. Boss Brainz — Giulio Underdog Sales
Retainer going from €1,700/mo to €2,500/mo in May 2026. A voice brain that runs sales coaching for Giulio's UK academy. War story: the economics of selling AI coaching *back* to the people who bought AI-Sales-Coach as students. The agent doesn't replace Giulio; it scales his evening hours. See [[Boss Brainz]], [[AI Sales Coach]].

### 3. Sakani — Jordanian real estate mobile app
12,000 JOD (€14,520) contract across 4 milestones. Bilingual Arabic/English, Expo + Next.js monorepo. Not an "AI product" — but we used AI *to build it*: M9 client feedback rounds were closed by AI-assisted codegen in hours, not days. This is the agency story. See [[Sakani App]].

### 4. DawaDose — pharmacy counter-feit checker (Jordan/Saudi)
Firecrawl + Claude, RTL Arabic. Pharmacists photograph a product, the system validates whether the importer is legal in that market. Safety, not novelty. See [[sources/dawadose]].

### 5. Sigatalachana — Greek grocery invoice OCR
Gemini Vision + Python. A family-run Nicosia grocery was losing hours every day typing supplier invoices. The agent now reads them from a phone photo, pushes into accounting. See [[sources/sigatalachana-ai-agent]].

### 6. Geppetto — real-time multilingual voice orchestrator
Shipped 22 commits on 2026-04-20 for the 2026-04-21 demo. Whisper (Chinese/Spanish auto-detect) + TTS + Textual dashboard state machine. Demo for Arijit Sircar (€5–10k v1 budget, start 2026-05-01). Cite only if talk runs voice-heavy. See [[sources/geppetto]] (to create) / `~/Projects/geppetto`.

### 7. Kronospan — NL-to-SQL offline on NVIDIA DGX Spark
Industrial-scale Cypriot wood manufacturer. Ask finance questions in plain English, get SQL answers on-prem — no cloud, no data leaves the site. Enterprise AI story, counter-balances the SME war stories. See [[sources/kronospan]].

## Numbers to land in the talk

Pulled from ERP as of 2026-04-21:

| What | Number |
|------|-------:|
| Clients in pipeline | 51 |
| Active client projects | 14 |
| Live client sites | 10 |
| Demo/pipeline projects | 8 |
| Voice-AI projects shipped or shipping | 7+ (Sophia, Boss Brainz, Alfred, Alexis, Carton, Geppetto, shai) |
| GitHub repos in org | ≥25 in QualiasolutionsCY + SakaniQualia |
| MRR committed | €2,350/mo → €3,150/mo from May 2026 |
| Team size | 5 (1 owner, 3 employees, 1 trainee) |
| Countries served | Cyprus, Jordan, UK, UAE, Saudi, Greece |

Frame for the audience: *five people, five countries, and the difference is AI in the delivery loop.*

## Patterns that make this work (the mechanics)

These are not PowerPoint clichés — they're how we actually ship. Good for a technical audience or a "how did you do this" Q&A.

### Fresh-context subagents
Every planning/build/verify step runs in an isolated AI context. Task 50 gets the same attention as task 1. No garbage accumulation. This is why we can run 3 client streams in parallel without drift.

### Model routing over model loyalty
OpenRouter, not OpenAI-direct. The model under the hood changes quarterly. The code doesn't. See [[Concepts/OpenRouter Routing]].

### Agencies are the right unit
A single founder can't integrate 6 APIs, RLS-gate a database, ship a mobile app, and also pitch. An agency with an AI-native delivery layer can. That's the product/market fit.

### RLS-first, every table
Supabase with row-level security on from day one. Keeps us out of the "we shipped a leaky MVP" category that most AI startups live in. See `rules/security.md`.

### Voice before chat
Chat looks AI-ish and scares SMEs. Voice feels like a receptionist. Every SME deployment we've done had higher first-month retention when we led with voice.

## Format suggestions for the talk

- **10-minute keynote**: thesis + 3 war stories + one number slide + one "how to start" slide. Cold open on a recorded Sophia call.
- **20-minute talk**: thesis + 5 war stories + mechanics section + Q&A prompt.
- **Panel**: lead with the one-sentence, answer everything with a named project, never speak in abstractions.

## Things NOT to say

- Don't say "AI is transforming everything" — the audience will tune out. Name the invoice clerk you replaced at Froutaria Siga ta Lachana.
- Don't demo something that might break live unless you've rehearsed 3x. Geppetto's multilingual demo is fragile on a hotel Wi-Fi.
- Don't quote OpenAI model names from memory — they'll be wrong by conference day. Say "we route across providers" and move on.
- Don't undersell Cyprus. MENA-adjacent + EU compliant + English-speaking is a real moat. Lean into it.

## Pre-conference checklist

- [ ] Confirm Andreas meeting 2026-04-21 12:00 — get slot length, audience size, whether demo laptop is allowed
- [ ] Confirm Panel Discussion meeting 2026-04-21 11:00 — what's the panel question?
- [ ] Decide 3 of the 7 war stories to lead with
- [ ] Record a 30-second Sophia clip for cold open
- [ ] Print business cards with portal.qualiasolutions.net QR (Panel attendees → lead magnet)
- [ ] Pre-stage a `sigatalachana-ai-agent` demo (offline backup if live demo fails)
- [ ] Update [[Qualiafinal]] hero to reflect AI-first positioning if anyone Googles the company mid-talk

## Related

- [[wiki/analysis/qualia-ai-thesis]]
- [[wiki/concepts/voice-agents]]
- [[wiki/concepts/openrouter-routing]]
- [[wiki/concepts/sme-ai-automation]]
- [[wiki/index]]
- [[Qualia Framework v2]]
