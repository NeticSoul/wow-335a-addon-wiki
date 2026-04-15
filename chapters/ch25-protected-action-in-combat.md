---
title: "Ch 25 — Taking Protected Action in Combat"
type: chapter
source_chapters: [25]
part: "III — Advanced Addon Techniques"
pages: 38
chars: 77787
created: 2026-04-15
updated: 2026-04-15
tags: [secure-handlers, snippets, restricted-environment, combat, state-driver]
---

# Ch 25 — Taking Protected Action in Combat

## Overview

Extends Ch 15 with the **snippets** and **secure handler** system introduced before WotLK. Allows addons to execute limited secure code during combat through carefully controlled mechanisms.

## Key Concepts

### The Problem
Ch 15 showed that addons can't modify protected frames during combat. But legitimate needs exist: hiding target frame on deselect, updating unit frames, etc. Snippets solve this.

### Snippets
- Strings containing Lua code, stored in frame **attributes**
- Compiled by secure code into executable functions
- Run in a **restricted environment** with limited access
- Convention: use `[[long brackets]]` for snippet strings

```lua
SecureHandlerExecute(PlayerFrame, [[self:Hide()]])
```

### Secure Handler Templates

| Template | Trigger |
|----------|---------|
| `SecureHandlerClickTemplate` | `OnClick` → runs `_onclick` attribute |
| `SecureHandlerShowHideTemplate` | `OnShow`/`OnHide` → runs `_onshow`/`_onhide` |
| `SecureHandlerStateTemplate` | State change → runs `_onstate-*` |
| `SecureHandlerEnterLeaveTemplate` | Mouse enter/leave |
| `SecureHandlerBaseTemplate` | Base (for composition) |

### Restricted Environment
Snippets can only access a limited set of functions and variables:
- Frame references via `self`, `owner`
- Attribute reading/writing: `self:GetAttribute()`, `self:SetAttribute()`
- Child frame access: `self:GetFrameRef("name")`
- Control: `self:Show()`, `self:Hide()`, `self:SetAlpha()`
- Table operations, math, string operations
- **Cannot**: call C API functions, access globals, create frames

### Frame References
Share frame references between secure and insecure code:
```lua
-- In insecure code (setup):
SecureHandlerSetFrameRef(frame, "target", otherFrame)
-- In snippet:
local target = self:GetFrameRef("target")
target:Show()
```

### State Drivers
Register frames to respond to game state changes:
```lua
RegisterStateDriver(frame, "visibility", "[combat] show; hide")
RegisterStateDriver(frame, "state-combat", "[combat] in; out")
```

State conditions: `[combat]`, `[nocombat]`, `[group:party]`, `[group:raid]`, `[modifier:shift]`, `[target=focus,exists]`, etc.

### Wrap Scripts
Attach snippets that run before/after existing script handlers:
```lua
SecureHandlerWrapScript(button, "OnClick", frame, preBody, postBody)
```

### Unit Watch System
Automatically show/hide frames based on unit existence:
```lua
RegisterUnitWatch(frame)  -- shows frame when frame.unit exists
UnregisterUnitWatch(frame)
```

## Connections

- See [Secure Execution](../concepts/secure-execution.md) concept page
- Extends [Ch 15](ch15-secure-templates.md) secure template foundations
- State drivers used in [Ch 26](ch26-unit-frames-group-templates.md) unit frames
- Key for combat-active addons (action bars, unit frames, raid frames)
