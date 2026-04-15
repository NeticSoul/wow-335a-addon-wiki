---
title: "Ch 23 — Creating Dropdown Menus"
type: chapter
source_chapters: [23]
part: "III — Advanced Addon Techniques"
pages: 20
chars: 27178
created: 2026-04-15
updated: 2026-04-15
tags: [dropdown, menus, UIDropDownMenu, context-menu]
---

# Ch 23 — Creating Dropdown Menus

## Overview

Covers the UIDropDownMenu system for creating dropdown and context menus. Uses Blizzard's built-in template and API functions.

## Key Concepts

### Four Steps to Create a Dropdown
1. Add a toggle button
2. Create a frame inheriting `UIDropDownMenuTemplate`
3. Initialize the dropdown with `UIDropDownMenu_Initialize()`
4. Write click handler to toggle with `ToggleDropDownMenu()`

### UIDropDownMenu API

| Function | Purpose |
|----------|---------|
| `UIDropDownMenu_Initialize(frame, initFunc)` | Set up menu items |
| `UIDropDownMenu_AddButton(info [, level])` | Add a menu item |
| `UIDropDownMenu_CreateInfo()` | Create info table for an item |
| `ToggleDropDownMenu(level, value, frame)` | Show/hide menu |
| `CloseDropDownMenus()` | Close all open menus |
| `UIDropDownMenu_SetWidth(frame, width)` | Set menu width |
| `UIDropDownMenu_SetText(frame, text)` | Set display text |

### Menu Item Info Table
```lua
local info = UIDropDownMenu_CreateInfo()
info.text = "Option 1"
info.value = "opt1"
info.func = function(self) print("Selected:", self.value) end
info.checked = (currentValue == "opt1")
info.isTitle = false        -- true = non-clickable header
info.notCheckable = false   -- true = no checkbox
info.hasArrow = false       -- true = submenu
info.keepShownOnClick = false
UIDropDownMenu_AddButton(info)
```

### Multi-Level Menus
The `initFunc` receives a `level` parameter:
```lua
local function InitMenu(self, level)
    level = level or 1
    if level == 1 then
        -- top-level items (some with hasArrow = true)
    elseif level == 2 then
        -- submenu items
    end
end
```

### Context Menus
Right-click menus that appear at the cursor position.

## Connections

- Uses [Frame Templates](../concepts/frame-templates.md) (`UIDropDownMenuTemplate`)
- Related to [widget interaction](ch12-interacting-with-widgets.md)
- Context menus commonly attached to [unit frames](ch26-unit-frames-group-templates.md)
