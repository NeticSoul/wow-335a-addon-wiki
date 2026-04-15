---
title: Function Hooking
type: concept
source_chapters: [19]
created: 2026-04-15
updated: 2026-04-15
tags: [hooking, pre-hook, post-hook, hooksecurefunc]
---

# Function Hooking

Replacing or wrapping an existing function to alter its behavior while keeping the original functional.

## Types

| Type | Runs | Can Modify Args? | Can Block Original? |
|------|------|-----------------|-------------------|
| **Pre-hook** | Before original | Yes | Yes |
| **Post-hook** | After original | No (returns only) | No |
| **Secure hook** | After original | No | No |

## Pre-Hook Pattern
```lua
local orig = ChatFrame1.AddMessage
function ChatFrame1.AddMessage(self, text, ...)
    text = date("%X") .. " " .. text  -- modify arg
    return orig(self, text, ...)        -- call original
end
```

## Secure Hook (hooksecurefunc)
Safe for Blizzard functions — no taint:
```lua
hooksecurefunc("FunctionName", function(...)
    -- post-hook only, cannot modify args/returns
end)

hooksecurefunc(GameTooltip, "SetUnitBuff", function(self, ...)
    -- hook a widget method
end)
```

## Script Hook (HookScript)
Add handler without replacing existing one:
```lua
frame:HookScript("OnClick", function(self, button)
    -- runs AFTER the existing OnClick handler
end)
```

## Best Practices
- Use varargs `...` to pass through unknown arguments
- Return the original's results
- Store original in a **local** variable
- Use `hooksecurefunc` for Blizzard functions (avoids taint)
- Multiple addons can hook the same function (chain)

## See Also
- [Ch 19](../chapters/ch19-function-hooking.md)
- [Secure Execution](secure-execution.md)
