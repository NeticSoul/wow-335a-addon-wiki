---
title: OnUpdate and Throttling
type: concept
source_chapters: [18, 14, 21]
created: 2026-04-17
updated: 2026-04-17
tags: [onupdate, throttling, performance, timers, animation]
---

# OnUpdate and Throttling

## Summary

`OnUpdate` is the **per-frame rendering callback** on every WoW frame widget. It fires once per graphics refresh (typically 30–100+ times per second, tied to the player's FPS) for each *visible* frame that has an `OnUpdate` script assigned. It is the mechanism the UI uses for timers, animations, throttling event bursts, and deferred work.

Because the handler runs every frame, even small inefficiencies compound. `OnUpdate` is a common source of performance problems in addon code. A common pattern is to **accumulate `elapsed`, gate work on a threshold, then reset**.

## Detailed Explanation

### Signature and Semantics

```lua
frame:SetScript("OnUpdate", function(self, elapsed)
    -- self:    the frame
    -- elapsed: seconds (float) since the previous OnUpdate tick for this frame
end)
```

- Fires only while the frame is **visible** (`IsVisible()` returns true). `Hide()` pauses it; `Show()` resumes it (the next `elapsed` reflects the full gap).
- `elapsed` is per-frame, delivered by the renderer. It varies with FPS: at 60 FPS, `elapsed ≈ 0.0167`; at 30 FPS, `≈ 0.033`; during loading or zoning, can spike to seconds.
- **All visible frames' `OnUpdate` scripts run before each render pass completes**, sequentially. Heavy work directly reduces FPS.
- Unlike `OnEvent`, there is no registration step; merely assigning the script is enough.
- Setting `OnUpdate` to `nil` cleanly detaches it.

### When to Use / Not Use

**Use `OnUpdate` for:**

- Smooth animations driven by render time.
- Cooldown sweep overlays, status-bar smoothing.
- Timers, delays, and deferred execution.
- **Coalescing** bursts of events (e.g. multiple `BAG_UPDATE` firings into one recompute).
- Polling state that has no event (e.g. target cast time progress).

**Do NOT use `OnUpdate` for:**

- Anything with a covering event — `OnEvent` is usually cheaper when an event already exists. Use `BAG_UPDATE`, not `OnUpdate` + `GetContainerNumSlots`.
- Work that can run on `ADDON_LOADED`, `PLAYER_LOGIN`, `PLAYER_REGEN_DISABLED/ENABLED`, etc.

### Core Patterns

#### 1. Throttle — "run at most every T seconds"

```lua
local T = 0.5
frame.tick = 0
frame:SetScript("OnUpdate", function(self, elapsed)
    self.tick = self.tick + elapsed
    if self.tick >= T then
        self.tick = 0
        DoWork()
    end
end)
```

#### 2. Delay — "run once, N seconds from now"

```lua
local function Delay(seconds, callback)
    local f = CreateFrame("Frame")
    f.left = seconds
    f:SetScript("OnUpdate", function(self, elapsed)
        self.left = self.left - elapsed
        if self.left <= 0 then
            self:SetScript("OnUpdate", nil)
            self:Hide()
            callback()
        end
    end)
end
```

Reusable scheduler — construct one hidden frame per pending call. A more memory-efficient version keeps a frame pool and a min-heap of deadlines.

#### 3. Event coalescer — "one run per burst"

```lua
local throttle = 0.2
local ticker = CreateFrame("Frame")
ticker:Hide()
ticker.acc = 0
ticker:SetScript("OnUpdate", function(self, e)
    self.acc = self.acc + e
    if self.acc >= throttle then
        self.acc = 0
        self:Hide()         -- Hide() stops further OnUpdate until next event
        Recompute()
    end
end)

-- Trigger from the burst event:
bagFrame:RegisterEvent("BAG_UPDATE")
bagFrame:SetScript("OnEvent", function() ticker:Show() end)
```

Every event-triggered `Show()` resets the pending window; the work runs ~`throttle` seconds after the last burst event.

#### 4. Combat-only ticker (CombatTracker, Ch 14/18)

```lua
ct:SetScript("OnEvent", function(self, event)
    if event == "PLAYER_REGEN_DISABLED" then
        self:SetScript("OnUpdate", CombatTracker_OnUpdate)   -- start
    elseif event == "PLAYER_REGEN_ENABLED" then
        self:SetScript("OnUpdate", nil)                      -- stop
        CombatTracker_UpdateText()                           -- final sync
    end
end)
```

Attach only when relevant; detach when idle. The most important single optimization for per-frame handlers.

#### 5. Animation sweep (e.g. cooldown ring)

```lua
cd:SetScript("OnUpdate", function(self, elapsed)
    self.t = self.t + elapsed
    if self.t >= self.duration then
        self:SetScript("OnUpdate", nil)
        self:Hide()
        return
    end
    self:SetValue(1 - self.t / self.duration)
end)
```

### Performance Rules

1. **Detach when idle.** A hidden frame skips the handler, but keeping a handler attached on a visible frame running an empty `if` is still a cost multiplied by visibility. Prefer `SetScript("OnUpdate", nil)` + `Show()`.
2. **Prefer locals.** Global lookups inside `OnUpdate` show up in profiles. `local GetTime = GetTime` at file scope is standard.
3. **Avoid table creation in the handler.** Reuse scratch tables; pre-allocate.
4. **Avoid string concatenation.** `SetFormattedText` beats `SetText(...)` concatenations.
5. **Avoid expensive queries per tick.** `UnitHealth`, `UnitAura`, `GetContainerItemInfo` are cheap individually but add up. Cache results; invalidate on the relevant event.
6. **Short-circuit with `return`.** A handler that runs 60× per second but returns in 3 instructions is acceptable; one that allocates a table is not.

### Practical Starting Points

This table is editorial guidance based on the documented `OnUpdate` behavior and the examples in Chapters 14, 18, and 21.

| Use case | Reasonable throttle |
|----------|---------------------|
| Combat tracker totals display | 0.5 – 1.0 s |
| Status bar smoothing toward target value | every tick, but only if delta > epsilon |
| Nameplate update | 0.1 s |
| Cooldown sweep | every tick (animation) |
| Inventory/bag recount after burst | 0.2 – 0.5 s |
| Long-running "every N seconds" chores | use a single shared ticker, not one per task |

### Interaction with Events

- `OnUpdate` ticks do not fire while the client is frozen (loading screens, `/reload`). `elapsed` catches up in a single call when execution resumes.
- `OnUpdate` is not fired for frames whose chain of parents has any hidden ancestor.
- Protected frames (see [secure-execution](secure-execution.md)) can use `OnUpdate`; the handler is still insecure. Hiding a protected frame from an `OnUpdate` in combat from tainted code is blocked.

### Debugging

- `/run UIParent:SetScript("OnUpdate", function() print(1) end)` kills your FPS instantly — illustrative of cost.
- Use `GetFramerate()` as a quick scale check. Drops during a specific addon's operation indicate its `OnUpdate` handlers.
- Library `!Profiler` and the CPU profiling built into `GetAddOnCPUUsage` (with `/console scriptProfile 1` + `/reload`) can attribute cost to specific handlers.

## Examples

### Reusable delay helper (Ch 18)

```lua
local DelayFrame = CreateFrame("Frame"); DelayFrame:Hide()
local queue = {}

function Delay(seconds, fn)
    queue[#queue+1] = { t = seconds, fn = fn }
    DelayFrame:Show()
end

DelayFrame:SetScript("OnUpdate", function(self, elapsed)
    for i = #queue, 1, -1 do
        local item = queue[i]
        item.t = item.t - elapsed
        if item.t <= 0 then
            table.remove(queue, i)
            item.fn()
        end
    end
    if #queue == 0 then self:Hide() end
end)
```

### Bag-warning throttle (Ch 18)

```lua
local Bags = CreateFrame("Frame")
Bags.THROTTLE = 0.5
Bags.WARN     = 0.2
Bags.acc      = 0
Bags:RegisterEvent("BAG_UPDATE")

local function Scan(self)
    local maxS, freeS = 0, 0
    for b = 0, NUM_BAG_SLOTS do
        maxS  = maxS  + GetContainerNumSlots(b)
        freeS = freeS + GetContainerNumFreeSlots(b)
    end
    local pct = freeS / maxS
    if pct ~= self.last then
        if pct == 0 then
            RaidNotice_AddMessage(RaidWarningFrame, "Bags full!",
                                  ChatTypeInfo["RAID_WARNING"])
        elseif pct <= self.WARN then
            RaidNotice_AddMessage(RaidWarningFrame, "Low on bag space",
                                  ChatTypeInfo["RAID_WARNING"])
        end
        self.last = pct
    end
end

Bags:SetScript("OnEvent",  function(self) self:Show() end)
Bags:SetScript("OnUpdate", function(self, e)
    self.acc = self.acc + e
    if self.acc >= self.THROTTLE then
        self.acc = 0
        Scan(self)
        self:Hide()
    end
end)
```

## Common Bugs

- **Reading `elapsed` as delta since frame 0** — it is delta since the *previous* `OnUpdate` for this frame, which is not monotonic across `Hide`/`Show`.
- **Forgetting to `SetScript(..., nil)` after a one-shot** — the handler keeps running, silently burning CPU.
- **Attaching `OnUpdate` to a permanently visible parent** (UIParent, WorldFrame) — the handler runs forever even when idle.
- **Relying on `OnUpdate` for time-critical sequencing** — with FPS drops, `elapsed` can be 1s+ mid-combat; sequencing that assumes "this runs every 30ms" is wrong.
- **Unhiding a frame just to trigger one tick** — use `after = 0` pattern (set acc to the threshold); or restructure to a direct call.
- **Tainting protected frames by mutating their keys inside `OnUpdate`** — `OnUpdate` runs in the same insecure context; see [secure-execution](secure-execution.md).

## Related Concepts

- [events-system](events-system.md) — prefer events when they exist
- [function-hooking](function-hooking.md) — `OnUpdate` is frequently attached to hooked frames
- [secure-execution](secure-execution.md) — `OnUpdate` cannot take protected action
- [combat-log](combat-log.md) — archetypal use case for throttled UI refresh
- [Ch 18 — OnUpdate](../chapters/ch18-onupdate.md)
- [Ch 14 — CombatTracker](../chapters/ch14-combat-tracker.md)
- Best practices: [Ch 31](../chapters/ch31-best-practices.md)
