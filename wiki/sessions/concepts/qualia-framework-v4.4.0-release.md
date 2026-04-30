---
title: "Qualia Framework v4.4.0 Release"
aliases: [v4.4.0, plan-contract-validator, agent-runs-tracking, framework-release-april-2026-v2]
tags: [qualia-framework, release, infrastructure, quality-gates]
sources:
  - "daily/2026-04-28.md"
created: 2026-04-28
updated: 2026-04-28
---

# Qualia Framework v4.4.0 Release

Qualia Framework v4.4.0 was built and published to npm on 2026-04-28, adding three new modules to the framework substrate: a plan-contract validator (`bin/lib/plan-contract.js`), an agent-runs tracker (`bin/lib/agent-runs.js`), and a CLI `agents` subcommand. The full cycle completed in one day: spec review → implementation → 226 tests passing (5 suites) → PR #17 → merged to main at `8f1a930` → tagged `v4.4.0` → published to npm. Skill rewiring to consume the new substrate was deferred to v4.5.0.

## Key Points

- **Plan-contract validator:** `bin/lib/plan-contract.js` validates task plans before execution — checks for shell metacharacter injection, vague acceptance criteria, and structural completeness
- **Agent-runs tracker:** `bin/lib/agent-runs.js` provides start/finish/prune operations and a reader for querying agent execution history — observability layer for builder/verifier/planner agents
- **CLI `agents` subcommand:** New CLI entry point for inspecting agent runs from the terminal
- **Zero-dependency approach:** Both new modules follow the framework's zero-dependency design philosophy (same as Memory MCP, plan-contract, and other `bin/lib/` modules)
- **Shell metacharacter validation:** The plan-contract catches dangerous command patterns in task verification commands — backticks, pipes, and other shell metacharacters trigger validation failures
- **Tarball verified:** 301kB / 100 files before merge — keeping the framework lightweight
- **Full release cycle completed:** 226 tests passing (5 suites), PR #17 merged at `8f1a930`, tagged `v4.4.0`, published to npm; v4.5.0 scope defined as skill wiring (planner/builder/verifier consuming new APIs)
- **Hardening items shipped alongside:** State lock failures, tracking-sync failures, and deploy gate failures now produce real errors; `set-erp-key` command added; `migrate` installs full hook set; help counts corrected to 28 skills / 9 hooks / 6 rules / 21 templates

## Details

