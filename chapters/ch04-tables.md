---
title: "Ch 4 — Working with Tables"
type: chapter
source_chapters: [4]
part: "I — Learning to Program"
pages: 24
chars: 35310
created: 2026-04-15
updated: 2026-04-15
tags: [lua, tables, arrays, iterators, metatables]
---

# Ch 4 — Working with Tables

## Overview

Tables are Lua's **only** data structure — used for arrays, dictionaries, records, objects, modules, and namespaces. This chapter covers creation, indexing, iteration, and introduces metatables.

## Key Concepts

### Associative Tables
- Created with `{}` constructor
- Any non-nil value can be a key
- Access via `table["key"]` or shorthand `table.key` (valid identifiers only)
- Setting a key to `nil` removes the entry

### Table Constructors
```lua
-- Record style
alice = {
    name = "Alice Applebaum",
    phone = "+1-212-555-1434",
    city = "Atlanta",
}

-- Array style (integer keys starting at 1)
colors = {"red", "green", "blue"}

-- Mixed
class_list = {
    "Alice", "Bob", "Carol",
    class_name = "ECS101",
}
```
Trailing commas are allowed and encouraged.

### Arrays
- Tables with consecutive integer keys starting at 1
- `#table` returns the length of the array part
- **Warning**: nil values in arrays cause unreliable length

### Table Library Functions
| Function | Purpose |
|----------|---------|
| `table.concat(t, sep)` | Join array elements into string |
| `table.insert(t, [pos,] val)` | Insert element (default: end) |
| `table.remove(t, [pos])` | Remove element (default: last) |
| `table.sort(t, [comp])` | Sort in-place (default: `<`) |
| `table.maxn(t)` | Largest numeric index |

### Iteration
- `ipairs(t)` — iterates array part in order (1, 2, 3, ...)
- `pairs(t)` — iterates all key/value pairs (unordered)
- `next(t, key)` — low-level iterator, returns next key/value

### Metatables (Introduction)
- `setmetatable(t, mt)` / `getmetatable(t)` 
- Metamethods: `__index`, `__newindex`, `__add`, `__tostring`, etc.
- `__index` enables prototype-based inheritance
- Used extensively in WoW's widget system

## Important Code Examples

```lua
-- Iteration
for i, v in ipairs(colors) do
    print(i, v)  -- 1 red, 2 green, 3 blue
end

for k, v in pairs(alice) do
    print(k, v)  -- unordered
end

-- Metatable inheritance
local defaults = {color = "blue", size = 10}
local mt = {__index = defaults}
local config = setmetatable({}, mt)
print(config.color)  -- "blue" (from defaults)
```

## Connections

- Tables are the foundation for [SavedVariables](../concepts/saved-variables.md)
- WoW frames and widgets are Lua tables with [metatables](../concepts/lua-language.md)
- Table iteration used in every addon that processes collections
- Extended by [Ch 5](ch05-advanced-functions.md) with `pairs`/`ipairs` generator patterns
