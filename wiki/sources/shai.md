---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia]
source_url: https://github.com/Qualiasolutions/shai
---

# shai

> Speech therapy practice app for children — AI-powered voice sessions with real-time safeguarding

## Overview

SHai Coach is a speech therapy platform designed for children with speech sound disorders, built for Jay (Glluztech). It provides supervised speech practice sessions powered by AI voice interaction (ElevenLabs) and real-time safeguarding with two-pass LLM transcript scanning. The system supports three user roles: SLT Clinicians, Supporters (TAs/Parents), and Students (Children aged 5-17), with strict UK data protection compliance.

## Tech Stack

- Next.js 16 App Router, React 19, TypeScript, Tailwind CSS
- Supabase PostgreSQL with RLS, Supabase Auth, Supabase Realtime
- ElevenLabs (TTS/STT, Alice voice model - British English)
- OpenRouter (Gemini 2.0 Flash primary, Claude/OpenAI failover)
- Sentry (error tracking), Vercel (deployment)

## Status

- **Language:** TypeScript
- **Last updated:** 2026-03-25
- **Visibility:** Private

## Key Features

- 6-phase evidence-based activity progression (Listening Discrimination through Sentences)
- AI safeguarding with two-pass LLM analysis (classification + detection) and severity levels
- ElevenLabs British English voice sessions with conversation-style interaction
- Complete session transcript retention and therapist review workflow
- Role-based access: therapist dashboard, supporter views, student practice interface
- Demo mode for development/testing with pre-seeded accounts
- 14-table database with 9 enums, full RLS policies
- 25 tests covering auth, rate limiting, PII detection, prompt injection, safeguarding

## Related

- [[Glluztech]] (client)
- [[theracoach]] (related speech therapy project)
- [[theranote]] (related therapy documentation project)
