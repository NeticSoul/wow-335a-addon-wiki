---
title: Frame Templates
type: concept
source_chapters: [10, 15, 26]
created: 2026-04-15
updated: 2026-04-17
tags: [templates, virtual, inheritance, parentKey, FontDefinition]
---

# Frame Templates

## Summary

A **frame template** is a virtual (never-instantiated) XML definition whose attributes, elements, scripts, and sub-widgets are copied into every widget that inherits from it. Templates are the UI system's mechanism for reuse: they let you write one definition and spawn many identical widgets (`CreateFrame("Button", name, parent, "MyTemplate")`).

Templates are also the **only way to mark a frame as protected** (secure) — every `Secure*Template` in the default UI is a template, and inheriting from it is what opts a frame into the secure execution system.

Templates apply to: Frames, Buttons (any Frame subtype), FontStrings, Textures, Animations, and AnimationGroups. **Font definitions** (`<Font name="..." virtual="true">`) look like templates but work differently — they're live objects that can be modified at runtime, with changes propagating to every inheriting font string.

## Detailed Explanation

### Declaring a Template

```xml
<Button name="MyButtonTemplate" virtual="true">
    <Size x="16" y="16"/>
    <Layers>
        <Layer level="BACKGROUND">
            <Texture name="$parentIcon" parentKey="icon">
                <Color r="1.0" g="1.0" b="1.0"/>
            </Texture>
        </Layer>
    </Layers>
</Button>
```

Requirements:

- **`virtual="true"`** — marks the element as a template. The engine never instantiates it directly.
- **`name`** — required and **global**. Collisions are silent; prefix with your addon name (`BagBuddyItemTemplate`, not `ItemTemplate`).
- Declaration must come **before** any use. In a single XML file, place templates near the top; across files, rely on TOC load order.

### Inheriting (XML)

```xml
<Button name="Btn1" inherits="MyButtonTemplate"/>
<Button name="Btn2" inherits="MyButtonTemplate">
    <Anchors>
        <Anchor point="TOPLEFT" relativePoint="TOPRIGHT" relativeTo="Btn1"/>
    </Anchors>
</Button>
```

Inheritance semantics:

- All attributes, child elements (`<Size>`, `<Anchors>`, `<Layers>`, `<Scripts>`, `<Frames>`, etc.), and sub-widgets from the template are copied.
- Child elements declared on the inheriting frame **override** the corresponding elements from the template (element-wise replacement, not merge).
- Multiple inheritance: `inherits="A, B, C"` — processed left-to-right; later templates override earlier ones.

### Inheriting (Lua)

```lua
local f = CreateFrame("Button", "Btn1", UIParent, "MyButtonTemplate")
```

Multiple templates:

```lua
CreateFrame("Button", name, parent, "BagBuddyItemTemplate, SecureActionButtonTemplate")
```

Most commonly used to compose a *visual* template with a *secure* template.

### `parentKey` — The Modern Access Pattern

Historically, sub-widgets inside a template were accessed via global name reconstruction:

```lua
_G[self:GetName() .. "Icon"]:SetTexture(1, 0, 0)
```

Patches ≥ 3.0 added `parentKey`, which stores the sub-widget as a direct table key on the parent frame:

```xml
<Texture name="$parentIcon" parentKey="icon">
```

```lua
self.icon:SetTexture(1, 0, 0)
```

Advantages:

- Faster — no `_G` lookup, no string concatenation.
- Doesn't require the instance to be named.
- Works cleanly under template inheritance; each instance gets its own `icon` key.
- Readable — subject of the method call is obvious.

Still name frames for `/framestack` debugging and for older templates that rely on `$parent` naming (e.g. dropdown menus).

### `$parent` Expansion

