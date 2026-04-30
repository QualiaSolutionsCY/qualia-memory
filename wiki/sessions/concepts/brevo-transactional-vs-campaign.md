---
title: "Brevo Transactional vs Campaign Sends"
aliases: [brevo-merge-tags, brevo-templateId-gotcha, sendinblue-transactional, brevo-smtp-api]
tags: [brevo, email, transactional, gotcha, underdog-sales]
sources:
  - "daily/2026-04-29.md"
created: 2026-04-29
updated: 2026-04-29
---

# Brevo Transactional vs Campaign Sends

Brevo (formerly Sendinblue) has fundamentally different behavior between its transactional and campaign send APIs. The most critical difference: `templateId` with merge tags (`{{ contact.FIRSTNAME }}`, `{{ contact.* }}`) only resolves in campaign sends, NOT in transactional `/smtp/email` API calls. Transactional sends require explicit `sender`, `subject`, and `htmlContent` with all personalization pre-rendered by the application. This gotcha broke the Underdog Academy webinar signup flow on 2026-04-29 and was one of four stacked bugs that each masked the next.

## Key Points

- **Merge tags don't resolve in transactional sends:** `{{ contact.FIELD }}` placeholders in Brevo templates are only populated during campaign sends (which have access to the contact database). Transactional `/smtp/email` calls pass through templates verbatim, leaving merge tags as literal text in the delivered email
- **Transactional requires inline HTML:** When using the transactional API, the application must provide fully rendered `htmlContent` with all personalization baked in — no relying on Brevo's server-side template engine
- **templateId still works for layout:** You can use `templateId` in transactional sends for the email layout/wrapper, but any dynamic content must come from `params` (Brevo's transactional parameter system), NOT from `{{ contact.* }}` merge tags
- **Stacked bugs mask each other:** The webinar signup had 4 layers of bugs — fixing one revealed the next: (1) `NEXT_PUBLIC_*` env var stale after `vercel redeploy`, (2) Supabase Edge Function rejecting `sb_publishable_*` key format, (3) Brevo `templateId` merge tags not resolving, (4) `support@` email address replacement needed
- **Campaign sends (Emails 2-10) are unaffected:** Only Email 1 (the immediate registration confirmation) used the transactional API. Follow-up nurture emails sent via Brevo's campaign/automation system resolve merge tags normally

## Details

The bug was discovered on 2026-04-29 when webinar signups on underdogsales.com stopped working after changes from a previous session. The failure presented as a complete signup breakdown — users could submit the form but received no confirmation email and the registration didn't complete. Debugging revealed four stacked bugs, each hiding the next:

**Layer 1 — NEXT_PUBLIC env vars baked at build time:** After adding new environment variables on Vercel, `vercel redeploy` was used to push the changes. However, `NEXT_PUBLIC_*` variables are compiled into the JavaScript bundle at build time. A `vercel redeploy` reuses the existing build artifact, so the old (missing) values persisted. A fresh `vercel --prod` build was required to pick up the new values.

**Layer 2 — Supabase Edge Function key format:** The Supabase anon key was provided in the newer `sb_publishable_*` format. Supabase Edge Functions don't yet support this format — they require the classic JWT-format anon key (`eyJ...`). The edge function silently rejected the key, causing the entire registration flow to fail before reaching Brevo.

**Layer 3 — Brevo merge tag resolution:** Once the edge function was reachable, the registration completed but the confirmation email arrived with literal `{{ contact.FIRSTNAME }}` text instead of the user's name. The `templateId` approach worked perfectly for campaign-type sends but failed silently for transactional sends. The fix was to switch Email 1 from `templateId` to inline `htmlContent` with all personalization pre-rendered by the edge function.

**Layer 4 — Contact email update:** `support@underdogsales.com` was replaced with `gsc@underdogsales.com` across all user-facing copy — a content fix that was independent of the technical bugs but needed to ship in the same deploy.

The stacked-bug pattern itself is a reusable debugging lesson: when a complex flow breaks and the first fix doesn't resolve it, consider that multiple bugs may have been introduced simultaneously (or existed dormantly). Each fix peels back a layer to reveal the next failure. The diagnostic approach is: fix one layer, test, observe the new failure mode, fix the next layer. Do not assume the flow is broken for a single reason.

The distinction between Brevo's send modes is also relevant for any future project using Brevo: plan the email architecture around which sends are transactional (immediate, triggered by user action) vs campaign (scheduled, batch, or automation-triggered). Transactional sends are simpler but require the application to handle all personalization. Campaign sends leverage Brevo's full template engine but are not suitable for immediate confirmation emails.

## Related Concepts

- [[concepts/underdog-sales-webinar-system]] — The webinar system where this gotcha was discovered; the registration confirmation email was the affected flow
- [[concepts/qualia-email-routing]] — Qualia's own email routing uses Resend for transactional sends (similar send-only pattern) and Zoho Mail for interactive email — same architectural split between transactional and interactive
- [[concepts/claude-code-operational-gotchas]] — NEXT_PUBLIC env var baking and Supabase Edge Function JWT key requirement are documented there as operational gotchas
- [[connections/silent-failure-swallowing]] — The merge tags failing silently (rendering as literal text instead of erroring) is another instance of silent failure — the email "sends" but with wrong content

## Sources

- [[daily/2026-04-29.md]] — Session at 20:24: "Switched from Brevo `templateId` to inline HTML for Email 1 — Brevo's `{{ contact.* }}` merge tags only resolve in campaign sends, not transactional `/smtp/email` calls"
- [[daily/2026-04-29.md]] — Session at 20:24: "Stacked bugs: env var stale → key format rejected → merge tags unresolved → email address wrong. Each fix revealed the next layer"
- [[daily/2026-04-29.md]] — Session at 20:24: "`NEXT_PUBLIC_*` env vars are baked at build time on Vercel — `vercel redeploy` won't pick up new values, requires fresh `vercel --prod` build"
- [[daily/2026-04-29.md]] — Session at 20:24: "Supabase Edge Functions still require JWT anon key — `sb_publishable_*` format not accepted"
