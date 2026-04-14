---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia, client-project]
source_url: https://github.com/Qualiasolutions/ALKEMY-V3.1
---

# ALKEMY-V3.1

> AI-Powered Film Pre-Production Platform — script analysis, AI casting, 3D worlds, compositing, video generation with identity preservation.

## Overview

ALKEMY transforms screenplays into production-ready visual assets. Upload a script and the platform breaks it down into characters, locations, vehicles, scenes, and shots — then generates AI visuals with identity preservation across all assets. Built for Alkemy Cyprus in partnership with Qualia Solutions. Live at alkemy.me.

## Tech Stack

- React 18, TypeScript (strict), Vite 6
- Tailwind CSS, Framer Motion
- Zustand (10 stores) + React Context orchestrator
- Supabase (Auth, PostgreSQL, Storage, 38 Edge Functions in Deno)
- Gemini 3 Pro (text/image), Veo 3.1 (video), Kling AI / O1 (identity preservation), FAL.ai
- Upstash Redis (rate limiting)
- Vercel (frontend), Supabase (edge functions)
- Sentry, Vercel Analytics
- Vitest (333 unit tests), Playwright (12 E2E tests)
- GitHub Actions CI/CD

## Status

- **Language:** TypeScript
- **Last updated:** 2026-04-14
- **Visibility:** Private

## Key Features

- Drag-and-drop screenplay upload with AI entity extraction
- AI casting with Kling O1 face-binding for cross-asset identity preservation
- Location scouting with 6-angle capture system per location
- Shot compositing (character + location + vehicle) with moodboard-driven styling
- Multi-model video generation (Veo 3.1, Kling 2.6 Pro, Kling O1 ref-to-video)
- C.O.R.A — AI cinematography assistant (context-aware sidebar)
- Timeline NLE with 13 transition types, WebCodecs H.264 export (720p/1080p/4K)
- Audio studio with AI music, SFX, and voice synthesis
- Motion control editor with camera path editing
- JWT auth, RLS, rate limiting, fail-closed quota enforcement

## Related

- [[Qualia ERP]]
