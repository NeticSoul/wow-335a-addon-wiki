---
title: XML Layout
type: concept
source_chapters: [7, 9, 10]
created: 2026-04-15
updated: 2026-04-15
tags: [xml, layout, ui, schema]
---

# XML Layout

WoW uses XML to define the **structure and layout** of UI elements. All visual behavior is in Lua; XML handles positioning, sizing, hierarchy, and template definitions.

## Structure
Root element: `<Ui>` with Blizzard namespace. Contains frame definitions.

```xml
<Ui xmlns="http://www.blizzard.com/wow/ui/" ...>
    <Frame name="MyFrame" parent="UIParent">
        <Size x="200" y="100"/>
        <Anchors><Anchor point="CENTER"/></Anchors>
        <Layers>
            <Layer level="ARTWORK">
                <Texture name="$parentBg" parentKey="bg"/>
                <FontString name="$parentTitle" parentKey="title"/>
            </Layer>
        </Layers>
        <Scripts>
            <OnLoad>MyFrame_OnLoad(self)</OnLoad>
        </Scripts>
    </Frame>
</Ui>
```

## Key Elements

| Element | Purpose |
|---------|---------|
| `<Frame>` | Base container |
| `<Button>` | Clickable frame |
| `<Texture>` | Image/color (in layers) |
| `<FontString>` | Text (in layers) |
| `<Anchors>` | Positioning |
| `<Size>` | Dimensions |
| `<Layers>` | Draw layer container |
| `<Scripts>` | Lua handler container |
| `<Backdrop>` | Background/border art |

## Special Tokens
- `$parent` in names → expands to parent frame's name
- `parentKey` → attaches child as `parent.key` in Lua

## Templates
Frames with `virtual="true"` are templates — never rendered, used as blueprints via `inherits="TemplateName"`.

## XML vs Lua
Everything in XML can be done in Lua with `CreateFrame()`. XML advantages: schema validation, clean separation of layout and logic. Lua advantage: dynamic creation at runtime.

## See Also
- [Ch 7 — Learning XML](../chapters/ch07-learning-xml.md)
- [Ch 9 — Frames and Widgets](../chapters/ch09-frames-and-widgets.md)
- [Ch 10 — Frame Templates](../chapters/ch10-frame-templates.md)
- [Frames and Widgets](frames-and-widgets.md)
- [Frame Templates](frame-templates.md)
