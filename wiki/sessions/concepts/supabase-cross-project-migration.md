---
title: "Supabase Cross-Project Auth Migration"
aliases: [auth-migration, cross-project-migration, user-migration-supabase, sales-coach-migration]
tags: [supabase, migration, auth, database, gotchas]
sources:
  - "daily/2026-04-28.md"
created: 2026-04-28
updated: 2026-04-28
---

# Supabase Cross-Project Auth Migration

A full user migration from AI-Sales-Coach (Supabase `vvppyijxpgeeijiozlve`) to Underdog Academy (Supabase `ywdrvvnganseomouplyb`) on 2026-04-28 revealed critical gotchas in Supabase's auth migration tooling. The most severe: `auth.admin.listUsers()` does NOT return `encrypted_password`, so any migration script using the Admin API silently fails to preserve bcrypt hashes — users cannot log in with their existing passwords post-migration. The correct approach is direct SQL extraction from `auth.users`.

## Key Points

- **`auth.admin.listUsers()` omits `encrypted_password`:** The Supabase Admin API strips password hashes from the response — must use direct SQL dump from `auth.users` to preserve bcrypt hashes for password-free migration
- **UUID preservation is mandatory:** The existing migration script (`migrate-sales-coach-users.ts`) didn't preserve original UUIDs, which would break all foreign key references (roleplays, profiles, streaks) — migrated users must keep their original auth.uid()
- **Collision-merge must never downgrade tiers:** When 4 email collisions were found (Giulio, Fawzi, Ben, Hudson), Phase 1b initially overwrote destination tiers blindly. Fix: always compare and keep the higher tier (`Math.max(currentTier, donorTier)`)
- **`auth.users.created_at` is NOT preserved on import:** Original signup dates only survive if explicitly copied to a `public.profiles` column — Supabase auth import overwrites `created_at` with the import timestamp
- **MCP query size limits are real:** Large auth table dumps via MCP tools fail; use file-based extraction or chunked queries instead

## Details

The migration covered 329 active users (all users, not just the 171 who had signed in) across three phases: auth import, profile migration, and roleplay history transfer. The user explicitly requested migrating all accounts rather than only active ones.

The most dangerous bug was in the pre-existing migration script `scripts/migrate-sales-coach-users.ts`. It used `auth.admin.listUsers()` to fetch source users, but the Supabase Admin API intentionally omits `encrypted_password` from its response (a security measure to prevent accidental password exposure via API). This meant the script appeared to work — users were created in the destination — but their passwords were silently lost, making post-migration login impossible without a password reset flow. The fix was to extract user data directly from the `auth.users` table via SQL, which returns the full row including the bcrypt hash.

The UUID preservation issue compounded the password problem. The original script generated new UUIDs for migrated users instead of preserving their source UUIDs. Since all downstream tables (roleplays, profiles, streaks, audit logs) reference users by their auth UID, generating new UUIDs would orphan all historical data. The rewritten migration explicitly sets the `id` field during auth user creation to match the source UUID.

Tier mapping followed a specific scheme: source `pro` tier mapped to destination `roleplay`, while `free` and `normal` mapped to `none`. The collision-merge logic initially had a bug where it blindly overwrote the destination user's tier with the mapped source tier. This was incorrect when the destination user already had a higher tier (e.g., `course` > `roleplay`). The fix ensures the higher of the two tiers is always kept.

A `vapi_call_id` unique constraint violation was caught during roleplay insertion — the source database had one duplicate pair that required deduplication before the batch insert. Historical roleplays were stamped with `counts_for_streak=false` so they don't retroactively grant streak credit in the destination system. Additionally, `app_metadata.access_tier` was stamped on all 342 auth users so the middleware's tier-gating works without requiring a database hit on every request.

Audit log (9,471 rows) and stripe_events (393 rows) were intentionally skipped — the Stripe API is the authoritative source of truth for payment data, making the local event cache redundant for migration purposes.

## Related Concepts

- [[concepts/claude-code-operational-gotchas]] — MCP query size limits discovered during this migration are a related operational gotcha
- [[concepts/supabase-client-access-patterns]] — Access control patterns that the migrated users must satisfy post-migration
- [[connections/data-identity-mismatches]] — UUID mismatch would have created the same class of invisible-data bug documented across Qualia
- [[concepts/underdog-sales-webinar-system]] — Same destination project (Underdog Academy); webinar system deployed the day before this migration

## Sources

- [[daily/2026-04-28.md]] — Session at 17:00: "Discovered existing `migrate-sales-coach-users.ts` had a critical bug — `auth.admin.listUsers()` doesn't return `encrypted_password`"
- [[daily/2026-04-28.md]] — Session at 17:00: "Migrated all 329 active users (not just 171 signed-in) — user said 'do all'"
- [[daily/2026-04-28.md]] — Session at 17:00: "Found 4 email collisions (Giulio, Fawzi, Ben, Hudson) already in destination — handled via merge logic"
- [[daily/2026-04-28.md]] — Session at 17:00: "Collision-merge must never downgrade tiers — Phase 1b blindly overwrote destination `course` users with source `roleplay` mapping"
- [[daily/2026-04-28.md]] — Session at 17:00: "`auth.users.created_at` is NOT preserved on Supabase auth import — original signup dates only survive if explicitly copied to a `public.profiles` column"
- [[daily/2026-04-28.md]] — Session at 17:00: "MCP token limit hit when trying to extract auth data directly — had to pivot to chunked approach and file-based data extraction"
