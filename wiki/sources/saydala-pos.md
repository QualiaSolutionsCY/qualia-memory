---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia]
source_url: https://github.com/Qualiasolutions/saydala-pos
---

# saydala-pos

> Pharmacy point-of-sale system for Abdellah Pharmacy in Amman, Jordan — bilingual Arabic/English with RTL support

## Overview

SaydalaPOS is a pharmacy point-of-sale system built for Abdellah Pharmacy in Amman, Jordan. It is a single-pharmacy, single-user system focused on simplicity over features, shipped at v1.0 with 9,337 lines of TypeScript. The app handles currency in Jordanian Dinars (stored as fils integers), includes full Arabic/English bilingual support with RTL, and uses Zustand for cart state persisted to localStorage.

## Tech Stack

- Next.js 15, React 19, TypeScript, Tailwind CSS
- Supabase (PostgreSQL, Auth, RLS)
- next-intl (Arabic/English i18n with RTL)
- Zustand (cart state, localStorage persistence)
- shadcn/ui
- Vercel (deployment)

## Status

- **Language:** TypeScript
- **Last updated:** 2026-03-03
- **Visibility:** Private

## Key Features

- Pharmacy POS with cart management and sales processing
- Currency handling in fils (1 JOD = 1000 fils) with 3-decimal display
- 16% VAT calculation on all sales
- Full Arabic/English bilingual support with automatic RTL
- Locale-based routing (`/[locale]/...`)
- Pharmacist guide and user manual included
- Supabase with RLS policies
- Project handover documentation

## Related

- [[Abdellah Pharmacy]] (client - Amman, Jordan)
- [[sigatalachana-ai-agent]] (related POS/inventory project)
