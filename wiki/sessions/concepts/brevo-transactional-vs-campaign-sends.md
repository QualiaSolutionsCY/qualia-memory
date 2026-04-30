---
title: "Brevo Transactional vs Campaign Sends"
aliases: [brevo-template-gotcha, brevo-merge-tags, sendinblue-transactional, smtp-email-api]
tags: [brevo, email, transactional, gotcha, underdog-sales]
sources:
  - "daily/2026-04-29.md"
created: 2026-04-29
updated: 2026-04-29
---

# Brevo Transactional vs Campaign Sends

Brevo (formerly Sendinblue) has a fundamental distinction between transactional and campaign email sends that is not obvious from the API surface. The transactional `/smtp/email` endpoint does NOT resolve `{{ contact.* }}` merge tags even when a `templateId` is provided — merge tags only resolve in campaign sends. This caused the Underdog Sales webinar confirmation email to arrive with raw `{{ contact.FIRSTNAME }}` placeholders instead of the registrant's name, silently breaking the entire signup flow.

## Key Points

- **`templateId` with merge tags only works in campaigns:** Brevo's transactional `/smtp/email` endpoint accepts `templateId` but does NOT resolve `{{ contact.FIELD }}` merge tags — the email arrives with raw placeholder text
- **Transactional sends require explicit fields:** `sender`, `subject`, and `htmlContent` must be provided inline — even when a `templateId` is specified, merge tags are not interpolated
- **Fix:** Switch from `templateId` to inline HTML for transactional Email 1 (registration confirmation); Emails 2–10 (campaign-triggered drip sequence) continue using templates with merge tags correctly
- **Stacked bugs masked this:** The Brevo template issue was the third of four stacked bugs — an env var build-time baking issue and a Supabase key format issue both had to be fixed first before the Brevo bug became visible
- **`gsc@underdogsales.com` replaced `support@`:** All user-facing email copy was updated to use the correct sender address

## Details

The bug was discovered on 2026-04-29 when the Underdog Sales webinar signup flow stopped working after a previous session's changes. Debugging revealed four stacked bugs, each masking the next:

1. **`NEXT_PUBLIC_*` env var baking:** Environment variables prefixed with `NEXT_PUBLIC_` are embedded into the JavaScript bundle at build time on Vercel. Running `vercel redeploy` (which reuses the existing build artifact) does NOT pick up new env var values — a fresh `vercel --prod` build is required. The Supabase URL env var had been updated but wasn't reflected in the deployed code because only a redeploy (not a rebuild) was triggered.

2. **Supabase Edge Function key format:** The newer `sb_publishable_*` key format is not accepted by Supabase Edge Functions — they still require the classic JWT-format `eyJ...` anon key. The edge function was configured with the new-format key after a project update, causing all function invocations to fail with auth errors.

3. **Brevo merge tag resolution:** After fixing the first two issues, emails were being sent but arrived with raw `{{ contact.FIRSTNAME }}` placeholders. The transactional `/smtp/email` endpoint was using `templateId` to reference a Brevo template containing merge tags, but transactional sends don't resolve these tags — only campaign sends do.

4. **Incorrect sender address:** `support@underdogsales.com` was used in email copy but the correct address is `gsc@underdogsales.com`.

Each fix revealed the next layer of failure. This cascading pattern is distinct from the [[connections/silent-failure-swallowing|silent failure swallowing]] pattern: in that pattern, one failure is silently absorbed and the system appears to work. Here, the system visibly fails, but the first visible failure masks deeper ones — fixing it doesn't restore functionality, it just reveals the next bug.

The practical lesson for any Brevo integration: if the email is triggered programmatically (API call, webhook, edge function), it's a transactional send. Use inline `htmlContent` with string interpolation for dynamic fields. Reserve `templateId` with merge tags for campaign-style sends triggered through Brevo's automation workflows, where the send context has access to the contact record's fields.

A secondary change was made in the same session: the sprint popup (a promotional overlay) was excluded from `/webinar/*` paths to avoid distracting from the RSVP conversion flow. This follows the same focused-conversion pattern as hiding the navigation header on webinar pages.

## Related Concepts

- [[concepts/underdog-sales-webinar-system]] — The webinar registration system where this bug manifested; the original deployment on 2026-04-27 passed all 7 e2e smoke test vectors, but the merge tag issue only surfaced after subsequent changes triggered a redeploy
- [[concepts/vercel-deploy-patterns]] — `NEXT_PUBLIC_*` build-time baking was the first bug in the stack; `vercel redeploy` vs `vercel --prod` distinction now documented there
- [[connections/supabase-tooling-escape-hatches]] — Supabase Edge Function JWT key format requirement is a fifth escape hatch in the same pattern
- [[concepts/qualia-email-routing]] — Qualia's own email split (Resend transactional + Zoho inbox) follows the same transactional vs interactive pattern at the provider level

## Sources

- [[daily/2026-04-29.md]] — Session at 20:24: "Brevo's `{{ contact.* }}` merge tags only resolve in campaign sends, not transactional `/smtp/email` calls"
- [[daily/2026-04-29.md]] — Session at 20:24: "Switched from Brevo `templateId` to inline HTML for Email 1 (transactional send) — Emails 2–10 (campaigns) still use templates fine"
- [[daily/2026-04-29.md]] — Session at 20:24: "4 stacked bugs causing the failure, each masked by the next: env var baking → key format → Brevo template → email sender"
- [[daily/2026-04-29.md]] — Session at 20:24: "Supabase Edge Functions don't support the new `sb_publishable_*` key format — must use classic JWT-format `eyJ...` anon key"
- [[daily/2026-04-29.md]] — Session at 20:24: "`NEXT_PUBLIC_*` env vars are baked at build time on Vercel — `vercel redeploy` reuses the build artifact and won't pick up new values"
