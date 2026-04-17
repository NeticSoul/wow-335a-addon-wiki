---
title: Combat Log
type: concept
source_chapters: [14, 21]
created: 2026-04-15
updated: 2026-04-17
tags: [combat-log, COMBAT_LOG_EVENT, GUID, subevent, bitfield, threat, damage]
---

# Combat Log

## Summary

The WoW 3.3.5a combat log is a **single event** (`COMBAT_LOG_EVENT_UNFILTERED`) that delivers every combat-related happening in the game world to addons, using a payload encoding with two pieces:

1. A fixed prefix of **8 base arguments** describing the time, event name, and source/destination actors.
2. A variable tail of per-sub-event arguments, structured as a **prefix/suffix grammar** where the sub-event name (`SPELL_DAMAGE`, `SWING_MISSED`, `SPELL_PERIODIC_HEAL`, ...) is a composition of one of ~5 action prefixes and ~15 outcome suffixes, plus a handful of non-conforming special events.

This structure must be decoded correctly for every event; getting it wrong produces misparsed data. Two related events exist:

- `COMBAT_LOG_EVENT` — fires for combat events that match the player's current in-game combat log filter (used only by `Blizzard_CombatLog`).
- `COMBAT_LOG_EVENT_UNFILTERED` — fires for **every** combat event regardless of filters. This is the version most combat-log addons register for.

## Detailed Explanation

### Base Arguments (always 8)

| Pos | Arg | Type | Meaning |
|-----|-----|------|---------|
| 1 | `timestamp` | number | Server-provided, millisecond precision (Unix epoch). Use for coalescing and reconciling across clients. |
| 2 | `combatEvent` | string | Sub-event name, e.g. `SPELL_DAMAGE`, `UNIT_DIED`, `SWING_MISSED`. |
| 3 | `sourceGUID` | string | Unique identifier of the source actor. May be `"0x0000000000000000"` (nil GUID) if no source. |
| 4 | `sourceName` | string | Display name of the source actor. May be `nil` or empty. |
| 5 | `sourceFlags` | number | Bitfield describing source actor. See § Unit Flags. |
| 6 | `destGUID` | string | Destination actor GUID. |
| 7 | `destName` | string | Destination actor display name. |
| 8 | `destFlags` | number | Destination flags bitfield. |

Arguments 9+ are **sub-event specific**. Never index them without first branching on `combatEvent`.

### Sub-Event Grammar: Prefix + Suffix

Most sub-event names are `<PREFIX>_<SUFFIX>`. Each prefix contributes its own argument tail (args 9+), then each suffix contributes more.

#### Prefixes (action type)

| Prefix | Additional args (after base 8) | Notes |
|--------|--------------------------------|-------|
| `SWING` | — | Melee auto-attack; carries no spell info. |
| `RANGE` | `spellId, spellName, spellSchool` | Ranged auto-shot, wand, throw. |
| `SPELL` | `spellId, spellName, spellSchool` | Any non-periodic spell action. |
| `SPELL_PERIODIC` | `spellId, spellName, spellSchool` | DoT/HoT ticks. |
| `SPELL_BUILDING` | `spellId, spellName, spellSchool` | Spell against a building (Wintergrasp). |
| `ENVIRONMENTAL` | `environmentType` (string: `"Drowning"`, `"Falling"`, `"Fatigue"`, `"Fire"`, `"Lava"`, `"Slime"`) | Environment damage. |

#### Suffixes (outcome)

