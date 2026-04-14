---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia]
source_url: https://github.com/Qualiasolutions/sigatalachana-ai-agent
---

# sigatalachana-ai-agent

> Automated invoice processing system for a Greek grocery store — OCR extraction, smart item matching, POS upload

## Overview

Sigatalachana is an automated invoice processing system for a Greek/Cypriot grocery store. It extracts invoice data via Gemini AI OCR, matches items against a POS catalog of 6000+ items using a 10-step priority matching algorithm (barcode, translation, fuzzy matching), detects price changes, and uploads to the POS system. The system supports Greek, Cypriot dialect, and English invoices through three interfaces: Telegram bot, web frontend, and REST API.

## Tech Stack

- Python 3.11+ (backend, Telegram bot, matching engine)
- FastAPI (REST API with SSE streaming)
- Google Gemini AI (OCR invoice extraction)
- Next.js (web frontend)
- Telegram Bot API
- Railway (backend deployment), Vercel (frontend deployment)

## Status

- **Language:** Python
- **Last updated:** 2026-03-05
- **Visibility:** Private

## Key Features

- Gemini AI OCR extraction from invoice images/PDFs
- 10-step priority matching algorithm with barcode, translation, and fuzzy matching
- Greek/English/Cypriot dialect support
- Price change detection with >5% alerts
- Multi-interface: Telegram bot, web frontend, REST API
- POS catalog integration (6000+ items)
- Rate-limited API endpoints (10/min tools, 3/min invoice processing)
- Supplier catalog management with JSON files

## Related

- [[Sigatalachana]] (client - Greek grocery store)
