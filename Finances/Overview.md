# Finances Overview

> Last refreshed from Qualia ERP (Zoho Books sync) on 2026-04-16.

## How we invoice (May 2026 onward)

Invoicing is owned by the Qualia ERP at `/admin?tab=finance` → **"New invoice from template"**. This is the canonical workflow — do not invoice directly from Zoho Books unless the ERP doesn't yet expose what you need (e.g. voiding, editing past invoices, recording payments).

**Four invoice templates** (in `lib/invoice-templates.ts` of the qualia-erp repo):

| Template | When to use | Pattern |
|---|---|---|
| `monthly_retainer` | Ongoing service clients (Underdog) | Multi-line: retainer + SEO + AI/API usage |
| `simple_service` | Single-line monthly service ([[Maison Maud]], [[Armenius]]) | One line, customizable description |
| `project_deposit` | 50% upfront on a new project (EventMaster pattern) | Requires proposal ref, e.g. `QS-2026-EVMSTR` |
| `project_balance` | Final 50% on go-live | Same proposal ref as the deposit |

**Two terms templates:** `generic` (default — used for everyone), `sakani_pda` (only [[Sakani]] — references the Platform Development Agreement).

**Client tax treatment** is stored on `clients.default_vat_treatment` and decides VAT automatically:
- `cyprus_vat` — 19% applies (Cyprus B2B)
- `non_eu_zero` — VAT zeroed (Maison Maud / Sakani — UAE / Jordan)
- `eu_reverse` — VAT zeroed (EU reverse-charge)

**MCP exposure:** the same workflow is callable from any Claude Code project via the qualia-erp MCP server:
- `mcp__qualia-erp__list_invoice_templates`
- `mcp__qualia-erp__list_billable_clients`
- `mcp__qualia-erp__list_invoices`
- `mcp__qualia-erp__create_invoice_draft`
- `mcp__qualia-erp__create_invoice_cover_email_draft`

**Handoff runbook:** `docs/finance-runbook.md` in the qualia-erp repo. Designed so a non-engineer can run monthly billing without touching Zoho directly.

**Defaults always create drafts** — nothing is sent to clients automatically. Review in Zoho Books, then click Send (which attaches the PDF).

## Monthly Recurring Revenue (April 2026)

| Client | Amount (EUR) | Notes |
|--------|-------------:|-------|
| [[Zyprus - Charalambous]] | 400 | 375 base + 25 3CX call routing |
| [[Armenius]] | 250 | Due 1st of each month |
| [[Giulio - Underdogsales\|Boss Brainz]] | 1,700 | Current rate (months 1–2). Bumps to 2,500/mo from May 2026 |
| **Total MRR (current)** | **€2,350** | |
| **Total MRR (from May 2026)** | **€3,150** | |

## 2026 Year-to-Date Collections (paid invoices)

| Customer | Total Paid | Invoices |
|----------|-----------:|----------|
| GSC UNDERDOG SALES LTD ([[Giulio - Underdogsales]]) | €19,651.75 | INV-1231, 1237, 1249, 1251, 1253 |
| Sakani (Smart IT Buildings L.L.C.) | €8,712.00 | SAKANI-001, 002 (2 of 4) |
| Mr. Marco Pellizzeri (Doctor Marco) | €2,758.42 | INV-1239 |
| ODC JOURNEYS LIMITED | €1,785.00 | INV-1246 |
| Sofian & Shehadeh ([[SSlaw]]) | €219.00 | INV-1245 |

## Outstanding (unpaid invoices)

### Overdue
| Customer | Amount | Date |
|----------|-------:|------|
| Mr. Issa Kandah | €1,641.64 | 2026-02-09 |
| [[Zyprus - Charalambous\|CSC Zyprus Property Group]] | €2,146.50 | 2025-08-11 |

### Drafts (to be sent / pending payment)
| Customer | Amount | Date |
|----------|-------:|------|
| Sakani — SAKANI-003 (due 2026-05-01) | €2,904.00 | 2026-04-03 |
| Sakani — SAKANI-004 (due 2026-07-01) | €2,904.00 | 2026-04-03 |
| PETA TRADING LTD | €3,150.72 | 2026-03-16 |
| ODC JOURNEYS LIMITED | €2,125.41 | 2026-02-28 |
| Craft Bakery Sweets | €1,295.00 | INV-001, INV-1232, INV-1247 |
| ODC JOURNEYS LIMITED | €143.99 | 2026-01-25 |

## Key Project-Based Engagements

### Sakani — Smart IT Buildings L.L.C.
- **Contract:** **12,000 JOD** (billed in EUR for accounting; ≈ €14,520 across 4 invoices)
- **Schedule:** 4 milestone invoices (30% / 30% / 20% / 20%)
- **Paid:** 2 of 4 (€8,712)
- **Remaining:** €5,808 across SAKANI-003 and SAKANI-004

### Boss Brainz ([[Giulio - Underdogsales]])
- 2 months collected at €1,700/mo
- From **May 2026** onwards → **€2,500/mo**
- Engagement is open-ended (5-month minimum was the original scope)

### Cold Calling Wiki ([[Giulio - Underdogsales]])
- €1,000 one-time — completed

### Zyprus / Charalambous
- 5,300 invoiced total (split across two brand names)
- 1,007 VAT, 2,000 add-on invoiced separately
- Plus 400/mo retainer
- Outstanding overdue: €2,146.50 (CSC Zyprus, since Aug 2025)

## Source

Qualia ERP `financial_invoices` and `financial_payments` tables (Zoho Books sync).
Refresh: query `https://vbpzaiqovffpsroxaulv.supabase.co/rest/v1/financial_invoices` with service role key.
