---
title: "Video Poster Preload Patterns"
aliases: [video-loading, poster-visibility, opacity-canplay-antipattern, intersection-observer-video]
tags: [frontend, performance, video, ux]
sources:
  - "daily/2026-04-27.md"
created: 2026-04-27
updated: 2026-04-27
---

# Video Poster Preload Patterns

The `opacity: ready ? 1 : 0` pattern — hiding a video element until the `canplay` event fires — is a common anti-pattern when the video element has a poster image. Because opacity applies to the entire element (not just the video stream), it hides both the video AND the poster, causing a blank or black screen until the video buffer loads. The correct approach is to show the poster immediately and let the video paint over it naturally once playback begins.

## Key Points

- **Opacity hides the poster too:** Setting `opacity: 0` on a `<video>` element until `canplay` fires hides the poster image, not just the unloaded video — users see a black rectangle instead of the poster
- **Poster-first rendering:** Remove the opacity gate entirely; the browser natively displays the poster frame while the video loads, providing instant visual content
- **Preload strategy:** Changing `preload` from `"none"` to `"metadata"` lets the browser fetch video dimensions and duration without downloading the full stream, enabling smoother transitions when playback starts
- **IntersectionObserver prefetch:** Bumping `rootMargin` from `200px` to `1200px` (~1.5 viewports ahead) triggers video loading well before the user scrolls to the element, eliminating visible buffering on scroll
- **Git rebase during deploy can silently revert fixes** if upstream redesigned the same files — always verify changes survived after rebase before shipping

## Details

The issue was discovered on the Qualia Solutions `/work` page (`qualiasolutions.net/work`), where project showcase videos inside `DeviceMockup3D.tsx` were loading with a black screen and visible lag when scrolling. The root cause was a React state pattern: a `ready` boolean was set to `true` only after the video's `canplay` event fired, and the component rendered with `opacity: ready ? 1 : 0`. This meant the poster image — which the browser would normally display immediately — was invisible behind a transparent element.

The fix involved two changes. First, the opacity gate was removed entirely from `DeviceMockup3D.tsx`, allowing the poster to render instantly regardless of video load state. Second, the video loading strategy was optimized: `preload` was changed from `"none"` to `"metadata"` (fetching just the header, not the full file), and the IntersectionObserver's `rootMargin` was increased from `200px` to `1200px`. The larger root margin means videos begin loading when they're approximately 1.5 viewports away from the visible area, so by the time the user scrolls to them, enough data has been fetched for smooth playback.

A secondary lesson from the same session: during the deploy, a git rebase against upstream silently reverted the video fix because upstream had redesigned the same files (`DeviceMockup3D.tsx`). The fix had to be re-verified and re-applied after the rebase. This reinforces the rule that after any rebase during a deploy pipeline, the developer must explicitly verify that all intended changes survived the merge — especially when upstream has been actively editing the same components.

## Related Concepts

- [[concepts/claude-code-operational-gotchas]] — Git rebase silently reverting fixes is a related operational gotcha
- [[concepts/vercel-deploy-patterns]] — The deploy context where these fixes were shipped and verified

## Sources

- [[daily/2026-04-27.md]] — Session at 01:50: "Removed `opacity: ready ? 1 : 0` pattern from DeviceMockup3D.tsx — it was hiding the poster behind a transparent video element, causing the black screen"
- [[daily/2026-04-27.md]] — Session at 01:50: "Changed video `preload` from `\"none\"` to `\"metadata\"` and bumped IntersectionObserver `rootMargin` from 200px to 1200px"
- [[daily/2026-04-27.md]] — Session at 01:50: "Git rebase during deploy can silently revert fixes if upstream redesigned the same files"
