# Sakani App

**ERP name:** Sakani
**Client:** [[Sakani]] — Smart IT Buildings L.L.C. (Amman, Jordan)
**Type:** Mobile app + Next.js admin (Turborepo monorepo)
**Stack:** Expo (React Native) + Next.js 16 (admin) + Supabase
**Status:** ERP "Active", deployment Vercel
**SRS:** v1.14 (source of truth) — `docs/SRS V1.14.docx`
**Local:** `~/Projects/sakani`
**Repo:** `SakaniQualia/sakani` (private)

## What It Is

Jordan's residential building management platform. Bilingual Arabic/English with full RTL support. Identity verification, financial ledger, access control, and operations.

## Apps in Monorepo

- `@sakani/admin` — Next.js admin
- `@sakani/mobile` — Expo mobile
- `@sakani/demo` — demo app

## Financial

| Item | Amount |
|------|-------:|
| **Total contract** | **12,000 JOD** |
| Billed (EUR equivalent) | €14,520 across 4 invoices |
| **Paid** | €8,712 (2 of 4) |
| **Remaining** | €5,808 (SAKANI-003 due 2026-05-01, SAKANI-004 due 2026-07-01) |

## Agent staging areas

- [[README|Sakani E2E Onboarding Test — Agent Briefing]] — overnight autonomous test runs for onboarding flows (W1, W2, W2-A, W3, W4, W4-A, W5, W12). See `wiki/sakani-e2e-onboarding/` for full context set.
- Scope boundary: [[scope-boundary]] — onboarding only, NOT finance (parallel agent owns finance)

## Related

- [[Sakani]]
- [[Finances Overview]]
