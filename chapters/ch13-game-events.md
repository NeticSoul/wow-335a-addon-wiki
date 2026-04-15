---
title: "Ch 13 — Responding to Game Events"
type: chapter
source_chapters: [13]
part: "II — Programming in WoW"
pages: 24
chars: 34700
created: 2026-04-15
updated: 2026-04-15
tags: [events, registration, OnEvent, bagbuddy, UNIT_COMBAT, ADDON_LOADED]
---

# Ch 13 — Responding to Game Events

## Overview

Covers the event system — how addons are notified of game state changes. Events are the primary mechanism for addons to react to what happens in the game world without polling.

## Key Concepts

### What Are Events?
Events are notifications from the game client to addons about state changes. Without events, the UI would need to continuously poll for updates.

### Registering for Events
```lua
local frame = CreateFrame("Frame")
frame:RegisterEvent("UNIT_COMBAT")
frame:RegisterEvent("UNIT_HEALTH")
-- Can only register one event per call
frame:UnregisterEvent("UNIT_HEALTH")  -- stop receiving
```

### Responding with OnEvent
```lua
frame:SetScript("OnEvent", function(self, event, ...)
    if event == "UNIT_HEALTH" then
        local unit = ...
        -- handle health change
    elseif event == "UNIT_COMBAT" then
        local unit, action, modifier, amount, dtype = ...
        -- handle combat event
    end
end)
```

Arguments received: `self` (frame), `event` (string name), `...` (event-specific args).

### Event Dispatch Patterns

**Single handler with if/elseif** (simple):
```lua
if event == "EVENT_A" then ... elseif event == "EVENT_B" then ... end
```

**Table dispatch** (scalable):
```lua
local handlers = {}
function handlers.PLAYER_LOGIN(self, ...)
    -- handle login
end
function handlers.UNIT_HEALTH(self, ...)
    -- handle health
end
frame:SetScript("OnEvent", function(self, event, ...)
    if handlers[event] then
        handlers[event](self, ...)
    end
end)
```

### Key Events

| Event | Purpose | Args |
|-------|---------|------|
| `ADDON_LOADED` | An addon finished loading | addonName |
| `PLAYER_LOGIN` | Player has entered the world | — |
| `PLAYER_ENTERING_WORLD` | Zone/instance change | — |
| `PLAYER_LEAVING_WORLD` | Leaving world | — |
| `UNIT_HEALTH` | Unit's health changed | unitID |
| `UNIT_MANA` | Unit's mana/power changed | unitID |
| `UNIT_COMBAT` | Combat action occurred | unit, action, modifier, amount, dtype |
| `PLAYER_REGEN_DISABLED` | Entered combat | — |
| `PLAYER_REGEN_ENABLED` | Left combat | — |
| `BAG_UPDATE` | Bag contents changed | bagID |
| `VARIABLES_LOADED` | SavedVariables are available | — |
| `PLAYER_LOGOUT` | About to disconnect | — |

### Initialization Pattern
Common pattern to initialize an addon safely:
```lua
local frame = CreateFrame("Frame")
frame:RegisterEvent("ADDON_LOADED")
frame:SetScript("OnEvent", function(self, event, addon)
    if addon == "MyAddon" then
        -- SavedVariables are now available
        -- Initialize addon state
        self:UnregisterEvent("ADDON_LOADED")
    end
end)
```

### BagBuddy Events
The chapter finishes BagBuddy by registering for `BAG_UPDATE`, `ITEM_LOCK_CHANGED`, and other inventory events to keep the display synchronized.

## Connections

- See [Events System](../concepts/events-system.md) concept page
- Events are half the input story; [widget scripts](ch12-interacting-with-widgets.md) are the other half
- Combat events detailed further in [Ch 14](ch14-combat-tracker.md) and [Ch 21](ch21-combat-log.md)
- Full events reference in [Ch 30](ch30-events-reference.md)
- `PLAYER_REGEN_DISABLED`/`ENABLED` critical for [Secure Execution](../concepts/secure-execution.md)
