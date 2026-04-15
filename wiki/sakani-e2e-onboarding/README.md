---
type: source
created: 2026-04-16
updated: 2026-04-16
tags: [sakani, e2e, testing, onboarding, agent-briefing]
---

# Sakani E2E Onboarding Test — Agent Briefing

> Command center for the overnight autonomous agent running end-to-end verification of Sakani's onboarding flows. Read this file first, then follow links to specific context.

## Mission

Walk the full onboarding flows (W1, W2, W2-A, W3, W4, W4-A, W5, W10-queue, W12) across the mobile app, the NestJS backend, and the admin console. For each checklist item, record PASS / FAIL / BLOCKED with evidence (screenshot, API response, DB state, or error trace). Produce a morning report that Fawzi can read in 5 minutes.

## Scope boundary — read before you do anything

**This run covers onboarding flows only.** Do NOT execute, inspect, or mutate:
- W6 dues cycles + AutoSettlement
- W7 credit top-up submission + approval
- W8 expenses
- W9 statements + exports
- W11 subscription webhook ingestion
- W14 BM money-in / opening balance
- W16 subscription checkout / trial → active → inactive transitions
- Any `/ledger/*`, `/cycles/*`, `/expenses/*`, `/credit/*`, `/subscription/*`, `/reimbursements/*` endpoints
- Any `financial/*` admin screens
- Any mobile screen under `(app)/(tabs)/financial*` or the subscription / paywall flows

**Why:** A parallel Claude Code session is working on finance simultaneously. Colliding on financial state (creating dues, posting expenses, toggling entitlements) will corrupt the other agent's test state and invalidate both runs. See [[scope-boundary]] for the exact file-level exclusion list.

**If an onboarding flow forces you into finance territory** (e.g., trial activation after BM approval, entitlement gating check on claim), record observed behavior but do NOT manipulate financial state. Check the flag, log, move on.

## Table of contents

1. [[scope-boundary]] — exactly what is and isn't yours to touch
2. [[environment]] — how to start dev servers, URLs, required services, dependencies
3. [[test-credentials]] — fixed test phone numbers, OTP codes, which user is fresh vs pre-seeded
4. [[checklist-source]] — primary pass/fail checklist (lives in the Sakani repo, not here)
5. [[bugs-to-verify]] — known bugs that must be re-checked for FIXED / STILL OPEN
6. [[open-questions]] — SRS decisions not yet locked; do NOT assume resolved
7. [[selectors-map]] — UI selectors per flow (populated by background agent — may be stub at run start)
8. [[reporting-template]] — exact shape of the morning report to produce
9. [[run-log]] — append-only log the agent writes to as it runs (start empty)

## How this agent is expected to operate

- **One checklist item at a time.** Load the item from [[checklist-source]], walk the flow, record the outcome in [[run-log]], move on.
- **Write to [[run-log]] incrementally.** Never batch a full night's worth of results into memory and write at the end — if you crash, everything is lost. Append after each item.
- **Use stable selectors.** Prefer accessible names, testIDs, and role attributes over brittle CSS paths. If a selector is missing or flaky, record it as a **tooling gap** in [[run-log]] — don't work around silently.
- **Report falsifiable evidence only.** "Screen looked right" ≠ PASS. PASS means you observed the specific spec-required behavior (data in DB, response code, audit entry, visible UI text in the expected language).
- **When in doubt, BLOCKED ≠ FAIL.** If a test is impossible to run (native file picker, provider outage, missing seed data), mark BLOCKED and explain. Don't inflate the FAIL count.
- **Respect [[scope-boundary]].** If a flow pulls you toward finance, stop at the onboarding boundary and note it as out-of-scope in the report.

## Deliverable at end of run

A single markdown file appended to [[run-log]] with the structure described in [[reporting-template]]:
- Summary counts: pass / fail / blocked / out-of-scope
- FAIL list with evidence per item
- BLOCKED list with reason
- Tooling gaps found
- Regression candidates (things that PASSED last run but FAILED this run — if prior run data exists)
- Confidence-weighted recommendation: ship / fix-critical-first / full-rebuild-needed

## Related

- [[Sakani App]] — project root
- [[Sakani]] — client profile
- [[Fawzi Goussous]] — project owner
