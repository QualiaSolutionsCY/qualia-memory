---
type: source
created: 2026-04-16
updated: 2026-04-16
tags: [sakani, e2e, bugs, regression-check]
---

# Bugs to Verify — Post-M9 State

> Known bugs from the M8 gap report (2026-04-13), the client feedback round 3 (2026-04-15), and the post-M9 codebase map (2026-04-16). For each, the agent must verify the current state and record RESOLVED / STILL OPEN / PARTIALLY FIXED.

## Format for each entry

```
- **[B#] Short title** — severity · area
  - **Symptom:** what the user sees
  - **Root cause:** where in the code
  - **Expected state after M9:** whether it should be fixed
  - **How to verify:** exact steps for the agent
  - **On FAIL, record:** what evidence to capture
```

---

## CRITICAL — block launch if still open

### [B1] `unit_claim_request.unit_id NOT NULL` blocks Path A claims
- Severity: CRITICAL · Area: DB + server
- **Symptom:** Any claim submit where building cannot be matched (NOT_FOUND path) returns a 500 with Postgres constraint violation `unit_id cannot be null`.
- **Root cause:** Migration `20260319000008_requests.sql` defined `unit_id NOT NULL REFERENCES public.units(id)`.
- **Expected state after M9:** RESOLVED. Migration `20260414000001` drops the NOT NULL constraint.
- **How to verify:** As `+962700000003`, upload a Goshan that references a building NOT in the DB. Observe: claim submit succeeds, `unit_claim_request.unit_id` is NULL, match_outcome = `ZERO` or NOT_FOUND.
- **On FAIL, record:** full error response body, SQL error from server log, the migration files in `supabase/migrations/` starting with `20260414`.

### [B3] BM role hidden in role-selection step
- Severity: HIGH · Area: mobile UX
- **Symptom:** Fresh user reaches the role selection step after profile and only sees the "Unit Owner" card. BM / Tenant / Guard cards are not rendered at all. W2 (BM onboarding) is inaccessible via standard mobile flow.
- **Root cause:** `apps/mobile/app/(app)/onboarding/index.tsx` line ~119: `const roleList: RoleChoice[] = ['owner'];`
- **Expected state after M9:** STILL OPEN (not addressed in M9 phase 1).
- **How to verify:** Sign up as `+962700000003`, complete profile step. On the role selection step, count the visible role cards. If only 1 card visible → STILL OPEN.
- **On FAIL, record:** screenshot of role selection, the roleList constant value from the source file.

### [B4] JWT refresh flag ignored by mobile client
- Severity: HIGH · Area: mobile + server handshake
- **Symptom:** After staff approves a BM setup request or an owner claim, the backend returns `requires_jwt_refresh: true`. The mobile client does not act on this. The approved user sees stale roles for up to 1 hour until the token naturally rotates.
- **Root cause:** No handler in `apps/mobile/app/(app)/claim-unit/[id].tsx` or `setup-request/[id].tsx` that detects this flag and calls `supabase.auth.refreshSession()`.
- **Expected state after M9:** STILL OPEN.
- **How to verify:** Submit a claim as `+962700000003`. Approve it from the admin console as `+962700000000`. Return to the mobile app (do NOT restart). Check: does `useProfile().role` show `OWNER_VERIFIED`? If not → STILL OPEN. Check DB: `unit_membership` row should exist with role OWNER_VERIFIED.
- **On FAIL, record:** the JWT claims (decode the access token), the DB state, the UI state (screenshot).

### [B2] Admin setup-request detail page field name mismatch
- Severity: HIGH · Area: admin console
- **Symptom:** Staff viewing a setup request detail page sees `—` (em dash) for most building information fields. Building name, address, floor count, unit count all show empty.
- **Root cause:** `apps/admin/src/app/(dashboard)/verification/setup-requests/[id]/page.tsx` declares `SetupRequestDetail` interface with field names `building_name_ar`, `building_name_en`, `city`, `number_of_floors`, `units_per_floor`. The actual `building_data` JSONB uses `name_ar`, `name_en`, `address.governorate`, and a nested `structure` object.
- **Expected state after M9:** STILL OPEN.
- **How to verify:** Log into admin as `+962700000000`. Navigate to Verification → Setup Requests → any detail page. If fields show `—` instead of actual building data → STILL OPEN.
- **On FAIL, record:** screenshot of the detail page, the raw `building_data` JSONB fetched from the DB for comparison.

