---
title: BagBuddy (Sample Addon)
type: entity
source_chapters: [8, 9, 10, 11, 12, 13]
created: 2026-04-15
updated: 2026-04-15
tags: [addon, sample, bags, inventory, tutorial]
---

# BagBuddy

The primary tutorial addon built across Part II (Chapters 8–13). A bag search/filter tool that evolves from a minimal addon into a fully featured inventory browser.

## Purpose
Lets the player search and filter inventory items by name, quality (rarity), and type.

## Progression Across Chapters

| Chapter | What's Added |
|---------|-------------|
| [Ch 8](../chapters/ch08-anatomy-of-addon.md) | TOC file, basic Lua skeleton, SavedVariables |
| [Ch 9](../chapters/ch09-frames-and-widgets.md) | Main frame, textures, fontstrings, backdrop |
| [Ch 10](../chapters/ch10-frame-templates.md) | Item button template, multiple inheritance |
| [Ch 11](../chapters/ch11-wow-api.md) | Container API (GetContainerItemInfo, etc.) |
| [Ch 12](../chapters/ch12-interacting-with-widgets.md) | Editbox for search, click handlers, tooltips on hover |
| [Ch 13](../chapters/ch13-game-events.md) | BAG_UPDATE events, dynamic refresh |

## Key Techniques Demonstrated
- TOC file structure and SavedVariables
- Backdrop styling
- Frame templates with `$parent` and `parentKey`
- Container/inventory API usage
- EditBox text input for filtering
- Event-driven UI refresh
- Tooltip integration on mouseover

## See Also
- [Addon Structure](../concepts/addon-structure.md)
- [Frames and Widgets](../concepts/frames-and-widgets.md)
- [Events System](../concepts/events-system.md)