| Suffix | Additional args |
|--------|-----------------|
| `_DAMAGE`, `_BUILDING_DAMAGE` | `amount, overkill, school, resisted, blocked, absorbed, critical, glancing, crushing` |
| `_MISSED`, `_PERIODIC_MISSED` | `missType ("ABSORB"/"BLOCK"/"DEFLECT"/"DODGE"/"EVADE"/"IMMUNE"/"MISS"/"PARRY"/"REFLECT"/"RESIST"), amountMissed` |
| `_HEAL`, `_PERIODIC_HEAL`, `_BUILDING_HEAL` | `amount, overhealing, absorbed, critical` |
| `_ENERGIZE`, `_PERIODIC_ENERGIZE` | `amount, powerType` |
| `_LEECH`, `_PERIODIC_LEECH` | `amount, powerType, extraAmount` |
| `_DRAIN`, `_PERIODIC_DRAIN` | `amount, powerType, extraAmount` |
| `_CAST_START`, `_CAST_SUCCESS` | — (SPELL prefix only) |
| `_CAST_FAILED` | `failedType` (reason string) |
| `_SUMMON`, `_RESURRECT`, `_CREATE`, `_INSTAKILL` | — |
| `_INTERRUPT`, `_DISPEL`, `_DISPEL_FAILED`, `_STOLEN` | `extraSpellID, extraSpellName, extraSchool, auraType` |
| `_EXTRA_ATTACKS` | `amount` |
| `_DURABILITY_DAMAGE`, `_DURABILITY_DAMAGE_ALL` | — |
| `_AURA_APPLIED`, `_AURA_REMOVED`, `_AURA_REFRESH`, `_AURA_BROKEN` | `auraType` (`"BUFF"` / `"DEBUFF"`) |
| `_AURA_APPLIED_DOSE`, `_AURA_REMOVED_DOSE` | `auraType, amount` |
| `_AURA_BROKEN_SPELL` | `extraSpellID, extraSpellName, extraSchool, auraType` |

#### Power Types (`powerType`)

| Value | Resource |
|-------|----------|
| `-2` | Health |
| `0` | Mana |
| `1` | Rage |
| `2` | Focus (pets) |
| `3` | Energy |
| `4` | Pet happiness |
| `5` | Runes |
| `6` | Runic power |

### Non-Conforming Special Events

These break the prefix/suffix pattern:

- `DAMAGE_SHIELD` — uses `SPELL` prefix args + `_DAMAGE` suffix args (damage from a reactive shield).
- `DAMAGE_SPLIT` — same shape; damage split across targets.
- `DAMAGE_SHIELD_MISSED` — `SPELL` + `_MISSED`.
- `ENCHANT_APPLIED`, `ENCHANT_REMOVED` — args: `spellName, itemID, itemName`.
- `PARTY_KILL` — no extra args.
- `UNIT_DIED`, `UNIT_DESTROYED` — no extra args; useful for death tracking.

### Spell Schools (bitmask)

`spellSchool` is a bitmask composed of:

| School | Bit value |
|--------|-----------|
| Physical | 1 |
| Holy | 2 |
| Fire | 4 |
| Nature | 8 |
| Frost | 16 |
| Shadow | 32 |
| Arcane | 64 |

Compound schools combine bits: Frostfire = Frost ∣ Fire = 20. Decode with `bit.band(school, <flag>)`; there is exactly one way to decompose a valid school value.

### Unit Flags (Source/Destination)

`sourceFlags` / `destFlags` are bitfields. Test with `bit.band(flags, COMBATLOG_OBJECT_<CONSTANT>_MASK) == COMBATLOG_OBJECT_<VALUE>`. The predefined globals (all exposed in the runtime):

**`COMBATLOG_OBJECT_TYPE_MASK`** — compare to one of:
- `COMBATLOG_OBJECT_TYPE_PLAYER`
- `COMBATLOG_OBJECT_TYPE_NPC`
- `COMBATLOG_OBJECT_TYPE_PET`
- `COMBATLOG_OBJECT_TYPE_GUARDIAN`
- `COMBATLOG_OBJECT_TYPE_OBJECT`

**`COMBATLOG_OBJECT_CONTROL_MASK`** — who *currently* controls (accounts for mind control):
- `COMBATLOG_OBJECT_CONTROL_PLAYER`
- `COMBATLOG_OBJECT_CONTROL_NPC`

**`COMBATLOG_OBJECT_REACTION_MASK`** — entity's disposition toward the player (not "who is attacking whom"; a yellow neutral mob fighting you still flags as neutral):
- `COMBATLOG_OBJECT_REACTION_HOSTILE`
- `COMBATLOG_OBJECT_REACTION_NEUTRAL`
- `COMBATLOG_OBJECT_REACTION_FRIENDLY`

**`COMBATLOG_OBJECT_AFFILIATION_MASK`** — closeness to player:
- `COMBATLOG_OBJECT_AFFILIATION_MINE` (=1)
- `COMBATLOG_OBJECT_AFFILIATION_PARTY`
- `COMBATLOG_OBJECT_AFFILIATION_RAID`
- `COMBATLOG_OBJECT_AFFILIATION_OUTSIDER` (=8)

