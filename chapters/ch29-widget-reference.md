---
title: "Ch 29 — Widget Reference"
type: chapter
source_chapters: [29]
part: "IV — Reference"
pages: 156
chars: 278807
created: 2026-04-15
updated: 2026-04-15
tags: [widgets, reference, methods, hierarchy, scripts]
---

# Ch 29 — Widget Reference

## Overview

Reference for all 25+ widget types in WoW, including their methods and supported script handlers. Organized by the widget type hierarchy.

## Widget Type Hierarchy

```
UIObject
├── ParentedObject
│   ├── Region
│   │   ├── LayeredRegion
│   │   │   ├── FontString
│   │   │   └── Texture
│   │   └── Frame
│   │       ├── Button
│   │       │   └── CheckButton
│   │       ├── ColorSelect
│   │       ├── Cooldown
│   │       ├── EditBox
│   │       ├── GameTooltip
│   │       ├── MessageFrame
│   │       ├── Minimap
│   │       ├── Model
│   │       │   ├── DressUpModel
│   │       │   ├── PlayerModel
│   │       │   └── TabardModel
│   │       ├── ScrollFrame
│   │       ├── ScrollingMessageFrame
│   │       ├── SimpleHTML
│   │       ├── Slider
│   │       └── StatusBar
│   └── ScriptObject (mixin)
└── Font (abstract)
    └── AnimationGroup
        └── Animation
            ├── Alpha
            ├── Scale
            ├── Translation
            ├── Rotation
            └── Path
```

## Abstract Types

### UIObject Methods
- `GetName()`, `GetObjectType()`, `IsObjectType("type")`

### ParentedObject Methods
- `GetParent()`

### ScriptObject Methods
- `GetScript("handler")`, `SetScript("handler", func)`, `HookScript("handler", func)`
- `HasScript("handler")`

### Region Methods
- `SetPoint()`, `GetPoint()`, `ClearAllPoints()`
- `SetWidth()`, `SetHeight()`, `SetSize()`
- `GetWidth()`, `GetHeight()`
- `Show()`, `Hide()`, `IsShown()`, `IsVisible()`
- `SetAlpha()`, `GetAlpha()`
- `SetParent()`, `GetParent()`

## Key Widget Types

### Frame
Base widget — container for textures, font strings, other frames.
Key methods: `CreateTexture()`, `CreateFontString()`, `RegisterEvent()`, `SetBackdrop()`, `EnableMouse()`, `SetMovable()`, `SetFrameStrata()`, `SetFrameLevel()`

### Button
Clickable frame with normal/pushed/highlight/disabled states.
Key methods: `SetText()`, `GetText()`, `Enable()`, `Disable()`, `RegisterForClicks()`

### EditBox
Text input field.
Key methods: `SetText()`, `GetText()`, `SetFocus()`, `ClearFocus()`, `SetMaxLetters()`

### Slider
Range value selection.
Key methods: `SetMinMaxValues()`, `SetValue()`, `GetValue()`

### StatusBar
Progress/resource bar.
Key methods: `SetStatusBarTexture()`, `SetStatusBarColor()`, `SetMinMaxValues()`, `SetValue()`

### GameTooltip
Special frame for displaying tooltips.
Key methods: `SetOwner()`, `AddLine()`, `SetUnitBuff()`, `SetBagItem()`, `ClearLines()`

### FontString
Text display on a layer.
Key methods: `SetText()`, `SetFont()`, `SetTextColor()`, `GetStringWidth()`

### Texture
Image/color display on a layer.
Key methods: `SetTexture()`, `SetTexCoord()`, `SetVertexColor()`, `SetBlendMode()`

## Connections

- See [Frames and Widgets](../concepts/frames-and-widgets.md) concept page
- See [reference/widget-hierarchy](../reference/widget-hierarchy.md) for navigable reference
- Widget creation in [Ch 9](ch09-frames-and-widgets.md)
- Widget interaction in [Ch 12](ch12-interacting-with-widgets.md)
