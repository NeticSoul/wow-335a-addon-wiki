---
title: "Ch 20 — Creating Custom Graphics"
type: chapter
source_chapters: [20]
part: "III — Advanced Addon Techniques"
pages: 14
chars: 13912
created: 2026-04-15
updated: 2026-04-15
tags: [graphics, textures, tga, blp, gimp, photoshop]
---

# Ch 20 — Creating Custom Graphics

## Overview

Covers creating custom textures for WoW addons using GIMP, Photoshop, and Paint Shop Pro. Details the technical requirements for WoW-compatible graphics.

## Key Concepts

### Texture Requirements
- **Dimensions**: width and height must be **powers of two** (32, 64, 128, 256, 512)
- Width and height can differ (e.g., 32×256 is valid)
- Maximum: **512×512** pixels (tile for larger)
- **Format**: TGA (Targa) or BLP (Blizzard proprietary)
- **Alpha channel**: 8-bit alpha + 24-bit color (32-bit total)
- Files must reside in the addon's directory

### Creating in GIMP
1. File → New → set power-of-two dimensions
2. Advanced Options → Colorspace: RGB, Fill: Transparency
3. Create artwork with layers
4. **Flatten/merge layers** before saving
5. File → Save As → .tga format
6. Ensure RLE compression is OFF, Origin is Bottom Left

### Creating in Photoshop
1. New document with power-of-two dimensions, transparent background
2. Design in RGB color mode
3. Save As → Targa (.tga), 32 bits/pixel

### Creating in Paint Shop Pro
1. New image with power-of-two dimensions
2. Raster Background, 16 million colors, transparent
3. Export → save as .tga

### Using Custom Textures
```lua
local tex = frame:CreateTexture(nil, "ARTWORK")
tex:SetTexture("Interface\\AddOns\\MyAddon\\myimage")
-- Note: no .tga extension in the path
```

### Using Blizzard's Existing Art
Many built-in textures available — extract FrameXML to discover paths.

## Connections

- See [Graphics and Textures](../concepts/graphics-and-textures.md) concept page
- Textures displayed via [Frames and Widgets](../concepts/frames-and-widgets.md)
- Layer system from [Ch 9](ch09-frames-and-widgets.md)
