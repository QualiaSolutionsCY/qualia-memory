---
title: "EventMaster Design Pivots"
aliases: [eventmaster-redesign, eventmaster-aesthetic, cyprus-event-marketplace-design]
tags: [design, frontend, iteration, client-feedback, eventmaster]
sources:
  - "daily/2026-04-28.md"
  - "daily/2026-04-29.md"
created: 2026-04-28
updated: 2026-04-29
---

# EventMaster Design Pivots

The EventMaster homepage and browse page (Cyprus event marketplace) underwent three rapid aesthetic pivots on 2026-04-28, driven by real-time user feedback. The final landing: off-white `#FBFAF6` + deep navy `#0C1E2C` + Geist sans + Cormorant italic accents, replacing the previous editorial ivory/gold Cormorant style from DESIGN.md. A key process lesson: this user (OWNER) wants fast action over options menus — present one strong opinion, iterate from rejection.

## Key Points

- **Three pivots in one session:** (1) Editorial ivory/gold → (2) Bold dark/terminal-modern with chartreuse accent (rejected: "ugly and too busy") → (3) Stripped-down minimal off-white/navy (accepted, then refined)
- **User feedback dynamics:** When asked for direction, Fawzi said "ANYTHING JUST CHANGE IT STOP ASKING" — lesson: bias toward action, present one strong opinion, iterate from there rather than offering menus of choices
- **Homepage locked to single screen:** Pill search bar hero, no sections below fold. Header: diamond mark + `Cyprus · est. 2026` micro-tag + nav + CTA. Headline: "The shortest path from *brief to booked.*"
- **Browse page restructured:** Venue listings removed entirely (venue-seekers redirected to `/plan` wizard), categories/filters moved to top horizontal sticky tab bar, sidebar eliminated, items-only 6-column grid at xl+
- **DESIGN.md NOT updated:** New aesthetic only applied to homepage and browse — `/plan`, `/vendor`, `/admin` still use old editorial tokens, creating visual inconsistency that needs follow-up
- **Plan wizard designed (2026-04-29):** Sticky header with 8-segment progress bar, sticky bottom action bar, unified question pattern (eyebrow → serif headline → subhead → content), sharp rectangle choice cards
- **State guard blocked ship:** `/qualia-ship` was invoked but correctly blocked — project is in `setup` state at Phase 2 (Auth + Roles + Schema), verification pending; design work done outside the phase pipeline creates state/codebase mismatch

## Details

The session began with the user requesting a complete design overhaul of EventMaster's homepage, which was using an editorial ivory/gold palette with Cormorant typography as defined in `.planning/DESIGN.md`. The first attempt went bold — a dark terminal-modern aesthetic with chartreuse accent — but was immediately rejected as "ugly and too busy." The second attempt stripped everything back to a minimal single-screen layout, which was accepted as the base direction.

The iterative refinement process is notable for its speed and decisiveness. Once the minimal direction was accepted, details were locked in rapid succession: the pill-shaped search bar (not a full search form), the diamond mark brand element (replacing a text-heavy logo), the `Cyprus · est. 2026` micro-tag for geographic grounding, and the headline "The shortest path from *brief to booked.*" which uses italic emphasis on the key phrase. The overall pattern matches what worked for [[concepts/urban-catering-design-transformation|Urban Catering]] on 2026-04-27: start with a strong palette reduction (fewer colors, more whitespace), lock the typography pairing early, then refine layout within those constraints.

The browse page restructure was a significant functional change, not just aesthetic. Venue listings were filtered out entirely — the platform's venues-seeking flow now routes through the `/plan` wizard rather than the browse grid. The remaining items grid uses portrait `aspect-[4/5]` card images with a sticky category tab bar at the top. The grid is locked to 6 items per row at xl+ breakpoints, which prevents the sparse-card problem seen in [[concepts/qualia-erp-phase-25|the ERP projects gallery]] where switching to fewer columns made cards feel oversized.