### [Goshan "1 1"] Parser defaults floor/seq to "1 1" and errors
- Severity: HIGH · Area: mobile + edge function
- **Symptom:** When uploading a Goshan, the parser always returns `floor_no: 1, floor_seq_no: 1` even when the document clearly shows different values, then the claim submit errors because the values are invalid for the building.
- **Reported:** Client feedback 2026-04-11 (CLIENT-FEEDBACK-2026-04-11.md line 186)
- **Expected state after M9:** Likely STILL OPEN — M9 changed upstream match logic but not the parser itself. The `parse-goshan` edge function source is NOT in the repo (deployed externally).
- **How to verify:** Upload a clean Goshan image with clearly visible non-1-1 unit numbers. Check the parse response: are floor_no and floor_seq_no extracted correctly? Or both "1"?
- **On FAIL, record:** the Goshan image, the parse response JSON, and a note that the edge function needs investigation outside this repo.

### [P1/P2] Consent checkbox overlap with Privacy Policy link
- Severity: MEDIUM · Area: mobile UX
- **Symptom:** On the profile step, the legal consent checkbox's tap target overlaps the "Privacy Policy" hyperlink text. Tapping the checkbox sometimes opens the privacy link instead of toggling the checkbox. The link points to `getsakani.com/privacy` which returns 404.
- **Reported:** M8-PLAYWRIGHT-VERIFICATION line 34, CLIENT-FEEDBACK-2026-04-15-AUDIT
- **Expected state after M9:** STILL OPEN.
- **How to verify:** Tap the center of the consent checkbox (not the label, not the link). Does the checkbox toggle cleanly? Navigate to `getsakani.com/privacy` — does it return 200 or 404?
- **On FAIL, record:** screenshot of tap area, curl of the privacy URL.

---

## HIGH — ship-blocking but not data-destructive

### [B5] Setup request approval runs 6 sequential ops without a DB transaction
- Severity: MEDIUM-HIGH · Area: server data integrity
- **Symptom:** If step 2 (bulk insert units) succeeds but step 3 (insert building_role_assignment) fails, the building exists with units but no BM_VERIFIED assignment. Partial state.
- **Root cause:** `server/src/modules/buildings/building-setup-request.service.ts` — 6-step sequential operation, no `BEGIN; COMMIT;` wrapper or DB transaction.
- **Expected state after M9:** STILL OPEN.
- **How to verify:** This is hard to test in normal flow. Observation: after a SUCCESSFUL approval, verify DB has a matching `building_role_assignment` row. For the bug itself, it only manifests under partial failure — flag as "architectural risk, cannot E2E-verify in normal run".
- **On FAIL, record:** architectural note; no action needed unless you encounter partial-state data during the run.

