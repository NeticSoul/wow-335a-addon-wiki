---
title: Lua Language
type: concept
source_chapters: [2, 3, 4, 5, 6]
created: 2026-04-15
updated: 2026-04-15
tags: [lua, language, fundamentals]
---

# Lua Language

Lua is a lightweight, embedded scripting language used by WoW for all addon behavior. WoW uses **Lua 5.1**.

## Type System
Eight types: `nil`, `boolean`, `number` (double), `string`, `table`, `function`, `userdata`, `thread`. Only `nil` and `false` are falsy.

## Key Language Features

| Feature | Details | Source |
|---------|---------|--------|
| First-class functions | Store in variables, pass as args, return | Ch 3, 5 |
| Closures | Functions capture enclosing scope | Ch 5 |
| Multiple returns | `return a, b, c` | Ch 5 |
| Varargs | `function f(...)` + `select()` | Ch 5 |
| Tables | Only data structure — arrays, dicts, objects | Ch 4 |
| Metatables | `__index`, `__newindex`, operator overloading | Ch 4 |
| Patterns | `%a`, `%d`, `%w`, `*`, `+`, `()` — NOT regex | Ch 6 |
| Local scope | `local x = ...` — global by default | Ch 2 |

## WoW Lua Restrictions
- No `io` library (no file access)
- No `os` library (replaced: `date()`, `time()`, `difftime()`)
- Limited `debug` library (only `debugstack()`)
- No `loadfile` or `dofile`
- No `require` (use TOC file loading)

## Performance Notes
- `local` access is ~30% faster than global
- Cache frequently used functions: `local pairs = pairs`
- Avoid table/closure creation in hot loops
- Use `string.format` over `..` for complex strings

## See Also
- [Ch 2 — Lua Basics](../chapters/ch02-lua-basics.md)
- [Ch 3 — Functions and Control](../chapters/ch03-functions-and-control.md)
- [Ch 4 — Tables](../chapters/ch04-tables.md)
- [Ch 5 — Advanced Functions](../chapters/ch05-advanced-functions.md)
- [Ch 6 — Lua Standard Libraries](../chapters/ch06-lua-standard-libraries.md)
- [Ch 31 — Best Practices](../chapters/ch31-best-practices.md)
