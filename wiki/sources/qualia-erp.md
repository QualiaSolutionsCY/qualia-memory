---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia, internal-tool]
source_url: https://github.com/Qualiasolutions/qualia-erp
---

# qualia-erp

> Internal project management platform for Qualia Solutions — task management, CRM, AI assistant, voice AI, knowledge base, and client portal.

## Overview

Qualia ERP is the internal project management platform for Qualia Solutions. It combines task management, CRM, team scheduling, payment tracking, and AI assistance (text chat + voice via VAPI) into a single platform. Includes a knowledge base with RAG (document embeddings via pgvector), real-time notifications, employee clock-in/out, and a client portal with workspace isolation. Live at portal.qualiasolutions.net.

## Tech Stack

- Next.js 16+ (App Router), React 19, TypeScript
- Supabase (PostgreSQL, pgvector for RAG)
- Tailwind CSS + shadcn/ui
- SWR (state management with auto-refresh)
- Google Gemini via AI SDK (AI chat with tool calling)
- VAPI (voice AI assistant with webhooks)
- Resend (email)
- Sentry (monitoring)
- Vercel (hosting)
- Jest (testing)

## Status

- **Language:** TypeScript
- **Last updated:** 2026-04-14
- **Visibility:** Public

## Key Features

- Task management with inbox, scheduling, and drag-and-drop
- Project tracking with roadmap phases and milestones
- CRM with lead pipeline and client portal
- Team schedule with timezone support (Cyprus/Jordan)
- Payment tracking with retainer automation
- AI chat assistant with tool calling (Gemini)
- Voice AI assistant via VAPI webhooks
- Knowledge base with RAG (document embeddings)
- Real-time notifications
- Employee clock-in/clock-out with session tracking
- Client portal with workspace isolation
- 45 server action domain modules
- Starter templates: ai-agent, platform, voice, website

## Related

- [[qualia-framework]]
- [[qualiafinal]]
