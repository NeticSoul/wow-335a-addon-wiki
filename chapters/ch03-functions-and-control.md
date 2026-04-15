---
title: "Ch 3 — Basic Functions and Control Structures"
type: chapter
source_chapters: [3]
part: "I — Learning to Program"
pages: 14
chars: 19642
created: 2026-04-15
updated: 2026-04-15
tags: [lua, functions, if, loops, for, while]
---

# Ch 3 — Basic Functions and Control Structures

## Overview

Introduces functions, conditionals (`if`/`elseif`/`else`), and basic loops (`while`, `for`, `repeat`). Functions in Lua are **first-class values** — they can be stored in variables, passed as arguments, and returned from other functions.

## Key Concepts

### Functions
- Created with `function() ... end` constructor
- Stored in variables: `hello = function() print("Hello!") end`
- **Syntactic sugar**: `function hello() ... end` is equivalent
- `local function` creates scope-limited functions
- Arguments receive `nil` if not provided
- Not all functions need `return` statements

### Function Arguments and Returns
```lua
function convert_c2f(celsius)
    local converted = (celsius * 1.8) + 32
    return converted
end
print(convert_c2f(0))   -- 32
print(convert_c2f(-40)) -- -40
```

### Functions as First-Class Values
- Can be compared with `==` and `~=`
- Can be bound to variable names, passed to functions, used as table keys
- Two functions with identical code are **distinct** values

### Conditionals
```lua
if <boolean expr> then
    -- ...
elseif <boolean expr> then
    -- ...
else
    -- ...
end
```

### Loops

**Numeric for**:
```lua
for i = 1, 10 do       -- start, stop
for i = 1, 10, 2 do    -- start, stop, step
for i = 10, 1, -1 do   -- countdown
```

**While loop**:
```lua
while condition do
    -- ...
end
```

**Repeat-until loop** (runs at least once):
```lua
repeat
    -- ...
until condition
```

- `break` exits the innermost loop
- Loop variable in `for` is **local** to the loop body

## Connections

- Builds on [Ch 2](ch02-lua-basics.md) variable and type foundations
- Functions are the basis for all addon behavior
- Extended in [Ch 5](ch05-advanced-functions.md) with varargs, multiple returns, closures
- Conditional logic used in every [event handler](../concepts/events-system.md)