Numerically ordered; `flags & MASK <= AFFILIATION_PARTY` reliably catches "me or my party members".

**`COMBATLOG_OBJECT_SPECIAL_MASK`** — transient markers:
- `COMBATLOG_OBJECT_RAIDTARGET1..8`
- `COMBATLOG_OBJECT_MAINTANK`, `COMBATLOG_OBJECT_MAINASSIST`
- `COMBATLOG_OBJECT_FOCUS`, `COMBATLOG_OBJECT_TARGET`
- `COMBATLOG_OBJECT_NONE`

### `CombatLog_Object_IsA` — convenience

```lua
CombatLog_Object_IsA(unitFlags, COMBATLOG_FILTER_MY_PET)
```

Predefined filter constants: `COMBATLOG_FILTER_EVERYTHING`, `COMBATLOG_FILTER_FRIENDLY_UNITS`, `COMBATLOG_FILTER_HOSTILE_PLAYERS`, `COMBATLOG_FILTER_HOSTILE_UNITS`, `COMBATLOG_FILTER_ME`, `COMBATLOG_FILTER_MINE`, `COMBATLOG_FILTER_MY_PET`, `COMBATLOG_FILTER_NEUTRAL_UNITS`, `COMBATLOG_FILTER_UNKNOWN_UNITS`. Returns `1` on match, `nil` otherwise.

### GUIDs

GUIDs identify every entity, including those that have no unit ID (mobs out of party range, distant players, temporary pets). Characteristics:

- **Strings**, not numbers — GUID values exceed Lua 5.1 number precision. Example: `"0x0100000002AB26D5"`.
- Persistent for players (for their lifetime on a given server).
- Pets and totems receive a **new** GUID each time they are summoned.
- NPC GUIDs live from spawn to death/despawn; may be **recycled** after server/instance restart.
- `UnitGUID(unitID)` queries the current GUID for a unit ID.
- `GetPlayerInfoByGUID(guid)` returns `localizedClass, classFilename, localizedRace, raceFilename, sex` for player GUIDs.
- Type information is embedded in the upper bits of the GUID. Rough decode:

```lua
-- Upper nibble tells entity type:
--   0 = player, 3 or 4 = NPC, other codes for pet/vehicle/etc.
local function GUIDKind(guid)
    local upper = tonumber(guid:sub(1, 5))
    return bit.band(upper, 0x00F)
end
```

Always key combat-log bookkeeping by GUID, never by name — names collide between servers, across faction changes, and during mind control.

### Threat API (companion to combat log)

Threat data is not delivered through the combat log; it comes from dedicated APIs and the `UNIT_THREAT_SITUATION_UPDATE` / `UNIT_THREAT_LIST_UPDATE` events (Ch 21).

- `UnitThreatSituation(unit[, target])` → one of `nil` (no threat), `0` (lowest), `1` (higher but not tanking), `2` (insecurely tanking — can lose aggro), `3` (securely tanking).
- `UnitDetailedThreatSituation(unit, target)` → `isTanking, status, threatPct, rawThreatPct, threatValue`.

### CombatTracker Pattern (Ch 14, 21)

A DPS/HPS tracker:

1. Subscribe to `COMBAT_LOG_EVENT_UNFILTERED`, `PLAYER_REGEN_DISABLED`, `PLAYER_REGEN_ENABLED`.
2. On `PLAYER_REGEN_DISABLED`: reset counters; record `GetTime()` as combat start.
3. On combat-log event: filter `sourceFlags` by `COMBATLOG_OBJECT_AFFILIATION_PARTY` (or `_MINE`); branch on `combatEvent` to accumulate damage/healing per `sourceGUID`.
4. On `PLAYER_REGEN_ENABLED`: stop; compute elapsed; divide totals.
5. Use `OnUpdate` throttle to update the display once per second during combat — *not* on every combat-log event. See [onupdate-throttling](onupdate-throttling.md).

## Examples

### Skeleton dispatcher

