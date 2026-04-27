---
title: "Connection: Data Identity Mismatches Across Qualia"
connects:
  - "concepts/erp-clock-out-name-mismatch"
  - "concepts/qualiafinal-public-booking-sync"
  - "concepts/supabase-client-access-patterns"
sources:
  - "daily/2026-04-27.md"
  - "daily/2026-04-26.md"
created: 2026-04-27
updated: 2026-04-27
---

# Connection: Data Identity Mismatches Across Qualia

## The Connection

Three bugs discovered across the Qualia ecosystem on 2026-04-26 and 2026-04-27 share the same root pattern: data exists in the database but is functionally invisible to consuming queries because an identifier field is missing, mismatched, or not yet created. The pattern manifests differently in each case — a missing `workspace_id` FK, an em-dash project name variant, and a missing `client_projects` row — but the diagnostic signature is identical: "the data is there, the user can't see it."

## Key Insight

The non-obvious relationship is that all three bugs would have been caught by the same verification technique: **before writing any INSERT (trigger, API route, or manual row), read the consuming query's WHERE clause and verify that every column in the filter is populated by the write path.** This "write-path-must-satisfy-read-path" principle is the unified fix:

- **Booking sync trigger** wrote to `meetings` without `workspace_id` → `getMeetings()` filters by `workspace_id` → invisible rows
- **Clock-out report** was created with a different `project_name` variant → `work-sessions.ts:772` matches on `ilike` project_name → report not found
- **Client access guard** checked `client_projects` table → new client had zero rows → `assertAppEnabledForClient` returned false for everything

The first two are data-layer bugs (wrong data written). The third is a logic-layer bug (correct check, no fallback for empty state). But in all three cases, the symptom presented identically to the user: "I did the thing, but the system doesn't recognize it."

## Evidence

- Booking sync: `mirror_public_booking_to_meeting()` trigger omitted `workspace_id`, while `getMeetings()` at `app/actions/meetings.ts:278` filters by it — [[concepts/qualiafinal-public-booking-sync]]
- Clock-out: report posted as `"Qualia Solutions — qualiasolutions.net Rebuild"`, session stored as `"Qualia Solutions"`, `ilike` at `app/actions/work-sessions.ts:772` fails — [[concepts/erp-clock-out-name-mismatch]]
- Client access: `assertAppEnabledForClient` at `lib/portal-utils.ts:189` returns false when `client_projects` has zero rows — [[concepts/supabase-client-access-patterns]]
- All three required manual intervention to unblock the affected user (backfill rows, force-close session, add allowlist)

## Related Concepts

- [[concepts/erp-clock-out-name-mismatch]]
- [[concepts/qualiafinal-public-booking-sync]]
- [[concepts/supabase-client-access-patterns]]
- [[concepts/qualia-portal-mcp-server]] — workspace scoping was also the critical security fix in the MCP audit
- [[connections/silent-failure-swallowing]] — Expands this pattern to include routing-logic swallowing (truthy-object trap) alongside data-layer mismatches
