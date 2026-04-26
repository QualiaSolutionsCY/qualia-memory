---
title: "Stacked PR Workflow"
aliases: [stacked-prs, pr-stacking, dependent-pr-chain]
tags: [git, workflow, code-review, qualia-framework]
sources:
  - "daily/2026-04-26.md"
created: 2026-04-26
updated: 2026-04-26
---

# Stacked PR Workflow

Stacked PRs are a series of pull requests where each builds on the previous one, merged in strict sequential order. The Qualia team established through the v4.3.0 release cycle that six stacked PRs is the practical ceiling before reviewer cognitive load degrades and review quality suffers.

## Key Points

- Six PRs (#8 → #9 → #10 → #11 → #12 → #13) were merged sequentially using `gh pr merge --merge --delete-branch`
- Each PR depended on the previous — rebase conflicts prevented out-of-order merging
- The team paused at 6 PRs rather than continuing to worktree parallelism specifically to avoid reviewer fatigue
- Worktree parallelism (v4.3.0 phase 3) was deferred until the merged stack was battle-tested for one week
- Parallel builder agents committing simultaneously can cause lint-staged to bundle unrelated changes into a single commit (wave collision)

## Details

The v4.3.0 release demonstrated both the power and limits of PR stacking. The six PRs covered a coherent progression: memory layer foundation (v4.2.0 PRs #8–#11) through self-healing and cron-flush capabilities (v4.3.0 PRs #12–#13). Because each PR built incrementally on the previous, the changes were individually reviewable even though the total delta was +5,119 lines across 27 files.

The decision to stop at six was deliberate. Adding a seventh PR (worktree parallelism) would have required the reviewer to hold too much context from the earlier PRs. The pause-and-merge pattern — stack up to the cognitive limit, merge everything, validate in production, then start a new stack — proved more effective than an ever-growing chain.

A specific anti-pattern was identified during Phase 25 of the Qualia ERP: when multiple parallel builder agents commit simultaneously in the same wave, lint-staged's stash/restore mechanism can bundle unrelated changes into a single commit. The code remains correct, but the git history becomes misleading. The recommendation is to consider sequential commits for parallel waves where git history clarity matters.

## Related Concepts

- [[concepts/qualia-framework-v4.3.0-release]] — The release that exercised this workflow
- [[concepts/qualia-erp-phase-25]] — Where lint-staged wave collision was discovered
- [[concepts/memory-loop-architecture]] — The feature set delivered through this PR stack

## Sources

- [[daily/2026-04-26.md]] — Sessions at 02:06 ("6 stacked PRs is the practical ceiling"), 02:29 (merge order), 03:35 (merge execution via `gh pr merge`), 04:09 (Hasan's Markitdown proposal deferred to avoid growing the stack)
