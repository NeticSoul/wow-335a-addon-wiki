---
title: "Ch 31 — Appendix A: Best Practices"
type: chapter
source_chapters: [31]
part: "V — Appendices"
pages: 24
chars: 48493
created: 2026-04-15
updated: 2026-04-15
tags: [best-practices, performance, code-quality, conventions]
---

# Ch 31 — Appendix A: Best Practices

## Overview

Generally accepted practices for addon development covering code quality, readability, performance, and WoW-specific patterns.

## Key Practices

### General Programming
- **Use meaningful variable names** — `GetBagItemCount(itemID)` not `ic(i)`
- **Keep functions short and focused** — single responsibility
- **Use local variables** wherever possible (faster lookup, avoids taint)
- **Comment complex code** — explain why, not what
- **Avoid premature optimization** — profile first

### Lua-Specific
- **Use `local`** — global access is ~30% slower than local
- **Avoid global pollution** — wrap addon in a single table namespace
- **Use `string.format`** over concatenation for complex strings
- **Cache frequently called functions** locally:
  ```lua
  local pairs = pairs
  local UnitHealth = UnitHealth
  ```
- **Avoid creating tables/closures in hot loops** (GC pressure)

### WoW-Specific
- **Throttle OnUpdate handlers** — don't do expensive work every frame
- **Unregister events** you no longer need
- **Use event-driven designs** over polling
- **Profile with `/dump GetFramerate()`** and `debugprofilestart()`/`debugprofilestop()`
- **Namespace your globals** — `MyAddon.SomeFunction`, not bare `SomeFunction`
- **Handle nil values defensively** when calling API functions

### Performance Tips
- Table indexing is faster with **string keys** than constructed keys
- Use `select()` instead of creating intermediate tables for varargs
- Minimize frame creation — reuse frames when possible
- Use `SetScript("OnUpdate", nil)` to disable unused handlers

### Taint Avoidance
- Don't unnecessarily touch global variables from `_G`
- Use `hooksecurefunc` instead of raw function hooking for Blizzard functions
- Test with `/console taintLog 2` to detect taint issues

## Connections

- Performance relates to [OnUpdate](ch18-onupdate.md) usage
- Taint avoidance relates to [Secure Execution](../concepts/secure-execution.md)
- Code organization relates to [Addon Structure](../concepts/addon-structure.md)
- See [Addon Libraries](../concepts/addon-libraries.md) for reusable code patterns
