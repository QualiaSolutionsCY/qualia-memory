---
title: "Underdog Sales Webinar Registration System"
aliases: [underdog-webinar, underdogsales-webinar, zoom-webinar-integration, brevo-registration-flow, csp-supabase-edge-functions]
tags: [underdog-sales, zoom, brevo, supabase, edge-functions, deployment, webinar, csp, security]
sources:
  - "daily/2026-04-27.md"
created: 2026-04-27
updated: 2026-04-27
---

# Underdog Sales Webinar Registration System

A production webinar registration system deployed to underdogsales.com/webinar on 2026-04-27, integrating Zoom Webinar API, Brevo (formerly Sendinblue) for transactional emails and contact management, and a Supabase edge function as the registration backend. The system required 8 Supabase secrets and was deployed using the `vercel build && vercel deploy --prebuilt --prod` workaround due to a persistent Vercel integration provisioning bug on the `qualiasolutionscy` team.

## Key Points

- **Zoom webinar created** (ID: 84224264035) for May 5, 2026 at 18:00 Cyprus time — auto-approval enabled, cloud recording on, Q&A enabled, internal Zoom notification emails disabled (Brevo handles all user-facing emails)
- **Full e2e smoke test passed:** registration, duplicate handling, invalid email rejection, CORS preflight, DB insertion, Brevo contact creation, and email delivery all verified working in production
- **Vercel `--prebuilt` workaround required:** Normal `vercel --prod` deploys fail with "Blocked" / `readyStateReason: "Provisioning integrations failed"` due to broken Vercel Marketplace integration provisioning (Supabase/Groq/Upstash/Neon) on the `qualiasolutionscy` team since ~Apr 21, 2026
- **Navigation hidden on webinar pages:** Header hidden on `/webinar` and `/webinar/thank-you` using the same pattern as `/individuals` — focused conversion flow without site chrome
- **CSP hotfix required post-launch:** The RSVP form was silently blocked in production because the Supabase edge function URL (`https://*.supabase.co`) was not in the Content Security Policy's `connect-src` directive — CSP must be revisited whenever new external service integrations are added to a site
- **Secret rotation needed:** Zoom Client Secret was shared in chat during the session — must be rotated via `npx supabase secrets set` after rotating in Zoom Marketplace

## Details

The registration system architecture follows a three-service pattern: the Next.js frontend at underdogsales.com/webinar collects registrations, a Supabase edge function handles the backend processing (validating input, inserting into the database, registering with Zoom API, and creating a Brevo contact), and Brevo sends the confirmation email. This separation keeps the frontend stateless and the edge function handles all third-party API orchestration in a single atomic operation.

Eight Supabase secrets were configured for the edge function, covering Zoom API credentials (Client ID, Client Secret, Account ID), Brevo API key, and Supabase connection details. The Zoom webinar was configured with specific choices: auto-approval means registrants are immediately confirmed without manual review, cloud recording captures the webinar for later distribution, Q&A enables audience interaction, and internal Zoom notification emails were disabled because Brevo provides branded, customized email delivery rather than Zoom's generic templates.

The e2e smoke test methodology is worth noting as a reusable pattern for any registration system. The test covered seven vectors: (1) successful registration with valid data, (2) duplicate email rejection (same email should not register twice), (3) invalid email format rejection, (4) CORS preflight handling (the edge function must accept OPTIONS requests from the frontend domain), (5) database insertion verification (row exists in Supabase after registration), (6) Brevo contact creation (registrant appears in Brevo contacts), and (7) actual email delivery (confirmation email arrives in inbox). Testing all seven vectors in production before declaring the system shipped prevents the class of "works locally but fails in prod" bugs caused by missing CORS headers, incorrect secret values, or email provider configuration issues.

A minor CSS fix was also applied: the italic "S" in "$17M in Sales" gradient text was being clipped by the container. The fix was adding `inline-block pr-1` to give the italic glyph sufficient horizontal space — a common issue with italic text at the boundary of inline elements, especially when combined with CSS gradient text effects (`background-clip: text`).

