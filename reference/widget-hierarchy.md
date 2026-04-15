---
title: Widget Hierarchy
type: reference
source_chapters: [9, 29]
created: 2026-04-15
updated: 2026-04-15
tags: [widgets, hierarchy, types, reference]
---

# Widget Hierarchy

All WoW UI elements derive from a common type hierarchy. 25+ widget types in total.

## Type Tree
```
UIObject
├── ParentedObject
│   ├── ScriptObject
│   │   ├── AnimationGroup
│   │   ├── Animation
│   │   │   ├── Alpha
│   │   │   ├── Path
│   │   │   ├── Rotation
│   │   │   ├── Scale
│   │   │   └── Translation
│   │   └── Region
│   │       ├── VisibleRegion
│   │       │   ├── LayeredRegion
│   │       │   │   ├── FontString
│   │       │   │   └── Texture
│   │       │   └── Frame
│   │       │       ├── Button
│   │       │       │   └── CheckButton
│   │       │       ├── ColorSelect
│   │       │       ├── Cooldown
│   │       │       ├── DressUpModel → PlayerModel → Model
│   │       │       ├── EditBox
│   │       │       ├── GameTooltip
│   │       │       ├── MessageFrame
│   │       │       ├── Minimap
│   │       │       ├── Model
│   │       │       │   └── PlayerModel
│   │       │       │       └── DressUpModel
│   │       │       ├── MovieFrame
│   │       │       ├── ScrollFrame
│   │       │       ├── ScrollingMessageFrame
│   │       │       ├── SimpleHTML
│   │       │       ├── Slider
│   │       │       └── StatusBar
│   │       └── Font (non-visual)
│   └── ControlPoint (animation waypoint)
└── FontInstance
```

## Abstract Types (cannot instantiate)

| Type | Provides |
|------|----------|
| UIObject | `GetName`, `GetObjectType`, `IsObjectType` |
| ParentedObject | `GetParent` |
| ScriptObject | `SetScript`, `GetScript`, `HookScript`, `HasScript` |
| Region | Anchoring, sizing, animation |
| VisibleRegion | `Show`, `Hide`, `SetAlpha`, `IsVisible` |
| LayeredRegion | `GetDrawLayer`, `SetDrawLayer`, `SetVertexColor` |

## Concrete Types

| Type | Created Via | Purpose |
|------|------------|---------|
| Frame | `CreateFrame("Frame")` | Base container, event handling |
| Button | `CreateFrame("Button")` | Clickable, text + textures |
| CheckButton | `CreateFrame("CheckButton")` | Toggle button |
| EditBox | `CreateFrame("EditBox")` | Text input field |
| Slider | `CreateFrame("Slider")` | Value slider |
| StatusBar | `CreateFrame("StatusBar")` | Health/mana bars |
| GameTooltip | `CreateFrame("GameTooltip")` | Contextual info popup |
| ScrollFrame | `CreateFrame("ScrollFrame")` | Scroll container |
| ScrollingMessageFrame | `CreateFrame("ScrollingMessageFrame")` | Chat-style auto-fade text |
| MessageFrame | `CreateFrame("MessageFrame")` | Floating message text |
| SimpleHTML | `CreateFrame("SimpleHTML")` | HTML rendering |
| Minimap | *(Blizzard only)* | Minimap widget |
| Model | `CreateFrame("Model")` | 3D model display |
| PlayerModel | `CreateFrame("PlayerModel")` | Character model |
| DressUpModel | `CreateFrame("DressUpModel")` | Equipment preview |
| MovieFrame | *(Blizzard only)* | Video playback |
| ColorSelect | `CreateFrame("ColorSelect")` | Color picker |
| Cooldown | `CreateFrame("Cooldown")` | Cooldown sweep animation |
| Texture | `frame:CreateTexture()` | Image/color region |
| FontString | `frame:CreateFontString()` | Text region |
| AnimationGroup | `region:CreateAnimationGroup()` | Animation container |
| Animation | *(via AnimationGroup)* | Base animation |

## See Also
- [Ch 29 — Widget Reference](../chapters/ch29-widget-reference.md)
- [Frames and Widgets](../concepts/frames-and-widgets.md)
- [Frame Templates](../concepts/frame-templates.md)
