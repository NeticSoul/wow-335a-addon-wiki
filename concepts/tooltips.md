---
title: Tooltips
type: concept
source_chapters: [24]
created: 2026-04-15
updated: 2026-04-15
tags: [tooltips, scanning, GameTooltip]
---

# Tooltips

Pervasive UI element for displaying contextual information. Also used as a data source — some info is only available by reading tooltip text.

## Types
- **Contextual**: shown on mouseover (e.g., `GameTooltip`)
- **Static**: standalone display for item links (e.g., `ItemRefTooltip`)

## Constructing Tooltips
```lua
GameTooltip:SetOwner(frame, "ANCHOR_RIGHT")
GameTooltip:AddLine("Title", 1, 1, 1)
GameTooltip:AddDoubleLine("Left", "Right")
GameTooltip:Show()
```

## Setting Game Data
`SetUnitBuff()`, `SetBagItem()`, `SetInventoryItem()`, `SetSpellByID()`, `SetHyperlink()`, etc.

## Scanning (Reading Tooltip Data)
```lua
local scanner = CreateFrame("GameTooltip", "MyScan", nil, "GameTooltipTemplate")
scanner:SetOwner(WorldFrame, "ANCHOR_NONE")
scanner:SetBagItem(bag, slot)
for i = 1, scanner:NumLines() do
    local text = _G["MyScanTextLeft" .. i]:GetText()
    -- parse with string patterns
end
```

## See Also
- [Ch 24](../chapters/ch24-tooltips.md)
- [Lua Language](lua-language.md) (string patterns for parsing)
