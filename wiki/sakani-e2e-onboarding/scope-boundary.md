---
type: source
created: 2026-04-16
updated: 2026-04-16
tags: [sakani, e2e, testing, scope, coordination]
---

# Scope Boundary — Onboarding E2E vs Finance E2E

> A second autonomous Claude Code session is running finance work in parallel. This file draws the line so the two agents don't corrupt each other's state.

## You (onboarding agent) own

- Authentication and OTP flow (W1) — sign-in, sign-up, verify-otp
- Profile collection (`onboarding_step` 0 → 1 → 2)
- Role selection step (acknowledge that only "owner" card is currently rendered; see [[bugs-to-verify]] B3)
- Legal reconsent gate
- Building setup request creation (W2-A via BM) and admin review (W3)
- Unit claim request (W4 / W4-A / OWNER_ASSISTED) including Goshan parse + match
- National ID upload + parse + cross-check against Goshan
- Staff verification queues (permit, setup-request, claim-request) — read and decide, admin side
- Role activation post-approval (OWNER_VERIFIED, BM_VERIFIED, unit_membership + building_role_assignment)
- JWT refresh after role grant (see [[bugs-to-verify]] B4 — known broken)
- Tenant invite flow (W5) — if reached (invite-only, needs OWNER_VERIFIED to start)
- Guard invite flow (W12) — if reached
- Developer mode (DEV_ADMIN) — only if DB/admin surface exists; currently unimplemented per M8 gap report
- All error code paths: EH-6.02, 6.03, 6.04, 6.09, 6.10, 6.11 (observe only), 6.12, 6.13, 6.17, 6.18, 6.19, 6.20, 6.22, 6.23, 6.24, 6.25, 6.29
- Audit events for every onboarding FR — verify emission to `audit_log` table

## Finance agent owns (DO NOT touch)

- W6 dues cycles + AutoSettlement
- W7 credit top-up submission, approval, evidence requirements, dedupe
- W8 expenses (creation, evidence, reversal)
- W9 statements, versioning, exports, INACTIVE gating *on the statement side*
- W11 subscription webhook ingestion (signature validation, idempotency, event normalization)
- W14 BM money-in (manual unit income, opening balance)
- W16 subscription checkout (quote, promo, checkout, confirm, activation)
- Reimbursement obligation creation and settlement (FR-3.7.31–36)
- Any mutation of `ledger_entry`, `unit_credit_balance`, `unit_month_dues_obligation`, `dues_config`, `dues_vote`, `vote_cast`, `statement`, `credit_topup_request`, `subscription_*` tables, `reimbursement_obligation`, `promo_code`, `promo_redemption`

## Grey zone — observe, don't mutate

You will cross through these in the course of onboarding flows, because entitlement gating sits at the boundary of identity and finance. Rules:

- **`subscription_entitlement.status`** — check its value during onboarding (trial, active, inactive) so you can predict whether a flow is allowed or paywalled. Do NOT transition it manually.
- **Trial activation** — when a BM is approved for the first time, the system flips `trial_started_at = now, trial_ends_at = now + 30d`. This is fine to OBSERVE after BM approval (it's a side effect of your onboarding scope). It is NOT fine to manually trigger or roll back.
- **`EH-6.11 SUBSCRIPTION_INACTIVE`** — if you hit the paywall during an identity flow, that's a BUG in your scope (identity flows should remain allowed when INACTIVE per FR-3.1.04). Log the observation, don't try to fix the state.
- **`building.subscription_status` mirror field** — read only. If you see a mismatch between this and `subscription_entitlement.status`, flag it in your report.

## File-level exclusion list

Do NOT read, write, or bash-invoke anything in these paths during your run:

```
server/src/modules/ledger/
server/src/modules/cycles/
server/src/modules/dues/
server/src/modules/credit/
server/src/modules/expenses/
server/src/modules/statements/
server/src/modules/subscription/
server/src/modules/reimbursement/
server/src/modules/webhooks/    (if it exists for provider webhooks)

apps/mobile/app/(app)/(tabs)/financial*
apps/mobile/app/(app)/payments/
apps/mobile/lib/api/ledger.ts
apps/mobile/lib/api/credit.ts
apps/mobile/lib/api/cycles.ts
apps/mobile/lib/api/expenses.ts
apps/mobile/lib/api/statements.ts
apps/mobile/lib/api/reimbursements.ts
apps/mobile/lib/api/subscription.ts

apps/admin/src/app/(dashboard)/financial/
```

You MAY read (but not write):
- `apps/mobile/lib/api/buildings.ts` (match endpoint lives here, so you need it)
- `apps/mobile/lib/api/documents.ts` (Goshan + National ID parsing)
- `server/src/modules/buildings/` (setup-request service + match service)
- `server/src/modules/units/` (claim-request service)
- `server/src/modules/admin-case/` (if AdminCase is triggered from an onboarding flow)

## Database coordination

You WILL be writing rows to:
- `profiles` (on sign-up + profile step)
- `buildings` (on BM setup approval or OWNER_ASSISTED auto-link)
- `units` (bulk insert on approval)
- `building_role_assignment` (on BM_VERIFIED grant)
- `unit_membership` (on OWNER_VERIFIED grant)
- `unit_claim_request` (on submit)
- `building_setup_request` (on submit)
- `document` (on Goshan / National ID / Permit upload)
- `audit_log` (side effect of every action)

You must NOT write to any of the finance tables listed above, even as a side effect. If an onboarding flow ends up touching one (e.g., BM approval flips `buildings.subscription_status` to TRIAL), that's an expected observable side effect — log it, don't block on it.

## Conflict resolution protocol

If your test flow observes a finance-side mutation that shouldn't have happened (e.g., a ledger entry appears during a claim approval), **do not roll it back**. Log it in [[run-log]] as `FINANCE_SIDE_EFFECT` with the mutation details, continue with your flow, and flag it in the morning report under "Cross-scope observations". The finance session will review it in their own morning report.

If the finance agent writes a conflicting state (e.g., flips entitlement to INACTIVE while you're mid-flow), your flow may observe EH-6.11 where it shouldn't. Note it and continue — the morning report will surface the collision and Fawzi will decide whether it's a test-state issue or a real bug.

## Related

- [[README]] — mission briefing
- [[bugs-to-verify]] — known bugs to re-check
- [[run-log]] — where to append observations
