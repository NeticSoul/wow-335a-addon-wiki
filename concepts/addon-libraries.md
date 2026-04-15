---
title: Addon Libraries
type: concept
source_chapters: [32]
created: 2026-04-15
updated: 2026-04-15
tags: [libraries, LibStub, Ace3, reuse]
---

# Addon Libraries

Reusable code shared between addons.

## Types
- **Standalone**: separate addon, declared as dependency
- **Embedded**: bundled inside each addon, managed by LibStub

## LibStub
Version singleton — only the newest copy of a library runs:
```lua
-- Define:
local lib = LibStub:NewLibrary("MyLib-1.0", 1)
if not lib then return end

-- Use:
local MyLib = LibStub("MyLib-1.0")
```

## Ace3 Framework

| Library | Purpose |
|---------|---------|
| AceAddon-3.0 | Addon lifecycle |
| AceEvent-3.0 | Event registration |
| AceDB-3.0 | SavedVariables with profiles |
| AceConsole-3.0 | Slash commands |
| AceGUI-3.0 | GUI widgets |
| AceConfig-3.0 | Configuration |
| AceComm-3.0 | Addon communication |
| AceSerializer-3.0 | Data serialization |
| AceLocale-3.0 | Localization |

## CallbackHandler
Custom publish/subscribe events for library APIs.

## See Also
- [Ch 32](../chapters/ch32-addon-libraries.md)
- [Saved Variables](saved-variables.md) (AceDB)
- [Addon Structure](addon-structure.md)
