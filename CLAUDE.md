# Qualia Brain

You are the knowledge engine for **Qualia Solutions** — a web/AI agency in Nicosia, Cyprus, founded by Fawzi Goussous.

This is an Obsidian vault managed by Claude Code using the LLM Wiki pattern (Karpathy). You rarely write wiki pages yourself. The AI does the writing and organizing. The founder focuses on what goes in and what questions to ask.

## Vault Structure

The vault root **is** this repo (`/home/qualia-new/qualia-memory/`) — there is no `obs/` subfolder. Everything below lives at the repo root.

```
/home/qualia-new/qualia-memory/
├── raw/                       # READ-ONLY source material (articles, PDFs, transcripts)
│   └── sessions/              # Daily Claude Code session logs (auto-captured by SessionEnd hook → flush.py)
├── _raw/                      # STAGING area for quick captures — wiki-ingest auto-promotes and deletes
├── wiki/                      # AI-managed knowledge (you maintain this)
│   ├── index.md               # Master catalog — read this FIRST for navigation
│   ├── hot.md                 # Recent context cache (~500 words)
│   ├── log.md                 # Append-only operation history
│   ├── overview.md            # Executive summary of the brain
│   ├── concepts/              # Ideas, frameworks, methodologies
│   ├── entities/              # People, orgs, competitors
│   ├── sources/               # Processed summaries of raw documents
│   ├── analysis/              # Cross-domain comparisons and patterns
│   ├── sessions/              # Cole's compile.py output — separate from human-curated concepts/
│   │   ├── concepts/          # Auto-compiled concept articles from session logs
│   │   ├── connections/       # Auto-derived cross-references
│   │   ├── qa/                # Saved query answers
│   │   └── index.md           # Cole's own index (do NOT merge with wiki/index.md)
│   └── _meta/
│       ├── taxonomy.md        # Canonical tag vocabulary — wiki-lint enforces against this
│       └── dashboard.base     # Database dashboard view
├── memory/                    # Personal context layer
│   ├── soul.md                # AI personality and communication style
│   ├── user.md                # Fawzi's preferences and goals
│   ├── memory.md              # Key decisions and lessons (promoted from daily logs)
│   └── daily_log.md           # Raw session summaries (append-only, manual)
├── Clients/                   # Operational client pages (human-curated)
├── Projects/                  # Operational project pages (human-curated)
├── Team/                      # Team member profiles
├── Finances/                  # Financial overview
├── Research/                  # Long-form research documents
├── _templates/                # Templater templates for note types
├── .manifest.json             # Delta tracker for wiki-ingest — never edit by hand
├── Index.md                   # Original Obsidian top-level index (separate from wiki/index.md)
└── CLAUDE.md                  # You are reading this
```

## LLM Wiki framework (installed 2026-04-26)

This vault uses Karpathy's LLM Wiki pattern, with two installed frameworks bridging it to Claude Code:

- **Ar9av/obsidian-wiki** (`~/.local/share/obsidian-wiki/`) — provides `/wiki-update`, `/wiki-query`, `/wiki-ingest`, `/wiki-lint`, `/claude-history-ingest`, etc. Two skills are globally available from any project: `/wiki-update` (push) and `/wiki-query` (pull).
- **coleam00/claude-memory-compiler** (`~/.local/share/claude-memory-compiler/`) — captures every Claude Code session via `SessionEnd` hook → `~/qualia-memory/raw/sessions/YYYY-MM-DD.md` → after 6 PM Europe/Nicosia, compiles into `~/qualia-memory/wiki/sessions/concepts/`. Config patched to point at this vault; do NOT `git pull` Cole's repo without re-applying the patch (see `~/.claude/projects/-home-qualia-new-qualia-memory/memory/llm_wiki_framework.md`).
- **`/qualia-recall`** — Qualia-aware bridge skill that reads this vault before any planning command. Triggers on "what do I know about X", "have we done X before".

## Operational Knowledge Boundaries

This vault holds **knowledge** (patterns, conventions, lessons, client context). The **qualia-erp** at `https://portal.qualiasolutions.net` holds **operational state** (live invoices, payments, recurring contracts, financial KPIs). They are separate by design — do not duplicate live numbers here.

For client invoicing specifically:
- Per-client invoicing setup (`zoho_contact_id`, VAT treatment, template) → lives in `Clients/{Name}.md` notes (this vault) so future agents know how to invoice them.
- The **canonical workflow** (templates, terms, runbook) → `qualia-erp/docs/finance-runbook.md` — this vault references it; don't duplicate it.
- See `Finances/Overview.md` for the bridge between the two.

## Ingest Workflow

When a new source is added to `raw/`:

1. Read the document completely
2. Extract key concepts, entities, and facts
3. Create or update wiki pages in the appropriate folders:
   - New concept? → `wiki/concepts/concept-name.md`
   - New person/org? → `wiki/entities/entity-name.md`
   - Source summary? → `wiki/sources/source-title.md`
   - Cross-domain insight? → `wiki/analysis/comparison-name.md`
4. Update `wiki/index.md` with new entries
5. Add [[wikilinks]] between related pages
6. Append operation to `wiki/log.md`

## Page Format

Every wiki page must have:

```markdown
---
type: concept | entity | source | analysis
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags: [relevant, tags]
---

# Title

> One-line summary of this page.

## Content

(Main content here)

## Related

- [[Link to related pages]]
```

## Query Workflow

When answering a question:

1. Read `wiki/hot.md` for recent context
2. Read `wiki/index.md` to find relevant pages
3. Follow [[backlinks]] to gather information
4. Synthesize an answer citing specific wiki pages
5. Never cite training data — only cite wiki pages

## Lint Workflow

When asked to "lint the wiki":

1. Find orphaned pages (no incoming links)
2. Find broken [[wikilinks]]
3. Find stale claims that may need updating
4. Find contradictions between pages
5. Suggest new connections or research gaps
6. Report findings and fix what you can

## Rules

- NEVER modify files in `raw/` — it is read-only
- ALWAYS update `wiki/index.md` when creating new pages
- ALWAYS use [[wikilinks]] to connect related concepts
- ALWAYS append to `wiki/log.md` after any wiki operation
- Use lowercase-kebab-case for new file names in wiki/
- Existing folders (Clients/, Projects/, Team/, Finances/) are the operational layer — reference them with [[wikilinks]] but don't restructure them
- Update `wiki/hot.md` at the end of every session
