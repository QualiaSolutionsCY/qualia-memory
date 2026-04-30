---
title: "Vercel Deploy Patterns"
aliases: [vercel-transient-errors, vercel-cold-cache, docs-only-deploy, vercel-gotchas, vercel-prebuilt-workaround, vercel-promote]
tags: [vercel, deployment, infrastructure, gotchas]
sources:
  - "daily/2026-04-27.md"
  - "daily/2026-04-29.md"
created: 2026-04-27
updated: 2026-04-29
---

# Vercel Deploy Patterns

Practical deployment patterns and gotchas discovered during Vercel production deploys across Qualia projects on 2026-04-27. Covers transient platform errors, integration provisioning failures, cold-cache latency characteristics, and safe workarounds for blocked deploys.

## Key Points

- **Transient "Unexpected error":** Vercel can throw generic "Unexpected error" failures with no build logs — these are platform-side issues, not code bugs; retrying later resolves them
- **Integration provisioning failure:** `vercel --prod` returns "Blocked" with `readyStateReason: "Provisioning integrations failed"` when Vercel Marketplace integrations (Supabase/Groq/Upstash/Neon) fail to provision — workaround is `vercel build && vercel deploy --prebuilt --prod` to skip server-side provisioning entirely
- **Cold-cache first-request latency:** First requests to a freshly deployed Vercel site hit 800ms–1.3s due to cold function/edge cache initialization; subsequent requests are significantly faster
- **Docs-only commits against healthy URL:** When only documentation or tracking files changed (zero runtime code), deploying against the existing healthy URL is safe — no need to block on a new deploy that changes nothing at runtime
- **Retry strategy:** Three consecutive "Unexpected error" failures cleared on a later retry without any code changes — the correct response is to wait and retry, not to debug the codebase
- **Multi-account `vercel promote` workaround:** When a Vercel org has multiple members, the CLI authenticates per-user. If that user's account has restrictions (spend caps, blocked status), deploys fail silently with "Blocked" badge. `vercel promote` on a successful Preview build routes through the project owner's identity, bypassing the blocked CLI user
- **`NEXT_PUBLIC_*` build-time baking:** Environment variables prefixed with `NEXT_PUBLIC_` are embedded into the JavaScript bundle at build time. `vercel redeploy` reuses the existing build artifact and will NOT pick up new env var values — a fresh `vercel --prod` build is required to bake in updated values

## Details

The Urban Catering project (urbancatering.com.cy) experienced three consecutive Vercel deploy failures with the generic "Unexpected error" message. No build logs were generated, which confirmed this was a Vercel platform issue rather than a build or configuration error. The deploys succeeded on a later retry without any code modifications. This pattern has been observed sporadically across multiple Qualia projects and is not correlated with project size, framework version, or deployment region.

The cold-cache latency was measured during post-deploy verification: the first HTTP request to a freshly deployed site returned in 800ms–1.3s, while subsequent requests to the same endpoint were significantly faster. This is expected behavior for Vercel's serverless/edge architecture — the first request triggers function initialization and cache population. For post-deploy health checks, the first measurement should not be treated as representative of steady-state performance. Allow at least one warm-up request before measuring latency for monitoring purposes.

The docs-only deploy pattern emerged when the Urban Catering project had pending commits that only modified `.planning/` tracking files and documentation — zero changes to any file that affects the built output. In this situation, the existing deployed URL is functionally identical to what a fresh deploy would produce. Rather than blocking on Vercel platform issues to ship a zero-impact deploy, the commits were pushed to git and the existing deployment URL was confirmed healthy. This pattern is safe only when the developer has verified that no runtime code, configuration, or environment variables changed in the pending commits.

A second, more persistent deploy failure was discovered later the same day during the Underdog Sales Academy webinar deployment. Unlike the transient "Unexpected error," this one is reproducible and systematic: `vercel --prod` returns "Blocked" with `readyStateReason: "Provisioning integrations failed"`. The root cause is broken Vercel Marketplace integration provisioning — when a project has marketplace integrations (Supabase, Groq, Upstash, Neon), the deploy pipeline attempts to provision these integrations before building, and fails. This bug has affected all projects on the `qualiasolutionscy` Vercel team since approximately April 21, 2026.

The workaround is to separate the build and deploy steps: `vercel build` runs the build locally (or in CI), producing a `.vercel/output/` directory, then `vercel deploy --prebuilt --prod` uploads the prebuilt output directly, skipping the server-side integration provisioning step entirely. This is not a temporary retry situation like the transient errors — it requires an active support ticket or Vercel dashboard fix for permanent resolution. Until then, `--prebuilt` is the standard deploy path for all affected projects.

