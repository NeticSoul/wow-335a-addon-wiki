---
title: Combat Log
type: concept
source_chapters: [21]
created: 2026-04-15
updated: 2026-04-15
tags: [combat-log, COMBAT_LOG_EVENT, GUID, subevent, threat]
---

# Combat Log

The most complex event system in WoW — provides detailed information about all combat actions.

## Event
`COMBAT_LOG_EVENT_UNFILTERED` fires for every combat action, regardless of filters.

## Standard Arguments (8 base)
`timestamp`, `combatEvent`, `sourceGUID`, `sourceName`, `sourceFlags`, `destGUID`, `destName`, `destFlags`

## GUIDs
Globally Unique Identifiers — uniquely identify every entity, even unnamed ones. More reliable than names.

## Sub-Event Categories

| Prefix | Trigger | Extra Args |
|--------|---------|-----------|
| `SWING_` | Melee attacks | — |
| `RANGE_` | Ranged attacks | spellId, spellName, spellSchool |
| `SPELL_` | Spell casts/effects | spellId, spellName, spellSchool |
| `SPELL_PERIODIC_` | DoT/HoT ticks | spellId, spellName, spellSchool |
| `ENVIRONMENTAL_` | Environmental damage | environmentalType |
| `UNIT_DIED` | Unit death | — |

## Source/Destination Flags
Bitmask checked with `bit.band()`:

| Flag | Meaning |
|------|---------|
| `COMBATLOG_OBJECT_TYPE_PLAYER` | Player character |
| `COMBATLOG_OBJECT_TYPE_NPC` | Non-player character |
| `COMBATLOG_OBJECT_TYPE_PET` | Pet |
| `COMBATLOG_OBJECT_REACTION_HOSTILE` | Hostile to player |
| `COMBATLOG_OBJECT_REACTION_FRIENDLY` | Friendly to player |
| `COMBATLOG_OBJECT_AFFILIATION_MINE` | Is the player |

## Threat API
- `UnitThreatSituation(unit)` → 0 (no threat), 1 (not tanking), 2 (insecure tank), 3 (secure tank)
- `UnitDetailedThreatSituation(unit, target)` → detailed percentage

## See Also
- [Ch 21](../chapters/ch21-combat-log.md)
- [Events System](events-system.md)