A notable risk was flagged but not resolved: DESIGN.md was not updated to reflect the new aesthetic. This means other pages (`/plan`, `/vendor`, `/admin`) still read from the old editorial ivory/gold tokens. Any future work on those pages using `/qualia-design` will produce output that clashes with the homepage. The update should codify the new off-white/navy/Geist aesthetic as the system-wide source of truth before any further page work.

The "bold/dark terminal" rejection pattern is a transferable lesson: trendy maximalist aesthetics (dark backgrounds, neon accents, terminal-style typography) can feel impressive in mockups but often read as "too busy" in production, especially for marketplace/directory sites where content density is already high. Cleaner minimal with warm off-white surfaces consistently lands better for content-heavy applications.

On 2026-04-29, a follow-up design session extended the off-white/navy aesthetic to the `/plan` wizard and further refined `/browse`. The plan wizard received a structured multi-step layout: sticky header with an 8-segment progress bar showing the current step, a sticky bottom action bar (Back/Next), and a unified question pattern where each step follows an eyebrow label → serif Cormorant headline → descriptive subhead → interactive content structure. Choice cards use sharp rectangles (no rounded corners) with selection state indicated by navy border and light background fill.

The browse page received additional refinements: the sidebar was fully removed and replaced with horizontal category tabs at the top of the page plus city pill filters below. Cards were redesigned as portrait `aspect-[4/5]` with vendor name overlay and no border boxes. The grid was density-locked at 6 columns for xl+ breakpoints with consistent max-width alignment across all page sections.

When `/qualia-ship` was attempted at the end of the session, the Qualia Framework's state guard correctly blocked the deploy — the project's state was `setup` at Phase 2 (Auth + Roles + Schema), which had not been planned, built, or verified. The design work was done outside the normal phase pipeline (plan → build → verify), creating a mismatch between the project's tracked state and the actual codebase. Three options were presented: follow the full road (plan all remaining phases), force override the state guard, or simply push the design branch to git without a formal ship. This illustrates that design-only changes that bypass the Qualia road create tracking debt that must be resolved before the project can progress through normal framework commands.

## Related Concepts

- [[concepts/urban-catering-design-transformation]] — Same designer (Fawzi), same day-before; similar pattern of palette reduction landing better than complex initial concepts
- [[concepts/qualia-erp-phase-25]] — ERP projects gallery suffered from sparse cards after column count change — EventMaster browse avoids this with 6-column grid lock
- [[concepts/verification-contract-gaps]] — State guard blocking ship is the intended behavior for unverified work — same principle as verification contracts requiring actual infrastructure checks

## Sources

- [[daily/2026-04-28.md]] — Session at 17:23: "First attempt (bold dark / terminal-modern with chartreuse accent) was rejected as 'ugly and too busy'"
- [[daily/2026-04-28.md]] — Session at 17:23: "When asked for direction, user expressed frustration at too many questions ('ANYTHING JUST CHANGE IT STOP ASKING')"
- [[daily/2026-04-28.md]] — Session at 17:23: "New aesthetic: Off-white `#FBFAF6` + deep navy `#0C1E2C` + Geist sans + Cormorant italic accents"
- [[daily/2026-04-28.md]] — Session at 17:23: "Browse page is items-only — venues filtered out, venue-seekers redirected to `/plan` wizard"
- [[daily/2026-04-28.md]] — Session at 17:23: "DESIGN.md NOT updated to match new aesthetic — other pages still use old editorial tokens"
- [[daily/2026-04-29.md]] — Session at 13:06: "Plan wizard: sticky header with 8-segment progress bar, sticky bottom action bar, unified question pattern, sharp rectangle choice cards"
- [[daily/2026-04-29.md]] — Session at 13:06: "Browse refinement: sidebar removed, horizontal category tabs + city pills, portrait aspect cards, 6-column grid locked"
- [[daily/2026-04-29.md]] — Session at 13:06: "`/qualia-ship` blocked by state guard — project in `setup` at Phase 2, design work done outside phase pipeline"
