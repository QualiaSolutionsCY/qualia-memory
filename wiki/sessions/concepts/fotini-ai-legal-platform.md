---
title: "Fotini AI Legal Case Management Platform"
aliases: [fotini-ai, fotini-platform, legal-case-management, fotini-kandri]
tags: [client-project, legal, invoicing, supabase, ai-assistant]
sources:
  - "daily/2026-04-28.md"
created: 2026-04-28
updated: 2026-04-28
---

# Fotini AI Legal Case Management Platform

An AI-powered legal case management platform deployed at fotini-ai.vercel.app for Cypriot lawyer Φωτεινή Κάνδρη (Fotini Kandri), based in Limassol. The platform was seeded with ~40 real cases across 6 legal categories, includes a full invoicing module with PDF generation and Resend email integration, and features an AI assistant (Knowledge) powered by Claude Sonnet 4.6 via OpenRouter.

## Key Points

- **6 legal categories:** Family Law (divorce, parental care, communication orders, alimony, property), Employment Law (unlawful dismissal, employment disputes), Civil Law (debt recovery), Immigration Law (detention annulment, asylum/AIDA, ECHR applications, action against Republic), Criminal Law (restraining order violation), Inheritance Law (estate admin, acceptance/renunciation)
- **Entity deduplication is critical:** Aleka Anastasiou appears in 5 cases, Sofia Artemi in 4, Marilena Foufouni in 3, Ammar Mohammed Ammar in 3 — clients must be deduplicated as entities, not created per-case
- **Invoicing module:** List/detail/create pages + 6 agent tools (list, get, summary, create, record payment, update status) + Postgres triggers for auto-reconciliation
- **PDF + email:** Server-side A4 court-style PDF generation + Resend email delivery with To/Cc/Subject/Message dialog; audit trail via `invoice_send_log` table
- **AI model swap:** Knowledge assistant switched from `gemini-2.5-flash-preview` (returning empty streams) to `anthropic/claude-sonnet-4.6` via OpenRouter

## Details

The platform was built to manage Fotini's full case portfolio. The real data population revealed several data modeling requirements that wouldn't be obvious from a generic CRM spec. First, the same client frequently appears across multiple cases — Aleka Anastasiou has concurrent cases in divorce, parental care, alimony, property, and criminal law. This means the data model must separate "client" entities from "case" entities with a many-to-many relationship, rather than the simpler one-client-per-case assumption. Entity deduplication during seeding was critical to prevent creating duplicate client records.

Second, case numbers follow Cypriot court conventions (e.g., "175/2016", "4261/2023", "6135/2024") but some cases don't yet have assigned numbers (`Δεν καθορίστηκε` / "Not determined"). The schema must handle null case numbers gracefully. Similarly, some cases have no next court date, and one case (divorce action 175/2016) has "files only, no client" — edge cases that require flexible nullable fields.

Third, fees follow a pattern: most cases are "€1000 + ΦΠΑ" (VAT), but employment cases range from €1000 to €3500, and immigration cases are typically €800. The grouped asylum application (5 applicants, cases 195-199/2025) is billed as a single €4000 + VAT engagement. The invoicing system's VAT auto-computation handles the ΦΠΑ pattern automatically.

The invoicing module ships server-side PDF generation producing A4 court-style documents (header, bill-to, line items, totals, notes, footer) and Resend email integration. The send feature supports both UI interaction (button with To/Cc/Subject/Message dialog) and AI agent invocation via a `send_invoice` tool. All sends are logged to `invoice_send_log` with recipients, Resend message ID, and success/failure status for audit trail.

The AI model swap was necessitated by `gemini-2.5-flash-preview` returning empty streams through OpenRouter. Switching to `anthropic/claude-sonnet-4.6` resolved the issue immediately. This is consistent with the general practice of using OpenRouter as the LLM gateway (per Qualia infrastructure standards) while being prepared to swap models when a specific provider has issues.

A questionnaire page was also built at qualiasolutions.net/fotini (access code: 391560) to collect Fotini's AI assistant preferences — channels (WhatsApp, Telegram), autonomous task scope, and budget options. This follows the Qualia pre-proposal pattern: collect structured requirements before drafting the proposal.

## Related Concepts

- [[concepts/qualia-os-unification]] — Fotini AI is a client project deployed through the Qualia project ecosystem
- [[connections/data-identity-mismatches]] — The entity deduplication requirement is the same class of issue: if clients are duplicated, downstream queries will show incomplete case histories
- [[concepts/supabase-client-access-patterns]] — The platform's access control for Fotini's login uses similar Supabase auth patterns

## Sources

- [[daily/2026-04-28.md]] — Session at 13:56: "User provided Fotini's complete real case portfolio in Greek — ~40+ cases across 6 legal categories with real client names, case numbers, fees, statuses, and court dates"
- [[daily/2026-04-28.md]] — Session at 13:56: "Deployed full invoicing module: list/detail/create pages, 6 agent tools, Postgres triggers for auto-reconciliation"
- [[daily/2026-04-28.md]] — Session at 13:56: "Built send-invoice feature: server-side PDF generation + Resend email integration"
- [[daily/2026-04-28.md]] — Session at 10:49: "Switched Knowledge assistant from `gemini-2.5-flash-preview` to `anthropic/claude-sonnet-4.6` via OpenRouter — Gemini was returning empty streams"
- [[daily/2026-04-28.md]] — Session at 15:22: "Questionnaire at qualiasolutions.net/fotini with access code 391560 for AI assistant proposal requirements"
