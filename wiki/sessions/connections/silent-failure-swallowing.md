---
title: "Connection: Silent Failure Swallowing Across Qualia"
connects:
  - "concepts/sophia-lead-routing-debug"
  - "concepts/erp-clock-out-name-mismatch"
  - "concepts/qualiafinal-public-booking-sync"
  - "concepts/supabase-client-access-patterns"
  - "concepts/underdog-quiz-validation-bug"
sources:
  - "daily/2026-04-27.md"
  - "daily/2026-04-26.md"
  - "daily/2026-04-29.md"
created: 2026-04-27
updated: 2026-04-29
---

# Connection: Silent Failure Swallowing Across Qualia

## The Connection

Five bugs across the Qualia ecosystem share a diagnostic signature: the system appears to complete an operation successfully, but the intended outcome is silently lost. The [[connections/data-identity-mismatches]] connection documented the first three instances (booking sync, clock-out, client access) as "write-path-doesn't-satisfy-read-path" problems. Sophia's lead-routing bug adds a fourth dimension: the write path succeeds, the read path succeeds, but the **routing logic between them** swallows the failure because it checks for object truthiness instead of operation success. The Underdog Academy quiz bug adds a fifth: a **schema validation layer** returns `{ok: false}` for every answer due to a Zod type mismatch, but the UI interprets this as "wrong answer" rather than "system error."

## Key Insight

The data-identity-mismatches pattern catches bugs where data is *stored wrong* (missing `workspace_id`, mismatched project name, absent `client_projects` row). Sophia's truthy-object bug is subtly different: the data is never written at all because the routing code interprets `{success: false}` as truthy and exits the decision tree early. Both present identically to the user — "I did the thing, the system didn't react" — but they require different verification techniques:

- **Data-identity mismatches** are caught by: "read the consuming query's WHERE clause and verify every column is populated by the write path"
- **Routing-logic swallowing** is caught by: "verify that error/failure return objects are checked by their semantic fields (`.success`, `.ok`, `.error`), not by JavaScript truthiness"
- **Schema-validation swallowing** is caught by: "verify that validation failure return shapes are distinguishable from domain-level failure (wrong answer) — and that the UI surfaces validation errors separately from business-logic outcomes"

The unified principle is: **every code path that can fail must make its failure visible to the next decision point.** Whether the failure is in data (wrong FK) or in logic (truthy error object), silent swallowing produces the same user experience — nothing happens, and no error is shown.

## Evidence

- Sophia lead-router: `if (ownerBasedResult)` is truthy on `{success: false}` → failed forward silently treated as success → lead dropped — [[concepts/sophia-lead-routing-debug]]
- Booking sync: trigger omits `workspace_id` → `getMeetings()` can't find the row → booking invisible — [[concepts/qualiafinal-public-booking-sync]]
- Clock-out: report has em-dash project name variant → `ilike` exact match fails → report appears missing — [[concepts/erp-clock-out-name-mismatch]]
- Client access: no `client_projects` rows → `assertAppEnabledForClient` returns false → client locked out — [[concepts/supabase-client-access-patterns]]
- Quiz validation: `uuidSchema` applied to arbitrary string option IDs (`"a"`, `"true"`) → Zod returns `{ok: false}` for every answer → every quiz scores 0, every paying user locked out of gated content — [[concepts/underdog-quiz-validation-bug]]
- All five required manual intervention (force-close session, backfill rows, add allowlist, redeploy lead-router, change schema validation)

## Related Concepts

- [[concepts/sophia-lead-routing-debug]]
- [[concepts/erp-clock-out-name-mismatch]]
- [[concepts/qualiafinal-public-booking-sync]]
- [[concepts/supabase-client-access-patterns]]
- [[concepts/underdog-quiz-validation-bug]]
- [[connections/data-identity-mismatches]] — The precursor connection documenting the data-layer subset of this pattern
