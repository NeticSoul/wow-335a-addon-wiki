---
title: "Ch 34 — Appendix D: Addon Author Resources"
type: chapter
source_chapters: [34]
part: "V — Appendices"
pages: 53
chars: 143484
created: 2026-04-15
updated: 2026-04-15
tags: [resources, community, distribution, wowinterface, curse, wowace]
---

# Ch 34 — Appendix D: Addon Author Resources

## Overview

Comprehensive guide to addon community websites, forums, distribution platforms, and development tools.

## Community Websites

| Site | Focus |
|------|-------|
| **WoW Official Forums** | Blizzard-sponsored, patch change notes from devs |
| **WoWProgramming** | Book companion site, forums, API docs |
| **WoWInterface** | UI customization community, many dev forums |
| **WowAce** | Addon development discussion (Curse network) |
| **Curse** | Distribution + forums |
| **Elitist Jerks** | Active addon discussion, bleeding edge |
| **IncGamers** | UI customization forums |

## Distribution Platforms

| Platform | Features |
|----------|---------|
| **Curse / CurseForge** | Major distribution, SVN hosting, ticket system |
| **WoWInterface** | Long-running platform, author profiles, screenshots |

## Development Tools

| Tool | Purpose |
|------|---------|
| **WowLua** | In-game Lua interpreter |
| **BugSack + BugGrabber** | Error capture and display |
| **TextureBrowser** | Browse available texture paths |
| **FrameStack** | /fstack — identify frames under cursor |
| **Event Trace** | /etrace — see events firing in real-time |
| **Blizzard FrameXML** | Extracted default UI source code |

### In-Game Debug Commands
- `/dump expression` — evaluate and print Lua expressions
- `/fstack` — frame stack viewer
- `/etrace` — event trace viewer
- `/console taintLog 2` — enable taint logging
- `/reload` — reload the UI

## Connections

- API documentation: [Ch 27](ch27-api-reference.md), [Ch 28](ch28-api-categories.md)
- Addon packaging: [Ch 33](ch33-version-control.md)
- Community libraries: [Ch 32](ch32-addon-libraries.md)
