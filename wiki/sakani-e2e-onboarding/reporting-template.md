---
type: source
created: 2026-04-16
updated: 2026-04-16
tags: [sakani, e2e, reporting, morning-report]
---

# Reporting Template — Morning Report

> Exact shape of the summary to produce at end of run. Fawzi reads this on waking up — must be scannable in 5 minutes, actionable in 20.

## When to produce it

At the end of the run, after the last checklist item is processed. Append to [[run-log]] with a clear separator:

```
===== MORNING REPORT — 2026-04-XX =====
```

Below that separator, follow this exact structure:

---

## 1. One-line verdict

Pick ONE of:

- 🟢 **SHIP-READY** — onboarding is solid; no critical fails; known gaps are acceptable
- 🟡 **FIX-CRITICAL-FIRST** — onboarding works for the happy path but 1+ criticals are regressions or ship-blockers
- 🔴 **REBUILD-NEEDED** — core flow is broken; cannot complete primary user journey
- ⚪ **INCONCLUSIVE** — run was blocked by environment issues; no real signal generated

## 2. Numbers

```
Totals
  Checklist items  : NNN
  PASS             : NNN
  FAIL             : NNN
  BLOCKED          : NNN
  OBSERVED-ONLY    : NNN

Workflow coverage
  W1  Auth          : P/F/B (e.g., "8/0/1")
  W2  Permit        : P/F/B
  W2-A Setup-req    : P/F/B
  W3  Unit registry : P/F/B
  W4  Claim (unit)  : P/F/B
  W4-A Claim (Gosh) : P/F/B
  W5  Tenant invite : P/F/B
  W10 Admin queue   : P/F/B
  W12 Guard invite  : P/F/B

Role coverage (did the agent reach each state?)
  OWNER_UNVERIFIED  : yes/no/attempted
  OWNER_VERIFIED    : yes/no/attempted
  BM_CANDIDATE      : yes/no/attempted   (expect NO — B3 blocks)
  BM_VERIFIED       : yes/no/attempted
  TENANT            : yes/no/attempted
  GUARD             : yes/no/attempted
  DEV_ADMIN         : yes/no/attempted   (expect NO — unimplemented)
```

## 3. Critical fails (max 5 — most important only)

For each:

```
### FAIL-1 · [B#] <Short title>
  - **Workflow:** W#
  - **Checklist item:** <ID from .planning/ONBOARDING-CHECKLISTS.md>
  - **Expected:** <one sentence>
  - **Observed:** <one sentence>
  - **Evidence:**
    - Screenshot: path/to/screenshot.png
    - API response: <inline JSON or path>
    - DB state: <inline SQL result>
    - Audit log: <event_id or null>
  - **Reproduce:** <3-step walkthrough>
  - **Blast radius:** users affected, frequency
  - **Root cause hypothesis (if clear):** file:line or area
```

If there are more than 5 critical fails, list only the top 5 here and say "See [[run-log]] for the remaining N critical fails".

## 4. Bug status summary (from [[bugs-to-verify]])

One row per tracked bug:

```
| Bug   | Status             | Evidence                    |
|-------|--------------------|----------------------------|
| B1    | VERIFIED_FIXED     | Path A claim accepted       |
| B2    | STILL_OPEN         | Admin detail shows '—'      |
| B3    | STILL_OPEN         | Only owner card rendered    |
| B4    | STILL_OPEN         | JWT stale after approval    |
| B5    | NOT_TESTED         | Architectural — cannot E2E  |
| B6    | PARTIALLY_FIXED    | Claim yes, setup-req no     |
| B7    | STILL_OPEN         | verify-otp.tsx still exists |
| B8    | STILL_OPEN         | 403 on tenant invite        |
| B9    | NOT_TESTED         |                             |
| B10   | STILL_OPEN         | Free text as reason_code    |
| B11   | NEW_BUG_CONFIRMED  | OA success back-navigable   |
| B12   | NEW_BUG_CONFIRMED  | Network err → NOT_FOUND     |
| B13   | NEW_BUG_CONFIRMED  | ID mismatch doesn't block   |
| B14   | NOT_TESTED         | Edit flow not reached       |
| B15   | NEW_BUG_CONFIRMED  | No audit for auto-link      |
```

