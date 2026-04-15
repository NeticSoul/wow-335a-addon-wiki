---
title: "Ch 24 — Scanning and Constructing Tooltips"
type: chapter
source_chapters: [24]
part: "III — Advanced Addon Techniques"
pages: 12
chars: 18972
created: 2026-04-15
updated: 2026-04-15
tags: [tooltips, GameTooltip, scanning, tooltip-lines]
---

# Ch 24 — Scanning and Constructing Tooltips

## Overview

Tooltips are the most pervasive UI element in WoW. This chapter covers both reading tooltip data (scanning) and creating custom tooltips. Often the only way to get certain information is by reading tooltip text.

## Key Concepts

### Two Types of Tooltips
- **Contextual**: shown on mouseover, hidden on mouse leave (e.g., `GameTooltip`)
- **Static**: standalone display, often for item links (e.g., `ItemRefTooltip`)

### Setting Tooltip Content
Methods to fill tooltips with game data:

| Method | Shows Info About |
|--------|-----------------|
| `SetUnitBuff(unit, index)` | A unit's buff |
| `SetUnitDebuff(unit, index)` | A unit's debuff |
| `SetInventoryItem(unit, slot)` | Equipped item |
| `SetBagItem(bag, slot)` | Bag item |
| `SetSpellByID(id)` | Spell by ID |
| `SetHyperlink(link)` | Item/spell link |
| `SetAction(slot)` | Action bar slot |
| `SetQuestItem(type, index)` | Quest reward |

### Constructing Custom Tooltips
```lua
GameTooltip:SetOwner(frame, "ANCHOR_RIGHT")
GameTooltip:AddLine("Title", 1, 1, 1)       -- yellow
GameTooltip:AddLine("Description", 0.5, 0.5, 0.5)  -- gray
GameTooltip:AddDoubleLine("Left", "Right", 1, 1, 1, 1, 1, 1)
GameTooltip:Show()
```

### Tooltip Anchor Points
`"ANCHOR_TOPLEFT"`, `"ANCHOR_TOPRIGHT"`, `"ANCHOR_BOTTOMLEFT"`, `"ANCHOR_BOTTOMRIGHT"`, `"ANCHOR_CURSOR"`, `"ANCHOR_NONE"`, etc.

### Scanning Tooltips
Create a hidden tooltip to read data:
```lua
local scanner = CreateFrame("GameTooltip", "MyScanTooltip", nil, "GameTooltipTemplate")
scanner:SetOwner(WorldFrame, "ANCHOR_NONE")
scanner:SetBagItem(bag, slot)
-- Read lines:
local numLines = scanner:NumLines()
for i = 1, numLines do
    local left = _G["MyScanTooltipTextLeft" .. i]:GetText()
    local right = _G["MyScanTooltipTextRight" .. i]:GetText()
    -- parse left/right text
end
```

### Why Scan Tooltips?
Some information is **only** available via tooltips:
- Item binding status ("Soulbound", "Bind on Equip")
- Item durability
- Set bonus information
- Detailed spell descriptions with player-specific values

## Connections

- See [Tooltips](../concepts/tooltips.md) concept page
- Uses [string patterns](ch06-lua-standard-libraries.md) for parsing tooltip lines
- Complements the [WoW API](../concepts/wow-api.md) where API functions don't expose data
- Uses `GameTooltipTemplate` from [Frame Templates](../concepts/frame-templates.md)
