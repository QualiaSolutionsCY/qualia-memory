---
title: "Qualiafinal Public Booking Sync"
aliases: [public-booking-mirror, booking-trigger-fix, mirror-trigger-silent-data-loss]
tags: [qualiafinal, supabase, triggers, database, debugging]
sources:
  - "daily/2026-04-26.md"
created: 2026-04-26
updated: 2026-04-26
---

# Qualiafinal Public Booking Sync

Public bookings submitted via qualiasolutions.net/contact were not appearing in the Qualia ERP schedule (portal.qualiasolutions.net). The root cause was a Postgres trigger (`mirror_public_booking_to_meeting()`) that inserted mirrored rows into the `meetings` table without setting `workspace_id`, while the consuming query (`getMeetings()` in `app/actions/meetings.ts:278`) filtered by `workspace_id`. This created a silent data loss pattern: rows existed in the database but were invisible to all application queries.

## Key Points

- The `mirror_public_booking_to_meeting()` trigger copied `public_bookings` rows into `meetings` but omitted the `workspace_id` column — a required FK for the consuming query
- `getMeetings()` filters by `workspace_id`, so mirrored rows without that field were invisible to the ERP schedule view despite existing in the database
- Fix was at the data layer (Postgres trigger), not the application layer — the trigger was patched to include the single existing Qualia workspace ID (`bf96d0a9-...`) as default
- 4 orphaned rows in production were backfilled via a migration
- The booking flow path is: qualiasolutions.net/contact -> `public_bookings` table -> trigger mirrors to `meetings` table -> ERP schedule reads `meetings`

## Details

This bug exemplifies a class of silent data loss that occurs when mirror/sync triggers are written without checking what the consuming query filters on. The trigger author correctly duplicated the booking data (title, time, contact info) but missed that the downstream consumer required `workspace_id` to be non-null. Because Postgres does not enforce application-level invariants like "every row must be visible to at least one query," the mirrored rows silently accumulated in the `meetings` table with no `workspace_id`, passing all database constraints but failing the application's visibility filter.

The fix was deliberately applied at the Postgres trigger level rather than relaxing the application query's `workspace_id` filter. Relaxing the filter would have exposed unscoped meetings to all workspaces, which is a worse failure mode. Instead, the trigger was patched to hardcode the single Qualia workspace ID as the default for all public booking mirrors. This is acceptable because qualiasolutions.net/contact serves only the one Qualia workspace; if multi-workspace public bookings are needed in the future, the trigger should look up the correct workspace based on booking metadata.

The 4 orphaned rows were backfilled directly in production via a SQL migration that set `workspace_id` on all existing `meetings` rows where it was null and the source was a public booking mirror. This immediate backfill was necessary because the bookings represented real client appointments that were being missed.

The general rule for any mirror/sync trigger: before writing the INSERT, read the consuming query and verify that every column in its WHERE clause is populated by the trigger. Missing a single filter column creates invisible rows — functionally equivalent to data loss from the user's perspective.

## Related Concepts

- [[concepts/qualia-erp-phase-25]] — Same ERP portal; the Phase 25 work also required verifying live Supabase state before deploying RPC-dependent code
- [[concepts/claude-code-operational-gotchas]] — Framework state drift is a similar class of "data exists but is invisible to the system" problem
- [[concepts/npm-vulnerability-triage]] — Both share the deployment verification discipline: check live state before considering work complete

## Sources

- [[daily/2026-04-26.md]] — Session at 20:07: "Patched the Postgres trigger rather than the application query — root cause was at the data layer, not the UI filter"
- [[daily/2026-04-26.md]] — Session at 20:07: "`mirror_public_booking_to_meeting()` trigger was inserting into `meetings` without `workspace_id`, while `getMeetings()` filters by `workspace_id` — silent data loss pattern"
- [[daily/2026-04-26.md]] — Session at 20:07: "Used the single existing Qualia workspace ID (`bf96d0a9-...`) as the default for mirrored public bookings"
- [[daily/2026-04-26.md]] — Session at 20:07: "Backfilled 4 orphan rows in production directly via migration"
