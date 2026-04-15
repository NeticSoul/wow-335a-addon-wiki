---
title: Events System
type: concept
source_chapters: [13, 14, 21, 30]
created: 2026-04-15
updated: 2026-04-15
tags: [events, OnEvent, registration, game-state]
---

# Events System

Events notify addons of game state changes. There are **400+ events** covering combat, inventory, quests, chat, UI, and more.

## How It Works
1. **Register**: `frame:RegisterEvent("EVENT_NAME")`
2. **Handle**: `frame:SetScript("OnEvent", handler)`
3. **Unregister**: `frame:UnregisterEvent("EVENT_NAME")`

Handler receives: `self` (frame), `event` (string), `...` (event-specific args).

## Dispatch Patterns

**If/elseif** (simple):
```lua
if event == "A" then ... elseif event == "B" then ... end
```

**Table dispatch** (scalable):
```lua
local h = {}
function h.PLAYER_LOGIN(self) ... end
frame:SetScript("OnEvent", function(s, e, ...) if h[e] then h[e](s, ...) end end)
```

## Key Events by Category

| Category | Events |
|----------|--------|
| Lifecycle | `ADDON_LOADED`, `PLAYER_LOGIN`, `PLAYER_ENTERING_WORLD`, `PLAYER_LOGOUT` |
| Combat | `PLAYER_REGEN_DISABLED/ENABLED`, `UNIT_COMBAT`, `UNIT_HEALTH`, `UNIT_MANA` |
| Combat Log | `COMBAT_LOG_EVENT_UNFILTERED` (40+ sub-events) |
| Inventory | `BAG_UPDATE`, `ITEM_LOCK_CHANGED` |
| Spells | `UNIT_SPELLCAST_START/STOP/SUCCEEDED`, `SPELL_UPDATE_COOLDOWN` |
| Target | `PLAYER_TARGET_CHANGED`, `UNIT_AURA` |
| Group | `PARTY_MEMBERS_CHANGED`, `RAID_ROSTER_UPDATE` |
| Chat | `CHAT_MSG_SAY/PARTY/GUILD/WHISPER` |

## PLAYER_REGEN Events
- `PLAYER_REGEN_DISABLED` = combat starts → last chance to configure [secure frames](secure-execution.md)
- `PLAYER_REGEN_ENABLED` = combat ends → can reconfigure secure frames

## See Also
- [Ch 13](../chapters/ch13-game-events.md), [Ch 30](../chapters/ch30-events-reference.md)
- [Combat Log](combat-log.md)
- [Secure Execution](secure-execution.md)
