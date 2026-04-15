---
title: Events Catalog
type: reference
source_chapters: [13, 30]
created: 2026-04-15
updated: 2026-04-15
tags: [events, catalog, reference]
---

# Events Catalog

400+ game events organized by category. Full descriptions in Ch 30.

## Categories

### Lifecycle
| Event | Fires When |
|-------|------------|
| `ADDON_LOADED` | Addon + SavedVariables loaded |
| `PLAYER_LOGIN` | Player enters world (first time) |
| `PLAYER_ENTERING_WORLD` | Player enters world (login, zone, reload) |
| `PLAYER_LEAVING_WORLD` | Before leaving world |
| `PLAYER_LOGOUT` | Player logs out |
| `PLAYER_ALIVE` | Player respawns from death |
| `PLAYER_DEAD` | Player dies |

### Combat State
| Event | Fires When |
|-------|------------|
| `PLAYER_REGEN_DISABLED` | Entering combat |
| `PLAYER_REGEN_ENABLED` | Leaving combat |
| `PLAYER_TARGET_CHANGED` | Target changes |
| `PLAYER_FOCUS_CHANGED` | Focus changes |
| `UNIT_HEALTH` | Unit health changes |
| `UNIT_MANA` | Unit mana changes |
| `UNIT_AURA` | Unit buffs/debuffs change |
| `UNIT_COMBAT` | Unit takes/deals combat action |

### Combat Log
| Event | Fires When |
|-------|------------|
| `COMBAT_LOG_EVENT_UNFILTERED` | Any combat action (see [Combat Log](../concepts/combat-log.md)) |

### Spellcasting
| Event | Fires When |
|-------|------------|
| `UNIT_SPELLCAST_START` | Cast begins |
| `UNIT_SPELLCAST_STOP` | Cast stops |
| `UNIT_SPELLCAST_SUCCEEDED` | Cast succeeds |
| `UNIT_SPELLCAST_FAILED` | Cast fails |
| `UNIT_SPELLCAST_INTERRUPTED` | Cast interrupted |
| `UNIT_SPELLCAST_CHANNEL_START` | Channel begins |
| `UNIT_SPELLCAST_CHANNEL_STOP` | Channel ends |
| `SPELL_UPDATE_COOLDOWN` | Cooldown starts/ends |

### Inventory & Items
| Event | Fires When |
|-------|------------|
| `BAG_UPDATE` | Container contents change |
| `BAG_UPDATE_COOLDOWN` | Item cooldown changes |
| `ITEM_LOCK_CHANGED` | Item lock state changes |
| `ITEM_PUSH` | Item added to inventory |
| `EQUIPMENT_SWAP_FINISHED` | Equipment set swap completes |
| `BANKFRAME_OPENED/CLOSED` | Bank interaction |

### Action Bar
| Event | Fires When |
|-------|------------|
| `ACTIONBAR_SLOT_CHANGED` | Action slot contents change |
| `ACTIONBAR_UPDATE_COOLDOWN` | Action cooldown changes |
| `ACTIONBAR_UPDATE_STATE` | Action state changes |
| `ACTIONBAR_UPDATE_USABLE` | Action usability changes |
| `ACTIONBAR_PAGE_CHANGED` | Bar page changes |

### Chat
| Event | Fires When |
|-------|------------|
| `CHAT_MSG_SAY` | Say message received |
| `CHAT_MSG_PARTY` | Party message |
| `CHAT_MSG_RAID` | Raid message |
| `CHAT_MSG_GUILD` | Guild message |
| `CHAT_MSG_WHISPER` | Whisper received |
| `CHAT_MSG_CHANNEL` | Custom channel message |
| `CHAT_MSG_ADDON` | Addon comm message |
| `CHAT_MSG_SYSTEM` | System message |

### Group & Raid
| Event | Fires When |
|-------|------------|
| `PARTY_MEMBERS_CHANGED` | Party composition changes |
| `RAID_ROSTER_UPDATE` | Raid roster changes |
| `PARTY_LEADER_CHANGED` | Party leader changes |
| `PARTY_LOOT_METHOD_CHANGED` | Loot method changes |

### Quest
| Event | Fires When |
|-------|------------|
| `QUEST_ACCEPTED` | Quest accepted |
| `QUEST_COMPLETE` | Quest ready to turn in |
| `QUEST_LOG_UPDATE` | Quest log changes |
| `QUEST_DETAIL` | Quest details shown |
| `QUEST_FINISHED` | Quest interaction ends |

### Auction House
| Event | Fires When |
|-------|------------|
| `AUCTION_HOUSE_SHOW/CLOSED` | AH interaction starts/ends |
| `AUCTION_ITEM_LIST_UPDATE` | Search results available |
| `AUCTION_OWNED_LIST_UPDATE` | Player's auctions change |
| `AUCTION_BIDDER_LIST_UPDATE` | Player's bids change |

### Trade & Merchant
| Event | Fires When |
|-------|------------|
| `TRADE_SHOW/CLOSED` | Trade window |
| `MERCHANT_SHOW/CLOSED` | Merchant interaction |
| `MAIL_SHOW/CLOSED` | Mail interaction |

### Talent & Skill
| Event | Fires When |
|-------|------------|
| `CHARACTER_POINTS_CHANGED` | Talent points change |
| `ACTIVE_TALENT_GROUP_CHANGED` | Dual spec swap |
| `SKILL_LINES_CHANGED` | Skill values change |

### Achievement
| Event | Fires When |
|-------|------------|
| `ACHIEVEMENT_EARNED` | Achievement completed |
| `CRITERIA_UPDATE` | Achievement criteria progress |

### UI & System
| Event | Fires When |
|-------|------------|
| `UI_ERROR_MESSAGE` | Red error text |
| `UI_INFO_MESSAGE` | Info text |
| `ADDON_ACTION_FORBIDDEN` | Protected API block |
| `ADDON_ACTION_BLOCKED` | Tainted action block |
| `UPDATE_MOUSEOVER_UNIT` | Mouseover unit changes |

### Zone & Map
| Event | Fires When |
|-------|------------|
| `ZONE_CHANGED` | Subzone changes |
| `ZONE_CHANGED_NEW_AREA` | Major zone changes |
| `ZONE_CHANGED_INDOORS` | Indoor/outdoor transition |
| `WORLD_MAP_UPDATE` | Map data update |

## See Also
- [Ch 30 — Events Reference](../chapters/ch30-events-reference.md)
- [Events System](../concepts/events-system.md)
