---
title: "Ch 32 — Appendix B: Utilizing Addon Libraries"
type: chapter
source_chapters: [32]
part: "V — Appendices"
pages: 10
chars: 15286
created: 2026-04-15
updated: 2026-04-15
tags: [libraries, LibStub, Ace3, embedded, standalone]
---

# Ch 32 — Appendix B: Utilizing Addon Libraries

## Overview

Covers creating and using addon libraries — reusable code shared between addons. Two approaches: standalone libraries (separate addons) and embedded libraries (bundled within each addon).

## Key Concepts

### Standalone Libraries
- Separate addon directory with its own .toc
- Other addons declare it as a dependency
- Simple, but requires users to install the library separately

### Embedded Libraries
- Library code packaged inside each addon
- No separate installation needed
- **LibStub** prevents multiple copies from conflicting

### LibStub
Version management singleton that ensures only **one copy** (the newest version) of a library is active:
```lua
-- Library definition
local MAJOR, MINOR = "MyLib-1.0", 1
local lib = LibStub:NewLibrary(MAJOR, MINOR)
if not lib then return end  -- newer version already loaded

function lib:DoSomething()
    -- library code
end

-- Library usage
local MyLib = LibStub("MyLib-1.0")
MyLib:DoSomething()
```

### Ace3 Framework
Most popular addon framework, a collection of libraries:

| Library | Purpose |
|---------|---------|
| `AceAddon-3.0` | Addon lifecycle (OnInitialize, OnEnable, OnDisable) |
| `AceEvent-3.0` | Event registration/callbacks |
| `AceDB-3.0` | SavedVariables wrapper with profiles |
| `AceConsole-3.0` | Slash command handling |
| `AceGUI-3.0` | GUI widgets and layouts |
| `AceConfig-3.0` | Configuration framework |
| `AceComm-3.0` | Addon-to-addon communication |
| `AceSerializer-3.0` | Data serialization |
| `AceLocale-3.0` | Localization framework |

### CallbackHandler
Provides publish/subscribe event system for libraries:
```lua
lib.callbacks = CallbackHandler:New(lib)
lib.callbacks:Fire("SomeEvent", arg1, arg2)
-- Consumer:
lib.RegisterCallback(obj, "SomeEvent", handler)
```

## Connections

- See [Addon Libraries](../concepts/addon-libraries.md) concept page
- LibStub uses metatables from [Ch 4](ch04-tables.md)
- AceEvent wraps the [Events System](../concepts/events-system.md)
- AceDB enhances [Saved Variables](../concepts/saved-variables.md)
