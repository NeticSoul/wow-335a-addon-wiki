---
title: Tooltips
type: concept
source_chapters: [24]
created: 2026-04-15
updated: 2026-04-17
tags: [tooltips, GameTooltip, scanning, ItemRefTooltip, OnTooltipSetItem, localization]
---

# Tooltips

## Summary

Tooltips are a dedicated widget type — `GameTooltip` — used both to **display contextual information** (mouseover popups) and, in the addon ecosystem, as a **data source**. Some item/spell/unit properties (e.g. soulbound status, set bonus wording, custom trinket effects, spell flavor text) have no API query; the only way to read them is to load the data into a hidden tooltip and parse the resulting font strings.

Two tooltip instances in the default UI cover the bulk of use:

- **`GameTooltip`** — contextual, moved and populated continuously as the mouse hovers.
- **`ItemRefTooltip`** — static, popped up when the user shift-clicks a link in chat. Can be dragged; closes via its X button.

Custom tooltip instances are created from `GameTooltipTemplate`.

## Detailed Explanation

### Anatomy

A `GameTooltip` is a frame containing a growing list of **two-column font strings**, named predictably:

```
GameTooltipTextLeft1   GameTooltipTextRight1    ← line 1 (slightly larger; used as title)
GameTooltipTextLeft2   GameTooltipTextRight2    ← line 2
GameTooltipTextLeft3   GameTooltipTextRight3
...
```

For any tooltip inheriting `GameTooltipTemplate`, replace `GameTooltip` with the tooltip's name. The columns grow as needed; the right column font string only exists for a line if `AddDoubleLine` was called for it.

### Lifecycle & Owner

Before populating, a tooltip must have an **owner** — the frame it is positioned relative to:

```lua
GameTooltip:SetOwner(ownerFrame, anchor [, xOff, yOff])
```

Anchor values:

| Anchor | Meaning |
|--------|---------|
| `ANCHOR_TOPRIGHT` | Above/right of owner. |
| `ANCHOR_RIGHT`    | To the right, vertically aligned. |
| `ANCHOR_BOTTOMRIGHT` | Below/right. |
| `ANCHOR_TOPLEFT` / `ANCHOR_LEFT` / `ANCHOR_BOTTOMLEFT` | Left variants. |
| `ANCHOR_CURSOR`   | Follows mouse cursor. |
| `ANCHOR_PRESERVE` | Keep existing position (for static tooltips). |
| `ANCHOR_NONE`     | No anchor; addon sets position manually. |

The tooltip is automatically **cleared** by `SetOwner` (contents from the previous owner are discarded). For invisible scanners, use `ANCHOR_NONE` with owner `WorldFrame` or `UIParent`:

```lua
scanner:SetOwner(WorldFrame, "ANCHOR_NONE")
```

### Adding Text (Three Methods)

#### `SetText(text [, r, g, b, a])`

Replaces the tooltip content with a single line (the title line).

```lua
ItemRefTooltip:SetText("Tooltip Title\n|cffffffffBody text|r", 1, 1, 1)
```

Embedded color codes (`|cAARRGGBB...|r`) are honored. Newlines split visually but remain a single font-string line; you are responsible for any wrapping.

#### `AddLine(text [, r, g, b, a, wrap])`

Appends a line. If `wrap` is `true`, long text wraps within the current tooltip width.

```lua
GameTooltip:AddLine("This text will wrap", 1, 1, 1, true)
```

#### `AddDoubleLine(textL, textR [, rL, gL, bL, rR, gR, bR])`

Appends a two-column line; note independent colors, but **no alpha** parameter.

```lua
GameTooltip:AddDoubleLine("Durability:", "100/100", 1, 1, 1, 0, 1, 0)
```

After adding lines, **call `tooltip:Show()`** — `Show` re-sizes the tooltip frame to fit the current content. Every `Add*` call silently increases the logical line count; `Show` commits the dimensions.

### Game-Element Loaders

The primary way to populate `GameTooltip` is via `Set*` methods that load the standard tooltip for a game object. Each method corresponds to a game context:

