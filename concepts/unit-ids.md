---
title: Unit IDs
type: concept
source_chapters: [11, 26]
created: 2026-04-15
updated: 2026-04-15
tags: [unit-ids, targeting, api]
---

# Unit IDs

String tokens used by many API functions to refer to specific units (players, NPCs, pets).

## Standard Unit IDs

| ID | Refers To |
|----|-----------|
| `"player"` | The player |
| `"target"` | Current target |
| `"focus"` | Focus target |
| `"pet"` | Player's pet |
| `"mouseover"` | Unit under cursor |
| `"npc"` | NPC in dialog |
| `"vehicle"` | Player's vehicle |
| `"party1"` – `"party4"` | Party members |
| `"partypet1"` – `"partypet4"` | Party pets |
| `"raid1"` – `"raid40"` | Raid members |
| `"raidpet1"` – `"raidpet40"` | Raid pets |
| `"boss1"` – `"boss4"` | Boss frames |
| `"arena1"` – `"arena5"` | Arena opponents |

## Compound IDs
Append `"target"` to get a unit's target: `"targettarget"`, `"party1target"`, `"focustarget"`, `"pettarget"`.

## Usage
```lua
local hp = UnitHealth("target")
local name = UnitName("party2")
local exists = UnitExists("focus")
```

## See Also
- [Ch 11 — WoW API](../chapters/ch11-wow-api.md)
- [WoW API](wow-api.md)
