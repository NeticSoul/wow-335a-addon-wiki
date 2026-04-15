---
title: Addon Structure
type: concept
source_chapters: [1, 8, 31]
created: 2026-04-15
updated: 2026-04-15
tags: [addon, toc, files, organization]
---

# Addon Structure

An addon is a named directory inside `Interface\AddOns\` containing a `.toc` file and associated Lua/XML/media files.

## Directory Layout
```
Interface\AddOns\MyAddon\
├── MyAddon.toc        # Required — metadata + file list
├── MyAddon.lua        # Behavior
├── MyAddon.xml        # Layout
├── Bindings.xml       # Key bindings (optional)
├── Locales\           # Localization files (optional)
├── Libs\              # Embedded libraries (optional)
└── Media\             # Custom textures/sounds (optional)
```

## TOC File
See [TOC File](toc-file.md) for complete directive reference.

## Loading Order
1. Dependencies loaded first (in dependency order)
2. Files listed in TOC loaded **in declared order**
3. XML processed first (frame creation), then Lua executes
4. `ADDON_LOADED` event fires when addon finishes loading
5. `PLAYER_LOGIN` fires when player enters the world

## Initialization Pattern
```lua
local frame = CreateFrame("Frame")
frame:RegisterEvent("ADDON_LOADED")
frame:SetScript("OnEvent", function(self, event, addon)
    if addon == "MyAddon" then
        -- SavedVariables now available
        -- Initialize state
        self:UnregisterEvent("ADDON_LOADED")
    end
end)
```

## Namespace Convention
Use a single global table to avoid polluting the global environment:
```lua
MyAddon = {}
function MyAddon.DoSomething() ... end
local private = {}  -- addon-private state
```

## See Also
- [Ch 1 — Programming for WoW](../chapters/ch01-programming-for-wow.md)
- [Ch 8 — Anatomy of an Addon](../chapters/ch08-anatomy-of-addon.md)
- [TOC File](toc-file.md)
- [Saved Variables](saved-variables.md)
- [Addon Libraries](addon-libraries.md)
