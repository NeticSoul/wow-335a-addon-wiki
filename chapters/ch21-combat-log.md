---
title: "Ch 21 — Responding to the Combat Log and Threat Information"
type: chapter
source_chapters: [21]
part: "III — Advanced Addon Techniques"
pages: 26
chars: 47847
created: 2026-04-15
updated: 2026-04-15
tags: [combat-log, COMBAT_LOG_EVENT, threat, GUID, combatlog-subevent]
---

# Ch 21 — Responding to the Combat Log and Threat Information

## Overview

Deep dive into the combat log system — the most complex event system in WoW. Covers `COMBAT_LOG_EVENT_UNFILTERED`, sub-events, GUIDs, source/destination flags, and the threat API.

## Key Concepts

### Combat Log Events
Two events:
- `COMBAT_LOG_EVENT` — filtered by the Blizzard combat log
- `COMBAT_LOG_EVENT_UNFILTERED` — fires for ALL combat events (most addons use this)

### Standard Event Arguments (first 8)
Every combat log event has:

| Arg | Name | Description |
|-----|------|------------|
| 1 | `timestamp` | Server timestamp (ms precision) |
| 2 | `combatEvent` | Sub-event name (e.g., `SWING_DAMAGE`) |
| 3 | `sourceGUID` | Globally Unique Identifier of source |
| 4 | `sourceName` | Name of source actor |
| 5 | `sourceFlags` | Bitfield: NPC/pet/player, relationship |
| 6 | `destGUID` | GUID of destination |
| 7 | `destName` | Name of destination actor |
| 8 | `destFlags` | Relationship flags for destination |

### GUIDs (Globally Unique Identifiers)
- Uniquely identify every entity (even unnamed/untargetable ones)
- Format: hex string encoding creature type, server ID, spawn ID
- Persist across combat events for reliable tracking
- More reliable than names (which can duplicate)

### Combat Sub-Events

**Swing events**: `SWING_DAMAGE`, `SWING_MISSED`
**Spell events**: `SPELL_DAMAGE`, `SPELL_HEAL`, `SPELL_MISSED`, `SPELL_CAST_START`, `SPELL_CAST_SUCCESS`, `SPELL_AURA_APPLIED`, `SPELL_AURA_REMOVED`, `SPELL_STOLEN`
**Range events**: `RANGE_DAMAGE`, `RANGE_MISSED`
**Environmental**: `ENVIRONMENTAL_DAMAGE`
**Unit events**: `UNIT_DIED`, `UNIT_DESTROYED`
**Other**: `DAMAGE_SHIELD`, `DAMAGE_SPLIT`, `SPELL_PERIODIC_*`

Spell-prefixed events add: `spellId`, `spellName`, `spellSchool` after the standard 8 args.

### Source/Destination Flags
Bitmask values combined with `bit.band()`:

| Flag | Meaning |
|------|---------|
| `COMBATLOG_OBJECT_TYPE_PLAYER` | Is a player |
| `COMBATLOG_OBJECT_TYPE_NPC` | Is an NPC |
| `COMBATLOG_OBJECT_TYPE_PET` | Is a pet |
| `COMBATLOG_OBJECT_CONTROL_PLAYER` | Player-controlled |
| `COMBATLOG_OBJECT_REACTION_HOSTILE` | Hostile |
| `COMBATLOG_OBJECT_REACTION_FRIENDLY` | Friendly |
| `COMBATLOG_OBJECT_AFFILIATION_MINE` | Is the player |
| `COMBATLOG_OBJECT_AFFILIATION_PARTY` | In player's party |
| `COMBATLOG_OBJECT_AFFILIATION_RAID` | In player's raid |

### Threat API
Functions to query threat information:
- `UnitThreatSituation(unit)` — returns threat status (0–3)
- `UnitDetailedThreatSituation(unit, target)` — detailed threat info

### CombatStatus Addon (Project)
Chapter builds CombatStatus — displays real-time combat statistics using the combat log.

## Connections

- See [Combat Log](../concepts/combat-log.md) concept page
- Builds on basic events from [Ch 13](ch13-game-events.md)
- Simpler combat events in [Ch 14](ch14-combat-tracker.md) (`UNIT_COMBAT`)
- Full events reference in [Ch 30](ch30-events-reference.md)
