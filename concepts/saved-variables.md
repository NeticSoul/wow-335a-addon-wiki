---
title: Saved Variables
type: concept
source_chapters: [8]
created: 2026-04-15
updated: 2026-04-15
tags: [persistence, savedvariables, data, toc]
---

# Saved Variables

The only way to persist data between game sessions. Declared in the TOC file and automatically saved/loaded by the client.

## Declaration
```
## SavedVariables: MyAddonDB
## SavedVariablesPerCharacter: MyAddonCharDB
```

## How It Works
1. On logout/reload: WoW serializes the named Lua variable to a file
2. On login: the file is loaded before `ADDON_LOADED` fires
3. The variable can be any type: string, number, boolean, or table (most common)

## Storage Location
```
WTF\Account\<AccountName>\SavedVariables\<AddonName>.lua          # global
WTF\Account\<AccountName>\<Server>\<Character>\SavedVariables\<AddonName>.lua  # per-char
```

## Common Pattern
```lua
-- On load
local frame = CreateFrame("Frame")
frame:RegisterEvent("ADDON_LOADED")
frame:SetScript("OnEvent", function(self, event, addon)
    if addon == "MyAddon" then
        MyAddonDB = MyAddonDB or {
            showFrame = true,
            scale = 1.0,
            position = {x = 0, y = 0},
        }
        self:UnregisterEvent("ADDON_LOADED")
    end
end)
```

## Limitations
- Only the named variable is saved — not local variables
- Cannot save functions, userdata, or circular table references
- Tables with metatables lose their metatables on reload
- Large tables can slow load times

## See Also
- [Ch 8 — Anatomy of an Addon](../chapters/ch08-anatomy-of-addon.md)
- [TOC File](toc-file.md)
- [Addon Libraries](addon-libraries.md) (AceDB for advanced profiles)
