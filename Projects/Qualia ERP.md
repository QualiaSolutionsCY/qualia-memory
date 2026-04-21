# Qualia ERP

**Type:** Internal tool — operational source of truth
**Client:** [[Index|Qualia Solutions]] (internal)
**Stack:** Next.js 16+, React 19, TypeScript, Supabase, Vercel
**Live:** portal.qualiasolutions.net
**Repo:** `Qualiasolutions/qualia-erp` (public, last push 2026-04-16)
**Local:** `~/Projects/qualia/qualia-erp`
**Supabase project ref:** `vbpzaiqovffpsroxaulv`

## Features

- Task management (with realtime via Supabase realtime)
- Client portal + admin config + branding
- Calendar / meetings (with attendees, mentorship)
- Financial page (Zoho Books integration via `financial_invoices`, `financial_payments` tables)
- Employee dashboard
- Clock in / out with planned logout times
- Reporting system + session reports
- Onboarding flows (internal_onboarding_version tracking)
- Project phases, project health system, knowledge base search
- Voice agent project type, multiple deployment platforms

## Tables (key)

- `clients` — 46 records (lead_status: cold/hot/active_client/inactive_client/dead_lead)
- `projects` — 38 records (status: Active/Done/Demos/Archived/Launched + project_group active/demos)
- `profiles` — 28 records (5 team + 23 client portal accounts)
- `financial_invoices` / `financial_payments` — synced from Zoho Books
- `tasks`, `inbox_tasks`, `task_attachments`, `portal_messaging`
- `meetings`, `meeting_attendees`
- `mentorship_skills`, `learning`
- `workspace_notes`, `phase_resources`, `project_notes`

## Migrations

90 migrations in `supabase/migrations/`. Latest: `20260416000000_create_session_reports.sql`

## Team

- [[Fawzi Goussous]] — Lead, admin role
- All 5 team members have profiles (Fawzi, Moayad, Hasan, Sally, Rama)

## Related

- [[Qualia Framework v2]] — reads tracking.json from git
- [[Qualia Dashboard]] — desktop widget showing live ERP status
