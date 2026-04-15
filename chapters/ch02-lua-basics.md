---
title: "Ch 2 — Exploring Lua Basics"
type: chapter
source_chapters: [2]
part: "I — Learning to Program"
pages: 26
chars: 38644
created: 2026-04-15
updated: 2026-04-15
tags: [lua, basics, variables, types, operators, strings]
---

# Ch 2 — Exploring Lua Basics

## Overview

Comprehensive introduction to the Lua programming language. Lua is lightweight, embedded, and often compared to Python for ease of use. WoW uses **Lua 5.1**.

## Key Concepts

### Running Lua
Three interpreter options:
1. **WowLua** — in-game addon (`/lua` or `/wowlua`)
2. **WebLua** — browser-based at wowprogramming.com
3. **Local interpreter** — downloaded Lua binary

### Variables and Types
Lua has **eight** basic types:

| Type | Description | Example |
|------|------------|---------|
| `nil` | Absence of value | `nil` |
| `boolean` | True/false | `true`, `false` |
| `number` | Double-precision float | `42`, `3.14` |
| `string` | Immutable text | `"hello"`, `'world'` |
| `table` | Associative array | `{key = "value"}` |
| `function` | First-class callable | `function() end` |
| `userdata` | C data (opaque to Lua) | Frames, widgets |
| `thread` | Coroutine | `coroutine.create()` |

- Variables are **global by default** — use `local` keyword for local scope
- `type()` function returns the type name as a string
- `nil` and `false` are falsy; everything else is truthy (including `0` and `""`)

### Operators
- **Arithmetic**: `+`, `-`, `*`, `/`, `%`, `^`
- **Relational**: `==`, `~=` (not equal), `<`, `>`, `<=`, `>=`
- **Logical**: `and`, `or`, `not`
- **String concatenation**: `..`
- **Length**: `#` (works on strings and array tables)

### Strings
- Delimited by `"double"`, `'single'`, or `[[long brackets]]`
- Immutable — operations return new strings
- Key functions: `string.len()`, `string.sub()`, `string.find()`, `string.format()`
- Coercion: numbers auto-convert to strings in concatenation, strings to numbers in arithmetic

### Scope
- `local` restricts variable to current block
- Each line in the interactive interpreter is its own scope
- Best practice: always use `local` unless global access is intentionally needed

## Important Code Examples

```lua
-- Type checking
print(type(42))        -- "number"
print(type("hello"))   -- "string"
print(type(nil))       -- "nil"

-- String concatenation
greeting = "Hello" .. " " .. "World"
print(#greeting)       -- 11

-- Logical operators (short-circuit)
print(nil or "default")   -- "default"
print("a" and "b")        -- "b"
```

## Connections

- Builds on [Ch 1](ch01-programming-for-wow.md) introduction
- Foundation for [Ch 3](ch03-functions-and-control.md) (functions, conditionals, loops)
- Types and scope are essential for [Lua Language](../concepts/lua-language.md) concept
- `type()` and string functions used extensively throughout the WoW API
