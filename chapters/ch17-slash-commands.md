---
title: "Ch 17 — Creating Slash Commands"
type: chapter
source_chapters: [17]
part: "III — Advanced Addon Techniques"
pages: 14
chars: 20536
created: 2026-04-15
updated: 2026-04-15
tags: [slash-commands, chat, input, macros]
---

# Ch 17 — Creating Slash Commands

## Overview

Covers creating custom slash commands (`/mycommand`) that users can type in chat to interact with addons. Also covers the underlying chat command system.

## Key Concepts

### Registering Slash Commands
```lua
SLASH_MYADDON1 = "/myaddon"
SLASH_MYADDON2 = "/ma"  -- alias
SlashCmdList["MYADDON"] = function(msg, editbox)
    print("Command received:", msg)
end
```

- `SLASH_<NAME><N>` globals define the trigger strings (N = 1, 2, 3...)
- `SlashCmdList["<NAME>"]` holds the handler function
- `msg` = everything after the slash command
- `editbox` = the editbox where the command was typed

### Parsing Arguments
```lua
SlashCmdList["MYADDON"] = function(msg)
    local cmd, rest = msg:match("^(%S+)%s*(.*)$")
    if cmd == "show" then
        MyAddon_Show()
    elseif cmd == "hide" then
        MyAddon_Hide()
    elseif cmd == "reset" then
        MyAddon_Reset()
    else
        print("Usage: /myaddon [show|hide|reset]")
    end
end
```

### Default Slash Commands
WoW has many built-in slash commands: `/say`, `/party`, `/raid`, `/cast`, `/use`, `/target`, `/focus`, `/script` (alias `/run`), etc.

### Macro System Integration
- Slash commands can be used in macros
- `/run` executes arbitrary Lua code
- Macros have a 255 character limit
- Macro conditionals: `[mod:shift]`, `[combat]`, `[target=focus]`, etc.

## Connections

- See [Slash Commands](../concepts/slash-commands.md) concept page
- Related to [Key Bindings](../concepts/key-bindings.md) as alternative input
- `/run` is how [Ch 2](ch02-lua-basics.md) test code in-game
- Used in virtually every addon for configuration