Inside template XML, the literal string `$parent` is replaced at instantiation time with the name of the **inheriting** frame (not the template's name). So:

```xml
<Texture name="$parentIcon">
```

in template `MyButtonTemplate`, when instantiated as `Btn1`, produces global `Btn1Icon`.

`$parent` expansion applies to the `name` attribute only. To nest: `$parentSomething_Icon` expands level-by-level.

### Template Scripts

A template can declare script handlers in `<Scripts>`:

```xml
<Button name="MyBtnTpl" virtual="true">
    <Scripts>
        <OnLoad>
            self:RegisterForClicks("AnyUp")
        </OnLoad>
        <OnClick>
            print("clicked", self:GetName())
        </OnClick>
    </Scripts>
</Button>
```

Inheriting frames can **replace** or **augment** these:

- Redeclaring the same handler in the inheriting frame **replaces** the template's handler.
- Use `<OnLoad inherit="prepend">` or `<OnLoad inherit="append">` to chain:

```xml
<Button name="Btn" inherits="MyBtnTpl">
    <Scripts>
        <OnLoad inherit="append">
            -- Runs AFTER the template's OnLoad
            self:SetText("Hi")
        </OnLoad>
    </Scripts>
</Button>
```

### Font Definitions (Fonts are Not Quite Templates)

A `<Font>` element with `virtual="true"` creates a **font definition object** — a live in-game object that font strings can inherit from:

```xml
<Font name="SystemFont_Shadow_Small" font="Fonts\FRIZQT__.TTF" virtual="true">
    <Shadow>
        <Offset><AbsDimension x="1" y="-1"/></Offset>
        <Color r="0" g="0" b="0"/>
    </Shadow>
    <FontHeight><AbsValue val="10"/></FontHeight>
</Font>

<Font name="GameFontNormalSmall" inherits="SystemFont_Shadow_Small" virtual="true">
    <Color r="1.0" g="0.82" b="0"/>
</Font>
```

Unlike frame templates, font definitions can be **mutated at runtime**; changes propagate live to every inheriting font string:

```lua
-- Change the base font size at runtime; every GameFontNormalSmall string updates:
SystemFont_Shadow_Small:SetFont("Fonts\\FRIZQT__.TTF", 13)
```

Font definitions appear throughout the default UI (`Fonts.xml`, `FontStyles.xml`). Use `inherits="GameFontNormal"` etc. rather than defining your own unless you need addon-specific styling.

### Blizzard Templates (Selection)

From `UIPanelTemplates.xml`, `SecureTemplates.xml`, `SecureHandlerTemplates.xml`, and FrameXML throughout:

| Template | Purpose |
|----------|---------|
| `UIPanelButtonTemplate` / `UIPanelButtonTemplate2` | Standard gold-text buttons (no size). |
| `GameMenuButtonTemplate` | Main-menu button. |
| `UIPanelCloseButton` | Standard corner "X" close button. |
| `UIPanelScrollBarTemplate` / `UIPanelScrollBarTemplateLight` | Scrollbars. |
| `FauxScrollFrameTemplate` | Virtual scroll (no real scroll child; driver code). |
| `ScrollingMessageFrameTemplate` | Log-like chat frame. |
| `UIDropDownMenuTemplate` | Dropdown menu container. |
| `UICheckButtonTemplate` | Checkbox with label. |
| `OptionsSmallCheckButtonTemplate` | Compact checkbox. |
| `InputBoxTemplate` / `SearchBoxTemplate` | Styled edit boxes. |
| `GameTooltipTemplate` | For creating scanner tooltips. |
| `ItemButtonTemplate` | Icon button with count + rarity border. |
| `SecureActionButtonTemplate` | Protected clickable for spells/items/macros. |
| `SecureUnitButtonTemplate` | Protected unit target/menu button. |
| `SecureHandlerBaseTemplate` | Base secure-snippet runner. |
| `SecureHandlerClickTemplate` | `_onclick` snippet support. |
| `SecureHandlerShowHideTemplate` | `_onshow`/`_onhide` snippets. |
| `SecureHandlerEnterLeaveTemplate` | Hover-driven secure logic. |
| `SecureHandlerAttributeTemplate` | `_onattributechanged` snippet. |
| `SecureHandlerStateTemplate` | State-driven snippet execution. |
| `SecureHandlerDragTemplate` | Secure drag-and-drop support. |
| `SecureHandlerMouseUpDownTemplate` | Secure mouse up/down. |
| `SecureFrameTemplate` | Blank protected frame. |
| `SecureGroupHeaderTemplate` | Raid/party unit-frame manager. |
| `SecureGroupPetHeaderTemplate` | Party/raid pet variant. |
| `SecureAuraHeaderTemplate` | Auto-laying-out aura buttons. |

The secure family is covered in [secure-execution](secure-execution.md). The group-header templates drive unit frames — see [Ch 26](../chapters/ch26-unit-frames-group-templates.md).

### Copying vs. Inheriting Blizzard Templates

Inheriting directly from a Blizzard template (`inherits="ItemButtonTemplate"`) couples you to Blizzard's internals. If they change it, your addon breaks. For visual templates that aren't *functionally* important (no secure behavior, no driver code), one common addon-author practice is to **copy the template definition** from the extracted FrameXML and rename it (`BagBuddyItemTemplate`). This insulates your addon from Blizzard patches.

For secure templates (`Secure*Template`, `SecureHandler*Template`) — inherit directly. Copies will not be marked protected.

## Examples

### Button template with icon + count + rarity glow (Ch 10)

```xml
<Button name="BagBuddyItemTemplate" virtual="true">
    <Size><AbsDimension x="37" y="37"/></Size>
    <Layers>
        <Layer level="BORDER">
            <Texture name="$parentIconTexture" parentKey="icon"/>
            <FontString name="$parentCount" parentKey="count"
                        inherits="NumberFontNormal"
                        justifyH="RIGHT" hidden="true">
                <Anchors>
                    <Anchor point="BOTTOMRIGHT">
                        <Offset><AbsDimension x="-5" y="2"/></Offset>
                    </Anchor>
                </Anchors>
            </FontString>
        </Layer>
        <Layer level="OVERLAY">
            <Texture name="$parentGlow" parentKey="glow" alphaMode="ADD"
                     file="Interface\Buttons\UI-ActionButton-Border">
                <Size x="70" y="70"/>
                <Anchors><Anchor point="CENTER"/></Anchors>
                <Color r="1.0" g="1.0" b="1.0" a="0.6"/>
            </Texture>
        </Layer>
    </Layers>
    <NormalTexture name="$parentNormalTexture"
                   file="Interface\Buttons\UI-Quickslot2">
        <Size><AbsDimension x="64" y="64"/></Size>
        <Anchors>
            <Anchor point="CENTER"><Offset x="0" y="-1"/></Anchor>
        </Anchors>
    </NormalTexture>
    <PushedTexture file="Interface\Buttons\UI-Quickslot-Depress"/>
    <HighlightTexture file="Interface\Buttons\ButtonHilight-Square"
                      alphaMode="ADD"/>
</Button>
```

Instantiated in Lua with anchor grid:

```lua
self.items = {}
for idx = 1, 24 do
    local item = CreateFrame("Button", "BagBuddy_Item" .. idx, self,
                             "BagBuddyItemTemplate")
    self.items[idx] = item
    if idx == 1 then
        item:SetPoint("TOPLEFT", 40, -73)
    elseif idx == 7 or idx == 13 or idx == 19 then
        item:SetPoint("TOPLEFT", self.items[idx-6], "BOTTOMLEFT", 0, -7)
    else
        item:SetPoint("TOPLEFT", self.items[idx-1], "TOPRIGHT", 12, 0)
    end

    item.icon:SetTexture("Interface\\Icons\\INV_Misc_Gear_01")
end
```

### Secure action button (Ch 15)

```xml
<Button name="QuickCastHeal" parent="UIParent"
        inherits="SecureActionButtonTemplate">
    <Size x="40" y="40"/>
    <Anchors><Anchor point="CENTER"/></Anchors>
    <Attributes>
        <Attribute name="type"  value="spell"/>
        <Attribute name="spell" value="Flash Heal"/>
        <Attribute name="unit"  value="player"/>
    </Attributes>
</Button>
```

### Composing a visual template with a secure template

```lua
local btn = CreateFrame(
    "CheckButton", "MyHealBtn", UIParent,
    "UICheckButtonTemplate, SecureActionButtonTemplate"
)
btn:SetAttribute("type", "spell")
btn:SetAttribute("spell", "Flash Heal")
```

Order matters when the two templates define the same attribute — rightmost wins.

## Common Bugs

- **Forgetting `virtual="true"`** — the template is treated as an actual frame and instantiated once, producing an invisible ghost frame and often breaking anchor references.
- **Template name collision** — two addons both define `ItemTemplate`. Prefix with addon name.
- **Template referenced before declared** — for XML inheritance within a single file, templates must appear above their users. For cross-file use, the containing TOC must list the template's file first.
- **Using `$parent` in a frame with no name** — expands to empty string; resulting globals collide.
- **Inheriting a Blizzard template then trying to change its `parentKey` subwidgets' structure** — template sub-elements are replaced wholesale, not merged; structural changes require copy-and-rename.
- **Expecting font-definition mutations to affect frame templates** — only font strings/definitions propagate; frame templates are resolved at instantiation only.
- **Secure template inherited after a non-secure one that redefines protected attributes** — the non-secure template's `<Attribute>` declarations may shadow the secure setup; keep `Secure*Template` last in `inherits=""`.
- **Sub-widgets not accessible** — you set `parentKey` on a template but the sub-widget was inside a layer declared *inside* another layer that got overridden. Flatten the hierarchy or re-declare.

## Related Concepts

- [frames-and-widgets](frames-and-widgets.md) — the widget types templates apply to
- [xml-layout](xml-layout.md) — XML schema for `<Frames>`, `<Layers>`, etc.
- [secure-execution](secure-execution.md) — why `Secure*Template` must be inherited, not copied
- [Ch 10 — Frame Templates](../chapters/ch10-frame-templates.md)
- [Ch 15 — Secure Templates](../chapters/ch15-secure-templates.md)
- [Ch 26 — Unit Frames with Group Templates](../chapters/ch26-unit-frames-group-templates.md)
