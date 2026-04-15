---
type: source
created: 2026-04-16
updated: 2026-04-16
tags: [sakani, e2e, environment, dev-servers]
---

# Environment — Dev Servers, URLs, Services

> What needs to be running before you start any test. Check each item before taking the first action, and re-check if any test fails unexpectedly.

## Working directory

Always run from `/home/qualia/Projects/sakani` (monorepo root). Never `cd` into subdirectories — pnpm workspaces need root context.

## Required services (check in order)

### 1. Supabase (remote, production instance)

- **Project ref:** `vgclogtusvsrvyxaxaqm` (eu-west-1)
- **Always-on** — nothing to start locally
- Access via Supabase MCP tool (preferred) or `npx supabase` CLI from repo root
- Required for: auth (OTP), DB (profiles, buildings, units, documents, audit_log), storage (Goshan uploads), edge functions (`parse-goshan`, `parse-national-id`)
- Sanity check: `mcp__supabase__list_tables` should return ~30 tables including `profiles`, `buildings`, `units`, `unit_claim_request`, `building_setup_request`, `audit_log`

### 2. NestJS backend (local)

- **Command:** `pnpm dev:server` (from repo root)
- **Port:** 3333 (default — verify in `server/src/main.ts` if `PORT` env differs)
- **URL:** `http://localhost:3333`
- **Health check:** `GET http://localhost:3333/health` (if route exists) or just `curl -i` the root
- Required for: setup-request endpoints, claim-request endpoints, admin verification actions, document upload proxying
- **Startup time:** ~10-20 seconds — wait for "Application is running" log before issuing requests

### 3. Admin Next.js console (local)

- **Command:** `pnpm dev:admin`
- **Port:** 3000 (Next.js default)
- **URL:** `http://localhost:3000`
- Required for: staff verification queues, review dialogs, approve/reject/return actions
- **Startup time:** 5-15 seconds for first compile, then ~1 second per route (on-demand compilation)
- Sign-in to admin requires a staff account; use the staff test credentials in [[test-credentials]]

### 4. Mobile app (Expo — optional depending on scope)

- **Command:** `pnpm dev:mobile`
- **Port:** 8081 (Metro bundler)
- **Access:** Expo Go on physical device (scan QR), or Android Studio / Xcode simulators
- **IMPORTANT:** Playwright cannot drive the mobile app directly. For mobile flows, use one of:
  - Expo web at `http://localhost:8081` (limited — no native file picker, no native notifications, RTL broken per M8 notes)
  - Detox (not currently wired up — check `apps/mobile/e2e/` for fixtures; if missing, this blocks most mobile flows)
  - Physical device / simulator screenshot comparison (slowest, most reliable)
- **Recommendation for overnight run:** Focus on what Playwright CAN drive (admin console, Expo web) and BLOCK the rest with clear notes. See [[reporting-template]] for how to mark BLOCKED.

## Environment variables

The local dev servers pull env from `.env.local` files per workspace. If any server fails to start with auth or Supabase errors, run from repo root:

```bash
vercel env pull .env.local --environment=preview  # if Vercel is linked
```

Do NOT create or edit `.env` files. If env is missing, mark the run as BLOCKED and report.

## Supabase edge functions (required for onboarding)

These are deployed externally, not in the local repo. If any returns 404 or 500, note the failure and continue — some tests will be BLOCKED but others will still run.

- `POST {SUPABASE_URL}/functions/v1/parse-goshan` — Goshan OCR + structured extract
- `POST {SUPABASE_URL}/functions/v1/parse-national-id` — National ID OCR

Both require `Authorization: Bearer <user-jwt>` + `apikey: <anon-key>` headers. The mobile client passes these automatically via `apps/mobile/lib/api/documents.ts`.

## Check-in routine before starting the run

```bash
# from /home/qualia/Projects/sakani
git status -sb                    # should be clean or only have your run-log edits
git log --oneline -3              # confirm you're on expected commit
pnpm dev:server &                 # start backend
pnpm dev:admin &                  # start admin
sleep 20                          # let them boot
curl -s -o /dev/null -w "%{http_code}\n" http://localhost:3333  # expect 200 or 404 (route not found but server up)
curl -s -o /dev/null -w "%{http_code}\n" http://localhost:3000  # expect 200
```

If either curl fails, mark the run BLOCKED and abort.

## Related

- [[README]] — mission briefing
- [[test-credentials]] — test phone numbers + OTP codes
- [[scope-boundary]] — files and modules not to touch
