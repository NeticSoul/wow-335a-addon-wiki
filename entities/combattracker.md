---
title: CombatTracker (Sample Addon)
type: entity
source_chapters: [14, 18, 19, 20, 21]
created: 2026-04-15
updated: 2026-04-15
tags: [addon, sample, combat, dps, tracker]
---

# CombatTracker

The primary tutorial addon for Part III. A DPS/damage tracking meter built incrementally across Chapters 14–21.

## Purpose
Monitors combat events and displays a real-time damage tracking window (similar to Recount/Details).

## Progression Across Chapters

| Chapter | What's Added |
|---------|-------------|
| [Ch 14](../chapters/ch14-combattracker.md) | Core addon, basic combat event parsing |
| [Ch 18](../chapters/ch18-onupdate.md) | OnUpdate for periodic UI refresh, smooth bar animation |
| [Ch 19](../chapters/ch19-function-hooking.md) | Hooking functions to extend behavior |
| [Ch 20](../chapters/ch20-custom-graphics.md) | Custom TGA textures for the tracker bars |
| [Ch 21](../chapters/ch21-combat-log.md) | Full COMBAT_LOG_EVENT_UNFILTERED parsing, GUID tracking |

## Key Techniques Demonstrated
- Combat log event parsing with sub-events
- GUID-based damage attribution
- OnUpdate throttling for smooth animation
- Function hooking
- Custom texture art (TGA format, power-of-two dimensions)
- Damage-per-second calculation over time

## See Also
- [Combat Log](../concepts/combat-log.md)
- [Events System](../concepts/events-system.md)
- [Function Hooking](../concepts/function-hooking.md)
- [Graphics and Textures](../concepts/graphics-and-textures.md)
