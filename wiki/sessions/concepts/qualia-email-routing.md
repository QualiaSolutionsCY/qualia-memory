---
title: "Qualia Email Routing"
aliases: [email-routing, resend-vs-zoho, bookings-email, qualiasolutions-email]
tags: [infrastructure, email, resend, zoho, qualiasolutions]
sources:
  - "daily/2026-04-29.md"
created: 2026-04-29
updated: 2026-04-29
---

# Qualia Email Routing

Email sending for qualiasolutions.net is split across two services: **Zoho Mail** handles `info@qualiasolutions.net` (inbox, replies, general correspondence) while **Resend** handles `bookings@qualiasolutions.net` (send-only transactional emails triggered from the contact page and booking confirmations). The Resend API key lives in the Vercel project env vars for `qualiasolutions-net`.

## Key Points

- **`bookings@qualiasolutions.net`** is send-only via Resend — no inbox; reply-to is set to `info@qualiasolutions.net`
- **`info@qualiasolutions.net`** is the only address configured in Zoho Mail — full inbox with send/receive
- **Resend API key location:** Vercel env vars for the `qualiasolutions-net` project (pull via `vercel env pull`)
- **Booking confirmation template:** Dark glass styling with teal accent, matching the qualiasolutions.net site design
- **If two-way booking email is needed,** `bookings@qualiasolutions.net` would need to be added as a Zoho alias — currently it exists only as a Resend sending identity

## Details

The split was discovered on 2026-04-29 when a booking confirmation email needed to be sent manually for a Panther x Qualia Solutions meeting (Wed Apr 29, 5-6 PM Asia/Nicosia timezone, Google Meet). The initial attempt to send via Zoho Mail failed because only `info@qualiasolutions.net` exists there — there is no `bookings@` alias in Zoho. The booking email flow is exclusively handled by Resend, triggered from the qualiasolutions.net contact page's booking form.

This routing split is a pragmatic architecture choice: Zoho handles the human-interactive email (receiving replies, general correspondence) while Resend handles the automated transactional email (booking confirmations, notifications). Resend is better suited for transactional email because it provides delivery tracking, bounce handling, and programmatic sending via API — capabilities that Zoho Mail's SMTP doesn't expose as cleanly. The tradeoff is that `bookings@` is send-only: if a client replies to a booking confirmation, the reply goes to `info@qualiasolutions.net` via the reply-to header.

For any future project that needs to send from a `@qualiasolutions.net` address, check whether the address is configured in Zoho (for interactive email) or as a Resend sending identity (for transactional/automated email). The Resend dashboard and Vercel env vars are the authoritative sources for transactional email configuration.

## Related Concepts

- [[concepts/underdog-sales-webinar-system]] — Another project using Resend for transactional email (Brevo handles webinar emails, but Resend is the standard for other Qualia projects)
- [[concepts/fotini-ai-legal-platform]] — Fotini AI uses Resend for invoice email delivery with PDF attachments — same Resend pattern for send-only transactional email

## Sources

- [[daily/2026-04-29.md]] — Session at 18:17: "`bookings@qualiasolutions.net` sends are handled by Resend, not Zoho Mail — Zoho only has `info@` configured"
- [[daily/2026-04-29.md]] — Session at 18:17: "Resend API key for qualiasolutions.net lives in the Vercel project env vars"
- [[daily/2026-04-29.md]] — Session at 18:17: "Booking confirmation emails use a dark glass styling with teal accent (matches site template)"
- [[daily/2026-04-29.md]] — Session at 18:17: "Reply-to set to `info@qualiasolutions.net` for all bookings@ sends"
