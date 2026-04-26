---
title: "Qualia Portal MCP Server"
aliases: [portal-mcp, erp-mcp-server, mcp-server-portal]
tags: [qualia-erp, mcp, security, api, architecture]
sources:
  - "daily/2026-04-26.md"
created: 2026-04-26
updated: 2026-04-26
---

# Qualia Portal MCP Server

An MCP (Model Context Protocol) server was built and deployed for the Qualia Portal (portal.qualiasolutions.net) on 2026-04-26, providing 6 read tools and 3 write tools for AI-assisted portal management. The server uses Streamable HTTP transport at `/api/mcp/mcp`, per-user `qlt_*` token authentication, rate limiting at 60 req/min, and workspace-scoped data access. A follow-up security audit found and fixed 3 HIGH, 1 MEDIUM, and 1 LOW vulnerability.

## Key Points

- 6 read tools + 3 write tools exposed via Streamable HTTP only (SSE disabled due to no Redis dependency on Vercel)
- Admin token management panel shipped at `/admin?tab=system` for minting and managing `qlt_*` per-user tokens
- Client-role tokens are rejected outright at the MCP auth layer, not just filtered — prevents any client data access through the MCP surface
- Legacy `CLAUDE_API_KEY` auth removed entirely — MCP requires `qlt_*` per-user tokens only, enforcing per-request workspace membership verification
- `createAdminClient()` without workspace scoping is a recurring pattern risk — the reports ingest endpoint got away with it because it's write-only, but MCP's read surface exposed cross-workspace data

## Details

The Portal MCP server represents the second MCP server in the Qualia ecosystem, after the Memory MCP (a read-only, zero-dependency stdio JSON-RPC server described in [[concepts/qualia-os-unification]]). While the Memory MCP is deliberately minimal and read-only (~120 lines, no auth needed because it only reads local files), the Portal MCP required a full security stack because it exposes production database content through an HTTP endpoint accessible by external connectors.

The transport decision was SSE vs Streamable HTTP. SSE was disabled because it requires persistent connections that need Redis for state management, and the Vercel deployment environment doesn't have a Redis dependency. Streamable HTTP at a single hardcoded path (`/api/mcp/mcp`) was chosen instead. The wildcard `[transport]` route was gated to return 404 for any path other than the hardcoded one, preventing route enumeration.

The security audit conducted immediately after the initial build revealed five issues. The three HIGH findings centered on workspace scoping: `createAdminClient()` was being used without workspace filtering, which meant the MCP's read tools could potentially return data from any workspace rather than only the workspace associated with the authenticated token. The fix was to mirror the scoping logic already present in the existing server actions (`app/actions/projects.ts` etc.), which serve as the canonical reference for proper workspace + role filtering. The MEDIUM finding was about token scope validation being necessary but not sufficient — workspace membership must also be verified per-request. The LOW finding was related to rate limiting configuration.

The rate limit was set to 60 requests per minute per token via a dedicated `mcpRateLimiter`, separate from the portal's existing rate limiting. This may need tuning based on real connector usage patterns — an AI connector making sequential tool calls could reasonably hit this limit during a complex query session.

The admin token panel at `/admin?tab=system` provides a UI for minting, viewing, and revoking MCP tokens. Users connect to the MCP server through Claude.ai Connectors using these tokens. This self-service token management avoids the need for manual database operations to grant MCP access.

## Related Concepts

- [[concepts/qualia-os-unification]] — The broader Qualia OS wiring effort; describes the Memory MCP (read-only, zero-dep) that contrasts with this Portal MCP (read/write, authenticated)
- [[concepts/qualiafinal-public-booking-sync]] — Both demonstrate the same class of bug: missing workspace scoping causes data to exist but be invisible (booking sync) or accessible across boundaries (MCP reads)
- [[concepts/qualia-erp-phase-25]] — Same ERP portal; the Phase 25 performance optimizations were deployed earlier the same day

## Sources

- [[daily/2026-04-26.md]] — Session at 20:37: "Built MCP server with 6 read tools + 3 write tools, admin token panel at `/admin?tab=system`"
- [[daily/2026-04-26.md]] — Session at 20:37: "SSE transport disabled (no Redis dependency on Vercel) — only Streamable HTTP at `/api/mcp/mcp`"
- [[daily/2026-04-26.md]] — Session at 20:37: "Security audit found 3 HIGH, 1 MEDIUM, 1 LOW — shipped hardening in follow-up commit"
- [[daily/2026-04-26.md]] — Session at 20:37: "`createAdminClient()` without workspace scoping is a recurring pattern risk — MCP's read surface exposed cross-workspace data"
- [[daily/2026-04-26.md]] — Session at 20:37: "Token scope validation (`hasScope()`) is necessary but not sufficient — workspace membership must also be verified per-request"
