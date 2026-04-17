---
title: Function Hooking
type: concept
source_chapters: [19]
created: 2026-04-15
updated: 2026-04-17
tags: [hooking, pre-hook, post-hook, hooksecurefunc, HookScript, taint]
---

# Function Hooking

## Summary

Function hooking is replacing a callable global or table method with a wrapper that preserves the original's behavior while adding pre-execution logic, post-execution logic, or both. In WoW 3.3.5a there are **four distinct hooking mechanisms**, only two of which are taint-safe:

| Mechanism | Kind | Can modify args? | Can modify returns? | Taint-safe? |
|-----------|------|------------------|---------------------|-------------|
| Manual wrapping (`orig = F; F = newF`) | Pre- or post-hook | Yes | Yes | **No** - taints |
| `frame:SetScript("OnX", newFn)` + captured original | Script pre/post-hook | Yes | - | **No** - taints if script was secure |
| `hooksecurefunc([t,] name, fn)` | Post-hook, side-effects only | No (discarded) | No (discarded) | **Yes** |
| `frame:HookScript("OnX", fn)` | Script post-hook | No | No | **Yes** |

The secure variants exist because the default UI relies on many functions being *untainted* - see [secure-execution](secure-execution.md). Any manual replacement of a Blizzard function from addon code taints it permanently for the session.

## Detailed Explanation

### Manual Pre-Hook

```lua
local origAddMessage = ChatFrame1.AddMessage
function ChatFrame1.AddMessage(self, text, ...)
    local ts = date("%X")
    text = string.format("%s %s", ts, text)
    return origAddMessage(self, text, ...)
end
```

Two disciplines make this future-proof:

1. **Accept `...`** so additions to the parameter list don't break the hook.
2. **Return `origAddMessage(...)`** even when the current API returns nothing - future returns will be preserved.

### Manual Post-Hook (Modifying Returns)

Post-hooks are harder because Lua cannot store a varargs tail into a table while preserving `nil`s. Two idioms:

**Pattern A - helper function consumes returns directly:**

```lua
SAVED_MONEY = 10000
local origGetMoney = GetMoney
local function adjust(realMoney, ...)
    return realMoney - SAVED_MONEY, ...
end
function GetMoney(...)
    return adjust(origGetMoney(...))
end
```

The trick: pass the original's vararg return straight into a function whose first parameter is the value of interest. Extra returns flow through untouched.

**Pattern B - `capture` / `release`:**

```lua
function capture(...)
    return { select("#", ...), ... }
end

function release(tbl, i)
    local size = tbl[1]
    i = i or 2
    if i <= size then
        return tbl[i], release(tbl, i + 1)
    end
end

local origGetMoney = GetMoney
function GetMoney(...)
    local r = capture(origGetMoney(...))
    r[1] = r[1] - SAVED_MONEY
    return release(r)
end
```

Stores arity in slot 1 so `nil` returns are preserved. Slower, but lets you inspect and mutate any position.

### Hooking a Script Handler (`SetScript`)

```lua
local origOnClick = QuestLogFrameAbandonButton:GetScript("OnClick")
local function newOnClick(...)
    local idx = GetQuestLogSelection()
    local completed = select(7, GetQuestLogTitle(idx))
    if completed then
        RaidNotice_AddMessage(RaidWarningFrame,
            "*** WARNING: quest is completed ***",
            ChatTypeInfo["RAID_WARNING"])
    end
    if origOnClick then return origOnClick(...) end
end
QuestLogFrameAbandonButton:SetScript("OnClick", newOnClick)
```

Rules:

- **Always** use `:GetScript(handlerName)` first - you must not assume the frame has no handler.
- Guard the call site: `if origOnClick then ... end`.
- This replaces the handler: your function **is** the handler now. Other addons that later call `GetScript` will retrieve *your* replacement.

### Secure Post-Hook: `hooksecurefunc`

```lua
hooksecurefunc([table,] functionName, hookFunction)
```

- `table` is optional; without it, hooks a *global* function.
- `hookFunction` runs **after** the original with the same args; **returns are discarded**.
- Cannot alter the original's behavior - purely observational / side-effects.
- Does **not** propagate taint from addon code into the hooked function's callers.

```lua
hooksecurefunc("UseAction", function(slot, unit)
    ChatFrame1:AddMessage(format("Used action %d on %s", slot, unit or "<no unit>"))
end)

hooksecurefunc(someFrame, "Show", function(self)
    ChatFrame1:AddMessage("Attempting to show someFrame")
end)
```

Note: `hooksecurefunc(frame, "Show", ...)` fires even when `frame:Show()` is called on an already-visible frame - unlike the `OnShow` script.

### Secure Script Hook: `HookScript`

Every widget exposes `HookScript(scriptName, handler)` - the analogue of `hooksecurefunc` for script handlers:

