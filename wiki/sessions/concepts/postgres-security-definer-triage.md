---
title: "Postgres SECURITY DEFINER Function Triage"
aliases: [security-definer-audit, revoke-public-gotcha, postgres-function-security, security-definer-triage]
tags: [postgres, security, supabase, rls, audit]
sources:
  - "daily/2026-04-28.md"
created: 2026-04-28
updated: 2026-04-28
---

# Postgres SECURITY DEFINER Function Triage

A production security audit of the Qualia ERP portal (portal.qualiasolutions.net) on 2026-04-28 triaged all 25 SECURITY DEFINER functions into four categories and applied targeted REVOKE migrations. The critical gotcha: `REVOKE FROM anon` is a no-op when the function has an implicit `GRANT EXECUTE TO PUBLIC` — anon inherits via PUBLIC. Must `REVOKE ALL ON FUNCTION ... FROM PUBLIC` first, then explicitly grant back only to `authenticated` and `service_role`.

## Key Points

- **Four triage categories:** RLS predicates (12 functions), app-callable RPCs (3), internal helpers (7), trigger-only (3) — each category gets a different security treatment
- **REVOKE FROM anon vs PUBLIC:** PostgreSQL's `REVOKE EXECUTE FROM anon` has no effect when `PUBLIC` has an implicit grant — anon inherits through PUBLIC. Must revoke from PUBLIC first, then re-grant to intended roles
- **Trigger-only functions need only service_role:** Functions like `handle_new_user`, `mirror_public_booking_to_meeting`, and `update_milestone_progress_on_issue_update` only fire from trigger context — revoking from all other roles is safe because triggers execute as the trigger owner
- **22 functions intentionally left with authenticated EXECUTE:** RLS predicates and app RPCs require `authenticated` to function — the audit confirms these grants are intentional, not oversights
- **Supabase advisor catches PUBLIC-inherited grants:** The `anon_security_definer_function_executable` lint rule detects both direct anon grants AND PUBLIC-inherited ones — useful as a post-migration verification tool

## Details

The Supabase security advisor returned 52 WARNs across the Qualia ERP portal, covering RLS-enabled tables without explicit policies and SECURITY DEFINER functions callable by `anon`. The triage determined that most warnings were intentional: tables like `idempotency_keys`, `public_bookings`, `session_reports`, and `website_leads` are write-only or service-role-only by design, and the SECURITY DEFINER functions serve specific architectural roles that require their current grant configurations.

The four triage categories map to clear action items:

1. **RLS predicates (12 functions):** Functions like `is_member_of_workspace()`, `user_has_role()`, etc. These are called by RLS policies during row evaluation. They MUST remain executable by `authenticated` because RLS evaluates in the context of the requesting user. Revoking from `anon` is correct (anon users shouldn't trigger RLS-predicate-dependent queries), but revoking from `authenticated` would break all RLS policies.

2. **App-callable RPCs (3 functions):** Functions called directly from application code via `supabase.rpc()`. These require `authenticated` EXECUTE to function. The grant is intentional.

3. **Internal helpers (7 functions):** Utility functions called by other functions or by the application internals. Most need `authenticated` to support their callers' execution context.

4. **Trigger-only functions (3 functions):** `handle_new_user`, `mirror_public_booking_to_meeting`, and `update_milestone_progress_on_issue_update`. These only execute when their associated trigger fires. Triggers run as the trigger owner (typically `postgres`), not as the requesting user, so EXECUTE grants to `anon`, `authenticated`, and `PUBLIC` are all unnecessary. These were collapsed to `service_role` only.

The initial migration was written as `REVOKE EXECUTE ON FUNCTION ... FROM anon` for each function. When the Supabase advisor re-check still showed the same warnings, investigation revealed the root cause: PostgreSQL functions have an implicit `GRANT EXECUTE TO PUBLIC` by default. Since `anon` is a member of `PUBLIC`, revoking directly from `anon` has no effect — the permission is inherited through the role hierarchy. The corrective second migration used `REVOKE ALL ON FUNCTION ... FROM PUBLIC` first, then explicitly `GRANT EXECUTE TO authenticated, service_role` for functions that needed it. This two-step approach (revoke from PUBLIC, then re-grant to specific roles) is the correct pattern for any Postgres function security hardening.

## Related Concepts

- [[concepts/qualia-portal-mcp-server]] — The Portal MCP server security audit (3 HIGH, 1 MEDIUM, 1 LOW) used similar workspace-scoping hardening on the same portal
- [[concepts/supabase-client-access-patterns]] — Client-role access patterns on the same portal; SECURITY DEFINER functions underpin the RLS predicates that enforce these patterns
- [[concepts/qualiafinal-public-booking-sync]] — The `mirror_public_booking_to_meeting` trigger function was one of the three collapsed to service_role-only in this audit

## Sources

- [[daily/2026-04-28.md]] — Session at 12:23: "Triaged all 25 SECURITY DEFINER functions into 4 categories: RLS predicates (12), app-callable RPCs (3), internal helpers (7), trigger-only (3)"
- [[daily/2026-04-28.md]] — Session at 12:23: "`REVOKE FROM anon` is a no-op when the function has an implicit `GRANT EXECUTE TO PUBLIC` — must `REVOKE ALL ON FUNCTION ... FROM PUBLIC` first"
- [[daily/2026-04-28.md]] — Session at 12:23: "Trigger-only functions collapsed to `service_role` only — triggers still fire because they run as trigger owner"
- [[daily/2026-04-28.md]] — Session at 12:23: "Supabase advisor lint catches PUBLIC-inherited grants, not just direct anon grants"
- [[daily/2026-04-28.md]] — Session at 10:49: "Security advisor returned 52 WARNs — determined these are intentional/safe to leave"
