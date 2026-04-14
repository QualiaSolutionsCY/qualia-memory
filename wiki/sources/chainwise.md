---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia]
source_url: https://github.com/Qualiasolutions/chainwise
---

# chainwise

> Cryptocurrency portfolio management platform with AI-powered personas — production at chainwise.tech

## Overview

ChainWise is a cryptocurrency portfolio management platform with AI-powered personas (Buddy, Professor, Trader) using Google Gemini models. Users can manage crypto portfolios with AI guidance tailored to different interaction styles. The platform integrates CoinGecko for market data, Stripe for payments, and includes comprehensive E2E testing with Playwright. Production live at chainwise.tech.

## Tech Stack

- Next.js 15.5.3 App Router + Turbopack, TypeScript
- Supabase (PostgreSQL with RLS, Auth)
- Google Gemini (AI personas, fallback from OpenRouter/DeepSeek)
- CoinGecko API (market data)
- Stripe (payments)
- Playwright (E2E testing), Vitest (unit testing)
- Sentry (monitoring)
- Vercel (deployment)

## Status

- **Language:** TypeScript
- **Last updated:** 2026-02-04
- **Visibility:** Private

## Key Features

- AI-powered personas: Buddy (friendly), Professor (educational), Trader (analytical)
- Cryptocurrency portfolio tracking and management
- CoinGecko market data integration
- Stripe payment processing
- Google Gemini AI with model failover
- Comprehensive test suite (Vitest + Playwright E2E)
- BMad Method workflow integration
- Manual testing guide and documentation
- Production live at chainwise.tech

## Related

- [[ChainWise client]]
