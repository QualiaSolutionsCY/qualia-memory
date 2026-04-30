---
title: "Connection: Supabase Tooling Escape Hatches"
connects:
  - "concepts/supabase-cross-project-migration"
  - "concepts/postgres-security-definer-triage"
  - "concepts/claude-code-operational-gotchas"
  - "concepts/verification-contract-gaps"
  - "concepts/brevo-transactional-vs-campaign"
sources:
  - "daily/2026-04-28.md"
  - "daily/2026-04-29.md"
created: 2026-04-28
updated: 2026-04-29
---

# Connection: Supabase Tooling Escape Hatches

## The Connection

Four distinct Supabase workflows across 2026-04-28 and 2026-04-29 each hit a wall with the standard tooling and required an escape hatch: (1) `supabase db push` failed due to migration history drift — escaped via MCP `apply_migration`, (2) `auth.admin.listUsers()` omitted password hashes — escaped via direct SQL dump, (3) MCP query size limits blocked large auth table extraction — escaped via file-based chunked extraction, (4) Supabase Edge Functions rejected the newer `sb_publishable_*` anon key format — had to fall back to the classic JWT-format `eyJ...` key. In each case, the standard happy-path tool appeared to work but silently lost critical data or failed entirely, requiring a manual alternative.

## Key Insight

The non-obvious relationship is that Supabase's tooling follows a "convenience layer over raw SQL" architecture, and each convenience layer makes opinionated decisions about what to include or exclude. The Admin API excludes `encrypted_password` (security choice). The CLI migration tracker rejects pushes when local and remote histories diverge (safety choice). The MCP tool has token/payload limits (infrastructure choice). Edge Functions don't support the newest key format (backward compatibility gap). Each choice is individually reasonable, but collectively they mean that **any non-trivial Supabase operation should have a planned escape hatch to raw SQL before execution begins.**

The escape hatch hierarchy for Supabase operations:
1. **CLI** (`supabase db push`, `supabase db diff`) — standard path
2. **MCP tool** (`apply_migration`, `execute_sql`) — when CLI migration history is tangled
3. **Dashboard SQL editor** — when MCP token limits are hit
4. **Direct SQL dump** (`pg_dump`, raw `SELECT` from auth schema) — when Admin API omits fields

This same "layered escape" pattern applies beyond Supabase: the builder sandbox can't reach `supabase` CLI (documented in [[concepts/verification-contract-gaps]]), so migration application escalates to the orchestrator. The SECURITY DEFINER triage (documented in [[concepts/postgres-security-definer-triage]]) required raw SQL grants that the Admin API doesn't expose.

## Evidence

- CLI migration drift: "Supabase CLI migration history had too much drift to auto-push; resolved by applying via MCP tool instead" — [[daily/2026-04-28.md]] Session at 10:49
- Admin API password omission: "`auth.admin.listUsers()` doesn't return `encrypted_password`, so password preservation silently fails" — [[concepts/supabase-cross-project-migration]]
- MCP query limits: "MCP token limit hit when trying to extract auth data directly — had to pivot to chunked approach and file-based data extraction" — [[concepts/supabase-cross-project-migration]]
- SECURITY DEFINER grants: Required `REVOKE ALL FROM PUBLIC` then `GRANT TO authenticated` — raw SQL operations not available through any convenience API — [[concepts/postgres-security-definer-triage]]
- Builder sandbox: "Builder agents can't reach Supabase CLI from within their sandbox" — [[concepts/verification-contract-gaps]]
- Edge Function key format: "Supabase Edge Functions don't support the new `sb_publishable_*` key format — must use classic JWT-format `eyJ...` anon key" — [[concepts/brevo-transactional-vs-campaign]] (discovered during same debugging session)

## Related Concepts

- [[concepts/supabase-cross-project-migration]]
- [[concepts/postgres-security-definer-triage]]
- [[concepts/claude-code-operational-gotchas]]
- [[concepts/verification-contract-gaps]]
- [[concepts/brevo-transactional-vs-campaign]] — The Edge Function JWT key format requirement was discovered during the same 4-bug debugging session
- [[concepts/underdog-sales-webinar-system]] — Where the Edge Function key format gotcha manifested in production
