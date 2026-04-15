---
title: Frames and Widgets
type: concept
source_chapters: [8, 9, 10, 12, 29]
created: 2026-04-15
updated: 2026-04-15
tags: [frames, widgets, textures, fontstrings, layers, anchor]
---

# Frames and Widgets

All UI elements in WoW are **widgets** — Lua tables with methods, organized in a type hierarchy.

## Widget Hierarchy (Simplified)
```
UIObject → Region → Frame → Button, EditBox, Slider, StatusBar, ...
                  → LayeredRegion → FontString, Texture
```

## Frame
- Base container; all textures/fontstrings require a parent frame
- Can register for events, receive mouse/keyboard input
- Created: `CreateFrame("Frame", "name", parent)` or XML `<Frame>`

## Key Properties

| Property | Set Via |
|----------|---------|
| Size | `SetSize(w, h)` or `<Size>` |
| Position | `SetPoint(point, relativeTo, relPoint, x, y)` or `<Anchors>` |
| Parent | `SetParent(frame)` or `parent="..."` |
| Strata | `SetFrameStrata("HIGH")` — broad layer |
| Level | `SetFrameLevel(5)` — within strata |
| Visibility | `Show()`, `Hide()`, `SetAlpha()` |
| Backdrop | `SetBackdrop({...})` |

## Draw Layers (back to front)
`BACKGROUND` → `BORDER` → `ARTWORK` → `OVERLAY` → `HIGHLIGHT`

## Anchor Points
Nine points: `TOPLEFT`, `TOP`, `TOPRIGHT`, `LEFT`, `CENTER`, `RIGHT`, `BOTTOMLEFT`, `BOTTOM`, `BOTTOMRIGHT`. Multiple anchors stretch the frame.

## Widget Types

| Type | Purpose | Key Methods |
|------|---------|-------------|
| Frame | Container | `RegisterEvent`, `SetBackdrop`, `SetMovable` |
| Button | Clickable | `SetText`, `Enable/Disable`, `RegisterForClicks` |
| CheckButton | Toggle | `SetChecked`, `GetChecked` |
| EditBox | Text input | `SetText`, `GetText`, `SetFocus` |
| Slider | Range | `SetMinMaxValues`, `SetValue` |
| StatusBar | Progress bar | `SetStatusBarTexture`, `SetValue` |
| GameTooltip | Tooltip | `SetOwner`, `AddLine`, `SetBagItem` |
| ScrollFrame | Scroll container | `SetScrollChild` |
| Minimap | Minimap | Special |
| Model | 3D model | `SetModel`, `SetCamera` |
| MessageFrame | Auto-fading text | `AddMessage` |
| SimpleHTML | HTML render | `SetText` |

## Script Handlers
See [Ch 12](../chapters/ch12-interacting-with-widgets.md) for complete handler reference. Key handlers: `OnClick`, `OnEnter`, `OnLeave`, `OnEvent`, `OnUpdate`, `OnShow`, `OnHide`, `OnLoad`.

## See Also
- [Ch 9](../chapters/ch09-frames-and-widgets.md), [Ch 12](../chapters/ch12-interacting-with-widgets.md), [Ch 29](../chapters/ch29-widget-reference.md)
- [XML Layout](xml-layout.md)
- [Frame Templates](frame-templates.md)
