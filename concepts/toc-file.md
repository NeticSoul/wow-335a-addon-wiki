---
title: TOC File
type: concept
source_chapters: [8]
created: 2026-04-15
updated: 2026-04-15
tags: [toc, metadata, directives, addon]
---

# TOC File

The Table of Contents file (`MyAddon.toc`) must match the addon directory name. It declares metadata and lists files to load.

## Directives

| Directive | Required | Purpose |
|-----------|----------|---------|
| `## Interface:` | Yes | API version (e.g., `30300` for 3.3.0) |
| `## Title:` | Recommended | Display name in addon list |
| `## Notes:` | Optional | Description tooltip |
| `## Author:` | Optional | Author name |
| `## Dependencies:` | Optional | Required addons |
| `## OptionalDeps:` | Optional | Optional addons (load order) |
| `## SavedVariables:` | Optional | Global persistent vars |
| `## SavedVariablesPerCharacter:` | Optional | Per-char persistent vars |
| `## LoadOnDemand:` | Optional | `1` = defer loading |
| `## LoadsWith:` | Optional | Load alongside another addon |
| `## LoadManager:` | Optional | External load manager |
| `## DefaultState:` | Optional | `enabled` or `disabled` |
| `## X-*:` | Optional | Custom metadata |

## Localization
Append locale code: `## Title-esES:`, `## Notes-deDE:`, `## X-Website-frFR:`

## Interface Version
Built from client version: 3.3.0 → `30300`. Mismatched = "Out of date" (loadable) or "Incompatible" (blocked).

## Example
```
## Interface: 30300
## Title: BagBuddy
## Notes: Filter your inventory by name or rarity
## Author: Iriel, Cladhaire
## SavedVariables: BagBuddyDB
## OptionalDeps: LibStub, Ace3
BagBuddy.xml
BagBuddy.lua
Locales\enUS.lua
Locales\esES.lua
```

## See Also
- [Ch 8 — Anatomy of an Addon](../chapters/ch08-anatomy-of-addon.md)
- [Addon Structure](addon-structure.md)
- [Saved Variables](saved-variables.md)
