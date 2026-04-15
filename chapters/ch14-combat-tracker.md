---
title: "Ch 14 — Tracking Damage with CombatTracker"
type: chapter
source_chapters: [14]
part: "III — Advanced Addon Techniques"
pages: 18
chars: 25395
created: 2026-04-15
updated: 2026-04-15
tags: [combat, events, addon-project, combattracker, dps]
---

# Ch 14 — Tracking Damage with CombatTracker

## Overview

Project chapter that builds CombatTracker — a damage tracking addon. Demonstrates spec-first design, event selection, and a complete addon from scratch.

## Key Concepts

### Spec-First Design
1. Define what the addon will do (user experience)
2. Find the right game events
3. Design the frame layout
4. Implement the logic

### CombatTracker Specs
- Frame draggable by the user
- On combat enter: store time, display "In Combat"
- On damage taken: show time, total damage, incoming DPS
- On combat end: display final statistics
- On click: send DPS summary to party (or print locally)

### Events Used

| Event | Purpose |
|-------|---------|
| `PLAYER_REGEN_DISABLED` | Player enters combat (regen stops) |
| `PLAYER_REGEN_ENABLED` | Player leaves combat (regen resumes) |
| `UNIT_COMBAT` | Unit takes/deals combat action |

`UNIT_COMBAT` args: `unit`, `action` ("WOUND", "HEAL", "DODGE", etc.), `modifier` ("CRITICAL", "CRUSHING"), `amount`, `damageType`.

### Implementation Pattern
1. Create a draggable frame (using `SetMovable` + drag handlers)
2. Register for the three events
3. Track state: `inCombat`, `combatStart`, `totalDamage`
4. Calculate DPS = totalDamage / elapsed time
5. Use `SendChatMessage()` to output results to party

### SendChatMessage
```lua
SendChatMessage("text", "PARTY")  -- send to party
SendChatMessage("text", "SAY")    -- say in local
```
Only works when in the right context (e.g., must be in a party to send to party).

## Connections

- Applies everything from Part II (frames, events, API)
- Uses [Events System](../concepts/events-system.md) extensively
- `PLAYER_REGEN_*` events are key to [Secure Execution](../concepts/secure-execution.md)
- More detailed combat tracking in [Ch 21](ch21-combat-log.md) via COMBAT_LOG_EVENT
