---
title: "Ch 27 — API Reference"
type: chapter
source_chapters: [27]
part: "IV — Reference"
pages: 486
chars: 852807
created: 2026-04-15
updated: 2026-04-15
tags: [api, reference, functions, signatures]
---

# Ch 27 — API Reference

## Overview

Alphabetical listing of **1000+ API functions** with signatures, arguments, returns, and descriptions. This is the largest chapter in the book (486 pages). Online companion: wowprogramming.com/docs.

## Reference Conventions

### Function Signatures
```
deposit = CalculateAuctionDeposit(runTime)
```
- Left of `=`: return values
- Right of `=`: function name and arguments
- No signature listed = no args, no returns

### Optional Arguments
Square brackets indicate optional args:
```
BuyMerchantItem(index [, quantity])
SendChatMessage("text" [, "chatType" [, "language" [, "channel"]]])
```
Nested brackets = must include preceding optionals.

### Argument Choices
```
loaded = IsAddOnLoaded(index) or IsAddOnLoaded("name")
```

### Protected Functions
Marked as protected — can only be called by secure/Blizzard code.

### Hardware Event Functions
Some functions require a hardware event (user click/keypress) in the call stack.

## Notable API Functions (Sampled)

### Unit Functions
- `UnitHealth(unit)` / `UnitHealthMax(unit)` — health values
- `UnitMana(unit)` / `UnitManaMax(unit)` — power values
- `UnitLevel(unit)` — level
- `UnitClass(unit)` — class name and file
- `UnitExists(unit)` — whether unit exists
- `UnitIsDeadOrGhost(unit)` — alive/dead state
- `UnitBuff(unit, index)` / `UnitDebuff(unit, index)` — auras

### Item Functions
- `GetItemInfo(itemID)` — name, link, quality, iLevel, etc.
- `GetContainerNumSlots(bag)` — slots in bag
- `GetContainerItemLink(bag, slot)` — item link
- `GetContainerItemInfo(bag, slot)` — icon, count, locked, etc.

### Spell Functions
- `GetSpellInfo(spellID)` — name, rank, icon, castTime, etc.
- `GetSpellCooldown(spellID)` — start, duration, enabled

### Action Functions (Protected)
- `CastSpellByName("spell" [, target])` — PROTECTED
- `TargetUnit(unit)` — PROTECTED
- `UseAction(slot)` — PROTECTED

### Utility Functions
- `GetTime()` — high-precision game time
- `GetCursorPosition()` — cursor location
- `GetLocale()` — client language
- `IsInInstance()` — instance type
- `InCombatLockdown()` — whether in combat

## Connections

- Organized by category in [Ch 28](ch28-api-categories.md)
- See [WoW API](../concepts/wow-api.md) concept page
- API functions introduced in [Ch 11](ch11-wow-api.md)
- Widget methods in [Ch 29](ch29-widget-reference.md)
- See [reference/api-categories](../reference/api-categories.md) for navigable summary