| Method | Loads tooltip for |
|--------|-------------------|
| `SetBagItem(bag, slot)` | Item in a bag slot. |
| `SetInventoryItem(unit, slot)` | Equipped item on a unit. |
| `SetLootItem(slot)` | Item in the open loot window. |
| `SetLootRollItem(rollID)` | Group-loot roll item. |
| `SetMerchantItem(index)` | Item for sale at vendor. |
| `SetBuybackItem(index)` | Buyback slot item. |
| `SetAuctionItem(listType, index)` | AH browse item. |
| `SetAuctionSellItem()` | Item being placed for sale. |
| `SetGuildBankItem(tab, slot)` | Guild bank item. |
| `SetInboxItem(index [, attachmentIndex])` | Mail attachment. |
| `SetSendMailItem(index)` | Outgoing mail attachment. |
| `SetTradePlayerItem(idx)` / `SetTradeTargetItem(idx)` | Active trade slot. |
| `SetQuestItem(type, index)` | Quest reward/required item. |
| `SetQuestLogItem(type, index)` | Item in quest log entry. |
| `SetQuestRewardSpell()` / `SetQuestLogRewardSpell()` | Spell reward. |
| `SetQuestLogSpecialItem(index)` | Special quest item. |
| `SetMerchantCostItem(itemIdx, costIdx)` | Token cost for vendor item. |
| `SetSpell(id, bookType)` | Spell from spellbook. |
| `SetTalent(tab, index)` | Talent tree entry. |
| `SetGlyph(slotID)` | Glyph. |
| `SetUnit(unit)` | Unit (player, NPC). |
| `SetUnitAura(unit, idx [, filter])` | Generic aura. |
| `SetUnitBuff(unit, idx)` / `SetUnitDebuff(unit, idx)` | Buff/debuff. |
| `SetHyperlink("item:12345:...")` | Generic hyperlink: spell, item, quest, enchant, achievement. |
| `SetHyperlinkCompareItem(link, compareIdx [, shift])` | Compare-to-equipped. |
| `SetSocketGem(idx)` / `SetSocketItem()` / `SetExistingSocketGem(idx)` | Socketing UI. |
| `SetPetAction(idx)` | Pet bar button. |
| `SetShapeshift(idx)` | Shapeshift button. |
| `SetTotem(slot)` | Active totem. |
| `SetTracking()` | Current tracking selection. |
| `SetTrainerService(idx)` | Class/profession trainer entry. |
| `SetTradeSkillItem(idx [, reagent])` | Tradeskill recipe item or reagent. |
| `SetCurrencyToken(idx)` / `SetBackpackToken(idx)` | Currency display. |
| `SetEquipmentSet(name)` | Named equipment set. |
| `SetPossession()` | Vehicle/possession UI. |

### Asynchronous Loading

Tooltip contents from server-authoritative data (items, spells) are **asynchronous**: the client may need to request item info from the server. The tooltip fires dedicated scripts when content is ready:

| Script | Fires when |
|--------|-----------|
| `OnTooltipSetItem` | Item tooltip filled. `self:GetItem()` → `name, link`. |
| `OnTooltipSetSpell` | Spell tooltip filled. `self:GetSpell()` → `name, rank, id`. |
| `OnTooltipSetUnit` | Unit tooltip filled. `self:GetUnit()` → `name, unit`. |
| `OnTooltipSetAchievement` | Achievement tooltip filled. |
| `OnTooltipSetQuest` | Quest tooltip filled. |
| `OnTooltipCleared` | Content cleared (owner change, new hover, etc.). |
| `OnTooltipSetFramestack`, `OnTooltipSetDefaultAnchor` | Specialty. |

In shared UI code, prefer **hooking** these via `HookScript` rather than `SetScript` — other addons may rely on existing handlers:

```lua
GameTooltip:HookScript("OnTooltipSetItem", function(self)
    local name, link = self:GetItem()
    if not link then return end
    -- add custom lines...
    self:Show()   -- recompute size after adding
end)
```

### Scanning: Reading Tooltip Data

The most reliable way to read server-authored tooltip data in addon code is to:

