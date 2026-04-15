---
type: source
created: 2026-04-16
updated: 2026-04-16
tags: [sakani, e2e, srs, gap-log, assumptions]
---

# Open Questions — Do NOT Assume Resolved

> Things the SRS has not yet decided or where the client has not yet given final direction. If you observe behavior in these areas, record the observation but do NOT treat either outcome as a PASS or FAIL. These are "unknown expected" — morning report goes in a separate "Observations" bucket.

## From `.planning/GAP-LOG.md`

### OQ-4: Signed URL TTL — configurable or fixed?

- **Default per SRS (D11):** 10 minutes
- **Question:** Should this be per-document-type (longer for legal docs, shorter for photos)?
- **Your behavior:** Use default 10-minute assumption. If you observe signed URL expiry in ANY onboarding flow (Goshan preview, National ID preview, permit preview), note the actual TTL observed and the document type. Do NOT mark fail for mismatch.
- **Relevance to onboarding:** Goshan upload + admin preview; National ID upload + admin preview; Permit upload + staff review.

### OQ-13: `building_contact` visibility when INACTIVE

- **SRS recommendation:** Allow view even when INACTIVE (safety rationale — emergency contacts must remain reachable)
- **Current code likely:** Blocks per general INACTIVE gating rule
- **Your behavior:** If you test a building in INACTIVE state, DO NOT attempt to view building_contact. This is out of your scope anyway (finance agent territory). If you accidentally observe it, note the outcome and move on.
- **Relevance to onboarding:** Marginal — only matters if your flow touches an INACTIVE building's contact card.

## From client meetings (live unresolved debates — see `.planning/knowledge-base/unfinalized.html`)

These are positions from John, Majdi, Nezar, Raad that were **not finalized** in meetings. The app may have moved in either direction. If you observe the app taking one position, don't mark it FAIL just because a different person argued for the other.

### OQ-M1: AI trust threshold for auto-approval of Goshan field matches

- **Position (John):** Eventually AI should auto-approve field matches with high confidence; staff only spot-check
- **Position (unresolved):** Majdi tasked with defining the confidence score; no threshold agreed
- **Your behavior:** Observe whether AI-parsed values from Goshan are:
  1. Shown to user for edit before submit (current expected)
  2. Silently accepted without user confirmation (premature auto-approval — FLAG)
  3. Blocked for staff review regardless (conservative but acceptable)
- The safest state is (1). Anything else is an observation, not a fail.

### OQ-M2: Mandatory vs optional expense receipts (finance scope — not yours)

Excluded from your scope. If you accidentally walk into an expense flow, back out.

### OQ-M3: Single-owner vs multi-owner registration for Goshan with multiple names

- **SRS FR-3.2.37:** Store a read-only snapshot of all deed owners; only the claiming user gets OWNER_VERIFIED (one-per-unit in Phase 1)
- **Client M4 discussion (Majdi):** Wanted a "secondary owner / acting occupant" role for the 30-40% of Jordan cases where the property is registered under a relative's name
- **Locked decision:** SRS wins — Phase 1 is one-per-unit only. Secondary owners are EXPLICITLY DEFERRED.
- **Your behavior:** Upload a Goshan with multiple owner names. Verify:
  1. ✅ The claim succeeds for the claiming user
  2. ✅ `unit_membership` has ONE row with OWNER_VERIFIED for the claiming user only
  3. ✅ The other names are stored somewhere (claim extracted_data or document metadata) as snapshot only
  4. ❌ NO secondary user gets any membership or role
- **If you observe (4) failing**, that's a real regression — mark FAIL.

### OQ-M4: Population and children-count fields on profile

- **Position (Majdi + Nezar):** Remove these — feels invasive, bank-interrogation-like
- **Current state per M7:** Profile fields removed per feedback
- **DB state:** `marital_status` + `number_of_kids` columns still exist in `profiles` table (dead columns per B8)
- **Your behavior:** Verify the profile UI does NOT ask for these fields. If it does → STILL OPEN regression. Verify the DB columns are never populated by your test runs. If any test user ends up with non-null values in these columns → FLAG (something is writing to dead columns).

### OQ-M5: Homepage navigation and back-stack behavior

- **Position (Nezar, unresolved):** Complained that after re-login the app goes back to "Building Setup" / "Claim My Unit" screens with no main page accessible
- **Your behavior:** After a successful onboarding completion, restart the app (or re-login). Does the user land on a home tab with building/unit context? Or back in the onboarding flow? Record the outcome.
- **Decision status:** Not locked in SRS. Observation only.

## SRS decisions known to be LOCKED (these ARE firm)

For contrast — do NOT treat these as unresolved. They are D1–D12 from SRS §10.2.1 and MUST be asserted:

- **D1:** OTP policy — TTL 10min, cooldown 60s, 5 resends, 5 verify attempts, 1hr lockout, 20/phone/day. **ASSERT in your run.**
- **D2:** Staff MFA required (TOTP + backup codes). **ASSERT if you touch staff admin.**
- **D3:** Single subscription provider per environment (not your scope).
- **D4:** Webhook retry policy (not your scope).
- **D5:** Canonical subscription event types (not your scope).
- **D6:** Resource-ID endpoints return 404 when unauthorized; non-resource actions may return 403. **ASSERT — if you deliberately try to access another user's claim request by ID, expect 404.**
- **D7:** Goshan building lookup inputs = `(locality/directorate + basin + parcel + permit_number)`. **ASSERT — verify the match endpoint uses these fields.**
- **D8:** `canonical_permit_identity_key` = `(permit_number + locality/directorate + basin + parcel)`. **ASSERT — setup request dedupe should use this.**
- **D9:** Pricing defaults (not your scope).
- **D10:** Promo codes (not your scope).
- **D11:** Signed URL TTL = 10 minutes (with OQ-4 caveat above).
- **D12:** Uptime 99.5% (not testable in one run).

## How this file interacts with the morning report

Your morning report has three buckets for items:
1. **PASS** — tested, worked as expected
2. **FAIL** — tested, didn't work, with evidence
3. **BLOCKED** — couldn't test due to environment / tooling / missing seed
4. **OBSERVED-ONLY** — behavior was recorded but has no pre-agreed expected outcome (anything from this file goes here)

Items from this file MUST go in the **OBSERVED-ONLY** bucket, not PASS or FAIL. Make it easy for Fawzi to scan — his decision makes these definitive next time.

## Related

- [[README]] — mission briefing
- [[bugs-to-verify]] — known bugs (different category — those HAVE expected outcomes)
- [[reporting-template]] — morning report shape
