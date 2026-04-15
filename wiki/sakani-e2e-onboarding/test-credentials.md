---
type: source
created: 2026-04-16
updated: 2026-04-16
tags: [sakani, e2e, credentials, test-users]
---

# Test Credentials — Users, OTP, Seed State

> Fixed identifiers for the overnight run. All OTPs are Supabase test phone OTPs — no real SMS is sent. Do not use these numbers in production.

## Test phone numbers (from M8-TESTING-CONTEXT)

| Phone (E.164) | Purpose | OTP code | Onboarding state at start |
|---|---|---|---|
| `+962700000000` | Staff admin (pre-seeded) | `123456` | Fully onboarded, `is_staff_admin = true` |
| `+962700000001` | Store reviewer (app store submissions) | `000000` | Pre-seeded building + roles for App Store review |
| `+962700000002` | Pre-seeded test owner | `123456` | Has completed onboarding, has unit |
| `+962700000003` | **FRESH user** — recommended for full-flow runs | `123456` | `onboarding_step = 0`, no roles, no memberships |
| `+962700000004` | Secondary fresh user | `123456` | Variable — check DB before run |

## Recommended strategy for the overnight run

- **Start with `+962700000003`** for full flow (auth → profile → claim unit or setup request → staff approve → verify role). This is the cleanest fresh slate.
- **Use `+962700000000`** ONLY for staff-side actions (log into the admin console, approve/reject/return). Never log in as staff on the mobile app for an identity flow — you'll pollute the test user's state.
- **After completing a flow on `+962700000003`, DO NOT reset the user state.** The next test run (in a future session) will re-use the same phone and can observe historical state. If a specific test needs a truly fresh user, use `+962700000004` or create a new synthetic phone via Supabase Auth (`+96270XXXXXXX` format) but note it in [[run-log]].

## Staff admin access (admin console)

To log into `http://localhost:3000` (or wherever the admin is running):

1. Navigate to the admin sign-in route
2. Enter phone `+962700000000`
3. Enter OTP `123456`
4. Verify you land on the verification queue dashboard
5. Check that the session shows staff badge / staff menu items

If staff login fails (admin console denies sign-in even with correct OTP), check:
- `profiles.is_staff_admin` flag is `true` for this user
- The admin console is using the same Supabase instance as the mobile app
- JWT `app_metadata.is_staff_admin` claim is being populated server-side

## Important: OTP retries and rate limiting

Supabase test phone numbers **skip real SMS** and always accept their hardcoded OTP. But the client-side `check_phone_onboarded` RPC still fires, and OTP rate-limit policies still apply per FR-3.1.15A / D1:

- Max 5 resends per session
- Max 5 verify attempts before lockout
- 1-hour lockout after exceeding
- 20 OTP requests per phone per day

If you hit a lockout during the run, mark the affected tests BLOCKED and move on — don't try to force through with other phone numbers (that pollutes real Supabase users).

## Database seed state (as of 2026-04-13, pre-M9)

Per `.planning/M8-TESTING-CONTEXT.md`:
- 5 profiles (the 5 test users above)
- 0 buildings
- 0 units
- 0 building_role_assignments
- 1 unit_claim_request (SUBMITTED, match_outcome = ZERO)
- 1 building_setup_request
- 1 Goshan document uploaded

**This may have changed since 2026-04-13.** Before your run, check the state with:

```sql
SELECT
  (SELECT count(*) FROM profiles) as profiles,
  (SELECT count(*) FROM buildings) as buildings,
  (SELECT count(*) FROM units) as units,
  (SELECT count(*) FROM building_role_assignment WHERE status = 'ACTIVE') as active_roles,
  (SELECT count(*) FROM unit_membership WHERE status = 'ACTIVE') as active_memberships,
  (SELECT count(*) FROM unit_claim_request WHERE status IN ('SUBMITTED', 'RETURNED_FOR_FIX')) as pending_claims,
  (SELECT count(*) FROM building_setup_request WHERE status IN ('SUBMITTED', 'RETURNED_FOR_FIX')) as pending_setups;
```

Record the baseline in [[run-log]] as your first entry so the morning report can show before-vs-after counts.

## Test documents (for upload flows)

Native file pickers can't be driven by Playwright. For any test requiring a Goshan upload, National ID upload, or Permit upload:

- **If mobile (native):** flow is BLOCKED on Playwright — mark accordingly
- **If Expo web:** you MAY be able to attach a file via the browser's file input if Expo exposes it; try it once, record result
- **If admin console:** admin side only reads existing documents — no upload from admin

Suggested test fixtures (if they exist in repo):
- `apps/mobile/assets/test/goshan-sample.jpg` — check if exists
- `apps/mobile/assets/test/national-id-front.jpg` / `back.jpg` — check if exists

If no fixtures exist, create dummy 1x1 JPEG bytes at runtime and upload them — parsing will fail but the UPLOAD itself can be verified.

## Related

- [[README]] — mission briefing
- [[environment]] — dev server check
- [[bugs-to-verify]] — which flows are known-broken so you don't waste a test slot
