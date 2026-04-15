---
title: Graphics and Textures
type: concept
source_chapters: [9, 20]
created: 2026-04-15
updated: 2026-04-15
tags: [graphics, textures, tga, blp, artwork]
---

# Graphics and Textures

Custom visual assets for addons.

## Requirements
- Dimensions: **power of two** (32, 64, 128, 256, 512 max)
- Format: **TGA** (32-bit with alpha) or BLP (Blizzard proprietary)
- Must reside in the addon directory

## Using Textures
```lua
local tex = frame:CreateTexture(nil, "ARTWORK")
tex:SetTexture("Interface\\AddOns\\MyAddon\\myimage")  -- no extension
tex:SetTexCoord(0, 0.5, 0, 1)   -- use portion of texture
tex:SetVertexColor(1, 0, 0, 0.8) -- tint red, 80% opacity
```

## Blizzard Art Paths
- `Interface\\Icons\\INV_Misc_QuestionMark`
- `Interface\\DialogFrame\\UI-DialogBox-Background`
- Use TextureBrowser addon or extract FrameXML to discover paths

## See Also
- [Ch 9](../chapters/ch09-frames-and-widgets.md), [Ch 20](../chapters/ch20-custom-graphics.md)
- [Frames and Widgets](frames-and-widgets.md)