```lua
local f = CreateFrame("Frame")
f:RegisterEvent("COMBAT_LOG_EVENT_UNFILTERED")
f:SetScript("OnEvent", function(_, _,
    timestamp, subevent, srcGUID, srcName, srcFlags,
    dstGUID, dstName, dstFlags, ...)

    -- Fast early rejection: only my party/raid is interesting
    if bit.band(srcFlags, COMBATLOG_OBJECT_AFFILIATION_MASK)
       > COMBATLOG_OBJECT_AFFILIATION_RAID then
        return
    end

    if subevent == "SPELL_DAMAGE" or subevent == "SPELL_PERIODIC_DAMAGE" then
        local spellId, spellName, school,
              amount, overkill, dmgSchool, resisted, blocked, absorbed,
              critical = ...
        AccumulateDamage(srcGUID, amount - (overkill or 0))
    elseif subevent == "SWING_DAMAGE" then
        local amount, overkill = ...
        AccumulateDamage(srcGUID, amount - (overkill or 0))
    elseif subevent == "UNIT_DIED" then
        MarkDead(dstGUID)
    end
end)
```

### Filtering your pet's damage

```lua
local MINE_OR_MYPET = bit.bor(COMBATLOG_OBJECT_AFFILIATION_MINE,
                              COMBATLOG_OBJECT_TYPE_PET)
-- Better: use CombatLog_Object_IsA
if CombatLog_Object_IsA(srcFlags, COMBATLOG_FILTER_MY_PET) == 1 then
    ...
end
```

### Spell-school decode

```lua
local function SchoolNames(mask)
    local t = {}
    if bit.band(mask, 1)  ~= 0 then t[#t+1] = "Physical" end
    if bit.band(mask, 2)  ~= 0 then t[#t+1] = "Holy"     end
    if bit.band(mask, 4)  ~= 0 then t[#t+1] = "Fire"     end
    if bit.band(mask, 8)  ~= 0 then t[#t+1] = "Nature"   end
    if bit.band(mask, 16) ~= 0 then t[#t+1] = "Frost"    end
    if bit.band(mask, 32) ~= 0 then t[#t+1] = "Shadow"   end
    if bit.band(mask, 64) ~= 0 then t[#t+1] = "Arcane"   end
    return table.concat(t, "+")
end
```

## Performance

`COMBAT_LOG_EVENT_UNFILTERED` is the highest-volume event by far. In 25-man raids it can fire **several hundred times per second**. Consequences:

- **Handler must be cheap.** Reject uninteresting events in the first few lines (affiliation mask, subevent whitelist).
- **Avoid table allocation** inside the handler. Reuse scratch tables.
- **Do not update the UI per event.** Accumulate into a buffer, refresh on an `OnUpdate` throttle.
- **Prefer `bit.band` over string comparison** for flag checks.
- The `sourceFlags & AFFILIATION_MASK > AFFILIATION_RAID` early-exit is a common way to reject outsider PvP and unrelated trash in raid logs.

## Common Bugs

- **Mis-indexing vararg tail** because the number of args after the base 8 depends on the sub-event — `select(12, ...)` may be `critical` for `SPELL_DAMAGE` but `auraType` for `SPELL_AURA_APPLIED`. Always decode per sub-event.
- **Tracking by name instead of GUID** — collides on name changes, vehicles, pets, mind control.
- **Ignoring `overkill`** in DPS totals — counts damage the target couldn't absorb; usually should be subtracted.
- **Assuming `UNIT_DIED` has source info** — `sourceGUID`/`sourceName` are often nil for environment deaths.
- **Listening to `COMBAT_LOG_EVENT` instead of the unfiltered version** — events drop silently according to the player's UI filter.
- **Parsing spell school as a single value** — it is a bitmask; Frostfire is 20, not a single school code.

## Related Concepts

- [events-system](events-system.md) — `COMBAT_LOG_EVENT_UNFILTERED` is an event but has its own payload model
- [onupdate-throttling](onupdate-throttling.md) — batching UI updates from combat-log bursts
- [unit-ids](unit-ids.md) — converting between GUIDs and unit IDs
- [secure-execution](secure-execution.md) — combat-log work is all insecure; no protection concerns
- Entity: [CombatTracker](../entities/combattracker.md), [CombatStatus](../entities/combatstatus.md)
- [Ch 14 — CombatTracker](../chapters/ch14-combat-tracker.md), [Ch 21 — Combat Log](../chapters/ch21-combat-log.md)
