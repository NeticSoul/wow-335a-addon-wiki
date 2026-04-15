---
title: "Ch 12 — Interacting with Widgets"
type: chapter
source_chapters: [12]
part: "II — Programming in WoW"
pages: 36
chars: 49883
created: 2026-04-15
updated: 2026-04-15
tags: [widgets, scripts, button, editbox, slider, interaction, bagbuddy]
---

# Ch 12 — Interacting with Widgets

## Overview

Covers widget script handlers (OnClick, OnEnter, OnLeave, etc.) and the specialized widget types: Button, EditBox, Slider, CheckButton, and StatusBar. Continues building BagBuddy.

## Key Concepts

### Widget Script Handlers
Handlers are Lua functions attached to frames that fire in response to user actions or state changes:

| Handler | Fires When |
|---------|-----------|
| `OnClick` | Frame is clicked |
| `OnDoubleClick` | Frame is double-clicked |
| `OnEnter` | Mouse enters frame area |
| `OnLeave` | Mouse leaves frame area |
| `OnMouseDown` | Mouse button pressed |
| `OnMouseUp` | Mouse button released |
| `OnDragStart` | Drag operation begins |
| `OnDragStop` | Drag operation ends |
| `OnShow` | Frame becomes visible |
| `OnHide` | Frame becomes hidden |
| `OnLoad` | Frame is first created from XML |
| `OnUpdate` | Every frame render (see [Ch 18](ch18-onupdate.md)) |
| `OnEvent` | Registered game event fires |
| `OnSizeChanged` | Frame dimensions change |

### Setting Handlers
In XML:
```xml
<Scripts>
    <OnClick>MyClickHandler(self, button, down)</OnClick>
</Scripts>
```
In Lua:
```lua
frame:SetScript("OnClick", function(self, button, down)
    print("Clicked with", button)
end)
```

### Button Widget
- Inherits from Frame
- Has three visual states: Normal, Pushed, Highlight
- `OnClick` provides: `self`, `button` ("LeftButton"/"RightButton"), `down` (boolean)
- Methods: `SetText()`, `GetText()`, `Enable()`, `Disable()`, `RegisterForClicks()`

### EditBox Widget
- Text input field
- `OnTextChanged`, `OnEnterPressed`, `OnEscapePressed`, `OnTabPressed`
- Methods: `SetText()`, `GetText()`, `SetMaxLetters()`, `SetFocus()`, `ClearFocus()`
- Auto-focus behavior can be enabled/disabled

### Slider Widget
- Range selection with thumb control
- `OnValueChanged` handler
- Methods: `SetMinMaxValues()`, `SetValue()`, `GetValue()`, `SetValueStep()`

### CheckButton Widget
- Toggle button (inherits Button)
- `OnClick` handler — check `self:GetChecked()` for state
- Methods: `SetChecked()`, `GetChecked()`

### StatusBar Widget
- Progress/health bar display
- Methods: `SetMinMaxValues()`, `SetValue()`, `SetStatusBarTexture()`, `SetStatusBarColor()`
- Used for health bars, cast bars, experience bars

### Frame Movement (Dragging)
```lua
frame:SetMovable(true)
frame:EnableMouse(true)
frame:RegisterForDrag("LeftButton")
frame:SetScript("OnDragStart", frame.StartMoving)
frame:SetScript("OnDragStop", frame.StopMovingOrSizing)
```

### Strata and Frame Level
**Strata** controls broad layering groups (back to front):
`BACKGROUND`, `LOW`, `MEDIUM`, `HIGH`, `DIALOG`, `FULLSCREEN`, `FULLSCREEN_DIALOG`, `TOOLTIP`

**Frame level** controls ordering within a strata.

## Connections

- Builds on [Ch 9](ch09-frames-and-widgets.md) frame fundamentals
- Script handlers are how addons respond to user input
- See [Frames and Widgets](../concepts/frames-and-widgets.md) concept page
- Events (Ch 13) are the other half of the input story
- StatusBar used in [unit frames](ch26-unit-frames-group-templates.md)
