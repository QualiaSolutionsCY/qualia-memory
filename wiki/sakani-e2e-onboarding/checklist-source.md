---
type: source
created: 2026-04-16
updated: 2026-04-16
tags: [sakani, e2e, checklist, pointer]
---

# Checklist Source — Where Your Pass/Fail Truth Lives

> The authoritative onboarding checklist lives in the Sakani repo. This file is only a pointer plus usage rules.

## Primary file

```
/home/qualia/Projects/sakani/.planning/ONBOARDING-CHECKLISTS.md
```

This file is the single source of truth for "what must be tested". It's SRS-anchored — every item cites FR / EH / DR / POL / NFR IDs that map to the source-of-truth spec at `docs/SRS V1.14.docx` (or the digest at `.planning/knowledge-base/SRS-v1.14-digest.md`).

## Revision history

- **Original (M8 audit):** 2026-04-13 — created during M8 SRS compliance audit
- **Post-M9 revision:** 2026-04-16 — updated to reflect M9 merge (Goshan-driven match, OWNER_ASSISTED flow, optional National ID, admin OA one-step approve)

## Scope of the checklist

Sections 0 through 9, covering:
- 0 — Authentication (W1, all roles)
- 1 — BM_CANDIDATE (W2)
- 2 — BM_VERIFIED upgrade (W2 continuation)
- 3 — OWNER unit claim (W4-A Goshan-first + W4 Unit-first + W2-A setup request path)
- 4 — OWNER_VERIFIED post-claim capabilities
- 5 — TENANT invite-only (W5)
- 6 — GUARD invite + activation (W12)
- 7 — DEV_ADMIN developer mode
- 8 — STAFF_ADMIN admin operations (W10)
- 9 — Cross-cutting rules (entitlement gating, scope isolation, error codes, localization)

## How to use

1. **Read the full file once** at start of run so you know the total item count
2. **Walk top-to-bottom** — do NOT skip around. Section order is flow order.
3. **Log each item** in [[run-log]] with its decimal ID (e.g., `1.16`, `3A.5`, `9.20`)
4. **Mark outcome** per item: PASS / FAIL / BLOCKED / OBSERVED
5. **Cite evidence** for every FAIL (screenshot, API response, DB state)

## Items that are known-broken (skip or expect to fail)

From [[bugs-to-verify]], these items will FAIL unless the bug has been newly fixed. Don't treat the fail as a surprise:

- Items under **section 1 (BM_CANDIDATE)** — B3 blocks the primary UI path; role card hidden
- Items under **section 2 (BM_VERIFIED)** — depend on section 1, cascades
- Items under **section 7 (DEV_ADMIN)** — entire module unimplemented per M8 gap report
- Any item mentioning tenant invites — B8 blocks (permission not seeded)
- Any item mentioning guard calendar viewing — B10 blocks (permission not seeded)
- Any item related to JWT refresh after approval — B4 blocks (flag ignored)

Mark these as FAIL with the bug ID in the tag, not as BLOCKED. They're failing in a known way, not environmentally blocked.

## Items that should be added in the post-M9 revision (if missing)

These are things M9 introduced that the pre-M9 checklist may not cover:

- **OWNER_ASSISTED path** — new M9 flow where owner creates building-and-claim in one step when building not found
- **Goshan-driven match endpoint** — `GET /buildings/match?basin_name=X&plot_number=Y` with MATCH / AMBIGUOUS / NOT_FOUND outcomes
- **Arabic cadastral normalization** — SQL function `normalize_ar(text)` used in match; verify hamza variants, tatweel, taa-marbuta all normalize correctly
- **Auto-link on existing building match** — when OWNER_ASSISTED finds an exact match via `match_building_by_goshan`, claim auto-links rather than creating a duplicate
- **Optional National ID in OA flow** — front + back upload, auto-parse, verify against Goshan `owner_national_ids[]` list
- **Admin one-step OA approval** — single "Setup building + approve claim" button
- **Trial activation on building creation** — `trial_started_at = now, trial_ends_at = +30d` as a side effect of BM approval (observe, don't touch per [[scope-boundary]])

The post-M9 revision of the checklist should have these as explicit items. If the checklist you're testing against doesn't have them, log a tooling gap and test them anyway using the M9 map in [[selectors-map]].

## Related

- [[README]] — mission briefing
- [[selectors-map]] — UI selectors per flow
- [[bugs-to-verify]] — known bugs
- [[run-log]] — where results go
