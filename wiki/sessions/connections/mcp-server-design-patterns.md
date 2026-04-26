---
title: "Connection: MCP Server Design Patterns Across Qualia"
connects:
  - "concepts/qualia-portal-mcp-server"
  - "concepts/qualia-os-unification"
  - "concepts/qualiafinal-public-booking-sync"
sources:
  - "daily/2026-04-26.md"
created: 2026-04-26
updated: 2026-04-26
---

# Connection: MCP Server Design Patterns Across Qualia

## The Connection

The Qualia ecosystem now has two MCP servers built on the same day (2026-04-26) with opposite design constraints, revealing a clear decision framework for MCP server architecture. The Memory MCP (read-only, zero-dep, stdio, no auth, ~120 lines) and the Portal MCP (read/write, token auth, Streamable HTTP, rate-limited, workspace-scoped) represent the two ends of the MCP security spectrum. The same workspace-scoping lesson that emerged from the Portal MCP audit also appeared in the qualiafinal booking sync trigger — missing scope filters cause data invisibility or unauthorized access.

## Key Insight

The non-obvious relationship is that MCP server design is fully determined by two variables: **data sensitivity** and **transport boundary**. The Memory MCP reads local Obsidian markdown files — low sensitivity, no network boundary — so zero auth is correct. The Portal MCP reads production database content over HTTP — high sensitivity, network boundary — so it requires the full security stack (per-user tokens, workspace membership verification, rate limiting, transport path hardening). Any future Qualia MCP server can be designed by placing it on this matrix.

The workspace-scoping pattern failure connects all three articles: the Portal MCP initially used `createAdminClient()` without workspace filtering (data accessible across workspaces), the booking sync trigger inserted rows without `workspace_id` (data invisible to queries), and the Memory MCP avoids the problem entirely by being scoped to local files that have no workspace concept. The lesson is that workspace scoping must be verified at every data access boundary — triggers, API routes, and MCP tools alike.

## Evidence

- Memory MCP: "zero-dep stdio JSON-RPC server (~120 lines) — read-only by design, writes only via humans + SessionEnd hooks" — [[concepts/qualia-os-unification]]
- Portal MCP: "6 read tools + 3 write tools, `qlt_*` per-user tokens, rate limit 60 req/min, workspace-scoped" — [[concepts/qualia-portal-mcp-server]]
- Booking sync: "`mirror_public_booking_to_meeting()` trigger inserting without `workspace_id`" — [[concepts/qualiafinal-public-booking-sync]]
- Both MCP servers built on 2026-04-26, giving a same-day A/B comparison of design choices
- Portal MCP security audit explicitly called out that existing server actions are the "canonical reference for proper workspace + role filtering" — the canonical patterns existed but weren't applied to the new surface

## Related Concepts

- [[concepts/qualia-portal-mcp-server]]
- [[concepts/qualia-os-unification]]
- [[concepts/qualiafinal-public-booking-sync]]