### [B6] Goshan document preview missing on admin setup-request detail
- Severity: MEDIUM · Area: admin
- **Symptom:** Staff reviewing a setup request cannot see the uploaded permit document. Only a document ID is shown, no link or embed.
- **Expected state after M9:** PARTIALLY FIXED. `GoshanDocumentViewer` was added to the **claim-requests** detail page (M9 phase 1). Setup-request detail page still lacks the permit viewer.
- **How to verify:** Open a claim-requests detail → verify Goshan image preview is visible. Open a setup-requests detail → verify permit document preview is visible (or confirm it's still missing).
- **On FAIL, record:** screenshots of both detail pages.

### [B8] OWNER_VERIFIED cannot create tenant invites
- Severity: HIGH · Area: permission seeding
- **Symptom:** A verified owner attempts to create a tenant invite. The request is denied with AUTHZ_DENIED because the permission `users:invite:tenant` is not seeded on the OWNER_VERIFIED role.
- **Expected state after M9:** Likely STILL OPEN (not mentioned in M9 commits).
- **How to verify:** Bring a user to OWNER_VERIFIED state (submit + approve a claim). Attempt to create a tenant invite via `POST /tenant-invites` or the mobile UI. Expected: success. Observed: likely 403 AUTHZ_DENIED.
- **On FAIL, record:** full request + response, plus the output of `SELECT * FROM role_permission WHERE role = 'OWNER_VERIFIED'`.

### [B10] OWNER_VERIFIED cannot view guard calendar
- Severity: MEDIUM · Area: permission seeding
- **Symptom:** Same root cause family as B8 — permission `guard:tasks:view` is not seeded for OWNER_VERIFIED.
- **Expected state after M9:** Likely STILL OPEN.
- **How to verify:** As OWNER_VERIFIED, attempt `GET /guard/calendar` for the building. Expected: success with busy/free data. Observed: likely 403.
- **On FAIL, record:** request + response.

---

## M9-ERA NEW BUGS (discovered by codebase audit 2026-04-16)

### [B11] OWNER_ASSISTED success screen can be navigated away from
- Severity: HIGH · Area: mobile UX
- **Symptom:** After successfully submitting an OWNER_ASSISTED claim, the user lands on a full-screen success view. If the user hits back or navigates, the claim form is still in a partially-filled state and can be resubmitted. Duplicate claims possible.
- **Location:** `apps/mobile/app/(app)/claim-unit/index.tsx:766-820`
- **How to verify:** Submit an OWNER_ASSISTED claim, see the success screen, then navigate back. Can you see and resubmit the claim form? Can you submit again and get a second `unit_claim_request` row?
- **On FAIL, record:** the two DB rows, the UI behavior.

### [B12] Match API silently falls back to NOT_FOUND on network error
- Severity: HIGH · Area: mobile
- **Symptom:** When the mobile client calls `GET /buildings/match?basin_name=X&plot_number=Y` and the request times out or errors, the client treats it as `outcome: NOT_FOUND` and routes the user to the OWNER_ASSISTED setup path. The user thinks the building doesn't exist when it actually does — but the call failed.
- **Location:** `apps/mobile/app/(app)/claim-unit/index.tsx:473-499`
- **How to verify:** Block the server temporarily (kill `pnpm dev:server`), trigger a Goshan parse that would match an existing building, observe that the UI routes to NOT_FOUND / OWNER_ASSISTED instead of showing a network error.
- **On FAIL, record:** the request that failed, the UI outcome.

### [B13] National ID verification mismatch doesn't block submit
- Severity: MEDIUM · Area: mobile
- **Symptom:** When the parsed national ID from the ID-card scan doesn't match any national ID extracted from the Goshan, the UI shows a toast but the user can still proceed to submit the claim with unverified identity.
- **Location:** `apps/mobile/app/(app)/claim-unit/index.tsx:412-417`
- **How to verify:** Upload a Goshan with known national IDs. Upload a National ID card with a DIFFERENT national ID number. Observe: toast appears, but submit button remains enabled.
- **On FAIL, record:** toast text, submit button state, final DB row.

### [B14] OWNER_ASSISTED admin display may not round-trip on edit
- Severity: MEDIUM · Area: admin
- **Symptom:** When OWNER_ASSISTED claims are displayed in the admin claim-requests detail page, the linked owner-submitted building_data JSON is shown. If an admin edits these fields, the field names may not match the schema and updates get lost.
- **How to verify:** Open an OWNER_ASSISTED claim in admin. Attempt to edit any of the building fields (if the UI allows). Save. Re-fetch. Do the values persist?
- **On FAIL, record:** before/after JSON.

### [B15] OWNER_ASSISTED auto-link not surfaced in audit or response
- Severity: MEDIUM · Area: server
- **Symptom:** When approve flow auto-links an OWNER_ASSISTED claim to an existing building via `match_building_by_goshan`, the admin gets no explicit confirmation of WHY the auto-link happened. Audit log has no dedicated event.
- **Location:** `server/src/modules/buildings/building-setup-request.service.ts:428-449`
- **How to verify:** Submit an OWNER_ASSISTED claim for a building that already exists. Approve it. Check `audit_log` for an entry explaining the auto-link match. Check the approval API response for a `linked_to_existing_building` flag.
- **On FAIL, record:** audit log entries around the approval timestamp.

---

## RESOLVED AS OF M9 (verify these are still fixed)

### [B1-fixed] Path A claim submission
Covered above. Verify Path A works.

### [B6-partial] Goshan viewer in admin claim detail
Covered above. Verify it renders.

---

## Lower priority / dead code

### [B7] Legacy `verify-otp.tsx` screen
Dead code in `apps/mobile/app/(auth)/verify-otp.tsx`. Main flow inlines OTP in sign-in.tsx. Verify you don't accidentally route to it during testing — if you do, it means a deep link or navigation bug exists.

### [B8-profile] `marital_status` and `number_of_kids` dead columns
Columns exist in `profiles` table but are never written. If your run sees non-null values in either column on the test users, flag it — something is writing to a supposedly dead column.

### [B9] Silent profile update error
If the profile update on step 1 fails, the mobile client only `console.error`s. The user proceeds to step 2 as if nothing happened, but `onboarding_step` is NOT set. If you observe a user moving past the profile step without `onboarding_step = 1` in the DB, you've hit this.

### [B10-rejection] `rejection_reason_code` passed as free text
Admin `ReviewDialog` sends the same string as both `notes` and `rejection_reason_code`. No enum enforced. Note if observed.

---

## Reporting format for this file

In [[run-log]], use the bug ID as a tag:

```
[23:41:02] [B1] VERIFIED_FIXED — Path A claim accepted, unit_id null persisted in DB
[23:47:18] [B3] STILL_OPEN — confirmed only 'owner' card rendered on role step
[23:52:44] [B4] STILL_OPEN — JWT after approval still shows old claims; restart cycled token
```

## Related

- [[README]] — mission briefing
- [[run-log]] — where you record findings
- [[reporting-template]] — morning summary format
