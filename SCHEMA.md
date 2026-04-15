# WoW 3.3.5a Addon Programming Wiki — Schema

## Purpose

This wiki is a structured, interlinked knowledge base built entirely from the book
"World of Warcraft Programming" (2nd Edition, covering WoW 3.3.5a / Wrath of the Lich King).
The LLM reads, summarizes, and cross-references the source chapters; the human browses,
queries, and directs the analysis.

## Source Material

All source material lives in `../chapters_text/`. These files are **immutable** —
the wiki reads from them but never modifies them. The manifest is in `manifest.json`.

The book has 34 chapters organized into 5 parts:

| Part | Chapters | Topic |
|------|----------|-------|
| I — Learning to Program | 1–7 | Lua language, XML for WoW |
| II — Programming in WoW | 8–13 | Addon anatomy, frames, API, events |
| III — Advanced Addon Techniques | 14–26 | Combat tracking, secure templates, hooking, graphics, menus, tooltips, unit frames |
| IV — Reference | 27–30 | API Reference, API Categories, Widget Reference, Events Reference |
| V — Appendices | A–D (31–34) | Best practices, libraries, version control, resources |

## Wiki Directory Layout

```
wiki/
├── SCHEMA.md              # This file — conventions and workflows
├── index.md               # Master catalog of all wiki pages
├── log.md                 # Chronological operation log
├── overview.md            # Book overview and synthesis
├── chapters/              # One summary page per chapter
│   └── chNN-slug.md
├── concepts/              # Cross-cutting concept pages
│   └── topic-slug.md
├── entities/              # Specific widgets, key API functions, notable addons
│   └── entity-slug.md
└── reference/             # Condensed reference pages (from ch 27–30)
    └── ref-slug.md
```

## Page Types

### Chapter Summary (`chapters/chNN-slug.md`)
- Frontmatter: `source`, `part`, `pages`, `chars`
- Sections: Overview, Key Concepts, Important Code Examples, Connections
- Links to concept and entity pages

### Concept Page (`concepts/topic-slug.md`)
- Frontmatter: `chapters` (list of source chapters)
- Sections: Definition, How It Works, Key APIs/Elements, Practical Usage, See Also
- Aggregates knowledge from multiple chapters on a single topic

### Entity Page (`entities/entity-slug.md`)
- Frontmatter: `type` (widget | api-function | addon | tool), `chapters`
- Sections: Description, Signature/Hierarchy, Usage, Related

### Reference Page (`reference/ref-slug.md`)
- Condensed, navigable summaries of the massive reference chapters
- Organized by category, with links to entity pages where they exist

## Frontmatter Convention

All wiki pages use YAML frontmatter:

```yaml
---
title: Page Title
type: chapter | concept | entity | reference
source_chapters: [1, 8, 11]
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags: [lua, api, frames]
---
```

## Linking Convention

- Internal wiki links use relative markdown links: `[Frames and Widgets](../concepts/frames-and-widgets.md)`
- Source references use: `(Source: Ch. N, p. X)`
- Wikilinks style `[[page-name]]` is also acceptable for Obsidian compatibility

## Ingest Workflow

1. Read the chapter source file from `../chapters_text/`
2. Write/update the chapter summary in `chapters/`
3. Extract or update concept pages in `concepts/`
4. Extract or update entity pages in `entities/`
5. Update `index.md` with new pages
6. Append an entry to `log.md`

## Query Workflow

1. Read `index.md` to identify relevant pages
2. Read those pages to gather information
3. Synthesize an answer with citations to source chapters
4. Optionally file the answer as a new wiki page

## Lint Workflow

Periodically check for:
- Orphan pages (no inbound links)
- Mentioned but missing pages (red links)
- Contradictions between pages
- Stale information
- Missing cross-references

## Conventions

- File names use kebab-case: `secure-execution.md`, `widget-button.md`
- Chapter files are prefixed: `ch01-`, `ch02-`, etc.
- All content comes exclusively from `chapters_text/` — no external sources
- Keep summaries concise but preserve key code examples and API signatures
- Prefer tables for structured data (API lists, widget methods, event parameters)