Use: `VERIFIED_FIXED`, `STILL_OPEN`, `PARTIALLY_FIXED`, `NEW_BUG_CONFIRMED`, `NOT_TESTED`, `INCONCLUSIVE`.

## 5. Blocked items

For each:

```
### BLOCKED-1 · <Short title>
  - **Workflow:** W#
  - **Reason:** <one of: native-file-picker / tooling-gap / missing-seed / service-down / env-var-missing>
  - **Would need:** <what's missing to unblock>
  - **Priority to unblock:** P0 / P1 / P2
```

## 6. Tooling gaps discovered

List any selector / automation / framework gaps that limited what you could test:

```
- Playwright cannot drive native file picker (known) — blocks all upload tests on mobile
- `getByRole('button', { name: /submit/i })` matched 3 elements on setup-request form — needs unique names or testIDs
- Admin ReviewDialog has no testID on approve button — had to use position
```

## 7. Observed-only findings (from [[open-questions]])

For each observed behavior where the expected outcome wasn't pre-agreed:

```
- **OQ-X:** <what was observed> — <which direction from the open question>
- **OQ-M5 (homepage after re-login):** After onboarding completion and re-login, user landed on the tabs home with building context. Nezar's pain point appears resolved.
```

## 8. Cross-scope observations (finance-side side effects)

If the run hit anything that looked like a finance-side mutation triggered by an onboarding flow, list it here. Don't judge, just report:

```
- BM approval for building #NNN flipped `buildings.subscription_status` from `INACTIVE` → `TRIAL` (expected side effect per SRS FR-3.2.12)
- `subscription_entitlement.trial_ends_at` set to <timestamp>
- No other finance mutations observed
```

## 9. Regression candidates

Only populate this section if a prior run's log exists in [[run-log]]:

```
- W4 Goshan-first happy path: PASSED last run (2026-04-XX), FAILED this run (2026-04-YY)
  - Diff in commits: `git log --oneline <prev>..<now>` if known
  - Suspect: <commit sha or area>
```

## 10. Recommendation

One paragraph. What should Fawzi do next?

Examples:
- "Fix B3 (BM role hidden) before any client demo — it blocks the primary BM flow. Everything else is non-critical."
- "Main blocker is the unreliable `parse-goshan` edge function. Investigate that first — 6 test cases depend on it and were all BLOCKED."
- "Three new M9 regressions (B11, B12, B13). Suggest a rollback of the OWNER_ASSISTED success screen change until the back-navigation bug is fixed."
- "Everything green. Safe to proceed with M9 stabilization and then re-enable the BM role card for next milestone."

## 11. Run metadata

```
Run start     : 2026-04-XX HH:MM:SS UTC
Run end       : 2026-04-XX HH:MM:SS UTC
Duration      : Nh Nm
Commit        : <short sha of HEAD at run start>
Branch        : main
Test user     : +962700000003 (primary)
Agent         : claude-<model>
Environment   : local dev (Supabase vgclogtusvsrvyxaxaqm + NestJS :3333 + admin :3000)
Checklist file: .planning/ONBOARDING-CHECKLISTS.md (revision <timestamp>)
```

---

## Markdown formatting rules

- Use code fences for SQL, JSON, and paths
- Use tables for status grids (bug status, coverage matrix)
- Use bold for severity markers
- Do NOT use emojis anywhere except the 4 verdict markers at the top (🟢🟡🔴⚪) — keeps it scannable
- Keep line width reasonable (80-100 chars) — Fawzi may read this on a phone

## Related

- [[README]] — mission briefing
- [[run-log]] — raw log that this report summarizes
- [[bugs-to-verify]] — B1–B15 referenced in section 4
- [[open-questions]] — OQ items for section 7
