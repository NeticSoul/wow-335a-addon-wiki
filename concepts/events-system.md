---
title: Events System
type: concept
source_chapters: [13, 14, 21, 30]
created: 2026-04-15
updated: 2026-04-17
tags: [events, OnEvent, registration, game-state, dispatch, eventtrace]
---

# Events System

## Summary

The event system is the **push-based notification channel** from the game client to addons. Instead of polling, addons register a frame for named events; when game state changes, the client fires the event and invokes each registered frame's `OnEvent` script. WotLK 3.3.5a exposes ~400 events, documented in [Ch 30](../chapters/ch30-events-reference.md) and enumerated in the [events catalog](../reference/events-catalog.md).

Events are one of the two primary addon input channels. The other is the [widget script](../chapters/ch12-interacting-with-widgets.md) system (mouse, keyboard, focus). Both deliver data through the same `OnEvent` / `OnXxx` handler mechanism on frames.

## Detailed Explanation

### Registration Model

Registration is per-frame, per-event. A single frame can be registered for many events, and the same event may be registered by many frames - each receives its own dispatch.

```lua
local f = CreateFrame("Frame", "MyEventFrame", UIParent)
f:RegisterEvent("UNIT_HEALTH")
f:RegisterEvent("UNIT_POWER")      -- one call per event
f:UnregisterEvent("UNIT_POWER")    -- stop receiving this specific event
f:UnregisterAllEvents()            -- clear every registration on this frame
```

Re-registering an already-registered event is a no-op, not an error. A frame with zero registrations never receives `OnEvent`. Registration survives `/reload`-relative lifetime only if the frame is re-created by the addon; registrations do not persist across sessions.

### Dispatch Contract

Each firing calls the frame's `OnEvent` handler exactly once with:

- `self` - the receiving frame
- `event` - the event name (string)
- `...` - zero or more event-specific payload values, always as varargs (never a table)

```lua
f:SetScript("OnEvent", function(self, event, ...)
    -- Use select("#", ...) and select(i, ...) to inspect varargs safely.
end)
```

Each frame has at most one `OnEvent` script. A frame registered for many events must branch inside the handler on `event`.

### Dispatch Patterns

**Conditional branching** - fine for 1-3 events:

```lua
f:SetScript("OnEvent", function(self, event, arg1)
    if     event == "UNIT_HEALTH" then UpdateHealth(arg1)
    elseif event == "UNIT_POWER"  then UpdatePower(arg1)
    end
end)
```

**Table dispatch** - scales cleanly; every method receives the same `self, ...` as the handler:

```lua
local h = {}
function h:PLAYER_LOGIN()             self:Init() end
function h:UNIT_HEALTH(unit)          self:UpdateHealth(unit) end
function h:ADDON_LOADED(addonName)    if addonName == "MyAddon" then self:LoadDB() end end

f:SetScript("OnEvent", function(self, event, ...)
    local fn = h[event]
    if fn then fn(self, ...) end
end)
for k in pairs(h) do f:RegisterEvent(k) end  -- auto-register every declared handler
```

This idiom is the basis of `AceEvent-3.0` and is a common default for addons with more than a handful of events.

### Lifecycle / Initialization Events

These events define the **loading timeline** and are the most important ordering primitives in the addon model:

| Event | When it fires | Typical use |
|-------|---------------|-------------|
| `ADDON_LOADED` | After an addon's files are parsed and its SavedVariables loaded | Initialize DB, migrate saved data |
| `VARIABLES_LOADED` | After **all** SavedVariables for every addon are loaded | Rarely needed; prefer `ADDON_LOADED` with your addon name |
| `PLAYER_LOGIN` | On login and UI reload, immediately before `PLAYER_ENTERING_WORLD` | Safe to query most player-state APIs |
| `PLAYER_ENTERING_WORLD` | Initial login **and** every zone/instance transition and reload | Re-sync frame state after reloads |
| `PLAYER_LEAVING_WORLD` | Before unload / zone transition | Rarely used |
| `PLAYER_LOGOUT` | Just before the client exits / `/reload` | Final in-Lua hook; SavedVariables write is automatic |

`ADDON_LOADED` carries the name of the addon that loaded as its single argument; the event fires once per loaded addon. Full ordering is documented in [addon-loading-lifecycle](addon-loading-lifecycle.md).

### Query Events (Asynchronous APIs)

Many APIs round-trip to the server and cannot return data synchronously. The contract is: **call the query, wait for the paired event, then read the result**.

| API call | Paired event |
|----------|--------------|
| `GuildRoster()` | `GUILD_ROSTER_UPDATE` |
| `RequestRaidInfo()` | `UPDATE_INSTANCE_INFO` |
| `QueryAuctionItems(...)` | `AUCTION_ITEM_LIST_UPDATE` |
| mail inbox ops | `MAIL_INBOX_UPDATE` |
| `RequestBattlefieldScoreData()` | `UPDATE_BATTLEFIELD_SCORE` |

Reading immediately after the query returns stale or empty data. Always consult the API reference for a function before assuming synchronous semantics.

### UNIT_* Event Family

Every `UNIT_*` event delivers a **unit ID** ([unit-ids](unit-ids.md)) as its first argument. The client fires the event for *any* unit whose state changed, so handlers must filter:

```lua
f:SetScript("OnEvent", function(self, event, unit)
    if unit ~= "player" and unit ~= "target" then return end
    ...
end)
```

For frames tracking a single unit, prefer the unit-filtered variant where available (via secure templates / `RegisterUnitEvent`) to avoid dispatch on every party/raid member.

