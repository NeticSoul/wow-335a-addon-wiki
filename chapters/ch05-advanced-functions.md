---
title: "Ch 5 — Advanced Functions and Control Structures"
type: chapter
source_chapters: [5]
part: "I — Learning to Program"
pages: 14
chars: 23221
created: 2026-04-15
updated: 2026-04-15
tags: [lua, varargs, closures, iterators, multiple-returns]
---

# Ch 5 — Advanced Functions and Control Structures

## Overview

Builds on Ch 3 with advanced function features: multiple return values, variable arguments (`...`), closures, and custom iterators using `pairs`/`ipairs` patterns.

## Key Concepts

### Multiple Return Values
```lua
function ConvertHexToRGB(hex)
    local r = tonumber(string.sub(hex, 1, 2), 16) / 255
    local g = tonumber(string.sub(hex, 3, 4), 16) / 255
    local b = tonumber(string.sub(hex, 5, 6), 16) / 255
    return r, g, b
end

local r, g, b = ConvertHexToRGB("FFCC99")
```

**Critical rule**: When a multi-return function call is the **last** argument, all returns are used. Otherwise, only the first return is used.

```lua
print(ConvertHexToRGB("FFFFFF"))            -- 1, 1, 1
print(ConvertHexToRGB("FFFFFF"), "extra")   -- 1, extra  (other returns eaten!)
```

WoW API uses multiple returns extensively (e.g., `GetRaidRosterInfo()` returns name, rank, subgroup, level, class, etc.).

### Variable Arguments (Varargs)
```lua
function myPrint(...)
    local args = {...}   -- pack into table
    for i, v in ipairs(args) do
        print(v)
    end
end
```

- `select("#", ...)` — count of varargs
- `select(n, ...)` — returns args from position n onward
- Used in WoW event handlers: `function OnEvent(self, event, ...)`

### Closures
Functions can capture variables from their enclosing scope:

```lua
function counter()
    local count = 0
    return function()
        count = count + 1
        return count
    end
end
local c = counter()
print(c())  -- 1
print(c())  -- 2
```

### Generic For and Iterators
- `pairs()` and `ipairs()` are **iterator factories** that return functions
- Custom iterators follow the same pattern
- The generic `for` calls the iterator function repeatedly until it returns `nil`

## Connections

- Multiple returns are essential for the [WoW API](../concepts/wow-api.md)
- Varargs used in [event handlers](../concepts/events-system.md) and [function hooking](../concepts/function-hooking.md)
- Closures used in [secure snippets](../concepts/secure-execution.md) and callback patterns
- See [Lua Language](../concepts/lua-language.md) for the full language overview
