---
title: "LLM Wiki Infrastructure"
aliases: [karpathy-wiki, qualia-memory, second-brain, obsidian-wiki]
tags: [knowledge-management, obsidian, wiki, infrastructure]
sources:
  - "daily/2026-04-26.md"
created: 2026-04-26
updated: 2026-04-26
---

# LLM Wiki Infrastructure

The LLM Wiki is a Karpathy-style personal knowledge base at `~/qualia-memory/` that compiles knowledge from AI conversations into structured, queryable Obsidian articles. It implements the three-layer pattern: raw (daily conversation logs) → wiki (compiled concept articles) → schema (AGENTS.md specification). The system uses `[[wikilinks]]` throughout, making it natively compatible with Obsidian's graph view, backlinks, and search.

## Key Points

- Implements Andrej Karpathy's raw → wiki → schema architecture combined with Ar9av's slash commands and Cole Medin's auto-capture hook
- Three access patterns: `/qualia-recall` to pull knowledge into projects, `/wiki-update` to push from projects into the vault, `/wiki-query` to search compiled articles
- `/wiki-query` validated live with a voice agents query — returned 6 cited pages, named projects, MRR figures, and identified gaps
- Claude Code `CronCreate` recurring tasks auto-expire after 7 days — cannot rely on them for permanent schedules like weekly lint
- Deep-mining 1,559 JSONL files (694 MB) of Claude conversation history would cost $250–400 via SDK — pivoted to free filesystem inventory (53 projects, 380 sessions catalogued)

## Details

The wiki's primary value proposition is that it survives past the two-week mark where most personal knowledge management systems die. This is achieved by making capture automatic (SessionEnd hook) rather than manual. The human never needs to organize, categorize, or maintain — the LLM handles synthesis, cross-referencing, and maintenance through the compile operation.

At personal knowledge base scale (50–500 articles), index-guided LLM retrieval outperforms vector similarity / RAG. The LLM reads `knowledge/index.md` first as a master catalog, then selects 3–10 relevant articles to read in full. This works because the LLM understands the intent behind a question, while cosine similarity only finds similar words. The scaling threshold for needing hybrid RAG is approximately 2,000 articles / 2M tokens.

Obsidian configuration was a significant portion of the setup work. Four plugins were recommended: Templater (already installed), Linter, Tag Wrangler, and Web Clipper (browser extension). Plugins like Smart Connections, Copilot for Obsidian, and Dataview were explicitly rejected as redundant with `/wiki-query` and Obsidian Bases. The graph view was configured with brand-aligned color groups matching `vault-colors.css`: cyan for wiki/concepts, coral red for analysis hubs, pink for entities/people, amber for sources, violet for sessions, green for clients, blue for projects, gold for finances, orange for research.

A critical gotcha was discovered: Obsidian holds graph configuration in memory and flushes to disk when the graph view closes. External edits to `graph.json` are overwritten if the graph tab is open. The workaround is to close the graph tab before editing, then reload Obsidian.

## Related Concepts

- [[concepts/memory-loop-architecture]] — The automated pipeline that feeds this wiki
- [[concepts/qualia-os-unification]] — The broader system integration where memory MCP connects to all projects
- [[concepts/prestashop-category-search]] — Example of domain knowledge that would be captured and compiled

## Sources

- [[daily/2026-04-26.md]] — Sessions at 02:31 (cost analysis, inventory approach, `/wiki-query` validation), 03:32 (Obsidian plugins, graph config, 3-loop explanation), 03:52 (graph coloring, Minimal theme, plugin gotchas)
