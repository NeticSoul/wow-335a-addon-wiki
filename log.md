# Log

Chronological record of wiki operations.

## [2026-04-15] init | Wiki scaffolding created

- Created SCHEMA.md with wiki conventions and workflows
- Created overview.md, log.md
- Source: 34 chapters, ~2M chars total from "World of Warcraft Programming" (3.3.5a)
- Wiki directory structure: chapters/, concepts/, entities/, reference/

## [2026-04-15] ingest | All 34 chapter summaries created

- Created ch01–ch34 in wiki/chapters/
- Each page: YAML frontmatter, overview, key concepts table, code examples, connections
- Part I (Ch 1–7): Lua + XML fundamentals
- Part II (Ch 8–13): Addon creation (BagBuddy tutorial)
- Part III (Ch 14–26): Advanced techniques (CombatTracker + specialized topics)
- Part IV (Ch 27–30): Reference (API, widgets, events)
- Part V (Ch 31–34): Appendices (best practices, libraries, VCS, resources)

## [2026-04-15] synthesize | 18 concept pages created

- Cross-cutting topic pages in wiki/concepts/
- lua-language, xml-layout, addon-structure, toc-file, saved-variables
- frames-and-widgets, frame-templates, wow-api, events-system, secure-execution
- unit-ids, combat-log, function-hooking, slash-commands, key-bindings
- tooltips, graphics-and-textures, addon-libraries

## [2026-04-15] reference | 3 reference summary pages created

- wiki/reference/api-categories.md — all 60+ API categories from Ch 28
- wiki/reference/widget-hierarchy.md — complete type tree from Ch 29
- wiki/reference/events-catalog.md — 400+ events organized by domain from Ch 30

## [2026-04-15] entities | 4 entity pages created

- BagBuddy (Part II tutorial addon)
- CombatTracker (Part III tutorial addon)
- CombatStatus (secure template demo)
- SquareUnitFrames (raid frame addon)

## [2026-04-15] index | Master index built

- wiki/index.md — complete catalog of all 62 wiki pages
- Organized by: chapters, concepts, reference, entities
- Statistics: 34 chapters + 18 concepts + 3 reference + 4 entities + 3 meta = 62 pages

## [2026-04-17] enrich | Deep rewrite of core concept cluster

Source: `private/mcp/chapters_text/` raw MCP extracts only (no cross-contamination).
Pattern applied: Summary / Detailed Explanation / Examples / Common Bugs / Related Concepts.

Rewritten (deep expansion, 3–6× original length):
- `concepts/events-system.md` — dispatch contract, table-dispatch idiom, lifecycle/query event tables, UNIT_* filtering, BAG_UPDATE quirk, cost model
- `concepts/secure-execution.md` — trusted vs tainted, protected frames, full SecureActionButton type table, modified-attribute grammar, 9 SecureHandler templates, restricted environment, frame handles, state drivers
- `concepts/saved-variables.md` — 11-step load timeline, serialization rules, LoD, merge-defaults init, schema migration, PLAYER_LOGOUT
- `concepts/combat-log.md` — 8 base args, full prefix/suffix matrix, special events, spell-school bitmask, unit flags masks, CombatLog_Object_IsA, GUID format, threat API
- `concepts/frames-and-widgets.md` — widget type hierarchy, parenting/anchoring/layering three-tier model, draw layers, widget cheat sheet, textures/font strings, $parent/parentKey, /framestack
- `concepts/frame-templates.md` — virtual declaration, inheritance, script chaining, font definitions, Blizzard template catalog, copy-vs-inherit strategy
- `concepts/function-hooking.md` — four mechanisms compared, pre/post hook patterns, capture/release, SetScript vs HookScript, hooksecurefunc, hook chain, hooking alternatives
- `concepts/tooltips.md` — anatomy, owner/anchor lifecycle, Set* methods, OnTooltipSet* async, scanner pattern, localization via GlobalStrings, custom tooltip creation

New concept pages:
- `concepts/addon-loading-lifecycle.md` — authoritative lifecycle event ordering; load/login/reload/logout timeline
- `concepts/onupdate-throttling.md` — OnUpdate semantics, Delay/Throttle/Repeat patterns, performance rules, throttle choice heuristic

Index updated to reflect 20 concept pages (was 18); total 64 wiki pages.
