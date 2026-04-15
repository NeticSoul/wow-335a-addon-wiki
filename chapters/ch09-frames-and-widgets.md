---
title: "Ch 9 — Working with Frames, Widgets, and Other Graphical Elements"
type: chapter
source_chapters: [9]
part: "II — Programming in WoW"
pages: 28
chars: 42406
created: 2026-04-15
updated: 2026-04-15
tags: [frames, widgets, textures, fontstrings, anchoring, layers, bagbuddy]
---

# Ch 9 — Working with Frames, Widgets, and Other Graphical Elements

## Overview

Core chapter that teaches frame creation, parenting, sizing, anchoring, textures, font strings, and the layer system. Introduces the **BagBuddy** addon project as a running example.

## Key Concepts

### Frames
- Central concept of WoW UI — all textures and font strings belong to a parent frame
- Frames can register for events, receive user interaction (clicks, drags)
- Created via XML `<Frame>` element or Lua `CreateFrame("Frame", name, parent)`

### Parenting
Three ways to set parent/child relationships:
1. **XML `parent` attribute**: `<Frame name="MyFrame" parent="UIParent">`
2. **XML hierarchy**: nested elements auto-parent
3. **Lua `SetParent()`**: change parent at runtime

Effects of parenting:
- Hidden parent → all children hidden
- Parent scale → children scale multiplicatively
- Parent alpha → children alpha multiplicatively

**UIParent** — special root frame used by most addons, hides when player presses Alt+Z.

### Sizing
- **Absolute**: `<Size x="384" y="512"/>` (pixels)
- **Relative**: `<RelDimension x="0.5" y="0.5"/>` (fraction of parent)

### Anchoring (Positioning)
Nine anchor points: `TOPLEFT`, `TOP`, `TOPRIGHT`, `LEFT`, `CENTER`, `RIGHT`, `BOTTOMLEFT`, `BOTTOM`, `BOTTOMRIGHT`.

```xml
<Anchor point="CENTER" relativeTo="UIParent" relativePoint="CENTER"/>
<Anchor point="TOPLEFT" relativeTo="OtherFrame" relativePoint="TOPRIGHT">
    <Offset x="5" y="0"/>
</Anchor>
```

- Frames can have **multiple anchors** (e.g., anchor both LEFT and RIGHT to stretch)
- `$parent` in names expands to parent's name

### Draw Layers
Five layers control rendering order (back to front):

| Layer | Typical Use |
|-------|------------|
| `BACKGROUND` | Background textures |
| `BORDER` | Border art |
| `ARTWORK` | Main content, icons |
| `OVERLAY` | Highlights, glows |
| `HIGHLIGHT` | Mouseover effects (auto-shown on hover) |

### Textures
- Display images or solid colors
- Contained within `<Layers><Layer level="...">` in XML
- Key methods: `SetTexture(path)`, `SetTexCoord()`, `SetVertexColor()`
- Can use Blizzard art paths: `"Interface\\Icons\\INV_Misc_QuestionMark"`

### Font Strings
- Display text content
- Contained within layers, like textures
- Key methods: `SetText()`, `SetFont()`, `SetTextColor()`, `SetJustifyH()`
- Can inherit from font objects for consistent styling

### Backdrops
Apply background and border art to frames:
```lua
frame:SetBackdrop({
    bgFile = "Interface\\DialogFrame\\UI-DialogBox-Background",
    edgeFile = "Interface\\DialogFrame\\UI-DialogBox-Border",
    tile = true, tileSize = 32, edgeSize = 32,
    insets = {left = 11, right = 12, top = 12, bottom = 11},
})
```

### Creating Frames in Lua (vs XML)
Everything possible in XML can also be done in Lua:
```lua
local f = CreateFrame("Frame", "MyFrame", UIParent)
f:SetSize(384, 512)
f:SetPoint("CENTER")
local tex = f:CreateTexture(nil, "BACKGROUND")
tex:SetAllPoints(f)
tex:SetTexture(0.1, 0.1, 0.1, 0.8)
```

## Connections

- Foundation for all visual addons
- See [Frames and Widgets](../concepts/frames-and-widgets.md) concept page
- Templates in [Ch 10](ch10-frame-templates.md) reduce repetitive frame definitions
- BagBuddy continues through Ch 10–13
- Widget interaction in [Ch 12](ch12-interacting-with-widgets.md)
