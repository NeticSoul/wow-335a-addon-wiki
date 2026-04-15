---
title: "Ch 7 — Learning XML"
type: chapter
source_chapters: [7]
part: "I — Learning to Program"
pages: 14
chars: 17414
created: 2026-04-15
updated: 2026-04-15
tags: [xml, layout, schema, ui]
---

# Ch 7 — Learning XML

## Overview

Introduces XML (eXtensible Markup Language) as used in WoW for defining UI frames and layouts. XML in WoW is purely structural — no presentation cues. All visual behavior is driven by Lua scripts.

## Key Concepts

### XML Basics
- **Tags**: case-sensitive identifiers in angle brackets (`<tag>`, `</tag>`)
- **Elements**: content enclosed between open/close tags, or self-closing (`<tag/>`)
- **Attributes**: named values in the start tag (`<tag attr="value">`)
- **Entities**: special character references (`&amp;`, `&lt;`, `&gt;`, `&apos;`, `&quot;`)
- **Comments**: `<!-- comment -->`

### XML vs HTML
- HTML describes **presentation** with some structure
- XML describes **structure** only, presentation determined by consuming application
- XML is stricter: proper nesting required, attributes must be quoted

### WoW XML Structure
Root element is always `<Ui>` with Blizzard namespace:
```xml
<Ui xmlns="http://www.blizzard.com/wow/ui/"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.blizzard.com/wow/ui/
    http://wowprogramming.com/FrameXML/UI.xsd">
    <!-- Frame definitions here -->
</Ui>
```

### Schema Validation
- WoW XML has an XSD schema for validation
- Can be validated using tools like `xmllint` or VS Code XML extensions
- Schema defines valid elements, attributes, and their relationships

### User Interface Customization Tool
- Extracts Blizzard's FrameXML source files
- Command: run from WoW directory, extracts to `BlizzardInterfaceArt/` and `BlizzardInterfaceCode/`
- Essential for learning from Blizzard's own UI code

### Key XML Elements (Preview)

| Element | Purpose |
|---------|---------|
| `<Frame>` | Base container widget |
| `<Button>` | Clickable frame |
| `<Texture>` | Image/color display |
| `<FontString>` | Text display |
| `<Scripts>` | Container for Lua script handlers |
| `<Anchors>` | Positioning system |
| `<Size>` | Dimensions |

## Connections

- XML syntax is the foundation for [Frames and Widgets](../concepts/frames-and-widgets.md) in Ch 9
- `<Scripts>` element connects XML layout to Lua behavior
- [Frame Templates](../concepts/frame-templates.md) in Ch 10 build on XML inheritance
- See [XML Layout](../concepts/xml-layout.md) for the complete concept page
