---
title: "Ch 22 — Creating Scroll Frames"
type: chapter
source_chapters: [22]
part: "III — Advanced Addon Techniques"
pages: 18
chars: 23388
created: 2026-04-15
updated: 2026-04-15
tags: [scroll-frame, FauxScrollFrame, scrollbar, ui]
---

# Ch 22 — Creating Scroll Frames

## Overview

Two approaches to scrolling content: real **ScrollFrames** (pixel-by-pixel scrolling) and **FauxScrollFrames** (row-based virtual scrolling).

## Key Concepts

### ScrollFrame
- Clips its scroll child to a visible region
- Smooth pixel-by-pixel scrolling
- Used by Quest Log for text
- Components: scroll frame + scroll child + scroll bar
- Key methods: `SetScrollChild()`, `SetVerticalScroll()`, `SetHorizontalScroll()`
- **Performance warning**: computationally expensive, avoid in combat

### FauxScrollFrame
- Simulates scrolling by updating a fixed number of visible rows
- More efficient for lists — only creates rows visible on screen
- Used by the Auction House
- `FauxScrollFrame_Update(frame, numItems, numVisible, rowHeight)` — call on scroll
- `FauxScrollFrame_GetOffset(frame)` — get current scroll offset

### Scroll Bar
- WoW provides `UIPanelScrollBarTemplate` for standard scroll bars
- Slider + up/down buttons
- Must be manually connected to the scroll frame

### Implementation Pattern
1. Create the ScrollFrame
2. Create a child frame (scroll child)
3. Create a scroll bar (using template)
4. Connect scroll bar's `OnValueChanged` to `SetVerticalScroll()`
5. Handle mouse wheel: `OnMouseWheel` handler

## Connections

- Uses [Frame Templates](../concepts/frame-templates.md) for scroll bar
- Widget interaction from [Ch 12](ch12-interacting-with-widgets.md)
- FauxScrollFrame used in dropdown implementations [Ch 23](ch23-dropdown-menus.md)
