---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia]
source_url: https://github.com/Qualiasolutions/qualiafinal
---

# qualiafinal

> Qualia Solutions company website — AI Studio app with chat, image/video generation, and full production pipeline.

## Overview

The main Qualia Solutions website, built as a React SPA with Vite. Features AI-powered chat (Groq), image/video generation (Gemini), Sentry error tracking, Google Analytics, and a comprehensive test suite. Originally scaffolded from Google AI Studio.

## Tech Stack

- React 19, TypeScript, Vite 6 + SWC
- Tailwind CSS v4
- Framer Motion (View Transitions API with fallback)
- Groq API (chat), Gemini API (image/video)
- Sentry (error tracking), Google Analytics
- Vercel (hosting + serverless functions)
- Playwright (E2E), Vitest (unit tests)

## Status

- **Language:** TypeScript
- **Last updated:** 2026-04-14
- **Visibility:** Private

## Key Features

- React 19 with lazy loading and View Transitions API
- Vite 6 + SWC for fast builds with file warming
- Image optimization (76% size reduction via vite-plugin-image-optimizer)
- Core Web Vitals optimized (LCP with fetchpriority/preconnect)
- SEO with PageSEO components and JSON-LD breadcrumbs
- API keys secured in Vercel serverless functions
- 108 unit tests + comprehensive E2E flows
- CI/CD via GitHub Actions (unit tests, build, E2E)

## Related

- [[Qualia ERP]]
- [[qualia-framework]]
