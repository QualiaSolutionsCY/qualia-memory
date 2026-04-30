---
title: "Memory Loop Architecture"
aliases: [memory-loop, auto-capture-loop, karpathy-wiki-loop]
tags: [memory, architecture, qualia-framework, knowledge-management]
sources:
  - "daily/2026-04-26.md"
  - "daily/2026-04-27.md"
  - "daily/2026-04-28.md"
  - "daily/2026-04-29.md"
created: 2026-04-26
updated: 2026-04-29
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

On day two (2026-04-27), the flush pipeline showed significant improvement: 15 `FLUSH_OK` and 3 `FLUSH_ERROR` out of 18 total attempts, bringing the error rate down to ~17% (from ~45% on day one). The three errors (at 08:38, 18:11, and 18:18) still produced only generic `exit code 1` without detailed stderr, but the overall reliability trend is strongly positive. The high volume of successful no-ops (sessions that produced nothing worth saving) confirms the flush correctly triages low-value sessions without wasting API calls. The late-evening flushes (19:30 x2, 19:38, 21:48, 22:01) all succeeded cleanly, suggesting the errors cluster earlier in the day when session volume is higher. The day-over-day improvement from 45% to 17% error rate suggests that either the Claude Agent SDK connection became more stable or that the flush process's retry/timeout behavior settled into a steady state.

On day three (2026-04-28), the flush pipeline regressed: 7 `FLUSH_OK` and 7 `FLUSH_ERROR` out of 14 total attempts, bringing the error rate to 50%. The seven errors (at 01:53, 14:23, 15:53, 16:26, 17:24, 18:42, and 21:33) all produced the same generic `exit code 1` without detailed stderr, consistent with the pattern from previous days. The errors were distributed throughout the day rather than clustering at any specific time, with the final error occurring after the v4.4.0 framework publish session. The 7 FLUSH_OK entries (12:02, 12:15, 14:13, 16:43, 16:58, 17:00, 18:32) were all clean no-ops. The regression from 17% to 50% suggests the error rate is not monotonically improving — it may be sensitive to session volume, Claude Agent SDK availability, or conversation complexity. The lack of any improvement in error diagnostics (still just `exit code 1`) makes root-cause analysis difficult; adding stderr capture to `flush.py` remains the highest-impact improvement for pipeline reliability.

On day four (2026-04-29), the flush pipeline continued its unreliable trend: 2 `FLUSH_OK` and 2 `FLUSH_ERROR` out of 4 total attempts. The two errors (at 03:11 and 13:04) produced the same generic `exit code 1`, while the two late-evening flushes at 19:20 and 19:45 both succeeded as clean no-ops ("Nothing worth saving from this session"). The 50% error rate (2/4) maintains the pattern of persistent SDK-level failures, though the small sample size limits confidence. The cumulative four-day statistics are: day 1 = 45% error rate (5/11), day 2 = 17% (3/18), day 3 = 50% (7/14), day 4 = 50% (2/4). The erratic day-over-day variation (45% → 17% → 50% → 50%) strongly suggests the errors are not caused by the flush code itself but by external factors — likely Claude Agent SDK availability, credential state, or resource constraints on the host machine. Adding stderr capture and structured error logging to `flush.py` remains the single highest-priority improvement for diagnosing these failures.

## Related Concepts

- [[concepts/qualia-framework-v4.3.0-release]] — The release that shipped the complete memory loop
- [[concepts/llm-wiki-infrastructure]] — The Karpathy-style wiki that the memory loop feeds
- [[concepts/qualia-os-unification]] — The broader system where memory MCP wires into all projects
- [[concepts/qualia-portal-employee-workflows]] — Uses the same cron + idempotent dedup pattern for employee task automation

## Sources

- [[daily/2026-04-26.md]] — Sessions at 02:06 (full loop described), 02:31 (cost analysis of deep-mine), 03:32 (Obsidian configuration and 3-loop model explanation), 03:57 (flush error)
- [[daily/2026-04-26.md]] — Memory flushes at 03:57, 18:40, 18:45, 19:01, 19:45 (FLUSH_ERROR with exit code 1); 16:36, 16:37 x2, 18:40, 19:19, 19:39 (FLUSH_OK — nothing worth saving)
- [[daily/2026-04-27.md]] — Memory flushes: 15 FLUSH_OK (02:08, 03:50, 03:53 x2, 04:13 x2, 08:38, 08:53, 15:48, 18:16, 19:30 x2, 19:38, 21:48, 22:01), 3 FLUSH_ERROR (08:38, 18:11, 18:18) — error rate improved from ~45% to ~17%
- [[daily/2026-04-28.md]] — Memory flushes: 7 FLUSH_OK (12:02, 12:15, 14:13, 16:43, 16:58, 17:00, 18:32), 7 FLUSH_ERROR (01:53, 14:23, 15:53, 16:26, 17:24, 18:42, 21:33) — error rate regressed from ~17% to 50%
- [[daily/2026-04-29.md]] — Memory flushes: 2 FLUSH_OK (19:20, 19:45), 2 FLUSH_ERROR (03:11, 13:04) — 50% error rate on day 4 (small sample)
