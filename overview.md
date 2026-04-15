---
title: "World of Warcraft Programming — Book Overview"
type: overview
created: 2026-04-15
updated: 2026-04-15
tags: [overview, synthesis]
---

# World of Warcraft Programming — Overview

**"World of Warcraft Programming: A Guide and Reference for Creating WoW Addons"** (2nd Edition) is a comprehensive guide to addon development for WoW 3.3.5a (Wrath of the Lich King, Interface 30300). It covers the Lua programming language, WoW-specific XML, the WoW API, and a wide range of UI programming techniques.

## Book Structure

The book is divided into five parts spanning 34 chapters:

| Part | Chapters | Focus | Pages |
|------|----------|-------|-------|
| **I — Learning to Program** | 1–7 | Lua language fundamentals and XML markup | ~122 |
| **II — Programming in WoW** | 8–13 | Addon anatomy, frames, API, events | ~164 |
| **III — Advanced Addon Techniques** | 14–26 | Combat tracking, secure templates, hooking, graphics, menus, tooltips, unit frames | ~274 |
| **IV — Reference** | 27–30 | API Reference, API Categories, Widget Reference, Events Reference | ~766 |
| **V — Appendices** | A–D (31–34) | Best practices, libraries, version control, resources | ~97 |

## Core Thesis

WoW provides an extremely powerful, event-driven addon system built on two languages:

- **[Lua](concepts/lua-language.md)** — defines addon behavior (logic, computation, data)
- **[XML](concepts/xml-layout.md)** — defines addon visual structure (frames, textures, font strings)

Addons operate within a **sandbox** with strict security rules. They cannot automate gameplay, access files, or communicate with external programs. The security model distinguishes **secure** (Blizzard) code from **tainted** (addon) code, enforcing restrictions especially during combat. See [Secure Execution](concepts/secure-execution.md).

## Key Projects Built in the Book

| Addon | Chapters | Purpose |
|-------|----------|---------|
| **BagBuddy** | 9–13 | Inventory filter/display — teaches frames, widgets, templates, API, events |
| **CombatTracker** | 14 | Damage tracking — teaches event handling, spec-first design |
| **CombatStatus** | 21 | Combat log parser — teaches the COMBAT_LOG_EVENT system |
| **ScrollFrameTest** | 22 | Scroll frame demo — teaches ScrollFrame and FauxScrollFrame |
| **DropDownTest** | 23 | Dropdown menus — teaches UIDropDownMenu system |
| **SquareUnitFrames** | 26 | Raid/party unit frames — teaches SecureGroupHeaderTemplate |

## Conceptual Map

```
┌─────────────────────────────────────────────────┐
│                  THE ADDON                       │
│                                                  │
│  .toc file ─► File list ─► XML + Lua loaded     │
│                                                  │
│  ┌──────────┐    ┌───────────┐    ┌───────────┐ │
│  │   XML    │    │    Lua    │    │   Media   │ │
│  │ (layout) │◄──►│ (behavior)│    │ (textures)│ │
│  └────┬─────┘    └─────┬─────┘    └───────────┘ │
│       │                │                         │
│       ▼                ▼                         │
│  ┌─────────────────────────────┐                 │
│  │     Frames & Widgets        │                 │
│  │  (Button, EditBox, etc.)    │                 │
│  └────────────┬────────────────┘                 │
│               │                                  │
│       ┌───────┴───────┐                          │
│       ▼               ▼                          │
│  ┌─────────┐   ┌──────────────┐                  │
│  │ Events  │   │   WoW API    │                  │
│  │ System  │   │ (1000+ funcs)│                  │
│  └─────────┘   └──────────────┘                  │
│                                                  │
│  ┌──────────────────────────────────────────┐    │
│  │        Security / Taint System           │    │
│  │  Secure code ←→ Protected frames         │    │
│  │  Tainted code → restricted during combat │    │
│  └──────────────────────────────────────────┘    │
└─────────────────────────────────────────────────┘
```

## Part Summaries

### Part I — Learning to Program (Ch 1–7)
Introduces the Lua programming language from scratch: variables, types, functions, control structures, tables, iterators, closures, coroutines (briefly), standard libraries (string, table, math), and XML markup for WoW UI layout. All foundational knowledge needed before touching the WoW API.

### Part II — Programming in WoW (Ch 8–13)
Bridges Lua/XML knowledge to actual addon development. Covers the [.toc file](concepts/toc-file.md) structure, [addon anatomy](concepts/addon-structure.md), [frames and widgets](concepts/frames-and-widgets.md), [frame templates](concepts/frame-templates.md), the [WoW API](concepts/wow-api.md), widget interaction, and the [event system](concepts/events-system.md). Builds the BagBuddy addon incrementally.

### Part III — Advanced Addon Techniques (Ch 14–26)
Deep-dives into specialized topics: [combat tracking](chapters/ch14-combat-tracker.md), [secure templates](concepts/secure-execution.md), [key bindings](concepts/key-bindings.md), [slash commands](concepts/slash-commands.md), OnUpdate, [function hooking](concepts/function-hooking.md), [custom graphics](concepts/graphics-and-textures.md), [combat log](concepts/combat-log.md), [scroll frames](chapters/ch22-scroll-frames.md), [dropdown menus](chapters/ch23-dropdown-menus.md), [tooltips](concepts/tooltips.md), protected action in combat, and [unit frames](chapters/ch26-unit-frames-group-templates.md).

### Part IV — Reference (Ch 27–30)
Massive reference material: 1000+ API functions (Ch 27), API function categories (Ch 28), 25+ widget types with methods (Ch 29), and 400+ game events (Ch 30).

### Part V — Appendices (Ch 31–34)
Best practices for code quality and performance, [addon libraries](concepts/addon-libraries.md) (standalone and embedded, including LibStub and Ace3), version control with SVN/Git, and community resources (WoWInterface, WowAce, Curse, etc.).

## See Also

- [Index](index.md) — master catalog of all wiki pages
- [Log](log.md) — chronological record of wiki operations
