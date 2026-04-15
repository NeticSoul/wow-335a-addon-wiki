---
title: Slash Commands
type: concept
source_chapters: [17]
created: 2026-04-15
updated: 2026-04-15
tags: [slash-commands, chat, input]
---

# Slash Commands

Custom chat commands (`/myaddon`) that users type to interact with addons.

## Registration
```lua
SLASH_MYADDON1 = "/myaddon"
SLASH_MYADDON2 = "/ma"
SlashCmdList["MYADDON"] = function(msg, editbox)
    -- msg = text after the command
end
```

## Argument Parsing
```lua
local cmd, rest = msg:match("^(%S+)%s*(.*)$")
```

## Built-in Commands
`/say`, `/party`, `/raid`, `/guild`, `/whisper`, `/cast`, `/use`, `/target`, `/focus`, `/script` (alias `/run`), `/reload`, `/dump`, `/fstack`, `/etrace`

## See Also
- [Ch 17](../chapters/ch17-slash-commands.md), [Key Bindings](key-bindings.md)
