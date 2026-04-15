---
title: WoW API
type: concept
source_chapters: [11, 27, 28]
created: 2026-04-15
updated: 2026-04-15
tags: [api, functions, c-functions, framexml, protected]
---

# WoW API

The WoW API provides **1000+ functions** for querying game state and taking action.

## Function Types

| Type | Origin | Example |
|------|--------|---------|
| C functions | Game engine | `UnitHealth()` |
| FrameXML functions | Blizzard Lua | `ChatFrame_AddMessage()` |
| Protected functions | Engine (restricted) | `CastSpellByName()` |

## Naming Conventions
- **CamelCase** = C functions: `GetSpellInfo`, `UnitHealth`
- **CamelCase with underscore** = FrameXML: `ChatFrame_AddMessage`
- **All lowercase** = utility: `hooksecurefunc`, `debugstack`, `securecall`

## Common Patterns
- Many functions use [Unit IDs](unit-ids.md): `UnitHealth("player")`
- Multiple returns: `name, rank, icon = GetSpellInfo(spellID)`
- Optional args in brackets: `BuyMerchantItem(index [, quantity])`

## Key Function Groups

| Group | Examples |
|-------|---------|
| Unit | `UnitHealth`, `UnitClass`, `UnitLevel`, `UnitExists` |
| Item | `GetItemInfo`, `GetContainerItemLink` |
| Spell | `GetSpellInfo`, `GetSpellCooldown` |
| Quest | `GetQuestLogTitle`, `GetNumQuestLogEntries` |
| Chat | `SendChatMessage` |
| Auction | `CalculateAuctionDeposit` |
| Settings | `GetCVar`, `SetCVar` |
| Utility | `GetTime`, `GetLocale`, `InCombatLockdown` |

## Protected API
Protected functions can only be called by Blizzard's secure code: `CastSpellByName()`, `TargetUnit()`, `UseAction()`. Addons work around this via [Secure Templates](secure-execution.md).

## Hardware API
Some functions require a hardware event in the call stack (user physically clicked/pressed a key).

## See Also
- [Ch 11](../chapters/ch11-wow-api.md), [Ch 27](../chapters/ch27-api-reference.md), [Ch 28](../chapters/ch28-api-categories.md)
- [Unit IDs](unit-ids.md)
- [Secure Execution](secure-execution.md)
