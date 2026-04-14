---
type: source
created: 2026-04-14
updated: 2026-04-14
tags: [repo, qualia]
source_url: https://github.com/Qualiasolutions/mpm
---

# mpm

> Employee discount platform — PWA for generating and redeeming discount codes at retail stores

## Overview

MPM Employee Discount Platform is a PWA where company employees receive and redeem discount codes at retail stores. Admins manage employees, divisions, discount rules, and validate codes at POS terminals. Employees view their discounts, generate time-limited QR/manual codes, and track spending. The root page acts as a router, redirecting to admin or dashboard based on the user's JWT role claim.

## Tech Stack

- Next.js 15 App Router, React 19, TypeScript
- Supabase (PostgreSQL with RLS, Auth with JWT role claims)
- Tailwind CSS
- PWA (Serwist service worker)
- Vercel (deployment)

## Status

- **Language:** TypeScript
- **Last updated:** 2026-03-25
- **Visibility:** Private

## Key Features

- Role-based route groups: admin panel and employee dashboard
- Time-limited discount code generation (QR + manual)
- POS validation terminal for admins
- Employee spending tracking and transaction history
- Division and discount rule management
- JWT-based middleware auth with `getUser()` validation
- PWA support via Serwist

## Related

- [[MPM client]]