```lua
local clickCount = 0
PlayerFrame:HookScript("OnClick", function()
    clickCount = clickCount + 1
    print(clickCount, "clicks and counting...")
end)
```

- Handler runs **after** any existing script, with the same args.
- Returns are discarded.
- No need to check if a script is already set - `HookScript` composes safely.
- To capture every secure action-button click, use `hooksecurefunc("SecureActionButton_OnClick", ...)` - `HookScript` only covers the specific button instance.

### The Hook Chain

Two addons hooking the same function via manual wrapping compose as a chain:

```
OtherAddon(wrap) -> YourAddon(wrap) -> Built-in original
```

Secure hooks (`hooksecurefunc` / `HookScript`) are **not** a chain - they are an unordered set of post-hooks invoked after the original returns.

**Ordering is not guaranteed.** You cannot know whether your hook runs first, middle, or last. Dependencies in the TOC can give a weak guarantee about *load* order, not *chain position* across unrelated addons.

### Unhooking

- Manual wrapping - cannot be reliably undone. `F = origF` overwrites *all* later hooks, not just yours.
- Secure hooks (`hooksecurefunc`, `HookScript`) - **no API to remove them**. Intentional.
- Standard workaround: keep a boolean flag inside your hook body and toggle it.

```lua
local active = true
hooksecurefunc("UseAction", function(slot, unit)
    if not active then return end
    ...
end)
```

### Performance

Every layer adds a function call, argument copy, and return copy. For high-frequency functions (called per frame or inside combat hotpath) a deep hook chain is measurable. Minimize by:

- Preferring secure hooks (slightly faster, no chain).
- Returning early from your hook on the overwhelmingly-common case.
- Avoiding global lookups inside the hook body.
- Not hooking at all when an event suffices - see Alternatives below.

### Hooking Alternatives (prefer when possible)

| Instead of hooking... | Prefer |
|-----------------------|--------|
| `FrameX` `OnShow` | Parent a child frame to `FrameX` and give the child its own `OnShow`. Visibility propagates. Remove by re-parenting. |
| `MerchantFrame_OnShow` | Register `MERCHANT_SHOW` event. |
| `SetAttribute` | Hook (or set) the `OnAttributeChanged` script instead. |
| Any Blizzard function with a covering event | Register the event. |

## Examples

### MapZoomOut (Ch 19): hooking `Minimap.SetZoom`

```lua
local timer      = CreateFrame("Frame")
local DELAY      = 20
local counter    = 0
local origSetZoom = Minimap.SetZoom

function Minimap.SetZoom(...)
    timer:Show()
    counter = 0
    return origSetZoom(...)
end

timer:SetScript("OnUpdate", function(self, elapsed)
    counter = counter + elapsed
    if counter >= DELAY then
        local z = Minimap:GetZoom()
        if z > 0 then
            origSetZoom(Minimap, z - 1)
        else
            MinimapZoomIn:Enable()
            MinimapZoomOut:Disable()
            self:Hide()
        end
    end
end)

if Minimap:GetZoom() == 0 then timer:Hide() end
```

Intercepts both the built-in zoom buttons and external `/script Minimap:SetZoom(...)` calls.

### Observe every UseAction

```lua
hooksecurefunc("UseAction", function(slot, unit, button)
    print("action", slot, "on", unit or "<target>")
end)
```

### Warn on frame Show (observational)

```lua
hooksecurefunc(someFrame, "Show", function()
    print("someFrame:Show() was called")
end)
```

## Common Bugs

- **Overwriting a Blizzard secure function via manual wrap**, then wondering why combat actions stop working - you tainted the function for the entire session.
- **Forgetting to return `orig(...)`** in a pre-hook - downstream callers expecting a value receive `nil`.
- **Capturing the original as upvalue from a conditional branch** - if the code path doesn't run, `orig` is `nil` and the hook silently returns nothing.
- **Assuming order** between hook chains - unreliable; design hooks to be commutative with other addons.
- **Trying to un-hook with `F = orig`** - wipes subsequent addons' hooks too.
- **Hooking `OnShow` to react to a frame becoming visible** - misses calls where the frame was already visible; `hooksecurefunc(frame, "Show", ...)` covers both.
- **Hooking inside combat lockdown** to modify a protected frame - modification is blocked (see [secure-execution](secure-execution.md)).

## Related Concepts

- [secure-execution](secure-execution.md) - taint propagation is the entire reason `hooksecurefunc` and `HookScript` exist
- [events-system](events-system.md) - the preferred alternative to hooking
- [frames-and-widgets](frames-and-widgets.md) - script handlers are the hook target for widget-side work
- [addon-libraries](addon-libraries.md) - `AceHook-3.0` wraps these primitives with un-hook bookkeeping
- [Ch 19 - Function Hooking](../chapters/ch19-function-hooking.md)
- [Ch 15 - Secure Templates](../chapters/ch15-secure-templates.md)
