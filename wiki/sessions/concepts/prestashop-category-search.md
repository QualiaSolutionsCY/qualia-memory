---
title: "PrestaShop Category-Based Search"
aliases: [prestashop-search, armenius-product-search, category-filtering]
tags: [prestashop, e-commerce, search, armenius, voice-agents]
sources:
  - "daily/2026-04-26.md"
created: 2026-04-26
updated: 2026-04-26
---

# PrestaShop Category-Based Search

PrestaShop's `/search` endpoint matches keywords against product descriptions, not just titles, causing irrelevant results for generic queries. The solution implemented for the Armenius voice/text widget was to use category-based filtering with a named enum mapping (e.g., `category="laptops"`) instead of keyword search, with brand-specific search as a fallback strategy.

## Key Points

- PrestaShop keyword search for "laptop" returned RAM modules because RAM product descriptions contain the word "laptop" — category filtering is the reliable path
- Category IDs were mapped to a named enum: Laptops=224, Desktops=221, Monitors=238, Keyboards=239, Headsets=241, Headphones=292, Smartphones=287, Tablets=288, Printers=376, Gaming=316, Laptops(refurb)=438
- `display=[id,associations]` filter on PrestaShop API strips product lists from category responses — must fetch without display constraint to get association data
- Products with price=0 are hidden/quote-only items — always filter these from results
- ElevenLabs tool schema `description` field teaches the LLM when to use params — include trigger phrases like "under N", "less than N"

## Details

The Armenius widget (qualia-widgets.vercel.app) serves as a product search interface backed by a Cloudflare Worker webhook (armenius-webhook.workers.dev). When users asked for "laptops under €800", the original keyword-based search returned irrelevant products because PrestaShop's relevance ranking is poor for generic terms. The fix was two-fold: add a `category` enum parameter to the `searchProducts` webhook tool, and add `max_price`/`min_price` parameters for price filtering.

The category ID discovery was itself a solved problem. Since the PrestaShop API key was stored as a Cloudflare secret and wasn't accessible during the session, a `/listCategories` endpoint was added to the webhook itself. This pattern — adding a discovery endpoint to the service that already holds the credentials — is reusable for any case where API keys aren't directly available.

Product list formatting (bulleted cards, euro symbol conversion) was applied only to the text chat widget, not the voice agent. This separation is important: voice agents need conversational output, while text widgets benefit from structured formatting. The formatting logic lives in the widget JavaScript layer, not in the LLM prompt, to avoid cross-contamination between modalities.

A CI pipeline is active for the Armenius project: push to main triggers typecheck → wrangler deploy → health check via `.github/workflows/deploy.yml`.

## Related Concepts

- [[concepts/llm-wiki-infrastructure]] — This domain knowledge would be captured for future reference
- [[concepts/qualia-os-unification]] — Armenius webhook is part of the broader Qualia project ecosystem

## Sources

- [[daily/2026-04-26.md]] — Sessions at 02:52 (search quality problem, category discovery, price filtering), 02:56 (widget formatting, CI pipeline, open bug with "Start a Call" buttons)
