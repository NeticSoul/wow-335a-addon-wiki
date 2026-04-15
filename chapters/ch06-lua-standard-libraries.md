---
title: "Ch 6 — Lua Standard Libraries"
type: chapter
source_chapters: [6]
part: "I — Learning to Program"
pages: 20
chars: 33388
created: 2026-04-15
updated: 2026-04-15
tags: [lua, stdlib, string, math, table, patterns]
---

# Ch 6 — Lua Standard Libraries

## Overview

Reference chapter for the Lua standard library functions available in WoW. Not all standard Lua libraries are available — `io`, `debug`, and `os` are mostly excluded for security. Some equivalents are provided as WoW API functions.

## Key Concepts

### Table Library
| Function | Signature | Purpose |
|----------|-----------|---------|
| `table.concat` | `str = table.concat(t [, sep [, i [, j]]])` | Join elements |
| `table.insert` | `table.insert(t, [pos,] val)` | Insert element |
| `table.remove` | `val = table.remove(t [, pos])` | Remove element |
| `table.sort` | `table.sort(t [, comp])` | Sort in-place |
| `table.maxn` | `max = table.maxn(t)` | Largest numeric key |

### String Library
| Function | Purpose |
|----------|---------|
| `string.byte(s [, i [, j]])` | Character codes |
| `string.char(...)` | Codes to string |
| `string.find(s, pattern [, init [, plain]])` | Pattern search |
| `string.format(fmt, ...)` | Printf-style formatting |
| `string.gmatch(s, pattern)` | Iterator over pattern matches |
| `string.gsub(s, pattern, repl [, n])` | Pattern replacement |
| `string.len(s)` | String length |
| `string.lower(s)` / `string.upper(s)` | Case conversion |
| `string.match(s, pattern)` | First pattern match |
| `string.rep(s, n)` | Repeat string n times |
| `string.reverse(s)` | Reverse string |
| `string.sub(s, i [, j])` | Substring extraction |

### Lua Patterns (NOT regex)
| Pattern | Meaning |
|---------|---------|
| `.` | Any character |
| `%a` | Letter |
| `%d` | Digit |
| `%w` | Alphanumeric |
| `%s` | Whitespace |
| `%p` | Punctuation |
| `%u` / `%l` | Upper / lower case |
| `%A`, `%D`, etc. | Complement (non-letter, non-digit) |
| `[set]` | Character class |
| `*` | 0 or more (greedy) |
| `+` | 1 or more (greedy) |
| `?` | 0 or 1 |
| `-` | 0 or more (lazy) |
| `()` | Capture |

### Math Library
Key functions: `math.abs`, `math.ceil`, `math.floor`, `math.max`, `math.min`, `math.random`, `math.sin`, `math.cos`, `math.sqrt`, `math.pi`, `math.huge`.

### WoW-Specific Utility Functions
| WoW Function | Std Library Equivalent |
|-------------|----------------------|
| `date()` | `os.date()` |
| `time()` | `os.time()` |
| `difftime()` | `os.difftime()` |
| `debugstack()` | `debug.traceback()` |

Additional WoW-specific: `strtrim()`, `strsplit()`, `strjoin()`, `tostringall()`, `format()` (alias for `string.format`).

### String Method Syntax
Lua supports OOP-style calls on strings:
```lua
("hello"):upper()  -- "HELLO"
s:find("pattern")  -- same as string.find(s, "pattern")
```

## Connections

- Patterns used extensively in [tooltip scanning](../concepts/tooltips.md)
- `string.format` is the most-used formatting tool in addons
- String functions critical for parsing [combat log events](../concepts/combat-log.md)
- `table.sort` and `table.insert` used in virtually every addon
- See [Lua Language](../concepts/lua-language.md) for the complete language overview
