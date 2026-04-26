---
title: "Memory Loop Architecture"
aliases: [memory-loop, auto-capture-loop, karpathy-wiki-loop]
tags: [memory, architecture, qualia-framework, knowledge-management]
sources:
  - "daily/2026-04-26.md"
created: 2026-04-26
updated: 2026-04-26
---

# Memory Loop Architecture

The memory loop is a fully automated knowledge capture and retrieval pipeline built into the Qualia Framework. It implements Andrej Karpathy's three-layer pattern (raw → wiki → schema) combined with Ar9av's slash commands and Cole Medin's auto-capture hook. The full cycle runs without manual intervention: SessionEnd hook → daily-log → cron flush → concepts/ → loader → builder reads before writing → postmortem catches misses.

## Key Points

- **Auto-capture:** The SessionEnd hook fires on every Claude Code session close, extracting conversation context into `daily/YYYY-MM-DD.md` log files
- **Cron flush:** `bin/knowledge-flush.js` runs weekly (Sunday 3 AM) as a non-interactive process, promoting raw daily-log entries to structured concept articles without requiring an active Claude session
- **Three access patterns:** `/qualia-recall` pulls knowledge into any project, `/wiki-update` pushes from a project into the vault, `/wiki-query` searches across compiled articles
- **Self-healing:** `/qualia-postmortem` identifies which agent, rule, or skill should have caught a failure and proposes a delta to prevent recurrence
- **End-to-day auto-compilation:** If past 6 PM local time and the daily log has changed since last compile (hash comparison against `state.json`), compilation spawns automatically

## Details

The architecture addresses a fundamental problem with personal knowledge management: systems that require manual maintenance die within two weeks. By hooking into the natural session lifecycle (start, compact, end), knowledge capture happens as a side effect of doing work rather than as a separate chore.

The flush process uses Claude Agent SDK's `query()` with `allowed_tools=[]` and `max_turns=2` to decide what is worth saving from a session transcript. It sets `CLAUDE_INVOKED_BY=memory_flush` to prevent recursive hook firing. Deduplication guards prevent the same session from being flushed twice within 60 seconds. The flush correctly no-ops with `{"event":"skipped","reason":"no-recent-daily-log"}` when no entries exist yet — a defensive design validated during the v4.3.0 smoke test.

Both PreCompact and SessionEnd hooks spawn `flush.py` as a fully detached background process (using `start_new_session=True` on Linux/Mac, `CREATE_NEW_PROCESS_GROUP | DETACHED_PROCESS` on Windows) so the flush survives after Claude Code's hook process exits. PreCompact is critical for long sessions that trigger multiple auto-compactions before session close — without it, intermediate context would be lost to summarization.

The Obsidian vault at `~/qualia-memory/` serves as the human-readable interface to the compiled knowledge. Graph visualization uses brand-aligned color groups (cyan for wiki/concepts, coral for analysis hubs, pink for entities, amber for sources). The vault works natively with Obsidian's graph view, backlinks, and search due to standard `[[wikilink]]` format.

On the first day of operation (2026-04-26), the flush pipeline produced mixed results: several `FLUSH_OK` no-ops (expected when sessions had nothing worth saving), but also five `FLUSH_ERROR` entries (at 03:57, 18:40, 18:45, 19:01, and 19:45) with `exit code 1`. The errors occurred without detailed stderr output, suggesting the Claude Agent SDK call itself failed rather than the extraction logic. The 45% error rate on day one (5 errors out of 11 flush attempts) indicates that error reporting needs improvement to diagnose SDK-level failures, though the core architecture is sound — graceful no-op on empty input works, and the idempotent dedup guards prevent data corruption even when flushes fail. The same idempotent dedup pattern used here is also applied in the [[concepts/qualia-portal-employee-workflows|ERP's employee task automation]].

## Related Concepts

- [[concepts/qualia-framework-v4.3.0-release]] — The release that shipped the complete memory loop
- [[concepts/llm-wiki-infrastructure]] — The Karpathy-style wiki that the memory loop feeds
- [[concepts/qualia-os-unification]] — The broader system where memory MCP wires into all projects
- [[concepts/qualia-portal-employee-workflows]] — Uses the same cron + idempotent dedup pattern for employee task automation

## Sources

- [[daily/2026-04-26.md]] — Sessions at 02:06 (full loop described), 02:31 (cost analysis of deep-mine), 03:32 (Obsidian configuration and 3-loop model explanation), 03:57 (flush error)
- [[daily/2026-04-26.md]] — Memory flushes at 03:57, 18:40, 18:45, 19:01, 19:45 (FLUSH_ERROR with exit code 1); 16:36, 16:37 x2, 18:40, 19:19, 19:39 (FLUSH_OK — nothing worth saving)
