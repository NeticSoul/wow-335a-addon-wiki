---
title: Key Bindings
type: concept
source_chapters: [16]
created: 2026-04-15
updated: 2026-04-15
tags: [keybindings, input, keyboard, mouse]
---

# Key Bindings

System for binding keyboard/mouse input to addon commands.

## Bindings.xml
```xml
<Bindings>
    <Binding name="MYADDON_TOGGLE" header="MYADDON">
        MyAddon_Toggle()
    </Binding>
</Bindings>
```
Label from global: `BINDING_NAME_MYADDON_TOGGLE = "Toggle MyAddon"`

## API
- `SetBinding("KEY", "COMMAND")` — bind key
- `SetBindingClick("KEY", "ButtonName")` — key simulates button click
- `SetOverrideBindingClick(frame, priority, "KEY", "ButtonName")` — frame-scoped override
- `ClearOverrideBindings(frame)` — remove overrides
- `SaveBindings(1)` — save account-wide / `SaveBindings(2)` — per-character

## Priority Order
1. Override bindings (highest)
2. Custom bindings
3. Default bindings (lowest)

## See Also
- [Ch 16](../chapters/ch16-key-bindings.md), [Secure Execution](secure-execution.md)
