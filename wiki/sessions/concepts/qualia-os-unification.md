---
title: "Qualia OS Unification"
aliases: [qualia-os, repo-wiring, unification-plan]
tags: [architecture, qualia-framework, qualia-erp, infrastructure, strategy]
sources:
  - "daily/2026-04-26.md"
created: 2026-04-26
updated: 2026-04-26
---

# Qualia OS Unification

Qualia OS unification is the Week 1 initiative to wire the Qualia Framework, ERP, and Memory repos together into a cohesive operating system, guided by a deep-research report. The key insight was to stop building new products and instead wire existing repos using the report's "Balanced Stack" recommendations (Promptfoo, Langfuse, LiteLLM, n8n, Presidio).

## Key Points

- Three tactical mistakes identified in a prior strategic plan: (1) proposing to rewrite qualia-memory as an API when it already works as an Obsidian wiki, (2) proposing a new `.qualia/project.json` when `tracking.json` + `STATE.md` already serve the purpose, (3) ignoring the deep-research report's actual tool recommendations
- `tracking.json` extended with `client_id` and `framework_version` fields instead of creating new identity files — backwards compatible (null values behave as today)
- Memory MCP built as a zero-dependency stdio JSON-RPC server (~120 lines) — read-only by design, with path traversal guard for security
- ERP report contract audit found 3 concrete mismatches: `client_id` and `framework_version` not accepted by ERP, `session_id` sent but stored as generic metadata instead of an indexed column

## Details

The unification work began by reviewing a previous strategic plan and cross-referencing it against a deep-research report stored at `~/.claude/knowledge/deep-research-2026-04-26.md`. The review revealed that the prior plan proposed building three new things (memory API, identity file, custom dashboard) when the existing tools already covered those needs. The corrected approach maps the deep-research report's "Balanced Stack" onto four existing repos.

The Memory MCP follows the framework's zero-dependency design philosophy. It is wired globally via `bin/install.js` (the same pattern used for `next-devtools`) rather than per-project `.mcp.json` files. Writes happen only through humans and SessionEnd hooks — the MCP is read-only at the server level, which is a deliberate architectural constraint preventing tools from mutating the knowledge base during normal operation.

The ERP integration required a small delta (~30 LOC + 1 migration) once the audit document spelled out the exact mismatches. The framework PR (#14) must merge before the ERP PR (#87) ships to production because the ERP depends on the new fields. This dependency ordering was documented and enforced manually.

The three-week roadmap from the unification session: Week 1 completes the wiring (tracking.json, Memory MCP, ERP contract fix). Week 2 adds Promptfoo CI template with `/qualia-eval` command and deploys Langfuse on Railway plus LiteLLM gateway in front of OpenRouter. Week 3 deploys n8n on Railway for lead pipeline (website form → Presidio scrub → ERP `/api/v1/leads`).

## Related Concepts

- [[concepts/qualia-framework-v4.3.0-release]] — The framework release that enables unification
- [[concepts/memory-loop-architecture]] — The memory infrastructure being wired in
- [[concepts/qualia-erp-phase-25]] — ERP performance work running in parallel with unification
- [[concepts/qualia-portal-mcp-server]] — The second MCP server in the ecosystem; contrasts with Memory MCP (read/write + token auth vs read-only + no auth)

## Sources

- [[daily/2026-04-26.md]] — Session at 03:09 (first instance: tactical mistakes, tracking.json extension, Memory MCP design, ERP audit, 3-week roadmap)
