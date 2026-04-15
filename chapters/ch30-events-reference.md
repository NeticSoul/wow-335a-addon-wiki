---
title: "Ch 30 — Events Reference"
type: chapter
source_chapters: [30]
part: "IV — Reference"
pages: 28
chars: 46691
created: 2026-04-15
updated: 2026-04-15
tags: [events, reference, catalog]
---

# Ch 30 — Events Reference

## Overview

Reference listing of **400+ game events** with brief descriptions. Online companion: wowprogramming.com/docs/events.

## Event Categories (Sampled)

### Action Bar Events
`ACTIONBAR_HIDEGRID`, `ACTIONBAR_PAGE_CHANGED`, `ACTIONBAR_SHOWGRID`, `ACTIONBAR_SLOT_CHANGED`, `ACTIONBAR_UPDATE_COOLDOWN`, `ACTIONBAR_UPDATE_STATE`, `ACTIONBAR_UPDATE_USABLE`

### Achievement Events
`ACHIEVEMENT_EARNED`

### Addon Events
`ADDON_ACTION_FORBIDDEN`, `ADDON_LOADED`

### Arena/PvP Events
`ARENA_OPPONENT_UPDATE`, `ARENA_TEAM_INVITE_REQUEST`, `ARENA_TEAM_ROSTER_UPDATE`, `BATTLEFIELDS_SHOW`, `UPDATE_BATTLEFIELD_STATUS`

### Auction Events
`AUCTION_BIDDER_LIST_UPDATE`, `AUCTION_HOUSE_CLOSED`, `AUCTION_HOUSE_SHOW`, `AUCTION_ITEM_LIST_UPDATE`, `AUCTION_OWNED_LIST_UPDATE`

### Bag/Container Events
`BAG_OPEN`, `BAG_CLOSED`, `BAG_UPDATE`, `BAG_UPDATE_COOLDOWN`, `ITEM_LOCK_CHANGED`, `ITEM_LOCKED`

### Chat Events
`CHAT_MSG_ADDON`, `CHAT_MSG_CHANNEL`, `CHAT_MSG_GUILD`, `CHAT_MSG_PARTY`, `CHAT_MSG_RAID`, `CHAT_MSG_SAY`, `CHAT_MSG_SYSTEM`, `CHAT_MSG_WHISPER`

### Combat Events
`COMBAT_LOG_EVENT`, `COMBAT_LOG_EVENT_UNFILTERED`, `PLAYER_REGEN_DISABLED`, `PLAYER_REGEN_ENABLED`, `UNIT_COMBAT`, `UNIT_HEALTH`, `UNIT_MANA`

### Group Events
`GROUP_ROSTER_UPDATE`, `PARTY_INVITE_REQUEST`, `PARTY_MEMBERS_CHANGED`, `RAID_ROSTER_UPDATE`

### Player Events
`PLAYER_ALIVE`, `PLAYER_DEAD`, `PLAYER_ENTERING_WORLD`, `PLAYER_LEAVING_WORLD`, `PLAYER_LEVEL_UP`, `PLAYER_LOGIN`, `PLAYER_LOGOUT`, `PLAYER_TARGET_CHANGED`, `PLAYER_MONEY`

### Quest Events
`QUEST_ACCEPTED`, `QUEST_COMPLETE`, `QUEST_DETAIL`, `QUEST_LOG_UPDATE`, `QUEST_PROGRESS`

### Spell/Aura Events
`SPELL_UPDATE_COOLDOWN`, `UNIT_AURA`, `UNIT_SPELLCAST_START`, `UNIT_SPELLCAST_STOP`, `UNIT_SPELLCAST_SUCCEEDED`

### UI Events
`UPDATE_BINDINGS`, `UPDATE_MACROS`, `VARIABLES_LOADED`, `UI_ERROR_MESSAGE`, `UI_INFO_MESSAGE`

## Connections

- See [Events System](../concepts/events-system.md) concept page
- See [reference/events-catalog](../reference/events-catalog.md) for navigable reference
- Event handling covered in [Ch 13](ch13-game-events.md)
- Combat log events in detail: [Ch 21](ch21-combat-log.md)
