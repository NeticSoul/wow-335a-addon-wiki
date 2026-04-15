---
title: "Ch 1 — Programming for World of Warcraft"
type: chapter
source_chapters: [1]
part: "I — Learning to Program"
pages: 10
chars: 14516
created: 2026-04-15
updated: 2026-04-15
tags: [introduction, addon, wow]
---

# Ch 1 — Programming for World of Warcraft

## Overview

Introductory chapter that establishes what WoW addons are, what they can and cannot do, and the tools and languages used to create them. WoW was released Nov 23, 2004 and uses a two-language system: **Lua** for behavior and **XML** for visual layout.

## Key Concepts

### What Is an Addon?
A collection of files inside a named directory within `Interface\AddOns\`:
- **TOC file** (.toc) — identifies the addon and lists files to load
- **Lua scripts** — define behavior
- **XML files** — define visual elements
- **Media files** — graphics and sounds

### What Addons Can Do
- Display additional information (sell prices, cast counts)
- Change display of interface elements (combat text, auction house)
- Provide new ways for players to take action (unit frames, action buttons)

### What Addons Cannot Do
- Automatic character movement
- Automatic target selection
- Automatic selection and use of spells or items
- Real-time communication with external programs

Blizzard's philosophy: **"smart players, not smart buttons"** — addons can display info and improve access, but must not make automatic decisions.

### The Game Client
Two major parts:
1. **Game world** — 3D space (terrain, players, enemies, objects) — NOT accessible via scripting
2. **User interface** — action buttons, unit frames, maps, options — fully customizable via addons

### Blizzard Load-on-Demand Addons
Many default UI features are modular addons loaded only when needed:

| Addon | Purpose |
|-------|---------|
| Blizzard_AuctionUI | Auction house interface |
| Blizzard_CombatLog | Linear combat log display |
| Blizzard_RaidUI | Raid management interface |
| Blizzard_AchievementUI | Achievement browser |
| Blizzard_Calendar | In-game calendar |
| Blizzard_BarbershopUI | Character customization |
| And ~15 more... | |

### Installation Directories

| OS | Default Path |
|----|-------------|
| Windows XP | `C:\Program Files\World of Warcraft` |
| Windows Vista | `C:\Users\Public\Games\World of Warcraft` |
| Mac OS X | `/Applications/World of Warcraft` |

Addons go in: `<WoW Dir>\Interface\AddOns\<AddonName>\`

### Tools Introduced
- **User Interface Customization Tool** — extracts Blizzard's FrameXML files for reference
- **WowLua** — in-game Lua interpreter addon by the book's authors
- **WebLua** — browser-based Lua interpreter at wowprogramming.com

## Connections

- Introduces the two core languages: [Lua](../concepts/lua-language.md) and [XML](../concepts/xml-layout.md)
- Sets up the [Addon Structure](../concepts/addon-structure.md) explored in depth in Ch 8
- Mentions [Secure Execution](../concepts/secure-execution.md) restrictions expanded in Ch 15 and 25
- The BagBuddy project from Ch 9–13 builds on everything introduced here
