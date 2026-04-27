---
title: "Urban Catering Design Transformation"
aliases: [urban-catering-redesign, editorial-web-design, urbancatering-brand]
tags: [design, frontend, branding, qualia-design, urban-catering]
sources:
  - "daily/2026-04-27.md"
created: 2026-04-27
updated: 2026-04-27
---

# Urban Catering Design Transformation

The Urban Catering homepage (urbancatering.com.cy) underwent a full design transformation on 2026-04-27, replacing a dark overlay-heavy aesthetic with a light editorial style. The rebuild used `/qualia-design` to regenerate all 7 homepage section components plus `globals.css` and `ScrollNav` across 9 commits on the `polish/phase1-css-primitives` branch. The brand palette was locked to white surfaces, cream-100 alternating sections, and navy-950 (brand blue) accents, with Playfair Display as the display font.

## Key Points

- **Brand palette:** White surfaces with cream-100 alternating section backgrounds, navy-950 as primary accent, Playfair Display display font — replacing prior dark overlay-heavy design
- **Hero approach:** Full-bleed food photo with light gradient overlay (black/60 → black/10), bottom-left editorial composition instead of a centered hero pattern — creates a more cinematic, distinctive entry point
- **Services editorial numbering:** Replaced emoji icons with editorial numbering (01, 02, 03…) at 5xl Playfair Display — more distinctive and professional than icon grids, which tend toward generic card layouts
- **ScrollNav minimalism:** Stripped the white container from scroll navigation, leaving only minimal floating brand-blue dots — reduces visual noise while maintaining functionality
- **Design agent workflow:** `/qualia-design` rebuilt all 7 sections + globals.css in a single pass across 9 commits — the "one-shot design transformation" pattern works well for full-page redesigns on existing content

## Details

The redesign was triggered after the project's state was already `shipped` from prior work. The user invoked `/qualia-design` to remake the entire homepage while keeping the same content — a common use case where visual presentation needs upgrading without content changes. The design agent worked across all 7 homepage section components, the global stylesheet, and the scroll navigation component, producing 9 sequential commits on a dedicated polish branch.

The hero section's transformation illustrates a reusable editorial pattern for food/hospitality sites. The prior design used heavy dark overlays that obscured the food photography. The replacement uses a light gradient (transitioning from 60% black opacity at the top to 10% at the bottom) that preserves photo detail while maintaining text readability. The bottom-left editorial composition — with the heading and CTA positioned in the lower-left quadrant — creates visual tension that draws the eye diagonally across the image, which is more engaging than the default centered layout.

The editorial numbering pattern for the services section (01, 02, 03… rendered at 5xl in Playfair Display) is a transferable design technique. Emoji and icon grids are the most common approach for service listings, but they tend toward visual monotony — especially when the icons are generic (wrench, globe, people). Large-scale typographic numbering creates a clear reading order, adds visual weight without additional assets, and pairs naturally with a display serif font. This pattern is particularly effective for service counts under 10, where the numbering communicates scarcity/exclusivity.

The deploy phase revealed that the earlier Vercel platform errors (3× "Unexpected error") were transient — the deploy succeeded on a later retry without code changes. This behavior is documented in [[concepts/vercel-deploy-patterns]]. The branch `polish/phase1-css-primitives` was pushed to remote but no PR was created at session end — merging to main remains pending.

## Related Concepts

- [[concepts/vercel-deploy-patterns]] — Deploy gotchas discovered during the same session (transient errors, cold-cache latency, docs-only commit safety)
- [[concepts/qualia-erp-phase-25]] — Another project where layout transformation was needed: ERP projects gallery went from multi-column to single-column, requiring card density adjustment — same class of layout/card mismatch problem
- [[concepts/video-poster-preload-patterns]] — Visual presentation fix shipped on the same day for qualiasolutions.net; both sessions focused on improving visual quality of content delivery

## Sources

- [[daily/2026-04-27.md]] — Session at 04:13 (Urban Catering): "Brand palette locked: white surfaces, cream-100 alternating sections, navy-950 (brand blue) accents, Playfair Display display font"
- [[daily/2026-04-27.md]] — Session at 04:13 (Urban Catering): "Hero approach: full-bleed food photo with light gradient overlay (black/60→black/10), bottom-left editorial composition instead of centered hero pattern"
- [[daily/2026-04-27.md]] — Session at 04:13 (Urban Catering): "Services section: replaced emoji icons with editorial numbering (01, 02, 03…) at 5xl Playfair"
- [[daily/2026-04-27.md]] — Session at 04:13 (Urban Catering): "ScrollNav: stripped white container, now minimal floating brand-blue dots"
- [[daily/2026-04-27.md]] — Session at 04:13 (Urban Catering): "Design agent rebuilt all 7 homepage section components + globals.css + ScrollNav across 9 commits on `polish/phase1-css-primitives`"
