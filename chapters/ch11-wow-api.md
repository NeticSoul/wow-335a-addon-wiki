---
title: "Ch 11 — Exploring the World of Warcraft API"
type: chapter
source_chapters: [11]
part: "II — Programming in WoW"
pages: 20
chars: 35450
created: 2026-04-15
updated: 2026-04-15
tags: [api, functions, unit-ids, c-functions, framexml, protected]
---

# Ch 11 — Exploring the World of Warcraft API

## Overview

Introduces the WoW API — the set of 1000+ functions that addons use to query game state and take action. Covers the three types of API functions, unit IDs, and how to explore the API.

## Key Concepts

### Types of API Functions

| Type | Description | Example |
|------|------------|---------|
| **C functions** | Defined in C, exposed to Lua. Most common. | `UnitHealth("player")` |
| **FrameXML functions** | Defined in Blizzard's Lua code | `ChatFrame_AddMessage()` |
| **Protected functions** | Only usable by Blizzard code | `CastSpellByName()` |

### Library-like APIs
WoW provides replacements for excluded standard libraries:

| Standard Library | WoW Equivalent |
|-----------------|----------------|
| `os.date` | `date()` |
| `os.time` | `time()` |
| `os.difftime` | `difftime()` |
| `debug.traceback` | `debugstack()` |

Additional utility functions with all-lowercase names: `debugprofilestart()`, `hooksecurefunc()`, `securecall()`, etc.

### Unit IDs
A recurring pattern — many API functions operate on **unit IDs**:

| Unit ID | Refers To |
|---------|-----------|
| `"player"` | The player |
| `"target"` | Player's target |
| `"focus"` | Player's focus target |
| `"pet"` | Player's pet |
| `"party1"` – `"party4"` | Party members |
| `"raid1"` – `"raid40"` | Raid members |
| `"boss1"` – `"boss4"` | Boss frames |
| `"mouseover"` | Unit under cursor |
| `"npc"` | NPC in dialog |

Compound IDs: `"targettarget"`, `"party1target"`, `"pettarget"`, etc.

### Key API Function Groups
- **Unit functions**: `UnitHealth()`, `UnitMana()`, `UnitLevel()`, `UnitClass()`, `UnitExists()`
- **Item functions**: `GetItemInfo()`, `GetContainerItemLink()`
- **Spell functions**: `GetSpellInfo()`, `GetSpellCooldown()`
- **Quest functions**: `GetQuestLogTitle()`, `GetNumQuestLogEntries()`
- **Chat functions**: `SendChatMessage()`
- **Auction functions**: `CalculateAuctionDeposit()`
- **Settings functions**: `GetCVar()`, `SetCVar()`

### Hardware Input Functions
Some API functions can only be called in response to hardware events (mouse clicks, key presses). These are more restrictive than protected functions — they require a direct user action in the call stack.

### Exploring the API
- Use `GetAddOnMetadata()` to read TOC metadata
- Extract FrameXML to study Blizzard's implementation
- Reference: Ch 27 (API Reference), Ch 28 (API Categories)
- Online: wowprogramming.com/docs

## Connections

- See [WoW API](../concepts/wow-api.md) concept page
- Unit IDs: see [Unit IDs](../concepts/unit-ids.md) concept page
- Protected functions relate to [Secure Execution](../concepts/secure-execution.md)
- Full reference in [Ch 27](ch27-api-reference.md) and [Ch 28](ch28-api-categories.md)
- Widget methods in [Ch 29](ch29-widget-reference.md)
