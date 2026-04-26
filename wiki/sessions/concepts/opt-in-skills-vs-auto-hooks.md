---
title: "Opt-in Skills vs Auto-Hooks"
aliases: [auto-hook-antipattern, markitdown-proposal, skill-activation-pattern]
tags: [qualia-framework, architecture, design-pattern, hooks, skills]
sources:
  - "daily/2026-04-26.md"
created: 2026-04-26
updated: 2026-04-26
---

# Opt-in Skills vs Auto-Hooks

Auto-conversion hooks that run on every session are an anti-pattern when the conversion is heavy or situational. The principle was established during the v4.3.0 release cycle when Hasan proposed adding Microsoft's Markitdown CLI as a framework dependency with an auto-convert-on-session-start hook. The proposal was rejected in its auto-hook form but approved as an opt-in `/qualia-ingest` skill (~150 LOC, no hooks, no install changes).

## Key Points

- Hasan proposed: install Markitdown as a dependency, auto-convert DOCX/PPTX/XLSX files on every SessionStart hook — rejected because it adds latency to every session for a rarely-needed capability
- Counter-proposal accepted: opt-in `/qualia-ingest` skill that the user invokes explicitly when they have files to convert
- Claude Code's Read tool already handles PDFs natively — Markitdown's unique value is limited to DOCX, PPTX, XLSX, and audio formats
- This is the same class of issue as v4.1.0 audit finding #11 ("invisible knowledge files") — auto-loading content the user didn't ask for
- The memory loop's auto-capture (SessionEnd hook) is the counterexample: it works because it's lightweight, universal, and non-blocking

## Details

The decision reveals a clear design principle for the Qualia Framework's hook system: auto-hooks are justified when they meet three criteria simultaneously — lightweight execution (under 1 second), universal applicability (every session benefits), and non-blocking (failure doesn't break the session). The memory loop's SessionEnd hook passes all three: it copies a transcript file (fast), every session produces knowledge worth capturing (universal), and it spawns a detached background process (non-blocking).

Markitdown auto-conversion fails all three criteria. Converting DOCX/PPTX files is slow (multi-second), most sessions don't involve document ingestion (situational), and adding a Python dependency (`markitdown`) to every framework install increases surface area. The skill-based alternative is strictly better: the user runs `/qualia-ingest` only when they have documents to process, the conversion happens in that session's context, and the dependency can be lazily installed on first use.

The principle extends beyond Markitdown. Any proposed auto-hook should be evaluated against the three criteria. If it fails any one of them, it should be a skill instead. This is the framework's version of the Unix philosophy: do one thing well, and let the user compose capabilities explicitly rather than running everything automatically.

The `/qualia-ingest` skill was scoped at ~150 LOC with no hooks and no `install.js` changes — a follow-up PR for v4.3.1 or v4.4.0. The skill would support Markitdown's unique formats (DOCX, PPTX, XLSX, audio transcription) while leaving PDFs to Claude Code's native Read tool.

## Related Concepts

- [[concepts/memory-loop-architecture]] — The counterexample: an auto-hook that correctly passes all three criteria (lightweight, universal, non-blocking)
- [[concepts/qualia-framework-v4.3.0-release]] — The release cycle during which this design principle was established
- [[concepts/qualia-os-unification]] — The broader system integration where skill activation patterns matter

## Sources

- [[daily/2026-04-26.md]] — Session (04:09, first instance): "Markitdown: rejected Hasan's auto-convert-on-session-start + install.js approach. Approved concept as opt-in `/qualia-ingest` skill only"
- [[daily/2026-04-26.md]] — Session (04:09, first instance): "Auto-conversion hooks that run every session are an anti-pattern (same class of issue as v4.1.0 audit finding #11 'invisible knowledge files')"
- [[daily/2026-04-26.md]] — Session (04:09, first instance): "Claude Code's Read tool handles PDFs natively now — Markitdown's unique value is DOCX/PPTX/XLSX/audio only"
