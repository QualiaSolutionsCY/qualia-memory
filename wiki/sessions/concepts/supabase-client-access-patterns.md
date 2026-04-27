---
title: "Supabase Client Access Patterns"
aliases: [supabase-storage-rls, client-access-control, assertAppEnabledForClient, storage-policy-gotchas]
tags: [supabase, rls, security, portal, access-control]
sources:
  - "daily/2026-04-27.md"
created: 2026-04-27
updated: 2026-04-27
---

# Supabase Client Access Patterns

Two distinct Supabase access control pitfalls discovered during client account setup on the Qualia Portal (portal.qualiasolutions.net) on 2026-04-27: storage bucket policies that silently fail when individual CRUD operations are missing, and application-level access guards that fail closed too aggressively when prerequisite data doesn't exist yet.

## Key Points

- **Storage CRUD independence:** Supabase storage policies must have all four operations (SELECT, INSERT, UPDATE, DELETE) checked independently — having SELECT + DELETE without INSERT causes silent RLS failures on file upload with no error surfaced to the user
- **Fail-closed access guard:** `assertAppEnabledForClient` in `lib/portal-utils.ts:189` denied all access when no `client_projects` link existed for a user, blocking even base functionality like viewing requests or messages
- **Default allowlist pattern:** The fix added a hardcoded allowlist of base apps (`home`, `requests`, `messages`, `billing`, `settings`) that clients can always access without needing a `client_projects` row — project-scoped features still require the link
- **Client shell vs admin pages:** "Clients should see everything" meant only the client shell items (Requests, Messages, Payments, Settings) — NOT admin pages like Clients/Control/Workspace; clarifying scope prevented a dangerous permissions expansion
- **Empty state UX:** The "Submit your first request" button linked back to the same page (useless) — replaced with guidance pointing to the header's New Request button

## Details

The storage policy gap was discovered when setting up a client account for Tasos Kyriakides (`an.kyriakides@gmail.com`). File uploads to the `project-files` bucket under the `feature-requests/<uid>/` path were silently failing. Investigation revealed that only SELECT and DELETE policies existed for the bucket — no INSERT policy. Supabase storage RLS evaluates each operation independently, so having read and delete permissions grants no implicit write permission. The fix was adding an INSERT policy scoped to `feature-requests/<uid>/...`. This is a common trap because developers often test with the service role key (which bypasses RLS) and only discover missing policies when a client-role user attempts the operation.

The `assertAppEnabledForClient` issue was more subtle. The function checks whether a client user has access to a given portal app by looking up their `client_projects` entries and checking which apps are enabled for those projects. When a newly created client account had zero `client_projects` rows, the function returned false for every app — including basic shell items like Requests and Messages that every client should see. The fix was to add a default allowlist: if the user is a client and the requested app is in the base set (`home`, `requests`, `messages`, `billing`, `settings`), access is granted regardless of `client_projects` state. This preserves the security model for project-scoped features while ensuring new clients aren't locked out of the entire portal.

The broader lesson is that access control functions that depend on prerequisite data (like association tables) must have a fallback for the "no data yet" state. Failing closed is correct for security-sensitive operations, but failing closed on basic navigation creates a broken onboarding experience. The pattern of default allowlists for base functionality plus explicit grants for elevated features applies to any multi-tenant portal.

## Related Concepts

- [[concepts/qualia-portal-mcp-server]] — Another surface where workspace-scoped access control required careful policy design
- [[concepts/qualiafinal-public-booking-sync]] — Same class of bug: missing data (workspace_id there, client_projects here) causing silent functional failures
- [[concepts/qualia-portal-employee-workflows]] — Portal access patterns for internal users; this article covers the external client perspective

## Sources

- [[daily/2026-04-27.md]] — Session at 03:45: "Storage INSERT policy added for `feature-requests/<uid>/...` in `project-files` bucket — only SELECT and DELETE existed, causing silent upload failures"
- [[daily/2026-04-27.md]] — Session at 03:45: "`assertAppEnabledForClient` (lib/portal-utils.ts:189) failed closed when no `client_projects` link existed. Added a default allowlist"
- [[daily/2026-04-27.md]] — Session at 03:45: "Clarified that 'clients should see everything' meant only the client shell items — NOT admin pages"
- [[daily/2026-04-27.md]] — Session at 03:45: "Removed 'Submit your first request' button — it linked back to the same page (useless)"
