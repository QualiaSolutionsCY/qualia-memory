---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia, client-project]
source_url: https://github.com/Qualiasolutions/dawadose
---

# dawadose

> AI Product Availability Checker for DawaDose — scrapes competitor pharmacy sites to find out-of-stock products.

## Overview

An AI-powered tool for DawaDose (online pharmacy marketplace in Jordan and Saudi Arabia) that checks competitor websites for out-of-stock products. Takes a product name as input, scrapes configured competitor sites in real-time using Firecrawl, then uses Claude to analyze results and confirm product matches. Returns availability status, price, and direct links. Arabic-first UI with RTL support.

## Tech Stack

- Next.js 16, React 19, TypeScript
- OpenRouter API (Claude Sonnet for analysis)
- Firecrawl API (web scraping)
- Tailwind CSS
- Vercel (hosting)

## Status

- **Language:** TypeScript
- **Last updated:** 2026-04-12
- **Visibility:** Public

## Key Features

- Natural language product search across competitor sites
- Real-time scraping of competitor pharmacies (sifsaf.com, beautyboxjo.com)
- AI-powered product matching and confirmation
- Returns availability status, price, and direct links
- Arabic-first UI with RTL support
- Teal (#207686) brand color scheme
- Clear "not found" reporting when products unavailable

## Related

- [[Qualia ERP]]
