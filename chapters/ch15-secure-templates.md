---
title: "Ch 15 — Taking Action with Secure Templates"
type: chapter
source_chapters: [15]
part: "III — Advanced Addon Techniques"
pages: 24
chars: 51662
created: 2026-04-15
updated: 2026-04-15
tags: [security, secure-templates, taint, protected, combat, SecureActionButton]
---

# Ch 15 — Taking Action with Secure Templates

## Overview

Explains the security model that prevents addons from automating gameplay. Introduces the taint/secure code system and the secure templates that let addons interact with protected actions in a controlled way.

## Key Concepts

### The Security Problem
Early addons automated gameplay: auto-healing, auto-movement, auto-targeting. Blizzard's response: the **patch 2.0 security model**.

### Secure vs Tainted Code
- **Secure code** = Blizzard's code (loaded from FrameXML)
- **Tainted code** = addon code (untrusted)
- Secure code can call protected functions (CastSpell, Target, etc.)
- Tainted code **cannot** call protected functions
- If tainted code interferes with secure code, taint **spreads**

### Protected Frames
- Can take restricted actions (cast spells, target, etc.)
- During combat: only secure code can create, modify, or control them
- Outside combat: addon code can freely create and configure them

### SecureActionButtonTemplate
The primary secure template — a button that can perform protected actions based on **attributes**:

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `type` | Action type | `"spell"`, `"item"`, `"macro"` |
| `spell` | Spell to cast | `"Heal"` |
| `item` | Item to use | `"Hearthstone"` |
| `unit` | Target unit | `"target"` |
| `macro` | Macro text | `"/cast Heal"` |
| `macrotext` | Macro body | `"/cast Heal\n/say Healing!"` |

```lua
local btn = CreateFrame("Button", "MySecureButton", UIParent, "SecureActionButtonTemplate")
btn:SetAttribute("type", "spell")
btn:SetAttribute("spell", "Heal")
btn:SetAttribute("unit", "target")
```

### Attribute Modifiers
Attributes can change based on modifier keys and mouse buttons:
- `type1` = left click, `type2` = right click
- `shift-type1` = shift+left, `ctrl-type2` = ctrl+right
- Chains: `*type1` = any click, `shift-*type1` = shift+any

### SecureUnitButtonTemplate
For frames that represent a unit — enables target on click, show menu on right-click:
```lua
btn:SetAttribute("type1", "target")
btn:SetAttribute("unit", "player")
btn:SetAttribute("type2", "menu")
btn:SetAttribute("menu", function() ... end)
```

### What Can Go Wrong: Taint
- Reading a tainted global variable can taint the reader
- `_G["variable"]` access can spread taint
- Use `issecurevariable()` to check
- Errors like "Action blocked by an addon" indicate taint issues

### PLAYER_REGEN Events
- `PLAYER_REGEN_DISABLED` — last chance to configure secure frames before combat lockdown
- `PLAYER_REGEN_ENABLED` — combat over, can reconfigure secure frames

## Connections

- See [Secure Execution](../concepts/secure-execution.md) concept page
- Extended in [Ch 25](ch25-protected-action-in-combat.md) with secure handlers and snippets
- `PLAYER_REGEN_*` events from [Ch 13](ch13-game-events.md) and [Ch 14](ch14-combat-tracker.md)
- Key bindings in [Ch 16](ch16-key-bindings.md) also interact with secure system
- Unit frames in [Ch 26](ch26-unit-frames-group-templates.md) use SecureUnitButtonTemplate
