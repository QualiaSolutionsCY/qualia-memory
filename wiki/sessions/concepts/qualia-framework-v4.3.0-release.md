---
title: "Qualia Framework v4.3.0 Release"
aliases: [v4.3.0, framework-release-april-2026]
tags: [qualia-framework, release, infrastructure]
sources:
  - "daily/2026-04-26.md"
created: 2026-04-26
updated: 2026-04-26
---

# Qualia Framework v4.3.0 Release

Qualia Framework v4.3.0 was released on 2026-04-26 as a major milestone encompassing six stacked pull requests (#8 through #13) merged sequentially into main. The release advanced main from commit `dc4ca80` to `1f4d312`, adding 5,119 lines across 27 files. The version was bumped from 4.1.1 directly to 4.3.0 (skipping 4.2.x) to reflect the cumulative scope of all merged changes.

## Key Points

- Six stacked PRs merged in strict dependency order: #8 → #9 → #10 → #11 → #12 → #13, each building on the previous
- PR #12 introduced `/qualia-postmortem` self-healing skill, adversarial verifier flag, and knowledge loader subdirectory support
- PR #13 shipped `bin/knowledge-flush.js`, a cron-runnable non-interactive flush that completes the [[concepts/memory-loop-architecture|memory loop]] end-to-end without requiring a Claude session
- Weekly cron installed: `0 3 * * 0 node ~/.claude/bin/knowledge-flush.js` (Sunday 3 AM)
- `npm publish` was blocked by 2FA OTP requirement — awaiting Fawzi's authenticator code at session end
- Framework doctor confirmed green: 28 skills, 8 agents, 9 hooks

## Details

The release consolidated work spanning the memory layer (v4.2.0) through self-healing and cron-flush capabilities (v4.3.0). The five `[Unreleased]` CHANGELOG blocks were merged into a single `[4.3.0] — 2026-04-26` section. PR #16 was opened specifically for the version bump and CHANGELOG consolidation, then merged to main.

A notable bug was discovered and fixed during the release cycle: the `logEvent` function in the Stop hook was silently swallowing ENOENT errors when `~/.claude/` did not exist. This was caught by the test suite and fixed with a defensive `mkdir` guard. The fix reflects a broader principle — hooks fire in environments where assumed directories may not yet be created.

PR #14 (ERP tracking with `client_id` and `framework_version` fields) was intentionally held for v4.3.1 because the ERP-side migration had not been confirmed as ready. This demonstrates the pattern of keeping deployment-dependent changes gated until both sides are prepared.

The npm publish step requires interactive 2FA OTP input, which cannot be fully automated in a CI-less workflow. After publish completes, the remaining steps are: tag `v4.3.0`, push the tag, and run a fresh-HOME smoke test with `npx qualia-framework@4.3.0 install` followed by `doctor`.

During the release cycle, Hasan proposed adding Microsoft's Markitdown CLI as a framework dependency with an auto-convert-on-session-start hook. The proposal was reviewed and counter-proposed: the concept was approved but only as an opt-in `/qualia-ingest` skill (~150 LOC, no hooks, no install changes), not as an auto-hook. This established the [[concepts/opt-in-skills-vs-auto-hooks|opt-in skills vs auto-hooks]] design principle — auto-hooks must be lightweight, universal, and non-blocking; everything else should be an explicit skill invocation. The `/qualia-ingest` skill was scoped for v4.3.1 or v4.4.0.

## Related Concepts

- [[concepts/memory-loop-architecture]] — The memory loop that v4.3.0 completes end-to-end
- [[concepts/stacked-pr-workflow]] — The 6-PR stacking pattern used for this release
- [[concepts/qualia-os-unification]] — The broader wiring effort that v4.3.0 contributes to
- [[concepts/opt-in-skills-vs-auto-hooks]] — Design principle established during this release (Markitdown proposal)

## Sources

- [[daily/2026-04-26.md]] — Sessions at 02:06, 02:29, 03:35, 04:09 document the merge, cron install, version bump, and npm publish attempt
- [[daily/2026-04-26.md]] — Session (04:09, first instance): Hasan's Markitdown proposal reviewed, counter-proposed as opt-in skill
