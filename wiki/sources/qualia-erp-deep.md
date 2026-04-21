---
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [qualia-erp, internal-tool, supabase, nextjs, voice-ai, portal]
---

# Qualia ERP — Deep Reference

## At a Glance

**Live**: [portal.qualiasolutions.net](https://portal.qualiasolutions.net)
**Supabase Project**: `vbpzaiqovffpsroxaulv`
**Stack**: Next.js 16.2, React 19, TypeScript 5, Supabase (PostgreSQL + pgvector), Vercel, Tailwind + shadcn/ui
**Codebase**: ~29k LOC (app + lib + components)
**Tables**: 76 (core, CRM, projects, AI, finances, work-tracking, portal)
**Domain**: Internal ERP for Qualia Solutions — clients, projects, finances, tasks, meetings, voice AI, branded client portal

## Top-Level Routes (App Router)

```
app/
├── (routes)/                    # Main portal routes (auth-protected)
│   ├── page.tsx                 # Dashboard
│   ├── tasks/                   # Unified inbox (scope=mine|all|client-visible)
│   ├── projects/[id]/           # Project roadmap, files, deployments
│   ├── clients/                 # CRM with leads, pipeline, contacts
│   ├── schedule/                # Team calendar (Cyprus/Jordan TZ)
│   ├── team/                    # Workforce, trainees, skills
│   ├── payments/                # Financial dashboard (Zoho-synced)
│   ├── knowledge/               # RAG knowledge base (pgvector)
│   ├── research/                # Research tasks + findings
│   ├── seo/                     # SEO tracking (admin-only)
│   ├── settings/                # Integrations, notifications
│   ├── admin/                   # Admin zone (reports, attendance, migrate)
│   └── agent/                   # AI chat assistant
├── (portal)/                    # Client portal routes
│   ├── page.tsx                 # Portal dashboard (workspace isolation)
│   ├── [id]/                    # Client-branded project hub
│   │   ├── features/            # Feature requests
│   │   ├── files/               # File sharing
│   │   └── updates/             # Activity feed
│   ├── billing/                 # Invoice/payment history
│   ├── requests/                # Client feature requests (aggregated)
│   └── settings/                # Client workspace settings
├── api/
│   ├── chat/route.ts            # AI chat endpoint (Gemini + OpenRouter)
│   ├── embeddings/route.ts      # pgvector document embedding
│   ├── tts/route.ts             # Text-to-speech via VAPI
│   ├── health/route.ts          # Status probe
│   ├── cron/                    # 8 scheduled jobs (Vercel Cron)
│   │   ├── zoho-sync/           # Daily financial sync at 04:00 UTC
│   │   ├── morning-email/       # Team briefing
│   │   ├── weekly-digest/       # Engagement summary
│   │   ├── attendance-report/   # Work session summary
│   │   ├── research-tasks/      # Auto-assign research work
│   │   ├── reminders/           # Task reminders
│   │   ├── uptime-check/        # Portal health
│   │   └── cleanup-dry-run-reports/  # Delete stale dry_run=true rows
│   ├── webhooks/
│   │   ├── vercel/              # GitHub PR deployment sync
│   │   └── github/              # Push events → `.planning/` sync
│   └── v1/reports/              # Qualia Framework session report ingest
└── auth/                        # Auth routes (login, signup, reset)
```

## Core Domains (from `/app/actions/*` and `/lib/*`)

**60 server action modules** organized by domain:

### Clients & CRM (10 modules)
- `clients.ts` — CRUD, lead pipeline, contact mgmt
- `client-invitations.ts` — Portal access provisioning
- `client-activities.ts` — Client interaction log
- `client-portal/` — 5 sub-modules (admin, billing, files, messaging, portal-config)

### Projects & Deployment (12 modules)
- `projects.ts` — Create, archive, settings
- `project-assignments.ts` — Engineer ↔ project binding + auto-assign from milestones
- `project-deployments.ts` — GitHub PR ↔ Vercel integration
- `project-links.ts` — External project URLs
- `deployments.ts` — Track project outputs
- `github.ts` — GitHub repo linking
- `vercel.ts` — Vercel deployment tracking

### Tasks & Workflow (8 modules)
- `tasks.ts` — CRUD, status, priority, attachments, bulk ops
- `inbox.ts` — Task assignment, scope (mine/all/client-visible)
- `task-skills.ts` — Skill practice logging
- `task-reflections.ts` — Session reflection capture
- `activities.ts` — Activity log (who did what, when)
- `daily-flow.ts` — Daily planning, checkins, standup
- `checkins.ts` — Daily team status
- `issues.ts` — Legacy issue tracking (deprecated v2, kept for data)

### Finances (5 modules)
- `financials.ts` — Invoice CRUD, expense tracking, Zoho sync driver
- `zoho.ts` — Token mgmt, Zoho API client
- `client-portal/invoices.ts` — Client billing portal reads

### AI & Voice (5 modules)
- `ai-conversations.ts` — Chat session lifecycle (create, fetch, delete)
- `ai-messages.ts` — Message CRUD + tool-call logging
- `ai-context.ts` — AI user memory + system context
- `knowledge.ts` — RAG: document ingestion, embedding, vector search
- `ai-memory.ts` — Multi-turn conversation memory

### Team & Workforce (6 modules)
- `team-dashboard.ts` — Team overview, attendance
- `team-members.ts` — Engineer profiles, skills, XP
- `trainees.ts` — Trainee progress, daily logs
- `owner-updates.ts` — Leadership notifications

### Data Sync & Integration (4 modules)
- `planning-sync.ts` — GitHub `.planning/` ↔ DB sync (roadmap ingestion)
- `phase-reviews.ts` — Roadmap phase + milestone tracking
- `research.ts` — Research task assignment
- `portal-workspaces.ts` — Portal workspace isolation

### Reporting & Analytics (7 modules)
- `reports.ts` — Qualia Framework session reports ingest/query (QS-REPORT-NN)
- `work-sessions.ts` — Work session tracking
- `health.ts` — Cron job health monitoring
- `session-reports.ts` — Session digest aggregation
- `seo.ts` — SEO keyword tracking
- `notifications.ts` — Notification rule engine
- `dashboard-notes.ts` — Dashboard widget state

## Integrations

### Zoho (Financials)
- **Direction**: Uni-directional read (Zoho → Qualia)
- **Flow**: Cron daily 04:00 UTC (`app/api/cron/zoho-sync/route.ts`) + manual "Sync Now" button
- **Tables Synced**: `financial_invoices`, `financial_payments`
- **Dedup**: Upsert by `zoho_id`; resolves `client_id` at sync time via `projects` table (customer→project→client mapping)
- **Client Reference**: `lib/integrations/zoho.ts` — token mgmt, paginated fetchers (`getZohoAllInvoices`, `getZohoPayments`)

### VAPI (Voice AI)
- **Type**: Voice assistant via webhooks
- **Integration Points**: `app/api/tts/route.ts` (webhook consumer) + `lib/voice-assistant-intelligence.ts` (conversation logic)
- **Storage**: `ai_conversations`, `ai_messages` tables + text-to-speech response rendering
- **Public Key**: `NEXT_PUBLIC_VAPI_PUBLIC_KEY` (env)

### GitHub (Deployments & Planning)
- **Webhook**: `app/api/github/webhook/route.ts` — push events trigger `.planning/` sync
- **Sync Engine**: `lib/planning-sync-core.ts` — parses `.planning/` YAML, ingests phases → `project_phases` + `phase_items`
- **Auto-assign Hook**: `app/actions/auto-assign.ts` — converts phase items into inbox tasks for active milestone
- **Ref**: `lib/integrations/github.ts` — Octokit client for repo queries

### Vercel (Deployments)
- **Integration**: `lib/integrations/vercel.ts` — track deployments, promote staging → prod
- **Admin Tools**: Vercel read/write tools in AI chat (list projects, check deployment status, redeploy)
- **Scope**: User: `qualiasolutionscy` (Qualia Solutions — Development)

### Supabase (Database & Auth)
- **Auth**: JWT via custom claims hook; `auth.uid()` derived in RLS policies
- **RLS**: Enabled on all 76 tables; three roles: `admin`, `employee`, `client`
- **Storage**: Bucket `project-files` for logos, project files (uploaded via `app/actions/logos.ts`)
- **Realtime**: Used for notifications + portal messaging (subscribed in SWR hooks)

### OpenRouter & Google Gemini (AI)
- **AI SDK**: `@ai-sdk/google` + `@ai-sdk/openai` (via OpenRouter)
- **Endpoint**: `app/api/chat/route.ts` — streams tool-augmented chat responses
- **Tools**: 40+ domain tools (read/write, GitHub, Vercel, Supabase ops)
- **Role-based**: Admin tools filtered for non-admin users (see `lib/ai/tools/index.ts` ADMIN_ONLY_TOOLS)

## Key Tables (76 total)

**Categorized by domain:**

### Core
`tasks`, `projects`, `project_phases`, `phase_items`, `clients`, `meetings`, `profiles`, `teams`, `workspaces`, `issues` (legacy)

### Project Management
`project_assignments`, `project_integrations`, `project_deployments`, `project_environments`, `project_files`, `project_notes`, `task_attachments`, `task_time_logs`, `phase_comments`, `phase_resources`, `phase_reviews`, `phase_templates`

### Client Portal
`client_projects`, `client_contacts`, `client_activities`, `client_feature_requests`, `client_invoices` (mapped from Zoho)

### AI & Knowledge
`ai_conversations`, `ai_messages`, `ai_reminders`, `ai_user_context`, `ai_user_memory`, `documents` (pgvector embeddings), `knowledge` (legacy? or sub-table), `skill_practice_log`

### Finances
`financial_invoices` (Zoho-synced), `financial_payments` (Zoho-synced), `expenses` (manual)

### Work Tracking
`work_sessions`, `daily_checkins`, `claude_sessions`, `trainee_daily_logs`, `trainee_progress`, `user_achievements`, `user_skills`

### Team & Workforce
`team_members`, `skills`, `skill_categories`, `teaching_notes`, `activity_log`, `activities`

### Admin & Orchestration
`session_reports` (Qualia Framework ingest), `notifications`, `reminders`, `research_entries`, `research_findings`, `blog_posts`, `messages`, `comments`

**View**: `calculate_task_progress`, `calculate_project_progress` + 15 helper functions (RLS policies, role checks)

## Recent Milestones

### Phase 10 Shipped (April 19, 2026)
**Commit**: `5b84ff0` — "report(2026-04-19): phase-10 shipped — god-module split + cache components"

**Accomplishments**:
- **Cache Components**: Enabled `cacheComponents: true` in `next.config.ts`; added `'use cache'` directives to high-traffic read paths (projects, tasks)
- **God-module Split**: `client-portal.ts` (530 LOC) → 6 domain sub-modules (`admin.ts`, `billing.ts`, `files.ts`, `messaging.ts`, `invoices.ts`, `index.ts`)
- **Upstash Redis**: Swapped in-memory rate limiter for Upstash Redis sliding-window (`lib/rate-limit.ts` via `@upstash/redis` + `@upstash/ratelimit`)
- **Security**: Decrypt workspace tokens at every consumer (rotation support); legacy `TOKEN_ENCRYPTION_KEY_OLD` fallback removed

**Recent commits** (last 30, Apr 2026):
- `e8f425e` fix(cache): remove redundant `dynamic=force-dynamic` (incompatible with cacheComponents)
- `865d94c` perf: enable Cache Components + cached read paths for projects
- `994981e` refactor: split god-module into 6 domain sub-modules
- `d9b3bbf` test: add characterization tests (10 client-portal.ts exports)
- `e426e6b` feat(portal): client-branded welcome/sidebar + Files panel
- `2535c21` fix(portal): backfill invoice `client_id` + attachments to feature requests
- `94ca120` fix(sync): delete stale GitHub-sourced phases after roadmap sync
- `2eaaad5` refactor: remove dead `/inbox` route, add bulk action re-exports
- `789b801` feat(rate-limit): swap in-memory for Upstash Redis sliding-window
- `da3fd42` report(2026-04-19): late session — hotfix, rename, roadmap UX, backfill

### Phase 9 (Tasks Consolidation)
- Unified `/tasks` route (inbox default, `?scope=all` for admin workspace view, `scope=client-visible` for clients)
- Bulk actions: assign, mark done, delete (admin-only)

### Phase 6 (Financial Consolidation)
- Zoho sync pipeline: paginated fetchers, daily cron, "Sync Now" button
- Consolidated tables: `financial_invoices`, `financial_payments` (dropped legacy `payments`, `recurring_payments`, `client_invoices`)
- Client invoicing: RLS-gated portal view with Zoho data

### Framework v4.0.4 Sync
- Session report contract: `docs/framework-contract.md` (vendored from `qualia-framework/docs/erp-contract.md`)
- Ingest endpoint: `app/api/v1/reports/route.ts` (dual auth: qlt_* bearer or legacy CLAUDE_API_KEY)
- Dedup key: `(framework_project_id, client_report_id)` via UPSERT unique index
- Dry-run cleanup: `app/api/cron/cleanup-dry-run-reports/route.ts` (daily 03:00 UTC)

## Key Mechanics

### RLS (Row-Level Security)
- **Approach**: Every table has policies; `auth.uid()` is the auth source; role derived via `user_role` enum in `profiles`
- **Three Roles**: `admin` (all data), `employee` (workspace + assigned projects), `client` (client-visible only)
- **FK Join Pattern**: Supabase returns FK joins as arrays; always normalize with `normalizeFKResponse()` from `lib/server-utils.ts`
- **Workspace Isolation**: Portal clients see only their `client_projects`; achieved via `client_projects` join path in RLS

**Policy Examples**:
- Admin: full read/write on all tables
- Employee: read own workspace tasks; write only assigned tasks or created items
- Client: read only `is_client_visible=true` tasks; read own invoices via `client_projects` FK

### Voice AI Wiring
1. **VAPI Webhook** (`app/api/tts/route.ts`) — receives voice transcript + metadata
2. **Insert to DB**: Create `ai_conversations` row, append `ai_messages` (role=user)
3. **Intent Detection** → Response Generation (lib/voice-assistant-intelligence.ts logic):
   - Check context (current task, project, time of day)
   - Generate contextual greeting or action (e.g., "Mark this as done?")
   - Append AI message (role=assistant) with TTS response
4. **Webhook Response**: Return `speakableText` + `messages` array to VAPI client
5. **Persistence**: All messages persisted for audit + memory (10-turn context window)

### Auth Flow
1. **Signup**: `/auth/signup` → Supabase email confirmation
2. **Login**: `/auth/login` → JWT custom claims hook sets `user_role` + `workspace_id` in claims
3. **Middleware** (`middleware.ts`): `auth.getClaims()` on every request; redirect unauth to `/auth/login`
4. **API Routes**: NOT middleware-protected; each route must check `CRON_SECRET` or call `auth.getClaims()` separately
5. **Session**: Stored in Supabase JWT; refreshed on tab focus via `supabase.auth.refreshSession()`

### SWR Cache & Invalidation
- **37 hooks** in `lib/swr.ts` (e.g., `useInboxTasks`, `useProjects`, `useTasks`, `useClients`)
- **33 invalidation fns** (e.g., `invalidateTasks`, `invalidateProjects`)
- **Refresh cadence**: 45s when tab visible; stops when hidden
- **Pattern**: Server action returns `ActionResult`; client calls action + calls invalidation fn with `immediate: true` to update UI instantly

### Server Actions Pattern
All mutations in `app/actions/*.ts`:
```typescript
type ActionResult = { success: boolean; error?: string; data?: unknown };
export async function createTask(input: CreateTaskInput): Promise<ActionResult> {
  const supabase = await createClient(); // server-only, never in client code
  const { data: { user } } = await supabase.auth.getUser();
  // Validate input, check RLS perms, mutate DB
  return { success: true, data: newTask };
}
```

## Tech Debt / Known Issues

### From CLAUDE.md
| Priority | Issue                       | Status      |
|----------|----------------------------|------------|
| P1       | Test coverage (1.68% → 50%)| Pending    |
| P2       | God-module splits          | Shipped P10|
| P3       | Rate-limiting (Redis)      | Shipped P10|

### From Latest Commits
- `dynamic=force-dynamic` incompatibility with Cache Components (fixed in e8f425e)
- Dry-run report cleanup via cron (now automated)
- Workspace token rotation (encryption key rollover support)

### Known Unknowns
- **Phase 11+**: Not yet documented in `.planning/`; likely: mobile app, API v2, performance optimization
- **Vercel Workflow**: Listed in package.json devDeps but not integrated yet (WDK integration pending?)
- **Offline mode**: Not mentioned; SWR falls back to stale cache when offline

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                     QUALIA ERP (portal.qualiasolutions.net) │
├─────────────────────────────────────────────────────────────┤
│ Frontend: React 19 + SWR (45s refresh) + shadcn/ui         │
├─────────────────────────────────────────────────────────────┤
│ Routes: Next.js 16 App Router                               │
│  ├─ (routes)/*          → Internal ERP (auth-protected)    │
│  ├─ (portal)/*          → Client workspace (RLS-gated)     │
│  ├─ api/*               → Webhooks, crons, AI chat         │
│  └─ auth/*              → Supabase Auth                    │
├─────────────────────────────────────────────────────────────┤
│ Server Actions: 60 modules in app/actions/ (domain-driven)  │
├─────────────────────────────────────────────────────────────┤
│ Database: Supabase (PostgreSQL)                             │
│  ├─ 76 tables (RLS on all)                                 │
│  ├─ pgvector for RAG (documents, embeddings)               │
│  ├─ Realtime subscriptions (notifications, messaging)      │
│  └─ Auth: JWT custom claims hook (user_role, workspace_id) │
├─────────────────────────────────────────────────────────────┤
│ External Services:                                           │
│  ├─ Zoho Books (financials) → cron sync daily 04:00 UTC    │
│  ├─ VAPI (voice AI) → webhook→db→response flow             │
│  ├─ GitHub (deployments) → push events sync .planning/     │
│  ├─ Vercel (hosting) → deployment tracking + env mgmt      │
│  ├─ Google Gemini + OpenRouter (AI chat) → tool-augmented  │
│  ├─ Upstash Redis (rate-limiting) → sliding-window         │
│  ├─ Resend (email) → morning briefing, reminders           │
│  └─ Sentry (monitoring) → error tracking                   │
├─────────────────────────────────────────────────────────────┤
│ Optimizations (Phase 10):                                   │
│  ├─ Cache Components ('use cache' directive)               │
│  ├─ Upstash Redis rate-limiting                            │
│  ├─ God-module splits (6x smaller action modules)          │
│  └─ TypeScript strict mode                                 │
└─────────────────────────────────────────────────────────────┘
```

## Deployment & Ops

**Vercel Team**: `qualiasolutionscy` (Qualia Solutions — Development)
**Production URL**: https://portal.qualiasolutions.net
**Deploy Command**: `vercel --prod --yes` (default scope)
**Pre-commit**: ESLint + Prettier via husky/lint-staged
**Post-deploy Checklist**: HTTP 200, auth flow, console errors, API latency <500ms

**Crons** (vercel.json registered):
- `zoho-sync`: Daily 04:00 UTC
- `morning-email`: Daily 06:00 UTC
- `weekly-digest`: Weekly Monday 08:00 UTC
- `attendance-report`: Daily 18:00 UTC
- `research-tasks`: Daily 07:00 UTC
- `reminders`: Every 2h
- `uptime-check`: Every 5m
- `cleanup-dry-run-reports`: Daily 03:00 UTC

## Related Documentation

- [[sources/qualia-framework-deep]] — Framework v4.0.4, session reports, .planning/ contract
- [[Qualia ERP]] — Main project page
- [[Qualia Framework v2]] — AI agents, tool calling, memory
- `docs/framework-contract.md` — Vendored ERP contract spec
- `.planning/PROJECT.md` — Portal v2 goals (Assembly-inspired redesign)
- `.planning/DESIGN.md` — Design system spec
- `CLAUDE.md` — This project's working reference (routes, db, conventions)

---

**Codebase LOC**: ~29k (app + lib + components)
**Tables**: 76 (core + portal + AI + finances + work-tracking)
**Actions**: 60 domain modules
**Integrations**: 6 (Zoho, VAPI, GitHub, Vercel, Supabase, OpenRouter/Gemini)
**Key Achievement**: Phase 10 shipped with Cache Components + god-module refactor + Redis rate-limiting