These patterns apply to all Qualia projects deployed on Vercel, which is the standard hosting platform for all Next.js applications in the ecosystem. The deploy process uses `vercel --prod` via CLI when the platform is healthy, falling back to `vercel build && vercel deploy --prebuilt --prod` when integration provisioning is broken. Auto-deploys from GitHub are disabled across all projects.

A third deploy failure mode was discovered on 2026-04-29 during the Underdog Academy quiz fix deployment. CLI deploys from the "Qualia Framework" Vercel user account were returning "Blocked" status — distinct from the integration provisioning issue, this was related to the specific user account's restrictions (likely spend cap or org policy). Successful deploys were happening via git-triggered previews from the "Qualiasolutions" account. The workaround is `vercel promote` on a successful Preview build, which promotes an existing successful deployment to production without triggering a new build through the blocked CLI user. This is faster than `--prebuilt` and avoids the blocked account entirely. The permanent fix is to re-authenticate the Vercel CLI with the Qualiasolutions account or resolve the billing/spend cap on the Qualia Framework user.

The deploy workaround hierarchy for Qualia projects is now:
1. **`vercel --prod`** — standard path (when platform is healthy and CLI user is not blocked)
2. **`vercel build && vercel deploy --prebuilt --prod`** — when integration provisioning fails (skips server-side provisioning)
3. **`vercel promote`** — when the CLI user account is blocked (promotes an existing Preview build to production)

A fourth deploy gotcha was discovered on 2026-04-29 during the Underdog Sales webinar signup debugging: `NEXT_PUBLIC_*` environment variables are embedded into the JavaScript bundle at build time by Next.js. This means `vercel redeploy` — which reuses the existing build artifact — does NOT pick up updated env var values. Only a fresh `vercel --prod` build (which runs the full Next.js build process and bakes in current env var values) will reflect changes. This is particularly insidious because `vercel redeploy` appears to succeed, and the dashboard shows the new env var values, but the running code still uses the old values from the previous build. The practical rule: any time a `NEXT_PUBLIC_*` env var changes, trigger a full rebuild, not a redeploy. Non-prefixed (server-side) env vars are read at runtime and do not have this limitation.

## Related Concepts

- [[concepts/claude-code-operational-gotchas]] — `next build` zombie processes are a related deploy-time gotcha
- [[concepts/npm-vulnerability-triage]] — Deploy verification discipline applies to both vulnerability remediation and routine deploys
- [[concepts/video-poster-preload-patterns]] — The video fix was deployed during the same session where git rebase silently reverted changes, a deploy pipeline risk
- [[concepts/underdog-sales-webinar-system]] — Where the integration provisioning bug and `--prebuilt` workaround were first discovered
- [[concepts/underdog-quiz-validation-bug]] — Where the `vercel promote` multi-account workaround was discovered during the quiz fix deployment

## Sources

- [[daily/2026-04-29.md]] — Session at 20:24 (Underdog Sales): "`NEXT_PUBLIC_*` env vars are baked at build time — `vercel redeploy` reuses the build artifact and won't pick up new values; need fresh `vercel --prod`"
- [[daily/2026-04-27.md]] — Session at 04:13 (Urban Catering): "Vercel platform can throw transient generic 'Unexpected error' with no build logs — retrying later resolves it; not a code issue"
- [[daily/2026-04-27.md]] — Session at 04:13 (Urban Catering): "Cold-cache first-request latencies on Vercel hit 800ms–1.3s; subsequent requests are faster"
- [[daily/2026-04-27.md]] — Session at 04:13 (Urban Catering): "Shipped docs-only commits against existing healthy deploy when Vercel had platform errors — zero runtime code changed"
- [[daily/2026-04-27.md]] — Session at 01:50: "Deployed to https://qualiasolutions.net/work — live and verified (HTTP 200, 592ms)"
- [[daily/2026-04-27.md]] — Session at 18:30 (Underdog Sales): "Bypassed Vercel's broken integration provisioning using `vercel build && vercel deploy --prebuilt --prod`"
- [[daily/2026-04-27.md]] — Session at 18:30 (Underdog Sales): "The `qualiasolutionscy` Vercel team has this integration provisioning bug affecting all projects — needs a support ticket or dashboard fix"
- [[daily/2026-04-29.md]] — Session at 00:19 (Underdog Academy): "Vercel CLI deploys as 'Qualia Framework' user getting Blocked status — successful deploys via git-triggered previews from 'Qualiasolutions' account"
- [[daily/2026-04-29.md]] — Session at 00:19 (Underdog Academy): "Workaround: `vercel promote` on successful Preview builds to bypass blocked CLI user"
