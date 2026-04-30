---
title: "ElevenLabs Voice Agent Tool Design"
aliases: [voice-agent-tools, url-spelling-bug, client-vs-webhook-tools, qualia-concierge-v2]
tags: [elevenlabs, voice-agents, tool-design, qualiasolutions, bug]
sources:
  - "daily/2026-04-29.md"
created: 2026-04-29
updated: 2026-04-29
---

# ElevenLabs Voice Agent Tool Design

Both ElevenLabs voice agents on qualiasolutions.net (Qualia Concierge v2 and AI Assistant) were spelling out cal.com URLs character by character during booking flows. The root cause was webhook-type tools returning URLs in their responses — the agent treated the URL as conversation text and read it aloud. The fix unified both agents to identical config, switched all booking tools to client-type (which execute silently via frontend JS handlers), and swapped the LLM from Gemini 2.5 Flash to Claude Haiku 4.5 for better response quality.

## Key Points

- **Webhook tools return responses the agent speaks:** ElevenLabs "webhook" tools send the response back to the agent as conversation input — if that response contains a URL, the agent will spell it out character by character. "Client" tools execute via frontend JS handlers silently, never entering the conversation stream
- **Tool type determines voice behavior:** For booking flows, client tools are correct (capture lead → check availability → book meeting all execute silently); webhook tools are only appropriate when the agent needs to speak the result (e.g., answering a factual question)
- **Unified both agents:** User confirmed "both should act the same" — merged Qualia Concierge v2 and AI Assistant into identical config with 8,255 byte prompt preserving all personality, work history, team details, and objection handling
- **LLM swap: Gemini 2.5 Flash → Claude Haiku 4.5:** User requested higher quality; Haiku chosen for speed+quality balance over Sonnet (which would add latency to voice interactions)
- **Older AI Assistant agent is unreferenced in codebase:** Only v2 is used in `QualiaAssistant.tsx` — the older agent could be retired if confirmed redundant

## Details

The bug was reported as both agents "spelling out links and random shit" when users asked to book meetings. Investigation revealed two different tool architectures across the agents: the v2 agent used webhook tools that returned cal.com booking URLs plus a prompt instruction to "read that message aloud verbatim," while the older agent used client tools wired via frontend `voiceAgentTools.ts` that executed silently on the client side. The older agent's client tools were the correct pattern — they capture leads, check availability, and book meetings without injecting any response into the voice stream.

The distinction between ElevenLabs tool types is critical for voice agent design. **Webhook tools** make an HTTP request to an external endpoint and feed the response back to the LLM as context — the agent then decides what to say based on that response. If the response contains a URL, ID, or any machine-readable string, the agent will attempt to verbalize it. **Client tools** send a signal to the frontend JavaScript, which handles the action entirely in the browser (showing a booking widget, capturing form data, etc.) without returning anything to the voice stream. The rule is: any tool that handles booking, lead capture, or data submission should be a client tool. Only tools whose results need to be spoken (e.g., "your appointment is confirmed for Tuesday at 3 PM") should be webhook tools — and even then, the webhook response should be natural language, never raw URLs or IDs.

When patching ElevenLabs agents via API, a practical gotcha was discovered: tool attachment requires using the tool IDs returned from the agent read endpoint, not re-posting the full tool schemas. Re-posting schemas creates duplicate tools rather than updating the existing ones.

The LLM swap from Gemini 2.5 Flash to Claude Haiku 4.5 follows the same pattern seen in [[concepts/fotini-ai-legal-platform|Fotini AI]], where Gemini Flash was replaced by Claude Sonnet due to empty streams. In this case, the swap was quality-driven rather than bug-driven — the user wanted more natural, higher-quality conversational responses. Haiku was chosen over Sonnet as a speed+quality tradeoff appropriate for real-time voice interactions where latency directly impacts user experience.

The decision to stay on ElevenLabs rather than switching to Retell AI was deliberate. Retell's strengths are telephony-heavy flows (SIP trunking, call transfer, IVR menus), while ElevenLabs excels at website-embedded voice agents with high voice quality. Since the Qualia concierge is a website widget (not a phone system), and three agents were already wired with working client tools, migrating to Retell would have been unnecessary churn. The bug was in tool/prompt configuration, not the platform itself.

## Related Concepts

- [[concepts/fotini-ai-legal-platform]] — Same Gemini-to-Claude model swap pattern; there it was Gemini Flash → Claude Sonnet for empty streams, here it was Gemini Flash → Claude Haiku for quality improvement
- [[concepts/sophia-lead-routing-debug]] — Both are "system appeared to work but produced wrong output" bugs — Sophia silently swallowed a failed forward, the voice agents loudly spelled URLs. Different failure modes (silent vs loud) from the same root cause class: tool response not properly handled by the consuming layer
- [[concepts/prestashop-category-search]] — The Armenius voice/text widget also needed careful separation of voice vs text output formats — voice agents need conversational output while text widgets benefit from structured formatting
- [[concepts/qualia-os-unification]] — ElevenLabs is part of the standard Qualia voice stack (Retell AI + ElevenLabs + Telnyx) documented in infrastructure rules

## Sources

- [[daily/2026-04-29.md]] — Session at 19:20: "Both agents 'spelling out links and random shit' — webhook tools returning cal.com URLs that the agent read aloud character by character"
- [[daily/2026-04-29.md]] — Session at 19:20: "Switched LLM from Gemini 2.5 Flash to Claude Haiku 4.5 — user requested higher quality, Haiku chosen for speed+quality balance"
- [[daily/2026-04-29.md]] — Session at 19:20: "Unified both agents to identical config — 8,255 byte prompt preserved all personality, work history, team details, objection handling"
- [[daily/2026-04-29.md]] — Session at 19:20: "Stay on ElevenLabs over Retell for website concierge — tools already work, voice quality good, Retell only wins for telephony-heavy flows"
- [[daily/2026-04-29.md]] — Session at 19:20: "The older AI Assistant agent isn't referenced anywhere in codebase — only v2 is used in `QualiaAssistant.tsx`"
- [[daily/2026-04-29.md]] — Session at 19:20: "ElevenLabs tool types: 'client' tools execute via frontend JS silently, 'webhook' tools return responses the agent speaks — for booking flows, client tools are correct"
