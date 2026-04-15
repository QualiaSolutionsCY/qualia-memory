---
type: source
created: 2026-04-16
updated: 2026-04-16
tags: [sakani, e2e, log, append-only]
---

# Run Log — Append Only

> The overnight agent writes to this file incrementally as each checklist item is processed. Never batch — append after every observation. On crash, only the last unfinished item is lost.

## Format

Each entry is a single line, formatted:

```
[HH:MM:SS] [ITEM_ID] [OUTCOME] [TAGS] — <one-line note>
```

- **HH:MM:SS** — local time
- **ITEM_ID** — checklist ID (e.g., `1.16`, `3A.5`, `9.20`) from `.planning/ONBOARDING-CHECKLISTS.md`
- **OUTCOME** — one of: `PASS`, `FAIL`, `BLOCKED`, `OBSERVED`
- **TAGS** — optional bracketed tags: `[B1]`, `[W4-A]`, `[OQ-M5]`, `[REGRESSION]`, `[FINANCE_SIDE_EFFECT]`
- **Note** — human-readable one-liner. If you need more space, link to a separate file in `.qa-screenshots/` or `.qa-logs/`.

For structured evidence (screenshots, SQL dumps, API responses), save them under `/home/qualia/Projects/sakani/.qa-logs/<run-timestamp>/<item-id>-<artifact>.<ext>` and reference the path in the log note.

## Baseline state (record FIRST before any test action)

```
<before first test entry, capture a baseline snapshot>

[HH:MM:SS] BASELINE — Capturing pre-run DB state
  profiles: N
  buildings: N
  units: N
  active_roles: N
  active_memberships: N
  pending_claims: N
  pending_setups: N
  commit: <sha>
  branch: main
  test_user_state: 962700000003 onboarding_step=0
```

## Example entries

```
[22:14:02] W1.1 PASS — OTP sent to 962700000003, received 123456 expected
[22:14:18] W1.2 PASS — Anti-enumeration confirmed (same error for known/unknown phones)
[22:14:45] W1.5 BLOCKED — No way to measure resend cooldown via Playwright without waiting 60s per cycle
[22:15:12] 0.8 OBSERVED — audit_log has AUTH_LOGIN_SUCCESS with actor=962700000003
[22:15:34] 1.16 PASS [W3] — Draft units auto-created from 3-floor × 4-unit structure (12 units total)
[22:17:02] 3A.5 PASS [W4-A] — Building lookup returned exactly 1 match for test Goshan
[22:17:28] 3A.5 FAIL [B12][W4-A] — Match endpoint timed out; mobile silently routed to NOT_FOUND → OWNER_ASSISTED
  Evidence: .qa-logs/2026-04-16-run1/3A.5-timeout.png
  Evidence: .qa-logs/2026-04-16-run1/3A.5-network-trace.har
[22:18:11] 3A.7 PASS — OWNER_UNVERIFIED membership created pending staff review
[22:19:04] 3A.13 PASS [B1-fixed] — Second OWNER_VERIFIED attempt correctly rejected with EH-6.09
[22:21:02] 3A.10 OBSERVED [B4] — Staff approved, mobile JWT still shows old claims 30 seconds later
  This confirms B4 STILL_OPEN
[22:25:33] 5.5 PASS — Tenant search attempt correctly denied with 403
[22:30:18] 6.5 BLOCKED — Cannot test second guard activation; no building has a first guard seeded yet
```

## When something out of scope happens

```
[22:45:01] FINANCE_SIDE_EFFECT — BM approval flipped buildings.subscription_status to TRIAL (expected per FR-3.2.12)
  trial_started_at: 2026-04-16T22:45:01Z
  trial_ends_at: 2026-05-16T22:45:01Z
  Not modifying — continuing with onboarding flow
```

## When a tooling gap is found

```
[22:48:19] TOOLING_GAP — Admin ReviewDialog approve button has no accessible name or testID
  Workaround: getByRole('button').nth(2) — brittle, avoid
  Recommendation: add data-testid="review-dialog-approve-btn"
```

## End-of-run marker

After all checklist items are processed and before writing the morning report:

```
[HH:MM:SS] RUN_COMPLETE — N items processed: P pass / F fail / B blocked / O observed
```

Then append the morning report per [[reporting-template]] below the marker:

```
===== MORNING REPORT — 2026-04-XX =====

<full report here>
```

---

## ENTRIES START BELOW THIS LINE

<!-- Append new entries below. Never edit or reorder existing entries. -->
