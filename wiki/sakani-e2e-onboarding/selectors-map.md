---
type: source
created: 2026-04-16
updated: 2026-04-16
tags: [sakani, e2e, selectors, playwright, automation]
---

# Selectors Map — UI Targets for Automation

> Where to click, type, and read for each onboarding flow. Populated from the post-M9 codebase map (2026-04-16). Use role-based selectors first, fallback to accessible names, last resort is `data-testid`.

## Selector strategy (in priority order)

1. **`getByRole('button', { name: /let's go/i })`** — semantic, most resilient
2. **`getByLabel('Phone number')`** — form inputs via label
3. **`getByPlaceholder('07XXXXXXXX')`** — fallback for unlabeled inputs
4. **`getByText('Verify your ownership')`** — text content for headings/copy
5. **`locator('[data-testid="..."]')`** — when nothing else works

**Do NOT use:**
- Positional selectors (`nth-child`, `first()`, `last()`) — they break on reflow
- Long CSS paths (`div > section > div.container > button.primary`) — brittle
- Class-name selectors (styled-components hashes change) — unstable

---

## 1. Auth flow (W1)

### Mobile sign-in screen — `apps/mobile/app/(auth)/sign-in.tsx`

| Element | Suggested selector | Notes |
|---|---|---|
| Phone input | `getByLabel(/phone/i)` or placeholder `07XXXXXXXX` / `+9627XXXXXXXX` | Bilingual label. `normalizeJordanPhone()` runs client-side. |
| "Send OTP" button | `getByRole('button', { name: /send otp|ارسل|continue/i })` | Label changes with language |
| OTP input (6 digits) | 6 segmented boxes or single `input[type="number" maxlength="6"]` | M9 auto-submits on 6 chars — no explicit submit tap needed |
| "Resend code" link | `getByRole('button', { name: /resend|أرسل مرة أخرى/i })` | Disabled during cooldown |
| Language toggle (top right) | `getByRole('button', { name: /english|عربي/i })` | Triggers alert to restart app |

### Legacy `verify-otp.tsx`
- DEAD CODE (B7). Do NOT navigate here during testing. If you end up on this screen, it's a routing bug.

---

## 2. Onboarding profile + role + transition — `apps/mobile/app/(app)/onboarding/index.tsx`

State machine: `profile` → `role` → `transition`

### Step 1 — profile

| Element | Suggested selector | Required? |
|---|---|---|
| Arabic name field | `getByLabel(/الاسم العربي|arabic name/i)` or `getByPlaceholder(/اسمك بالعربي/i)` | Yes, Arabic-only regex filter |
| English name field | `getByLabel(/english name/i)` | Yes |
| Date of birth picker | Button opens custom calendar | Yes, min year 1930 |
| Gender segmented control | `getByRole('button', { name: /male|ذكر/i })` + FEMALE counterpart | Yes |
| Legal consent checkbox | `getByRole('checkbox')` or label text | Yes, gates button |
| **Warning (B3/P1):** Tapping the checkbox can open the Privacy Policy link instead. Test direct checkbox tap; if it opens the link, record FAIL for P1. |
| "Continue" button | `getByRole('button', { name: /continue|متابعة|next/i })` | Disabled until all fields + consent |

### Step 2 — role selection

| Element | Suggested selector | Notes |
|---|---|---|
| Role card list | `getByRole('button', { name: /unit owner|مالك/i })` | **B3 LIVE:** only `owner` card rendered. `bm`, `tenant`, `guard` cards NOT in DOM. |
| "Continue" button | `getByRole('button', { name: /continue|let's go/i })` | Always enabled (owner pre-selected) |

### Step 3 — transition

| Element | Notes |
|---|---|
| "Let's go" button | Routes by role: owner → `/(app)/claim-unit/`, bm → `/(app)/setup-request/`, tenant/guard → `/(app)/(tabs)/` |
| Tap triggers `onboarding_step = 2` in DB | Verify this write after tap |

---

## 3. Building setup request (BM path W2-A / W3)

### `apps/mobile/app/(app)/setup-request/index.tsx` (submit)

| Element | Suggested selector | Notes |
|---|---|---|
| Building name (EN) | `getByLabel(/building name|english/i)` | **M9 change:** Now optional. Server fallback fills from basin+plot or street. |
| Building name (AR) | `getByLabel(/arabic name/i)` | Also optional M9 |
| Building type segmented | RESIDENTIAL / COMMERCIAL / MIXED buttons | Required |
| Governorate picker | Dropdown of 12 Jordan governorates | Required |
| District | Free text | Required |
| Street | Free text | Required |
| Building number | Free text | Optional |
| Floor editor | Complex widget — add/remove floors + unit counts | Required ≥1 residential floor |
| Occupation permit upload | File picker button | Optional (PDF/JPEG/PNG/HEIC, 10 MB) |
| "Submit" button | `getByRole('button', { name: /submit/i })` | |

### `apps/mobile/app/(app)/setup-request/[id].tsx` (status / resubmit)

| Element | Notes |
|---|---|
| Status badge | SUBMITTED / IN REVIEW / RETURNED_FOR_FIX / APPROVED / REJECTED |
| Edit fields (when RETURNED_FOR_FIX only) | Name + address only — NOT the floor structure editor |
| "Resubmit" button | Sends PATCH `/buildings/setup-requests/:id/resubmit` |

### Admin setup-request queue — `apps/admin/src/app/(dashboard)/verification/setup-requests/page.tsx`

| Element | Notes |
|---|---|
| Queue table | **M9 filter:** `requestType: 'BM_SELF_SERVICE'` — OWNER_ASSISTED submissions are NOT shown here, they're under claim-requests |
| Detail link | Each row → `[id]/page.tsx` |

