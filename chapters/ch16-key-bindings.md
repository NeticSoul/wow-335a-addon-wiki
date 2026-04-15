---
title: "Ch 16 — Binding Keys and Clicks to Addon Code"
type: chapter
source_chapters: [16]
part: "III — Advanced Addon Techniques"
pages: 28
chars: 40750
created: 2026-04-15
updated: 2026-04-15
tags: [keybindings, input, Bindings.xml, mouse, keyboard]
---

# Ch 16 — Binding Keys and Clicks to Addon Code

## Overview

Covers the key binding system — how to bind keyboard and mouse input to addon commands. Keys and commands have a many-to-one relationship (many keys → one command).

## Key Concepts

### Key Names
- Letters/numbers: the key with Caps Lock on (e.g., `A`, `1`, `SPACE`)
- Special keys: `LEFT`, `RIGHT`, `NUMPAD3`, `F1`, `TAB`, `ESCAPE`
- Mouse buttons: `BUTTON1` (left), `BUTTON2` (right), `BUTTON3`–`BUTTON5`
- Modifiers: `SHIFT-`, `CTRL-`, `ALT-` prefixed to key name

### Bindings.xml
Addon-specific key bindings defined in `Bindings.xml` at the addon root:

```xml
<Bindings>
    <Binding name="MYADDON_TOGGLE" header="MYADDON_HEADER">
        MyAddon_Toggle()
    </Binding>
</Bindings>
```

- `name` attribute = command identifier
- Display label from global `BINDING_NAME_<name>` string
- Code inside tag executes when bound key is pressed
- `header` creates a section header in the bindings UI

### API Functions

| Function | Purpose |
|----------|---------|
| `SetBinding("KEY", "COMMAND")` | Bind a key to a command |
| `SetBindingClick("KEY", "ButtonName")` | Bind key to button click |
| `SetBindingSpell("KEY", "Spell")` | Bind key to cast spell |
| `SetBindingItem("KEY", "Item")` | Bind key to use item |
| `SetBindingMacro("KEY", "MacroIdx")` | Bind key to macro |
| `GetBindingKey("COMMAND")` | Get keys bound to command |
| `GetBindingAction("KEY")` | Get command bound to key |
| `SaveBindings(type)` | Save (1=account, 2=character) |
| `LoadBindings(type)` | Reload bindings |

### Override Bindings
Frame-specific bindings that take priority over global ones:
```lua
SetOverrideBindingClick(frame, isPriority, "KEY", "ButtonName")
ClearOverrideBindings(frame)
```
- `isPriority` = true means it overrides even other override bindings
- Override bindings are cleared when the owning frame is hidden

### Binding Stacking
Bindings have a priority order:
1. Override bindings (highest)
2. Custom key bindings
3. Default key bindings (lowest)

### Click Bindings
`SetBindingClick()` simulates clicking a named button frame when the key is pressed. This is the bridge between key input and the secure template system.

## Connections

- See [Key Bindings](../concepts/key-bindings.md) concept page
- Works with [Secure Templates](../concepts/secure-execution.md) for protected actions
- Override bindings used in [Ch 25](ch25-protected-action-in-combat.md) for combat
- Mouse/keyboard input first covered in [Ch 12](ch12-interacting-with-widgets.md)
