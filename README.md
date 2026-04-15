# WoW 3.3.5a Addon Development Wiki

A structured, interlinked knowledge base for World of Warcraft 3.3.5a (WotLK) addon development — **62 pages** of compiled, cross-referenced API docs, concepts, widget references, and implementation patterns.

<img width="1455" height="1110" alt="image" src="https://github.com/user-attachments/assets/82e33942-7a44-4661-afcb-59a300ba3393" />


---

## Origin

This wiki was built by processing the 34 chapters of:

> **World of Warcraft Programming: A Guide and Reference for Creating WoW Addons** (2nd Edition)
> *James Whitehead II & Rick Roe*

The book's content was ingested, summarized, cross-referenced, and organized into a persistent knowledge base using the [LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) pattern by [Andrej Karpathy](https://github.com/karpathy). Instead of re-reading raw documentation every time, the knowledge is already compiled and interlinked — ready for both humans and LLM agents.

## How to Use

**With Obsidian:** Open this folder as a vault. Browse the graph view, follow links between pages.

**With an LLM agent:** Point your coding assistant at [`index.md`](index.md) — it contains a navigable catalog of all 62 pages organized by topic.

**As plain Markdown:** Just read the files. Start with [`index.md`](index.md).

## Structure

```
├── index.md              ← Start here
├── overview.md           ← Book structure and conceptual map
├── SCHEMA.md             ← Wiki conventions and page types
├── log.md                ← Build log
├── chapters/      (34)   ← Chapter-by-chapter summaries
├── concepts/      (18)   ← Cross-referenced topic pages
├── reference/      (3)   ← API categories, widget hierarchy, events catalog
└── entities/       (4)   ← Sample addon breakdowns
```

### What's in Each Section

| Section | What you'll find | Examples |
|---------|-----------------|---------|
| **chapters/** | One summary per book chapter — key concepts, code examples, connections | `ch08-anatomy-of-addon.md`, `ch27-api-reference.md` |
| **concepts/** | Synthesized topic pages pulling from multiple chapters | `secure-execution.md`, `events-system.md`, `frames-and-widgets.md` |
| **reference/** | Condensed lookup tables for APIs, widgets, and events | `api-categories.md`, `widget-hierarchy.md`, `events-catalog.md` |
| **entities/** | Sample addon breakdowns showing how book concepts combine | `bagbuddy.md`, `combattracker.md` |

## Important: 3.3.5a Only

This wiki targets **WotLK 3.3.5a** (Interface 30300). These Retail-only namespaces do **not** exist in 3.3.5a:

`C_Timer`, `C_UnitAuras`, `C_Spell`, `C_Minimap`, `C_Map`, `C_Container`, `C_PvP`, `C_QuestLog`, `Enum.*`, `Mixin()`, `CreateFromMixins()`

## Acknowledgments

- **James Whitehead II & Rick Roe** for writing the definitive WoW addon programming guide
- **[Andrej Karpathy](https://github.com/karpathy)** for the [LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) pattern that inspired this project

## License

The compilation, cross-referencing, and synthesis of this wiki are original work released under the [MIT License](LICENSE). The underlying knowledge is derived from the publicly documented World of Warcraft addon API.
