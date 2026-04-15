---
title: "Ch 10 — Saving Time with Frame Templates"
type: chapter
source_chapters: [10]
part: "II — Programming in WoW"
pages: 16
chars: 24760
created: 2026-04-15
updated: 2026-04-15
tags: [templates, xml, inheritance, virtual, bagbuddy]
---

# Ch 10 — Saving Time with Frame Templates

## Overview

Introduces the template system for reusing frame definitions. Templates let you define a frame once and inherit from it multiple times, saving code and ensuring consistency. Also covers Blizzard's built-in templates.

## Key Concepts

### Creating Templates
A template is a frame marked `virtual="true"` — it's never rendered, just used as a blueprint:

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

### Inheriting from Templates
```xml
<Button name="Button1" inherits="MyButtonTemplate"/>
<Button name="Button2" inherits="MyButtonTemplate">
    <Anchors>
        <Anchor point="TOPLEFT" relativePoint="TOPRIGHT" relativeTo="Button1"/>
    </Anchors>
</Button>
```

### Template-Capable Elements
- Frames (and all frame subtypes)
- Font strings
- Textures
- Animations and animation groups
- Font definitions

### Multiple Inheritance
Frames can inherit from **multiple** templates:
```xml
<Frame name="MyFrame" inherits="Template1, Template2"/>
```
Later templates override earlier ones when conflicts occur.

### Dynamic Frame Creation from Templates
```lua
local frame = CreateFrame("Button", "DynamicButton", UIParent, "MyButtonTemplate")
```
This creates a new button with all properties from the template.

### Blizzard's Built-in Templates
| Template | Purpose |
|----------|---------|
| `GameMenuButtonTemplate` | Standard game menu button |
| `UIPanelButtonTemplate` | Standard panel button |
| `UIPanelCloseButton` | X close button |
| `UIDropDownMenuTemplate` | Dropdown menu container |
| `SecureActionButtonTemplate` | Button that can cast spells/use items |
| `SecureUnitButtonTemplate` | Button that can target/menu units |

### The `$parent` Token
In template names, `$parent` is replaced by the parent frame's name at creation:
```xml
<Texture name="$parentIcon"/>
<!-- If parent is "MyButton", texture becomes "MyButtonIcon" -->
```

### The `parentKey` Attribute
Attaches the child as a key on its parent's Lua table:
```xml
<Texture parentKey="icon"/>
<!-- Accessible as parentFrame.icon in Lua -->
```

## Connections

- Builds on [Ch 9](ch09-frames-and-widgets.md) frame fundamentals
- See [Frame Templates](../concepts/frame-templates.md) concept page
- Blizzard templates used heavily in [Secure Templates](../concepts/secure-execution.md) (Ch 15)
- `CreateFrame()` with templates is the standard dynamic UI pattern
- BagBuddy uses templates for its item slot display
