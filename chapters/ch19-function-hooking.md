---
title: "Ch 19 — Altering Existing Behavior with Function Hooking"
type: chapter
source_chapters: [19]
part: "III — Advanced Addon Techniques"
pages: 14
chars: 24729
created: 2026-04-15
updated: 2026-04-15
tags: [hooking, pre-hook, post-hook, hooksecurefunc, override]
---

# Ch 19 — Altering Existing Behavior with Function Hooking

## Overview

Function hooking means replacing or wrapping an existing function to alter its behavior. This chapter covers pre-hooks, post-hooks, secure hooking, and best practices.

## Key Concepts

### What Is Hooking?
Changing the behavior of an existing function by:
- Altering arguments or return values
- Preventing the original from running
- Taking extra action on each call

### Pre-Hook (Most Common)
Hook runs **before** the original:
```lua
local origAddMessage = ChatFrame1.AddMessage
function ChatFrame1.AddMessage(self, text, ...)
    local timestamp = date("%X")
    text = string.format("%s %s", timestamp, text)
    return origAddMessage(self, text, ...)
end
```

**Best practices**:
- Use varargs `...` to pass through unknown arguments
- Return the original's results to preserve expected behavior
- Store original in a **local** variable

### Post-Hook
Hook runs **after** the original — can modify return values:
```lua
local orig = SomeFunction
SomeFunction = function(...)
    local r1, r2 = orig(...)
    -- modify r1, r2
    return r1, r2
end
```

### Secure Hooking with `hooksecurefunc`
For hooking Blizzard functions without breaking taint:
```lua
hooksecurefunc("FunctionName", function(...)
    -- runs AFTER the original
    -- cannot modify arguments or returns
    -- will NOT taint the original function
end)
```

- **Only** post-hooks are possible with `hooksecurefunc`
- Cannot prevent the original from running
- Cannot modify arguments or return values
- Safe to use on protected/secure functions

### Widget Method Hooking
```lua
-- Hook a method on a specific frame
hooksecurefunc(GameTooltip, "SetUnitBuff", function(self, ...)
    -- additional logic after SetUnitBuff runs
end)
```

### Script Hook (HookScript)
Add additional handler without replacing existing one:
```lua
frame:HookScript("OnClick", function(self, button)
    -- runs AFTER the existing OnClick handler
end)
```

### Common Pitfalls
- Don't use global variables for storing originals (taint risk)
- Don't forget varargs — future API changes may add parameters
- Don't hook too early — target function must exist
- Multiple addons can hook the same function (chain of hooks)

## Connections

- See [Function Hooking](../concepts/function-hooking.md) concept page
- `hooksecurefunc` relates to [Secure Execution](../concepts/secure-execution.md)
- Script hooking builds on [widget handlers](ch12-interacting-with-widgets.md)
- Used in [tooltip scanning](ch24-tooltips.md) and many UI modifications
