---
title: "Connection: Voice AI Platform Selection Criteria"
connects:
  - "concepts/elevenlabs-voice-agent-tool-design"
  - "concepts/ophthalmos-voice-agent-proposal"
sources:
  - "daily/2026-04-29.md"
created: 2026-04-29
updated: 2026-04-29
---

# Connection: Voice AI Platform Selection Criteria

## The Connection

Two voice AI engagements on the same day (2026-04-29) — fixing the qualiasolutions.net website concierge (ElevenLabs) and proposing a telephony-heavy voice agent for Ophthalmos (Retell AI) — reveal a clear decision framework for platform selection. The determining variable is the primary interaction channel: website widget vs phone calls.

## Key Insight

The non-obvious relationship is that the platform choice is not about voice quality or AI capability (both ElevenLabs and Retell AI are strong on those axes) but about **infrastructure requirements**. A website-embedded voice agent needs clean browser audio capture, low-latency WebSocket streaming, and client-side tool execution (booking widgets, form fills) — ElevenLabs excels here with its client-tool architecture that executes silently via frontend JS. A telephony voice agent needs SIP trunking, call transfer, IVR menus, DTMF handling, and carrier-grade reliability at 200-300 calls/day — Retell AI + Telnyx is built for this.

The URL-spelling bug in the ElevenLabs concierge reinforces the distinction: ElevenLabs' client tools are perfect for website booking flows (capture lead, check availability, book meeting — all silent), but its webhook tools are dangerous because they inject tool responses into the voice stream. In a telephony context (Retell), this tool response architecture is different — Retell manages tool calls through its own orchestration layer, and results are formatted for speech by design.

Pricing also diverges: website concierge agents are essentially a premium contact form replacement (~$0-500/month in API costs at typical website traffic), while telephony agents handling 200-300 calls/day at 3-5 minutes each incur $1,500-5,000/month in carrier + API costs. This infrastructure cost difference justifies the 10-20x higher setup pricing for telephony agents (€11-15K for Ophthalmos vs essentially free setup for the website widget).

The Qualia standard stack covers both: **ElevenLabs for website widgets, Retell AI + Telnyx for telephony, ElevenLabs for voice synthesis in both.** The decision tree is:

1. Is the primary channel a website? → ElevenLabs agent with client tools
2. Is the primary channel phone calls? → Retell AI + Telnyx with ElevenLabs voices
3. Is it both? → Two separate agents, shared voice identity via ElevenLabs

## Evidence

- ElevenLabs concierge (qualiasolutions.net): website widget, 3 client tools (capture_lead, check_availability, book_meeting), URL-spelling bug caused by webhook tools — [[concepts/elevenlabs-voice-agent-tool-design]]
- Ophthalmos proposal: 200-300 calls/day, Cypriot Greek dialect, telephony infrastructure required, €11-15K setup + €600/month — [[concepts/ophthalmos-voice-agent-proposal]]
- The ElevenLabs article explicitly notes: "The decision to stay on ElevenLabs rather than switching to Retell AI was deliberate. Retell's strengths are telephony-heavy flows"
- Qualia infrastructure rules define the standard voice stack as "Retell AI + ElevenLabs + Telnyx"

## Related Concepts

- [[concepts/elevenlabs-voice-agent-tool-design]]
- [[concepts/ophthalmos-voice-agent-proposal]]
