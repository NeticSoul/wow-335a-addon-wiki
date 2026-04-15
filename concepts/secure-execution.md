---
title: Secure Execution
type: concept
source_chapters: [15, 25]
created: 2026-04-15
updated: 2026-04-15
tags: [security, taint, protected, secure-templates, snippets, combat]
---

# Secure Execution

WoW's security model prevents addons from automating gameplay. Protected actions (casting spells, targeting, using items) can only be triggered by secure code in response to user input.

## Core Concepts

| Concept | Description |
|---------|-------------|
| **Secure code** | Blizzard's FrameXML — trusted |
| **Tainted code** | Addon code — untrusted |
| **Protected functions** | `CastSpellByName`, `TargetUnit`, etc. — secure only |
| **Protected frames** | Can take restricted actions; locked during combat |
| **Taint propagation** | Tainted code touching secure code spreads taint |

## During Combat
- Cannot create protected frames
- Cannot show/hide/move protected frames
- Cannot change attributes on protected frames
- Can still read attributes and call non-protected API

## Outside Combat
- Full access to create, configure, and modify protected frames
- `PLAYER_REGEN_DISABLED` = last chance before lockdown
- `PLAYER_REGEN_ENABLED` = lockdown lifted

## Secure Templates (Ch 15)
Action buttons controlled by **attributes**, not code:
```lua
btn:SetAttribute("type", "spell")
btn:SetAttribute("spell", "Heal")
btn:SetAttribute("unit", "target")
```
Templates: `SecureActionButtonTemplate`, `SecureUnitButtonTemplate`

## Secure Handlers & Snippets (Ch 25)
Execute limited Lua during combat via snippets stored in attributes:
```lua
SecureHandlerExecute(frame, [[self:Hide()]])
```

Handler templates: `SecureHandlerClickTemplate`, `SecureHandlerShowHideTemplate`, `SecureHandlerStateTemplate`

### Restricted Environment
Snippets can only: read/write attributes, access frame refs, show/hide/set alpha, basic math/string.

### State Drivers
```lua
RegisterStateDriver(frame, "visibility", "[combat] show; hide")
```

### Unit Watch
```lua
RegisterUnitWatch(frame)  -- auto show/hide based on unit existence
```

## Detecting Taint
- `issecurevariable("varname")` — check if variable is tainted
- `/console taintLog 2` — enable taint logging
- Error: "Action blocked by an addon"

## See Also
- [Ch 15](../chapters/ch15-secure-templates.md), [Ch 25](../chapters/ch25-protected-action-in-combat.md)
- [Key Bindings](key-bindings.md) (interact with secure system)
- [Frames and Widgets](frames-and-widgets.md)
