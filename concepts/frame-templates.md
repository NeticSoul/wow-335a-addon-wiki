---
title: Frame Templates
type: concept
source_chapters: [10, 15, 26]
created: 2026-04-15
updated: 2026-04-15
tags: [templates, virtual, inheritance, reuse]
---

# Frame Templates

Templates let you define a frame once and inherit from it multiple times.

## Creating a Template
```xml
<Button name="MyTemplate" virtual="true">
    <Size x="32" y="32"/>
    <Layers>
        <Layer level="ARTWORK">
            <Texture parentKey="icon"/>
        </Layer>
    </Layers>
</Button>
```

## Using a Template
**XML**: `<Button name="Btn1" inherits="MyTemplate"/>`
**Lua**: `CreateFrame("Button", "Btn1", UIParent, "MyTemplate")`

## Multiple Inheritance
`inherits="Template1, Template2"` — later templates override conflicts.

## Blizzard Templates

| Template | Purpose |
|----------|---------|
| `UIPanelButtonTemplate` | Standard button |
| `UIPanelCloseButton` | Close (X) button |
| `GameMenuButtonTemplate` | Game menu button |
| `UIDropDownMenuTemplate` | Dropdown menu |
| `GameTooltipTemplate` | Tooltip |
| `SecureActionButtonTemplate` | Protected action button |
| `SecureUnitButtonTemplate` | Unit target/menu button |
| `SecureGroupHeaderTemplate` | Raid/party frame manager |
| `SecureHandlerClickTemplate` | Secure click handler |
| `UIPanelScrollBarTemplate` | Standard scrollbar |
| `FauxScrollFrameTemplate` | Virtual scroll frame |

## See Also
- [Ch 10](../chapters/ch10-frame-templates.md)
- [Frames and Widgets](frames-and-widgets.md)
- [Secure Execution](secure-execution.md)