### Admin setup-request detail — `apps/admin/src/app/(dashboard)/verification/setup-requests/[id]/page.tsx`

| Element | Notes |
|---|---|
| **B2 LIVE:** building info fields likely show `—` because interface uses wrong JSONB field names | Screenshot if still broken |
| Review dialog | Three buttons — Approve / Return for Fix / Reject |

### Admin ReviewDialog — `apps/admin/src/components/verification/ReviewDialog.tsx`

| Element | Required for action |
|---|---|
| Notes textarea | REQUIRED for REJECT, optional for RETURN_FOR_FIX |
| Approve button | `getByRole('button', { name: /approve/i })` |
| Return for Fix button | `getByRole('button', { name: /return for fix/i })` |
| Reject button | `getByRole('button', { name: /reject/i })` |

---

## 4. Unit claim request (W4 / W4-A / OWNER_ASSISTED)

### `apps/mobile/app/(app)/claim-unit/index.tsx` (submit)

M9-era flow: Upload Goshan → parse → match → route (MATCH / AMBIGUOUS / NOT_FOUND → OWNER_ASSISTED)

| Element | Suggested selector | Notes |
|---|---|---|
| "Upload Goshan" button | Opens platform modal (Take Photo / Library / Files) | NATIVE file picker — Playwright cannot drive |
| Parsed fields preview | After parse completes | Auto-filled: floor_no, floor_seq_no, owner_name_ar, area_name_ar, basin_name, plot_number, goshan_issue_date, second_owner_name_ar |
| Building selector (MATCH path) | Dropdown of matching buildings | From `/buildings/match` response |
| Floor + unit number fields | Text inputs, auto-filled from parse | Editable |
| "I'm the owner, not the BM" button (NOT_FOUND path) | Routes to OWNER_ASSISTED form | New M9 path |
| National ID front upload | Optional | M9: auto-parses on both uploads |
| National ID back upload | Optional | Auto-triggers `parseNationalIdApi` when both done |
| Review modal | Shows all parsed data for confirmation | Button: "Submit claim" |
| Success screen (OWNER_ASSISTED) | **B11 LIVE:** full-screen but back-navigatable — duplicate submit risk | |

### `apps/mobile/app/(app)/claim-unit/[id].tsx` (status)

| Element | Notes |
|---|---|
| Status: SUBMITTED / RETURNED_FOR_FIX / APPROVED / REJECTED | |
| Match outcome display | ZERO / ONE / MULTI_MATCH |
| Resubmit button | When RETURNED_FOR_FIX |

### Admin claim-request queue — `apps/admin/src/app/(dashboard)/verification/claim-requests/page.tsx`

| Element | Notes |
|---|---|
| Queue table showing claims | Includes OWNER_ASSISTED as of M9 |
| Filter chips | Status filters |

### Admin claim-request detail — `apps/admin/src/app/(dashboard)/verification/claim-requests/[id]/page.tsx`

| Element | Notes |
|---|---|
| Applicant details | Name, phone |
| **GoshanDocumentViewer (M9)** | B6 partial fix — renders Goshan image preview |
| Extracted data panel | From parse-goshan response |
| OWNER_ASSISTED badge | If `extracted_data.building_setup_request_id` is set |
| Owner-submitted building form | Shown side-by-side with extracted data (M9) |
| "Setup building + approve claim" button (M9 one-step) | ONE click does both actions for OWNER_ASSISTED claims |
| ReviewDialog | Normal approve/reject/return for non-OA claims |

---

## 5. Tenant invite (W5) — post-onboarding

Not in the initial onboarding flow. Requires OWNER_VERIFIED state.

| Element | Location | Notes |
|---|---|---|
| "Invite tenant" button | Mobile owner home or unit detail | **B8 LIVE:** likely denied — permission not seeded |
| Invite form | Fields: phone, expiry, max-use | |
| Invite link/token display | After creation | |

---

## 6. Guard invite (W12) — post-onboarding

Not in the initial onboarding flow. Requires BM_VERIFIED.

| Element | Notes |
|---|---|
| "Invite guard" button (mobile BM home or building management) | BM only |
| Guard activation step | BM confirms explicitly — one ACTIVE guard per building (EH-6.13) |

---

## 7. Language + RTL

| Element | Location | Notes |
|---|---|---|
| Language toggle | Sign-in screen top-right | Alert: "restart app" after change |
| RTL layout | Triggered by Arabic locale | **KNOWN LIMITATION:** Expo web stays LTR even with Arabic text. Only native build (APK / Expo Go) shows true RTL. |

---

## Global admin navigation

| Element | URL |
|---|---|
| Sign-in | `http://localhost:3000/auth/sign-in` (or similar) |
| Dashboard root | `http://localhost:3000/dashboard` |
| Verification queues parent | `http://localhost:3000/dashboard/verification` |
| Setup-requests queue | `http://localhost:3000/dashboard/verification/setup-requests` |
| Claim-requests queue | `http://localhost:3000/dashboard/verification/claim-requests` |
| Admin-cases | `http://localhost:3000/dashboard/financial/admin-cases` — **OUT OF SCOPE** (financial path) |

---

## What to do when a selector doesn't work

1. **Log a "tooling gap" in [[run-log]]** with the flow, the intended selector, and what you observed
2. **Try the fallback chain** (role → label → placeholder → text → data-testid)
3. **Try the lowest-tech fallback:** take a screenshot, read the DOM via MCP's `browser_snapshot` or equivalent, find the element, and write down what selector SHOULD work
4. **Never fudge a PASS** — if you can't click the button reliably, the test is BLOCKED, not PASS

## Related

- [[README]] — mission briefing
- [[environment]] — dev server URLs and ports
- [[bugs-to-verify]] — bugs tagged B1–B15 referenced by ID above
- [[run-log]] — append tooling gaps here