1. Create a **dedicated hidden scanner tooltip** (don't scrape from `GameTooltip` — other addons may have modified it).
2. `SetOwner(WorldFrame, "ANCHOR_NONE")` to keep it off-screen.
3. Call the appropriate `Set*` method.
4. Read `<TooltipName>TextLeft<N>:GetText()` and `<TooltipName>TextRight<N>:GetText()` for each line.
5. Compare against **`GlobalStrings.lua`** constants for localization.

```lua
local scanner = CreateFrame("GameTooltip", "MyAddonScanTip",
                            nil, "GameTooltipTemplate")
scanner:SetOwner(WorldFrame, "ANCHOR_NONE")

local function IsSoulbound(bag, slot)
    scanner:ClearLines()
    scanner:SetBagItem(bag, slot)
    for i = 1, scanner:NumLines() do
        local txt = _G["MyAddonScanTipTextLeft" .. i]:GetText()
        if txt == ITEM_SOULBOUND or txt == ITEM_ACCOUNTBOUND then
            return true
        end
    end
    return false
end
```

Why `ITEM_SOULBOUND` instead of `"Soulbound"`:

```lua
-- From GlobalStrings.lua:
ITEM_SOULBOUND     = "Soulbound"       -- enUS
ITEM_ACCOUNTBOUND  = "Account Bound"
-- In deDE client: ITEM_SOULBOUND = "Seelengebunden"
```

Hard-coding English strings breaks the addon on every non-English client. Always look up the corresponding `ITEM_*`, `QUEST_*`, etc. constant in `GlobalStrings.lua`.

### Color Codes in Tooltip Text

Tooltip text honors Blizzard color escapes:

```
|cAARRGGBB   <text>   |r
|Hitem:...|h[Name]|h   → hyperlink (does not trigger link handler in tooltip)
```

Reading `GetText()` returns the raw string **with escapes intact**. Strip with `string.gsub(s, "|c%x%x%x%x%x%x%x%x(.-)|r", "%1")` before pattern matching.

### Custom Tooltips

Create a permanent custom tooltip as a child frame:

```xml
<GameTooltip name="MyAddonTooltip" parent="UIParent"
             inherits="GameTooltipTemplate" hidden="true"/>
```

Or in Lua:

```lua
local t = CreateFrame("GameTooltip", "MyAddonTooltip",
                      UIParent, "GameTooltipTemplate")
t:Hide()
```

Custom tooltips have the same font-string naming pattern (`MyAddonTooltipTextLeft1`, ...).

### Modifying Existing Tooltip Borders/Backdrops

```lua
GameTooltip:SetBackdropColor(0, 0, 0, 0.8)
GameTooltip:SetBackdropBorderColor(1, 0, 0)   -- red border, e.g. for soulbound
```

Reset happens automatically in `OnTooltipCleared` — or on next `SetOwner`.

## Examples

### Add equipment-set info to item tooltips (Ch 24)

```lua
local inSet = {}
for i = 1, GetNumEquipmentSets() do
    local name = GetEquipmentSetInfo(i)
    for _, itemID in pairs(GetEquipmentSetItemIDs(name)) do
        inSet[itemID] = (inSet[itemID] and (inSet[itemID] .. ", ") or "") .. name
    end
end

GameTooltip:HookScript("OnTooltipSetItem", function(self)
    local name, link = self:GetItem()
    if not link or not IsEquippableItem(link) then return end
    local id = tonumber(link:match("Hitem:(%d+)"))
    if inSet[id] then
        self:AddLine("Equipment Set: " .. inSet[id], 0.2, 1, 0.2)
    else
        self:AddLine("Not in any equipment set", 1, 0.4, 0.4)
    end
    self:Show()   -- recompute size
end)
```

### Highlight soulbound items with a red border (Ch 24)

```lua
GameTooltip:HookScript("OnTooltipSetItem", function(self)
    local boundText = _G[self:GetName() .. "TextLeft2"]:GetText()
    if boundText == ITEM_SOULBOUND or boundText == ITEM_ACCOUNTBOUND then
        self:SetBackdropBorderColor(1, 0, 0)
    end
end)
GameTooltip:HookScript("OnTooltipCleared", function(self)
    self:SetBackdropBorderColor(1, 1, 1)   -- restore
end)
```

### Scanner for soulbound detection

```lua
local scan = CreateFrame("GameTooltip", "SoulboundScan",
                         nil, "GameTooltipTemplate")
scan:SetOwner(WorldFrame, "ANCHOR_NONE")

local function IsSoulbound(bag, slot)
    scan:ClearLines()
    scan:SetBagItem(bag, slot)
    for i = 2, math.min(scan:NumLines(), 4) do
        local t = _G["SoulboundScanTextLeft" .. i]:GetText()
        if t == ITEM_SOULBOUND or t == ITEM_ACCOUNTBOUND then
            return true
        end
    end
    return false
end
```

## Common Bugs

- **Using `SetScript` instead of `HookScript` on `OnTooltipSet*`** — destroys the default UI's handler and breaks comparison tooltips, quest tooltips, etc. Use `HookScript` in released code.
- **Hard-coding English strings** — breaks on non-enUS clients. Always use `GlobalStrings.lua` constants.
- **Forgetting `self:Show()` after `AddLine`** — content added but tooltip keeps old size; last line may be clipped.
- **Reading from `GameTooltip` directly** for data scanning — other addons' added lines pollute your scan. Use a dedicated scanner tooltip.
- **Parsing color codes as data** — call `gsub` to strip them before matching.
- **Scanner `GameTooltip` owner is visible** — scanner becomes visible. Always `SetOwner(WorldFrame, "ANCHOR_NONE")`.
- **Line-index mismatch** — you saw "soulbound" on line 2 in testing but in some locales it's on line 3. Scan lines 2–4, not just line 2.
- **Clicking a `|H...|h` link inside a custom tooltip** — links only trigger their handler in `ItemRefTooltip`-style static tooltips, not in `GameTooltip`. Use `SetHyperlink` to show on demand.
- **Setting tooltip position before `SetOwner`** — `SetOwner` resets anchors; set custom anchor only after.

## Related Concepts

- [frames-and-widgets](frames-and-widgets.md) — tooltips are a widget type
- [frame-templates](frame-templates.md) — `GameTooltipTemplate` inheritance
- [function-hooking](function-hooking.md) — `HookScript` pattern for `OnTooltipSet*`
- [lua-language](lua-language.md) — string patterns for parsing tooltip lines
- [events-system](events-system.md) — some tooltip updates driven by events (`MERCHANT_SHOW`, etc.)
- [Ch 24 — Tooltips](../chapters/ch24-tooltips.md)
