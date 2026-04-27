---
title: "Sophia Telegram Lead-Routing Debug"
aliases: [sophia-debug, telegram-lead-routing, truthy-object-trap, telegram-start-requirement, lead-router-fall-through]
tags: [sophia, telegram, debugging, lead-routing, supabase, edge-functions]
sources:
  - "daily/2026-04-27.md"
created: 2026-04-27
updated: 2026-04-27
---

# Sophia Telegram Lead-Routing Debug

A Paphos rental lead from Mikaella Vrionidou was silently dropped by Sophia (the Telegram lead-routing bot) instead of being forwarded to Evelina, the listing owner. The root cause was two compounding bugs: (1) Evelina never `/start`ed the bot, so Telegram returned 403 on `forwardMessage`, and (2) `lead-router.ts:482-496` checked `if (ownerBasedResult)` which is truthy even on `{success: false}`, silently swallowing the failed forward instead of falling through to backup agents (Marios A / Dimitris).

## Key Points

- **Truthy-object trap:** `{success: false, error: "..."}` is truthy in JavaScript — the lead-router checked `if (ownerBasedResult)` instead of `if (ownerBasedResult?.success)`, treating a failed forward as a successful routing and never falling through to backup agents
- **Telegram `/start` requirement:** Bot-to-user direct messaging requires the user to `/start` the bot first; without it, Telegram returns 403 on `forwardMessage` — easy to miss for agents added to group chats who never DM the bot
- **Supabase edge function log eventual consistency:** Don't trust absence of logs in the first pull — initial diagnosis (webhook stolen by `telegram-indexer`) was wrong because Supabase logs hadn't propagated yet; re-querying later revealed Sophia did receive the message
- **Zero lifetime leads since registration:** Evelina had zero leads since her Jan 11 registration — this bug existed since day one but went unnoticed because most Paphos listings aren't Evelina-owned
- **Defensive webhook fix:** Removed self-registering `?setup` endpoint from `telegram-indexer/index.ts` so only `telegram-sophia` can claim the webhook (deployed v34)

## Details

The debugging session began when Lauren flagged that a lead from Mikaella Vrionidou in the Paphos Telegram group was not forwarded to Evelina. The initial investigation focused on a red herring: the `telegram-indexer` service, which was suspected of stealing the webhook from `telegram-sophia`. Supabase edge function logs initially showed no POST requests to the Sophia function, supporting the webhook-theft hypothesis. However, this turned out to be a manifestation of Supabase's edge function log eventual consistency — the logs simply hadn't propagated yet. Re-querying later confirmed that Sophia did receive the message.

The real root cause was a compound failure. First, Evelina had never sent `/start` to the SOPHIA bot in a direct message. Telegram's bot API requires this one-time interaction before a bot can initiate direct messages to a user — group chat membership alone is insufficient. When Sophia attempted `forwardMessage` to Evelina's user ID, Telegram returned a 403 Forbidden error. Second, the error handling in `lead-router.ts:482-496` checked for a truthy result object rather than checking the `.success` property: `if (ownerBasedResult)` evaluates to `true` even when the result is `{success: false, error: "Telegram 403"}`. This meant the failed owner-based routing was treated as successful, and the code never fell through to the backup agent assignment logic.

The fix patched `lead-router.ts` to check `if (ownerBasedResult?.success)` instead of `if (ownerBasedResult)`, ensuring that failed owner-routing falls through to regular agent assignment. A `status=forward_failed` column was also added to the `telegram_leads` table for audit trail purposes. Both changes were deployed as v131 of the Sophia edge function. The `telegram-indexer` fix (removing the self-registering `?setup` endpoint) was a separate defensive measure deployed as v34, preventing any future webhook-theft scenarios.

The broader implication is that any Sophia agent with zero lifetime leads since registration may have the same `/start` problem. An audit of all registered agents for this issue was identified as a follow-up action item, along with asking Evelina to `/start` the bot to enable future lead delivery.

## Related Concepts

- [[concepts/erp-clock-out-name-mismatch]] — Same class of silent failure: system appears to work but a downstream check fails due to a subtle condition (project name mismatch there, truthy-object swallowing here)
- [[concepts/qualiafinal-public-booking-sync]] — Another instance of data existing but being functionally lost due to a missing check in the consuming path
- [[concepts/claude-code-operational-gotchas]] — Supabase log eventual consistency is an operational gotcha in the same vein as the gotchas catalogued there
- [[connections/data-identity-mismatches]] — The "system appears to work but doesn't" pattern documented across multiple Qualia bugs

## Sources

- [[daily/2026-04-27.md]] — Session at 19:19: "Real root cause: two bugs compounding — (1) Evelina never `/start`ed the bot so Telegram returns 403 on `forwardMessage`, and (2) `lead-router.ts:482-496` checked `if (ownerBasedResult)` which is truthy even on `{success: false}`"
- [[daily/2026-04-27.md]] — Session at 19:19: "Supabase edge function logs have eventual consistency — don't trust absence of logs in first pull; re-query before concluding a function wasn't invoked"
- [[daily/2026-04-27.md]] — Session at 19:19: "Evelina has had zero lifetime leads since registration (Jan 11) — this bug has existed since day one"
- [[daily/2026-04-27.md]] — Session at 19:19: "Removed self-registering `?setup` endpoint from `telegram-indexer/index.ts` — defensive fix so only `telegram-sophia` can claim the webhook (deployed v34)"
- [[daily/2026-04-27.md]] — Session at 19:19: "Patched `lead-router.ts` to check `if (ownerBasedResult?.success)` instead of `if (ownerBasedResult)` — failed owner-routing now falls through to regular agent assignment (deployed v131)"
