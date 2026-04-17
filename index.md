---
title: Wiki Index
type: index
created: 2026-04-15
updated: 2026-04-17
---

# WoW 3.3.5a Programming Wiki — Index

Complete page catalog for this wiki, built from "World of Warcraft Programming" 2nd Edition.

## Navigation
- [Overview](overview.md) — Book structure, conceptual map, parts summary
- [Schema](SCHEMA.md) — Wiki conventions, page types, workflows
- [Log](log.md) — Chronological operation log

---

## Chapter Summaries

### Part I — Learning to Program (Ch 1–7)
| Page | Topic |
|------|-------|
| [ch01](chapters/ch01-programming-for-wow.md) | Programming for WoW — intro, addon ecosystem, dev tools |
| [ch02](chapters/ch02-lua-basics.md) | Lua Basics — types, variables, operators, scope |
| [ch03](chapters/ch03-functions-and-control.md) | Functions and Control Structures — if/for/while/repeat |
| [ch04](chapters/ch04-tables.md) | Working with Tables — arrays, dicts, metatables |
| [ch05](chapters/ch05-advanced-functions.md) | Advanced Functions — closures, varargs, iterators |
| [ch06](chapters/ch06-lua-standard-libraries.md) | Standard Libraries — string, table, math, restricted libs |
| [ch07](chapters/ch07-learning-xml.md) | Learning XML — elements, attributes, WoW UI schema |

### Part II — Creating WoW Addons (Ch 8–13)
| Page | Topic |
|------|-------|
| [ch08](chapters/ch08-anatomy-of-addon.md) | Anatomy of an Addon — TOC, SavedVariables, loading |
| [ch09](chapters/ch09-frames-and-widgets.md) | Frames and Widgets — hierarchy, anchoring, layers |
| [ch10](chapters/ch10-frame-templates.md) | Frame Templates — virtual frames, inheritance |
| [ch11](chapters/ch11-wow-api.md) | WoW API — C functions, FrameXML, unit IDs |
| [ch12](chapters/ch12-interacting-with-widgets.md) | Interacting with Widgets — handlers, input, building UI |
| [ch13](chapters/ch13-game-events.md) | Game Events — registration, dispatch, key events |

### Part III — Advanced Addon Techniques (Ch 14–26)
| Page | Topic |
|------|-------|
| [ch14](chapters/ch14-combattracker.md) | CombatTracker — DPS tracker project |
| [ch15](chapters/ch15-secure-templates.md) | Secure Templates — protected actions, taint |
| [ch16](chapters/ch16-key-bindings.md) | Key Bindings — Bindings.xml, binding API |
| [ch17](chapters/ch17-slash-commands.md) | Slash Commands — SlashCmdList, argument parsing |
| [ch18](chapters/ch18-onupdate.md) | OnUpdate — per-frame updates, throttling, animation |
| [ch19](chapters/ch19-function-hooking.md) | Function Hooking — pre/post hooks, hooksecurefunc |
| [ch20](chapters/ch20-custom-graphics.md) | Custom Graphics — TGA textures, SetTexCoord |
| [ch21](chapters/ch21-combat-log.md) | Combat Log — CLEU parsing, GUIDs, sub-events |
| [ch22](chapters/ch22-scroll-frames.md) | Scroll Frames — ScrollFrame, FauxScrollFrame |
| [ch23](chapters/ch23-dropdown-menus.md) | Dropdown Menus — UIDropDownMenu, EasyMenu |
| [ch24](chapters/ch24-tooltips.md) | Tooltips — GameTooltip, scanning, construction |
| [ch25](chapters/ch25-protected-action-in-combat.md) | Protected Action in Combat — snippets, state drivers |
| [ch26](chapters/ch26-unit-frames.md) | Unit Frames — SecureGroupHeader, raid frames |

