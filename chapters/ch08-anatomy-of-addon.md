---
title: "Ch 8 — Anatomy of an Addon"
type: chapter
source_chapters: [8]
part: "II — Programming in WoW"
pages: 18
chars: 34722
created: 2026-04-15
updated: 2026-04-15
tags: [addon, toc, saved-variables, localization, events, frames]
---

# Ch 8 — Anatomy of an Addon

## Overview

Explains the complete structure of a WoW addon: the TOC file and its directives, XML and Lua file loading, the widget system introduction, and event-based programming fundamentals.

## Key Concepts

### TOC File Directives

| Directive | Purpose | Example |
|-----------|---------|---------|
| `## Interface:` | API version compatibility | `30300` |
| `## Title:` | Display name (localizable) | `My Addon` |
| `## Notes:` | Description tooltip (localizable) | `A useful addon` |
| `## Dependencies:` | Required addons (loaded first) | `LibStub, Ace3` |
| `## OptionalDeps:` | Optional addons (loaded first if present) | `SharedMedia` |
| `## LoadOnDemand:` | Defer loading until requested | `1` or `0` |
| `## LoadsWith:` | Load when another addon loads | `Blizzard_RaidUI` |
| `## DefaultState:` | Enable/disable by default | `enabled` / `disabled` |
| `## LoadManager:` | External addon manages loading | `AddonLoader` |
| `## SavedVariables:` | Global persistent variables | `MyAddonDB` |
| `## SavedVariablesPerCharacter:` | Per-character persistent variables | `MyAddonCharDB` |
| `## Author:` | Author name (non-standard but common) | `ArgyleSocks` |
| `## X-*:` | Custom metadata (localizable) | `## X-Website: ...` |

### Interface Version Number
Built from WoW client version. 3.3.0 → `30300`. Controls "Out of date" vs "Incompatible" warnings in the addon selection screen.

### File Loading Order
Files listed in the TOC are loaded **in order**. XML files are processed first (frame creation), then Lua scripts execute. Dependencies are loaded before the addon.

### Addon Categories
Community-standard categories: Action Bars, Auction, Battlegrounds/PvP, Buffs, Chat/Communication, Combat, Data Export, Development Tools, Frame Modification, Guild, Interface Enhancements, Inventory, Map, Miscellaneous, plus class-specific categories.

### SavedVariables
- The **only** way to persist data between sessions
- Variable contents saved to disk on logout/reload
- Can be string, number, boolean, or table
- Global (`SavedVariables`) vs per-character (`SavedVariablesPerCharacter`)
- File location: `WTF\Account\<name>\SavedVariables\<addon>.lua`

### Localization
- Directives can be localized: `## Title-esES:`, `## Notes-deDE:`
- Use `GetLocale()` API to detect client language
- Common pattern: load a default locale file, then override with locale-specific strings

### Widget System (Introduction)
- All UI elements are **widgets** — Lua tables with methods
- Widget hierarchy: UIObject → Region → Frame → Button, etc.
- Frames can contain **textures** and **font strings**
- Every widget can have a **name** (global) and/or **parentKey** (keyed on parent)

### Event-Based Programming (Introduction)
- Frames register for **events** using `RegisterEvent("EVENT_NAME")`
- The `OnEvent` script handler fires when registered events occur
- Arguments: `self` (frame), `event` (name), `...` (event-specific args)

## Important Code Examples

```lua
-- Minimal .toc file
## Interface: 30300
## Title: Hello Friend
## Notes: A simple greeting addon
HelloFriend.xml
HelloFriend.lua

-- Registering for events
local frame = CreateFrame("Frame")
frame:RegisterEvent("PLAYER_LOGIN")
frame:SetScript("OnEvent", function(self, event, ...)
    print("Hello! You have logged in.")
end)
```

## Connections

- TOC file: see [TOC File](../concepts/toc-file.md) concept page
- SavedVariables: see [Saved Variables](../concepts/saved-variables.md)
- Frames introduced here are detailed in [Ch 9](ch09-frames-and-widgets.md)
- Events introduced here are detailed in [Ch 13](ch13-game-events.md)
- Addon structure: see [Addon Structure](../concepts/addon-structure.md)
