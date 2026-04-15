---
title: "Ch 26 — Creating Unit Frames with Group Templates"
type: chapter
source_chapters: [26]
part: "III — Advanced Addon Techniques"
pages: 38
chars: 61175
created: 2026-04-15
updated: 2026-04-15
tags: [unit-frames, raid-frames, SecureGroupHeader, party, SquareUnitFrames]
---

# Ch 26 — Creating Unit Frames with Group Templates

## Overview

Capstone chapter for Part III. Builds **SquareUnitFrames** — a full raid/party unit frame addon using `SecureGroupHeaderTemplate`. Covers filtering, sorting, grouping, and display of raid members.

## Key Concepts

### SecureGroupHeaderTemplate
A protected template that manages creation, placement, and update of unit frames for party/raid members — even during combat.

### Configuration Process
1. Create header inheriting `SecureGroupHeaderTemplate`
2. Set filtering attributes (who to display)
3. Set sorting/grouping attributes
4. Supply a child template for individual unit frames
5. Optionally provide a configuration function

### Filtering Attributes

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `showPlayer` | Include the player | `true` |
| `showSolo` | Show when not in group | `true` |
| `showParty` | Show in party | `true` |
| `showRaid` | Show in raid | `true` |
| `nameList` | Specific names to show | `"Alice,Bob"` |
| `groupFilter` | Raid groups to show | `"1,2,3"` |
| `roleFilter` | Roles to show | `"MAINTANK,MAINASSIST"` |
| `strictFiltering` | All conditions must match | `true` |

### Sorting/Grouping Attributes

| Attribute | Purpose |
|-----------|---------|
| `sortMethod` | `"NAME"` or `"INDEX"` |
| `sortDir` | `"ASC"` or `"DESC"` |
| `groupBy` | `"GROUP"`, `"CLASS"`, `"ROLE"` |
| `groupingOrder` | Order of groups: `"1,2,3,4,5,6,7,8"` or `"WARRIOR,PALADIN,..."` |

### Display Attributes

| Attribute | Purpose |
|-----------|---------|
| `point` | Anchor point for growth direction |
| `xOffset` / `yOffset` | Spacing between frames |
| `maxColumns` | Maximum columns |
| `unitsPerColumn` | Rows per column |
| `columnSpacing` | Space between columns |
| `columnAnchorPoint` | Column direction |

### Initial Configuration Function
Called when a new child frame is created:
```lua
header:SetAttribute("initialConfigFunction", [[
    self:SetWidth(50)
    self:SetHeight(50)
]])
```

### SquareUnitFrames Implementation
The chapter builds a complete addon with:
- Health bars (StatusBar widget)
- Name labels
- Class coloring
- Click-to-target (SecureUnitButtonTemplate)
- Right-click context menu
- Group display with sorting by class

## Connections

- Culmination of [Secure Execution](../concepts/secure-execution.md) concepts (Ch 15, 25)
- Uses [StatusBar](ch12-interacting-with-widgets.md) for health display
- Uses [Frame Templates](../concepts/frame-templates.md) for child frames
- State drivers and unit watch from [Ch 25](ch25-protected-action-in-combat.md)
- [Unit IDs](../concepts/unit-ids.md) for party/raid members