### Part IV — Reference (Ch 27–30)
| Page | Topic |
|------|-------|
| [ch27](chapters/ch27-api-reference.md) | API Reference — full function signatures (486 pages) |
| [ch28](chapters/ch28-api-categories.md) | API Categories — categorized function index |
| [ch29](chapters/ch29-widget-reference.md) | Widget Reference — all widget methods/handlers |
| [ch30](chapters/ch30-events-reference.md) | Events Reference — 400+ events catalog |

### Part V — Appendices (Ch 31–34)
| Page | Topic |
|------|-------|
| [ch31](chapters/ch31-best-practices.md) | Best Practices — performance, naming, memory, security |
| [ch32](chapters/ch32-addon-libraries.md) | Addon Libraries — LibStub, Ace3 framework |
| [ch33](chapters/ch33-version-control.md) | Version Control — SVN, Git basics |
| [ch34](chapters/ch34-addon-author-resources.md) | Resources — websites, tools, community |

---

## Concept Pages

| Page | Topic | Key Chapters |
|------|-------|-------------|
| [lua-language](concepts/lua-language.md) | Lua 5.1 language synthesis | 2–6 |
| [xml-layout](concepts/xml-layout.md) | XML UI definition | 7, 9, 10 |
| [addon-structure](concepts/addon-structure.md) | Addon files and organization | 1, 8 |
| [toc-file](concepts/toc-file.md) | TOC directives reference | 8 |
| [saved-variables](concepts/saved-variables.md) | Persistent data | 8 |
| [addon-loading-lifecycle](concepts/addon-loading-lifecycle.md) | Load/login/reload event timeline | 8, 13 |
| [frames-and-widgets](concepts/frames-and-widgets.md) | Core UI widget system | 9, 12, 29 |
| [frame-templates](concepts/frame-templates.md) | Template and inheritance | 10, 15, 26 |
| [wow-api](concepts/wow-api.md) | API overview and types | 11, 27, 28 |
| [events-system](concepts/events-system.md) | Event registration and dispatch | 13, 30 |
| [secure-execution](concepts/secure-execution.md) | Taint, protection, snippets | 15, 25 |
| [unit-ids](concepts/unit-ids.md) | Unit ID tokens | 11, 26 |
| [combat-log](concepts/combat-log.md) | Combat log event parsing | 21 |
| [function-hooking](concepts/function-hooking.md) | Pre/post/secure hooks | 19 |
| [onupdate-throttling](concepts/onupdate-throttling.md) | Per-frame updates, delay/throttle patterns | 14, 18 |
| [slash-commands](concepts/slash-commands.md) | Custom chat commands | 17 |
| [key-bindings](concepts/key-bindings.md) | Input bindings | 16 |
| [tooltips](concepts/tooltips.md) | Tooltip construction and scanning | 24 |
| [graphics-and-textures](concepts/graphics-and-textures.md) | Custom artwork | 9, 20 |
| [addon-libraries](concepts/addon-libraries.md) | Libraries and Ace3 | 32 |

---

## Reference Pages

| Page | Content |
|------|---------|
| [api-categories](reference/api-categories.md) | All API function categories (from Ch 28) |
| [widget-hierarchy](reference/widget-hierarchy.md) | Complete widget type tree (from Ch 29) |
| [events-catalog](reference/events-catalog.md) | Events organized by domain (from Ch 30) |

---

## Entity Pages

| Page | Type | Description |
|------|------|-------------|
| [BagBuddy](entities/bagbuddy.md) | Sample Addon | Inventory filter/search (Part II tutorial) |
| [CombatTracker](entities/combattracker.md) | Sample Addon | DPS meter (Part III tutorial) |
| [CombatStatus](entities/combatstatus.md) | Sample Addon | Secure template demo (Ch 15, 25) |
| [SquareUnitFrames](entities/squareunitframes.md) | Sample Addon | Compact raid frames (Ch 26) |

---

## Statistics
- **Chapter summaries**: 34
- **Concept pages**: 20
- **Reference pages**: 3
- **Entity pages**: 4
- **Total wiki pages**: 64 (including index, overview, schema, log)
