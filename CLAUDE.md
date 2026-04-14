# Qualia Brain

You are the knowledge engine for **Qualia Solutions** — a web/AI agency in Nicosia, Cyprus, founded by Fawzi Goussous.

This is an Obsidian vault managed by Claude Code using the LLM Wiki pattern (Karpathy). You rarely write wiki pages yourself. The AI does the writing and organizing. The founder focuses on what goes in and what questions to ask.

## Vault Structure

```
obs/
├── raw/                   # READ-ONLY source material (articles, PDFs, transcripts)
├── wiki/                  # AI-managed knowledge (you maintain this)
│   ├── index.md           # Master catalog — read this FIRST for navigation
│   ├── hot.md             # Recent context cache (~500 words)
│   ├── log.md             # Append-only operation history
│   ├── overview.md        # Executive summary of the brain
│   ├── concepts/          # Ideas, frameworks, methodologies
│   ├── entities/          # People, orgs, competitors
│   ├── sources/           # Processed summaries of raw documents
│   ├── analysis/          # Cross-domain comparisons and patterns
│   └── meta/
│       └── dashboard.base # Database dashboard view
├── memory/                # Personal context layer
│   ├── soul.md            # AI personality and communication style
│   ├── user.md            # Fawzi's preferences and goals
│   ├── memory.md          # Key decisions and lessons (promoted from daily logs)
│   └── daily_log.md       # Raw session summaries (append-only)
├── Clients/               # Client profiles (existing)
├── Projects/              # Project details (existing)
├── Team/                  # Team member profiles (existing)
├── Finances/              # Financial overview (existing)
├── Research/              # Research documents (existing)
├── _templates/            # Templater templates for note types
└── Index.md               # Original vault index (existing)
```

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