A post-launch hotfix session (19:19) revealed a critical production blocker: the RSVP form submission was silently failing because the site's Content Security Policy did not include `https://*.supabase.co` in the `connect-src` directive. The CSP had been configured before the webinar feature was added, so the edge function URL was never whitelisted. The fix was a one-line CSP addition, but the broader lesson is that CSP policies must be audited whenever a new external service integration (Supabase edge functions, third-party APIs, etc.) is added to a site — the CSP was set up to protect existing functionality and does not automatically accommodate new endpoints.

In the same hotfix, the RSVP form was simplified: the separate first name and last name fields were merged into a single "Your Name" input, with the frontend splitting on whitespace at submit time so the edge function still receives both `first_name` and `last_name` fields. The `/webinar` route was also added to the standalone routes list in `LayoutShell.tsx` to hide the Navigation/Footer components — the same pattern used for `/individuals` and other focused conversion flows. The hotfix was shipped as a forced deploy (`--force`) since the project wasn't in the Qualia `polished` state. Remaining CSP warnings (GA remarketing pixel in `img-src`, Facebook pixel `form-action`, Facebook `frame-src`) were identified as cosmetic and non-blocking.

The `dangerouslySetInnerHTML` usage for JSON-LD structured data via `JSON.stringify` was audited and confirmed safe — `JSON.stringify` produces escaped output that cannot contain executable script content, making it a valid exception to the general prohibition on `dangerouslySetInnerHTML`.

The deploy process itself was notable because the standard `vercel --prod` command has been broken on the `qualiasolutionscy` Vercel team since approximately April 21, 2026. Every deploy attempt returns "Blocked" with `readyStateReason: "Provisioning integrations failed"`, caused by a Vercel Marketplace bug when provisioning integrated services (Supabase, Groq, Upstash, Neon). The workaround — `vercel build && vercel deploy --prebuilt --prod` — builds locally and uploads the prebuilt output, skipping the server-side integration provisioning step entirely. This affects all projects on the team, not just Underdog Sales. See [[concepts/vercel-deploy-patterns]] for the broader Vercel deploy gotcha catalog.

## Related Concepts

- [[concepts/vercel-deploy-patterns]] — The `--prebuilt` workaround for broken integration provisioning was discovered and documented during this deploy
- [[concepts/supabase-client-access-patterns]] — Supabase edge function secrets and RLS patterns apply to the registration backend
- [[concepts/qualia-portal-mcp-server]] — Another system using Supabase secrets for API authentication; same secret management discipline applies
- [[concepts/claude-code-operational-gotchas]] — Turbopack stale `.next` directory issue was discovered during this project's hotfix session
- [[concepts/sophia-lead-routing-debug]] — Another Supabase edge function deployment on the same day; both required post-deploy debugging

## Sources

- [[daily/2026-04-27.md]] — Session at 18:30: "Zoom webinar created (ID: 84224264035) for May 5, 2026 at 18:00 Cyprus time with auto-approval, cloud recording, Q&A enabled, internal Zoom emails disabled"
- [[daily/2026-04-27.md]] — Session at 18:30: "Full end-to-end smoke test passed: registration, duplicate handling, invalid email rejection, CORS preflight, DB insertion, Brevo contact creation, and email delivery all working"
- [[daily/2026-04-27.md]] — Session at 18:30: "Bypassed Vercel's broken integration provisioning using `vercel build && vercel deploy --prebuilt --prod`"
- [[daily/2026-04-27.md]] — Session at 18:30: "Rotate Zoom Client Secret — it was shared in chat"
- [[daily/2026-04-27.md]] — Session at 18:30: "The `qualiasolutionscy` Vercel team has this integration provisioning bug affecting all projects"
- [[daily/2026-04-27.md]] — Session at 19:19: "CSP errors blocking the RSVP form submission in production — Supabase edge function URL was not in the CSP `connect-src` directive"
- [[daily/2026-04-27.md]] — Session at 19:19: "Merged first/last name into a single 'Your Name' field, splitting on whitespace at submit time"
- [[daily/2026-04-27.md]] — Session at 19:19: "Shipped as forced hotfix (`--force`) since the project wasn't in the Qualia `polished` state"