The v4.4.0 release represents the framework's shift from "capture and compile knowledge" (v4.3.0's memory loop) to "validate and observe execution" (v4.4.0's plan-contract and agent-runs). Where v4.3.0 answered "what happened in this session?", v4.4.0 answers "is this plan safe to execute?" and "what did each agent do?"

The plan-contract validator serves as a pre-execution quality gate. Before a builder agent receives a task, the validator checks the task specification for structural completeness (required fields present), semantic quality (acceptance criteria are specific rather than vague), and security (verification commands don't contain shell metacharacters that could lead to command injection). A notable discovery during testing: `rm -rf /` does not trigger the metacharacter regex because it contains no actual metacharacters (backticks, pipes, `$(...)`, etc.) — the validator catches injection vectors, not destructive commands themselves. Destructive command detection would require a different approach (command allowlisting or pattern matching on dangerous binaries).

The agent-runs tracker provides a structured write/read interface for agent execution telemetry. The writer supports three operations: `start` (record that an agent began a task), `finish` (record completion with success/failure status and optional metadata), and `prune` (remove old entries to prevent unbounded growth). The reader supports querying by agent type, time range, and status. This replaces ad-hoc logging with a structured, queryable format that future skills can consume — for example, `/qualia-postmortem` could read agent-runs to identify which agent failed and why.

The `install.js` script was extended with the `QUALIA_BIN_FILES` constant to copy the new `bin/lib/` files during framework installation. A stale "v2 to v3" migration help line was also caught and fixed during the audit. The tarball size (301kB, 100 files) was verified as reasonable before the merge to main.

Two spec documents (plan-contract and agent-runs) were reviewed and revised for internal consistency before implementation. Fixes included: correcting atomicity claims (plan-contract validation is not atomic across multiple tasks), clarifying evidence structure in agent-runs, removing a truncation contradiction in the spec, adding a privacy section, and standardizing failure codes. This spec-then-build workflow — write the spec, review it for contradictions, then implement — produced clean implementations without mid-build design pivots.

The npm publish succeeded on the first attempt in a follow-up session (21:29), with a second attempt returning 403 confirming the package was already live. Unlike v4.3.0 where 2FA blocked the publish mid-session, v4.4.0's publish completed cleanly. PR #17 was merged to main at commit `8f1a930` and tagged `v4.4.0`. The full test suite (226 tests across 5 suites) passed before merge.

Alongside the core substrate modules, several hardening items were shipped: state lock failures, tracking-sync failures, and deploy gate failures now produce real errors instead of silent no-ops. A `set-erp-key` CLI command was added, the `migrate` command now installs the full hook set, and the help output was corrected to reflect actual counts (28 skills, 9 hooks, 6 rules, 21 templates).

Skill rewiring — updating existing skills like `/qualia-build` and `/qualia-verify` to consume plan-contract and agent-runs — was explicitly deferred to v4.5.0 to keep this release focused on the substrate layer. The user requested v4.5 skill wiring at the end of session 21:29 but work had not yet started. The user's local machine is still on v4.1.1 and needs `npx qualia-framework@latest install` to pick up v4.4.0.

## Related Concepts

- [[concepts/qualia-framework-v4.3.0-release]] — Predecessor release; v4.3.0 shipped the memory loop, v4.4.0 adds execution validation and observability
- [[concepts/verification-contract-gaps]] — The verification gaps discovered during Kids Festive M2 (plans passing without real infrastructure) are the class of problem that plan-contract validation aims to catch earlier in the pipeline
- [[concepts/qualia-os-unification]] — The zero-dependency design philosophy shared across all `bin/lib/` modules
- [[concepts/opt-in-skills-vs-auto-hooks]] — Plan-contract validation runs inline during `/qualia-build`, not as an auto-hook — consistent with the skill activation principle

## Sources

- [[daily/2026-04-28.md]] — Session at 21:15: "Built three new modules: `bin/lib/plan-contract.js` (validator + helpers), `bin/lib/agent-runs.js` (writer with start/finish/prune + reader), CLI `agents` subcommand"
- [[daily/2026-04-28.md]] — Session at 21:15: "Reviewed and revised two spec docs (plan-contract and agent-runs) for internal consistency — fixed atomicity claims, evidence structure, truncation contradictions, privacy section, failure codes"
- [[daily/2026-04-28.md]] — Session at 21:15: "Smoke test for shell metacharacter detection: `rm -rf /` doesn't trigger the regex (no metacharacters), need actual metacharacters like backticks/pipes to test properly"
- [[daily/2026-04-28.md]] — Session at 21:15: "Tarball verified at 301kB/100 files before merge"
- [[daily/2026-04-28.md]] — Session at 21:15: "Skill rewiring deferred to follow-up — this commit lands the substrate only"
- [[daily/2026-04-28.md]] — Session at 21:29: "npm publish succeeded on first attempt; second attempt got 403 confirming it was already live"
- [[daily/2026-04-28.md]] — Session at 21:29: "PR #17 merged to main at `8f1a930`, tagged `v4.4.0`, 226 tests passing (5 suites)"
- [[daily/2026-04-28.md]] — Session at 21:29: "v4.5 scope: wire planner, builder, and verifier skills to consume plan-contract format and write to agent-runs telemetry"
- [[daily/2026-04-28.md]] — Session at 21:29: "Hardened items shipped: state lock/tracking-sync/deploy gate failures produce real errors; `set-erp-key` command; `migrate` installs full hook set; help counts corrected to 28 skills / 9 hooks / 6 rules / 21 templates"