### BAG_UPDATE Quirk (Real-World Edge Case)

From the BagBuddy tutorial: `BAG_UPDATE` fires once per affected bag, can fire several times back-to-back (looting a stackable plus a new item -> two fires), and its bag-index argument can be **negative** due to the internal representation of equipped containers. Robust handlers filter `if bag >= 0 then ...`. Similar filtering/coalescing patterns recur across event families; do not trust a single firing, and always verify the argument domain in [Ch 30](../chapters/ch30-events-reference.md).

### Event Tracing (`/eventtrace`)

`Blizzard_DebugTools` ships a live event tracer. Commands:

```
/eventtrace          -- toggle window; starts capturing on first open
/eventtrace start    -- begin capture even if window is hidden
/eventtrace stop     -- halt capture
/eventtrace <N>      -- capture exactly N events, then stop
```

The window lists each event with its args, timestamp, and frame-delta since the previous event. It is the built-in way to **discover** which events accompany a user action (for example casting a spell, opening a mailbox, accepting a quest).

### Cost Model

- A registered frame whose event never fires adds no per-frame handler work.
- `OnEvent` dispatch is cheap C -> Lua, but the handler itself can be expensive. Filter early (`if unit ~= "player" then return end`) before doing work.
- `UNIT_AURA`, `UNIT_HEALTH_FREQUENT`, `COMBAT_LOG_EVENT_UNFILTERED`, and `UPDATE_MOUSEOVER_UNIT` are the highest-frequency events; in raids assume tens to hundreds of firings per second.
- Prefer `OnEvent` over `OnUpdate` polling whenever the data is event-backed. See [onupdate-throttling](onupdate-throttling.md) for the inverse case.

### Common Bugs

- **Registering inside the handler on first fire** without unregistering the init event - repeated initialization on every `ADDON_LOADED` for other addons.
- **Forgetting to filter `ADDON_LOADED` by name** - runs initialization every time *any* addon loads, including Blizzard_* on demand.
- **Registering for `UNIT_*` expecting only the tracked unit** - all units of the same category fire it; always filter `unit`.
- **Calling `GuildRoster()` and reading immediately** - data only arrives after `GUILD_ROSTER_UPDATE`.
- **Assuming `PLAYER_LOGIN` does not fire on `/reload`** - the raw events reference documents `PLAYER_LOGIN` on both login and UI reload; use `PLAYER_ENTERING_WORLD` when you specifically need the broader loading-screen/zone-transition behavior.

## Examples

### Minimal event-driven skeleton

```lua
local ADDON = "MyAddon"
local f = CreateFrame("Frame")
f:RegisterEvent("ADDON_LOADED")
f:RegisterEvent("PLAYER_LOGIN")
f:SetScript("OnEvent", function(self, event, arg1)
    if event == "ADDON_LOADED" and arg1 == ADDON then
        MyAddonDB = MyAddonDB or { version = 1 }
        self:UnregisterEvent("ADDON_LOADED")
    elseif event == "PLAYER_LOGIN" then
        -- Safe to inspect player state, group, inventory.
    end
end)
```

### BagBuddy inventory watcher (Ch 13)

```lua
function BagBuddy_OnEvent(self, event, ...)
    if event == "ADDON_LOADED" and ... == "BagBuddy" then
        BagBuddy_ItemTimes = BagBuddy_ItemTimes or {}
        for bag = 0, NUM_BAG_SLOTS do
            BagBuddy_ScanBag(bag, true)             -- seed counts on first load
        end
        self:UnregisterEvent("ADDON_LOADED")
        self:RegisterEvent("BAG_UPDATE")
    elseif event == "BAG_UPDATE" then
        local bag = ...
        if bag >= 0 then                             -- ignore negative pseudo-bags
            BagBuddy_ScanBag(bag)
            if BagBuddy:IsVisible() then BagBuddy_Update() end
        end
    end
end
```

### Combat bracket for secure reconfiguration

```lua
local f = CreateFrame("Frame")
f:RegisterEvent("PLAYER_REGEN_DISABLED")  -- combat START: last chance to mutate protected frames
f:RegisterEvent("PLAYER_REGEN_ENABLED")   -- combat END: lockdown released
f:SetScript("OnEvent", function(_, event)
    if event == "PLAYER_REGEN_DISABLED" then
        pendingReconfigure = false
    else
        if pendingReconfigure then ApplyProtectedLayout() end
    end
end)
```

See [secure-execution](secure-execution.md) for why this bracket is mandatory.

## Related Concepts

- [addon-loading-lifecycle](addon-loading-lifecycle.md) - full ordering of `ADDON_LOADED`, SavedVariables, `PLAYER_LOGIN`, `PLAYER_ENTERING_WORLD`
- [combat-log](combat-log.md) - `COMBAT_LOG_EVENT_UNFILTERED` has a payload model of its own
- [secure-execution](secure-execution.md) - `PLAYER_REGEN_DISABLED/ENABLED` gate combat lockdown
- [saved-variables](saved-variables.md) - `ADDON_LOADED` is a common init point
- [onupdate-throttling](onupdate-throttling.md) - alternative input channel for per-frame work
- [unit-ids](unit-ids.md) - payload shape for every `UNIT_*` event
- [frames-and-widgets](frames-and-widgets.md) - events are delivered through frames
- Reference: [events-catalog](../reference/events-catalog.md), [Ch 13](../chapters/ch13-game-events.md), [Ch 30](../chapters/ch30-events-reference.md)
